---
layout: post
title:  RedQueen
date:   2019-04-09 8:55:01 +0800
categories: 基于动态的恶意软件分析
tag: 反沙箱对抗技术
---
* content
{:toc}


# REDQUEEN(NDSS'19)

---

## REDQUEEN

I'm going to talk about fuzzing with **Input-to-State Correspondence**.

## AFL

beacuse our Fuzzer is based on AFL in quite some details. We're quickly going to revise the way AFL works.

So basically AFL is a feedback driven mutational Fuzzer that means we have a target program and some kind of seed corpus which might for example contain the empty file or something like that.

And I create a mutation of this seed input and then put in into the program and observe the coverage that this input triggered and if this coverage contained any new **code done** we are going to save the input in the queue 

and this process is repeated and inputs that do not trigger new coverage actually get removed from the queue.

and eventually the fuzzer can thus learn evolutionary what inputs are actually interesting and builds up a corpus of test cases .

## Roadblocks

So, however, with current fuzzers there is some roadblocks that often hindered the Fuzzer from continuing.

## Magic Bytes

So the most common example is something like this where a magic bytes are used for example in a header or something like that where a huge number or like 4-8 bytes need to be guessed correctly in a automated check, 

and the fuzzer well it basically does not hava a chance to guess that in a reasonable amount of time, 

## Checksums

and somewhat more severe example is checksums, checks and sometimes you even find nested checksum checks where the input that would cause the crashes looks something like this,

and there's an outermost checksum which verifies the rest of the input and there's an innermost checksum and that actually checks the actual content.

now the finding the actual content is easy for the fuzzer but for every mutation the fuzzer has to solve all of the checksums on the outside so afl's unable to deal with that kind of stuff. 

## Related Work

so there is a whole bunch of related work that tries to solve similar issues, and all of those indeed are able more or less to deal with magic bytes.

However a large number of these approaches are not able to deal with checksums or even nested checksums.

I would like my approach to be a fully automatic which excludes layout which needs massive amount of human interaction.

I like binary only fuzzing and I dislike precise environment models because they make it really hard to apply the technique for example for another operating system.

This is why we see the need for something new.

## IDEA

So what is that basically the idea or the observation that we have is  that program often display something that we called input-to-state correspondence, 

and that is basically that the input value of some part of the input pretty much shows up in the state of the program in a more or less unchanged way, 

## DESIGN

and we designed a fuzzer that exploits this kind of observation to solve the challenges that we talked about earlier. 

so how does it work, I like binary only fuzzing so we assume this is a binary target and we have some compare instruction that in this case implements a magic bytes check, checks if some value is equal to the string ABCD, 

and what we do is we identify or compare instructions we place a hook at, or compare instructions and then we take some input in this case.

for example, seedvalue, and execute the input with the hooks and the hooks are triggered. 

we observe the left hand side and the right hand side of the compare instructions. 

we do this not only for compare instructions with the right hand side is
a constant, we do that for all compare instructions actually. 

and then we create a custom mutation that is specific to
this exact input which we can use in
this case to replace some part of the
input because we know what was compared
and obtain a new input that done
hopefully and in this case actually
solves the compare
however there is obviously a whole bunch
of issues with this rather simple
approach that crop up in practice and
one of them is well stuff does not show
up in the state like it does sometimes
but not always there's a whole bunch of
encoding that are really commonly used
by programs that we have to deal with
somehow and usually people would be
using symbolic execution for that but we
don't like that so what you can do
instead is simply apply the encoding on
the observed value in the state and then
use that as limitation and sometimes we
see compare instructions that are
actually not used for equality checks
for anybody for inequality checks and we
can use the observed mutation of the
observed values and derive mutations
that will actually satisfy the
inequalities by adding or subtracting
one on the right-hand side of the
replacement rule then I mean we're doing
binary instrumentation here that has the
tendency to be quite slow which is true
so in our experiments typically we
observe the slowdown of ten times two
sometimes even hundred times so we
cannot do this for every fuzzing
execution because there would be way too
expensive
rather we do that only for new cue
entries so like if you're fuzzing for a
few days maybe a week or two then you
will see a lot of executions but you
will still only find a relatively small
number of cue entries and then it's okay
if you have thousand or ten thousand or
maybe like sixty thousand executions
where you only have ten executions per
second instead of thousand so and so far
we only talked about compare
instructions compilers are really nasty
beasts they use all kinds of weird
instructions to implement compare
instructions so I particularly like the
XOR and sup and I didn't surprise me
that much
but actually to use a lot effective
address to implement a compare and
struction did somewhat surprised me
and we also hook oil call instructions
because we also want to solve mem
compare and string compares and similar
functions and here we simply assume that
the first two arguments to the corner to
the function that we're calling
according to various calling convention
so we just try all of them appoint us
and if they actually point towards map
memory then we dump the first hundred
and twenty eight bytes from this memory
and treat them as values that are being
compared
which obviously finds way more
comparisons that there actually are but
we don't care because we have like
thousands of executions per second so
it's okay to have a few false positive
mutations however there's a limit to
that
so while building this we ended up with
inputs like this so for file system
drivers in that case there's examples
where the smallest possible file system
that the driver will mount is is like 64
kilobyte and it only consists of zeros
and now we have this mutation rule that
says well try to replace the zero with
awakes forty-four something like that
and now we have all the variants that
are introduced earlier with the encoding
and the plus 1 minus 1 so that quickly
adds up to lots and lots of trials that
we need to perform which actually slows
down the fuzzing process so we came up
with something that is basically a
really low fidelity way of performing
taint tracking without actually
performing taint tracking and the idea
here is that we created another version
of the same input that still covers the
same that still triggers the same paths
but when we try to replace as many bytes
as possible by random values so we just
try to spam random bytes into the input
and if they still trigger the same path
then we keep doing that and this gives
us something which we call a colorized
input and now we observe the compare
instructions from on the colorized input
and on the original input and there's
far fewer candidate positions and if we
actually take only the intersection of
those positions then we typically end up
with a very small number of candidate
positions that we can actually use for
fuzzing so this actually solves the
magic white issue in in our experience
for most targets I mean this is
obviously not perfect but pretty good
but they're still they check some stuff
and there's been some other papers
such as tifa's most recently but also
tents go up a bit earlier and all of
those papers pretty much have the same
approach as do we we try to identify the
checksum check with some heuristic and
then we just patch it out now where we
do something different from previous
approaches is that this obviously leads
to a whole bunch of false positives and
so previous approaches typically use
symbolic execution after the fuzzing
campaign to weed out false positive
crashes but that has the disadvantage
that the professor will fill up the
queue with inputs that are not actually
triggering interesting coverage if you
have a false positive patch in the patch
set so what we do instead is we fix any
new input as it is found so before we
add a new input to the queue we actually
try to fix all of the patched
instructions using the input to state
based method that we introduced earlier
and if that fails we remove the
offending patch and we discard the input
so that we always have a queue full with
valid inputs lastly like I've claimed
that we can do nested check sums which
happens sometimes so for example P and G
typically has nested check some checks
this has the problem where we need to
fix the checks in the right order and we
do that by topologically sorting the
dependency graph so we can observe that
if we change one of the check sums then
some other check sums become invalid and
this gives us a dependency graph and
then we can topologically saw it and
then we can use the techniques for magic
bite fixing to fix the checks and checks
in the right order so we implemented
that obviously in a prototype called Red
Queen which is based on Kol Kol as some
of you probably know is a kernel fuzzer
that we developed earlier and that is
why the target is actually running
inside of a VM because if you're fuzzing
your own kernel then you will lose a lot
of treasures to your kernel corrupting
whatever you use to save the crashes and
also this makes parallelization on the
same hardware easier and so on and so on
so there's basically three
things that we need we need to be able
to get coverage and Kol does that by
using enthalpy T which is a hardware
cell rated at racing solution developed
by Intel as the name suggests and we use
that and it's actually quite fast it's
quite comparable as we later see to the
compiler biased instrumentation if you
do it properly then we need a way to
communicate inputs with the target and
we do that by basically directly writing
them into the memory of the virtual
machine so the target has some fixed
buffer where we just like from the point
of view of the target the inputs just
magically appear and when the target has
consumed the input it triggers a hyper
call that informs our father that in
Nuuk Tata that in your input is needed
and lastly for Red Queen we actually
need a way to instrument the compare
instructions and the way we do that is
we use kb m and we use k vm debug
facilities such as placing breakpoints
in the code and then configuring the vm
control register such that the debug
exceptions are actually delivered to us
instead of the program that's actually
run at the target and this is design
allows us we extended K of l2 to also
target bring three programs and this
allows us to target a whole bunch of
different environments such as ring 3 or
ring 0 and and different in principle
different operating systems obviously we
also used our implementation to perform
an evaluation so the most interesting
experiment in my opinion is the code
coverage overtime and we evaluated
against a whole bunch of related fuzzers
that aim to overcome similar issues a
Red Queen obviously is the red line kfl
without Red Queen patches is the dark
blue line then we have FL la finta with
laughs into a patch which is the dark
green line ever fast is the bright green
line loser
well that ok Klee symbolic executors and
lastly hum fast but we used one force in
the enthalpy T mode so this is not the
instrumentation based home for us
and we evaluated that on all of the
binutils that actually consumed a single
input file without writing through the
input file and as you can see using
input as dead correspondence is actually
quite helpful we also measured how many
executions per second per second were
performed by various fuzzers and as you
can see our binary only approach in the
form of Kol is actually quite comparable
to the compiler based approaches Red
Queen performs much slower mostly not
because we're like having a massive
overhead but because we find more slow
paths so as we find more and more
coverage the paths that we trigger are
actually slower also we looked at the
different mutations that we implemented
in care of else or the deterministic
phase and the havoc phase are basically
one-to-one copies of what AFL does as is
the splicing mutation and then we have
Adams arm which is a well known other
father which we also implemented but as
you can see that wasn't too helpful and
using input to state correspondence is
actually pretty good bang for the buck
if you count the amount of time spent
and the amount of passed phone though
these numbers have to be taken with a
grain of salt because input to state
correspondence was the first stage that
we used and therefore it had a chance to
find many easy new paths that otherwise
maybe some other parts would have found
and also input to state corresponds
typically we had like over the fuzzing
campaign of the experiment we had less
than 10 minutes of time that we spent in
Red Queen because there isn't that much
to do we also evaluated on the
well-known lava em dataset and as you
can see we found over a hundred percent
in all of the targets you don't really
see that for unique but in unique we
found one additional bucket and in most
cases we needed less than five minutes
to find all of the planted boxes and we
even were able to find all of the bugs
if we do not have access to seeds then
we also found a whole bunch of bugs
which I personally think is a pretty bad
benchmark but anyway here they are
there's been quite a few bucks in
binutils you know on the Linux kernel
which demonstrates that we're able to
scale across various various target
environment and also when on programs
such as tcpdump imagemagick ftk AAC
which is used in all android phones for
media decoding and so on and so forth so
that's it from my side this is just an
overview so if you have any questions
please feel free to ask now so I think I
saw in like your evaluation a slice
there were a couple of programs were
like the Red Queen wasn't really
improving on like here just like cxx
failed on yes so can you comment on like
why that was the case I mean recreant
only solves a specific kind of issues
right it solves the kind of magic bite
issues and C X X field is a program that
takes strings that are encoded that are
encoded mangled symbols for C++ and
turns them into the plain symbols and
there's simply no magic bytes involved
there
so it's just one byte checks all the
time so we cannot really add anything
there okay thank you hi Eric Polly Penn
State first of all thanks for a good
talk and thanks for including confidence
intervals and your evaluation sorry I
didn't thanks for including confidence
intervals in your evaluation yeah a
couple questions so first of all I'm
curious since you're sort of relying on
a lot of these I guess C language
semantics did you did you analyze how
your fuzzer would adapt to say an
obfuscated executable I know we did not
okay and then in addition at this point
we're kind of seeing that the lava and
data set has been effectively solved for
this and I would not recommend anyone to
use love for EM at this point
yeah evaluation yes so so what do you
think what do you think that the next
fuzzing dataset will look like I
actually think that a large number of
real-world programs with coverage is the
best way to evaluate fuzzing performance
because a at least so far none of the
fuzz
are able to find bugs that are not
covered and be all of the interesting
behaviors also reflected in code
coverage so any kind of constraint that
is actually hard to solve for a father
is also a constraint that will result in
coverage other than that there is the
paper biomechanics actually he's not the
first author but only evaluating father
where he has a really interesting
approach on evaluating fathers based on
source to control history which i think
is probably the best way to do there's
also the Google fuzzing data set which
can be used and I think CGC is actually
somewhat underrated because most parent
fathers who focus on five formats and
CGC is actually more of an interactive
games kind of data set I think it would
be valid hi I'm Goku from Arizona State
University so I had a question literally
are patching so do you assume that only
the true would be evaluated all you
assume that only the true branch would
be always taken for the patching or I
suggest you ignore defaults branch as
well so do you out so you said that in
patching you assume that all the true
branches are taken as in yet us you
patch it in such a way that it goes the
true part already right it's to true so
they'll assume that defaults branches
won't have any bugs or I mean we don't
patch the jumps right so we patched the
comparison not the jumps and yes for the
patches we actually just assume that the
interesting property is that the values
become equal because for hash checks to
be wrong that's actually mostly not hard
for fathers to to manufacture and we do
not like change the branching behavior
itself and we're only old we do not
actually check if the comparison
resulted in true or false we just check
whether there was new coverage in yeah
did that answer your question yes thank
you
[Applause]