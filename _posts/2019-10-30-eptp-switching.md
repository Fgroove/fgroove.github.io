---
layout: post
title:   "双视图切换开发"
date:   2019-10-30 08:55:01 +0800
categories: 基于动态的恶意软件分析
tag: 双视图

---

* content
{:toc}




# 双视图开发切换

## 思路

### 1.背景

Intel处理器下，KVM利用VMX root和non-root模式来区分hypervisor和guest。

* VMEntry：内核模式下，由KVM的VMLAUNCH指令触发，guest需要信息填充在CPU的VMCS。
* VMExit：guest OS处理越权事件触发，比如访问硬件或中断，由硬件自动完成VMExit，exit reason等信息记录在VMCS，KVM据此采取下一步行动。

### 2.问题

VMeixt之后，驱动切换视图

## KVM开发

### 1.` arch\x86\kvm\x86.c\vmx_set_ve_info()  `

**描述：** \#VE信息域的设置  

```C
void vmx_set_ve_info(struct kvm_vcpu *vcpu, gfn_t gfn)
{
	kvm_pfn_t pfn=gfn_to_pfn(vcpu->kvm,gfn);
	kvm_get_pfn(pfn);
	vmcs_write64(VIRTUAL_EXCEPTION_INFO,pfn<<PAGE_SHIFT);
}
```

注释：对于hypercall的统一处理放在了`x86.c`文件的`kvm_emulate_hypercall()`中，其中a0存储的是RBX传入的值，表示#VE信息域（就是一空闲的内存页面）的虚拟机物理地址。

* Step 1：gfn_to_pfn//获取gfn（虚拟机物理页框号）对应的pfn（宿主机物理页框号）

* Step 2：kvm_get_pfn//根据pfn获取到对应的页面

* Step 3：将pfn对应的宿主物理地址写入到字段VIRTUAL_EXCEPTION_INFO字段中

*注：#VE信息域的设置一般不会出错*



### 2. `arch\x86\kvm\mmu.c\kvm_mmu_get_page()`  

**描述：**分配页表页

```C
//------------------------------------------------------------------------------
	u64 *addr=sp->spt;
	unsigned index;
	for(index=0;index<512;index++)
		addr[index]|=SUPRESS_VE_MASK;
//------------------------------------------------------------------------------
```

新增的代码是为了抑制每个新分配的页表页中的所有页表项的抑制VE位，这样就可以避免处理因为`not_present`产生的VE.

### 3 `arch\x86\kvm\mmu.c\kvm_set_gfns_access()`

**描述：**修改页表项权限

```c
int kvm_set_gfns_access(struct kvm_vcpu *vcpu,gfn_t gfn,unsigned nr,unsigned access)
{
	
	struct kvm_memory_slot *slot=gfn_to_memslot(vcpu->kvm,gfn);
	unsigned long npages=slot->npages;
	gfn_t base_gfn=slot->base_gfn;

	if(!slot->access_bitmaps)
	{
		slot->access_bitmaps=(unsigned char*)vmalloc(npages);
		memset(slot->access_bitmaps,0,npages);
	}
	u8 *access_bitmaps=slot->access_bitmaps;

	u64 gfn_index;
	struct kvm_shadow_walk_iterator iterator;	
	for(gfn_index = gfn;gfn_index < gfn+nr; gfn_index++)
	{
		u64 index=gfn_index-base_gfn;

		if((access_bitmaps[index]==FLAG_KERNEL&&access==FLAG_DATA))
			continue;

		access_bitmaps[index]=access;

		if(access==FLAG_KERNEL)
			continue;

		unsigned view;
		for(view=0;view<2;view++)
		{
			for_each_shadow_entry(vcpu,(u64)gfn_index<<PAGE_SHIFT,iterator,view)
			{
				
				if(iterator.level==1)
				{
					if(is_shadow_present_pte(*iterator.sptep))
					{
						spin_lock(&vcpu->kvm->mmu_lock);
						*iterator.sptep|=ACC_ALL;
						if(access==FLAG_DATA
							||(view==0&&access==FLAG_MALWARE)
							||(view==1&&access==FLAG_USER))
						{
							*iterator.sptep&=NOT_EXECUTABLE_MASK;
							*iterator.sptep&=CLEAR_SUPRESS_VE_MASK;
						}else
						{
							*iterator.sptep|=SUPRESS_VE_MASK;
						}
						
						kvm_flush_remote_tlbs(vcpu->kvm);
						spin_unlock(&vcpu->kvm->mmu_lock);
					}
					
					break;
				}
				if(!is_shadow_present_pte(*iterator.sptep))
				{
					break;
				}
			}
		}
		
	}
	return 1;
}
```

注释：gfn：虚拟机物理页框号  nr：页面数  access：访问权限标识  

* Step 1：gfn_to_memslot //获取gfn对应的内存槽，每个gfn都由内存槽管理，每个内存槽对应着多个gfn，内存槽管理着虚拟机所有的页面  

* Step 2：获取内存槽管理的gfn的基地址base_gfn和总的页面数npages。  

* Step 3：分配存储访问权限标识的位图（其实就是数组）。  

* Step 4：为传入的所有[gfn,gfn+nr）对应的页表项设置相应的访问权限  

  * Step 4.1：gfn-base_gfn//获取该gfn在位图中的索引值  
  * Step 4.2：如果位图中索引处的访问权限为KERNEL，且当前设置的访问权限为DATA。则表示此时为虚拟机全部内存页面初始化阶段，即将所有的非内核页面都设为数据段。  
  * Step 4.3：在位图中索引出对应的位置存储访问权限  
  * Step 4.4：在最后一级页表页面设置相应的页表项权限（仅修改存在的页表项的访问权限`is_shadow_present_pte(*iterator.sptep)`）     

  

页表项的修改细节，0，1，2分别表示R，W，X权限  

* Step 1：`spin__lock`//加锁  
* Step 2：`*iterator.sptep|=ACC_ALL`;//为页表项设置所有的访问权限  
* Step 3：如果`access=DATA`或者`view=0&&access=MALWARE`（在正常视图中访问恶意程序）或者`view=1&&access=USER`（在监控视图中访问非恶意的用户态程序）   
  * Step3.1：`*iterator.sptep&=NOT_EXECUTABLE_MASK;`//将页表项第二位置为0  
  * *Step3.2：`*iterator.sptep&=CLEAR_SUPRESS_VE_MASK;`//清除SUPPRESS_VE（页表项第63位），这样在访问到不可执行的代码段时，会交由自定义的Windows驱动处理   
  * Step3.3：`*iterator.sptep|=SUPRESS_VE_MASK;`//设置可执行页面的SUPPRESS_VE位，避免出现一些意想不到的错误  
* Step 4：`kvm_flush_remote_tlbs(vcpu->kvm);`//刷新TLB，让设置的页表项生效  
* Step 5：`spin_unlock(&vcpu->kvm->mmu_lock);`//解锁  
* Step 6：`if(!is_shadow_present_pte(*iterator.sptep)) { break;} ` //当任意一级页表项不存在时直接退出，等到利用`__direct_map`创建的相应的页表项之后再进行相应的修改  

### 4. `arch/x86/kvm/mmu.c/tdp_page_fault()`

**描述：** 修改该函数使其强制进行小页映射

```C
force_pt_level=true
```

### 5. `arch/x86/kvm/mmu.c/__direct_map()`

**描述：**建立页面的映射关系

* Step 1：`gfn_to_memslot`//获取gfn对应的内存槽  
* Step 2：`if(slot&&slot->access_bitmaps)`//需要在访问位图分配完成之后，即虚拟机的全部内存页面分为数据段和内核段之后，再进行下面的操作  
  * Step 2.1：获取该gfn在访问位图中的访问权限标志  
  * Step 2.2：当该gfn为数据段或者在相应的视图中不可执行时，修改传入`mmu_set_spte`中表示访问权限的字段`access_temp`,将其设为`NOT_EXECUTABLE`（0x6,注：虽然页表项的0，1，2位分别表示R,W，X，但是在mmu_set_spte中传入的0，1,2分别代表X，W,R，所以传入6时页表项无执行权限）  
* Step 3：清除无执行权限页面对应的页表项的抑制VE位,并刷新TLB（注：这里不用加锁，因为调用这个函数之前已经加锁了）  

## Windows驱动开发

### 1. `init_vcpus_veinfo`

为多个vCPU设置相应的20号中断处理向量以及设置#VE信息域。

```c#
NTSTATUS init_vcpus_veinfo(VOID)
{
	USHORT				cs;
	IDT_INFO			idtr = { 0 };
	ULONG64				IdtBase;
	ULONG				i, numOfProcessors;
	PROCESSOR_NUMBER	processorNumber;
	GROUP_AFFINITY		affinity, oldAffinity;
	NTSTATUS			status;

	InitializeListHead(&Malware_Head);

	numOfProcessors = KeQueryActiveProcessorCountEx(ALL_PROCESSOR_GROUPS);
	VeInfo_GVA = (u64 **)ExAllocatePoolWithTag(NonPagedPool, sizeof(u64*)*numOfProcessors, TAG);
	RtlZeroMemory(VeInfo_GVA, sizeof(u64*)*numOfProcessors);

	for (i = 0; i < numOfProcessors; i++) {
		status = KeGetProcessorNumberFromIndex(i, &processorNumber);
		if (!NT_SUCCESS(status)) {
			DbgPrint("Error\n");
			return status;
		}
		affinity.Group = processorNumber.Group;
		affinity.Mask = 1ULL << processorNumber.Number;
		affinity.Reserved[0] = affinity.Reserved[1] = affinity.Reserved[2] = 0;
		KeSetSystemGroupAffinityThread(&affinity, &oldAffinity);

		//---->hook idt20
		__sidt(&idtr);
		IdtBase = idtr.BASE;
		PKIDTENTRY64 idtEntry = IdtEntry(IdtBase, X86_TRAP_PF);

		cs = idtEntry->u.Selector;
		idtEntry = IdtEntry(IdtBase, X86_TRAP_VE);
		PackEntry(idtEntry, cs, (ULONG64)__gate_entry);
		memcpy(IdtEntry(IdtBase, X86_TRAP_VE), idtEntry, sizeof(idtEntry));

		VeInfo_GVA[i] = MmAllocPage();
		PHYSICAL_ADDRESS address = MmGetPhysicalAddress(VeInfo_GVA[i]);
		//DbgPrint("the ve info address is %llx \n", address.QuadPart >> PAGE_SHIFT);
		kvm_hypercall_set_veinfo(address.QuadPart >> PAGE_SHIFT);
		//end hook idt20

		KeRevertToUserGroupAffinityThread(&oldAffinity);
	}

	return STATUS_SUCCESS;
}
```



注释：主要就是两个作用：设置VE处理函数（20号中断向量），设置#VE信息域，其中#VE信息域的设置需要KVM层的配合，Window驱动层只需要分配一空闲的内存页面，然后将该页面对应的物理页框号告知KVM层即可。

* Step 1:`__sidt`//获取中断描述表的地址值
* Step 2：获取中断描述表的基地址
* Step 3：`IdtEntry(IdtBase, X86_TRAP_PF);`//获取PF对应的中断向量。
* Step 4：`idtEntry->u.Selector;`//获取中断向量中的代码段Selector字段（因为需要自定义VE的中断处理函数，而每个中断向量中的Selector的值是相同的，所以利用Step 3,4可以为VE中断向量设置正确的Selector值）
* Step 5：idtEntry = IdtEntry(IdtBase, X86_TRAP_VE);//获取VE中断向量在IDT中对应的项
* Step 6：`PackEntry(idtEntry, cs, (ULONG64)__gate_entry);`//设置自定义的中断处理函数 `__gate_entry`

\#VE信息域的设置不过多介绍，就是分配一空闲的内存页面而已

### 2.`__gate_entry`

\#VEx处理函数

```c#
nonono
```

注释： ` TRAP_ENTER ept_no_swap  1`和`TRAP_EXIT ept_ret_no_swap`这些语句不需要管  

VMFUNC的汇编代码  

```assembly
mov  eax,0    
mov  ecx,0    
db  0fh,01h,0d4h  
```

其中eax表示VM Function中函数编号，0号为eptp Switching，ecx 表示EPTP的索引值。上述代码表示切换到正常视图中处理#VE。  

其它的代码参考github https://github.com/asamy/ksm中vmx.asm代码  

### 3. `init_pfns_mem_access`

获取虚拟机全部内存页面的物理地址及内核区域的物理地址

```c#
NTSTATUS init_pfns_mem_access(VOID) {
	int status = 0;
	status = initKernelModuleEPTPermissions();
	if (status != STATUS_SUCCESS) {
		DbgPrint("Cannot set kernel module code ept permissions!\n");
		return status;
	}
	status = initAllPhysicalPageEPTPermissions();
	if (status != STATUS_SUCCESS) {
		DbgPrint("Error in init all physical page ept permissions!\n ");
		return status;
	}
	return status;
}
```

注释：主要为两个函数` initKernelModuleEPTPermissions`//获取所有的内核页面物理地址；

`initAllPhysicalPageEPTPermissions`//获取虚拟机所有的内存页面的物理地址     

* `initKernelModuleEPTPermissions`利用`GetSystemModuleInfo`获取已加载的内核模块，内核模块组成了虚拟机的内核区域  
* `initAllPhysicalPageEPTPermissions`利用`MmGetPhysicalMemoryRanges`获取虚拟机所有的内存地址段，因为有些内存地址是不经过EPT映射的，所以返回的内存是一段段的  

在获取到相应的物理地址会都将其转换为物理页框号（右移12位），然后赋予相应的访问标志即可，KVM会保存相应的内存页面的访问标识  

### 4.`__ept_handle_violation`

\#VE处理函数

注释：

* Step 1：`VeInfo_GVA[Index];`//获取vCPU对应的#VE信息域地址

* Step 2：`u64 virtAddr = info->gla; `

   `ULONG64 physical=MmGetPhysicalAddress((PVOID)virtAddr).QuadPart;` //获取发生#VE的虚拟地址对应的虚拟机物理地址（不建议采用info中的gpa）

* Step 3：`PsGetCurrentProcess();` `currentProcName=(PTSTR)((ULONG64)eprocess + 0x2e0);`//获取发生#VE的进程的进程名

* Step 4：当执行目标程序的镜像代码时，除了设置相应的页表项的权限外， 1）SwitchToAntherView（1）将视图切换到监控视图；  2）开启PT

* Step 5：当执行非目标程序的镜像代码时，除了设置相应的页表项权限外，当此时处于监控视图时 1）dump已生成的PT数据 2）关闭PT

 

注：因为__gate_entry一开始将视图切换到了正常视图，所以在Step 5中需要判断发生错误时的视图是否为监控视图