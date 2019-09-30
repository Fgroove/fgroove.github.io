Javalayout: post
title:   "完美世界"
date:   2019-09-27 08:55:01 +0800
categories: "Job Hunting"
tag: "Interview"

* content
{:toc}




# 完美世界

## 数据库

## 基础

#### 构造函数

class *p[4],指针调用几次构造函数？

0次，创建类的指针时，不会调用构造函数。

## 编程

### 1.不对号入座

全部坐错的我们称之为“不对号入座”。

| N=1  | 2    | 3    | 4    | 5    | 6    | ...... |
| :--: | ---- | ---- | ---- | ---- | ---- | ------ |
|  0   | 1    | 2    | 9    | 44   | 265  | ...... |

满足数列 $A_n = (n-1)*(A_{n-1}+ A_{n-2})$

### 2.01背包问题

| value | weight | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |        |
| :---: | :----: | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ------ |
|   5   |   2    | 0    | 5    | 5    | 5    | 5    | 5    | 5    | 5    | 5    | 5    | 第一件 |
|   4   |   2    | 0    | 5    | 5    | 9    | 9    | 9    | 9    | 9    | 9    | 9    |        |
|   3   |   3    | 0    | 5    | 5    | 9    | 9    | 9    | 12   | 12   | 12   | 12   |        |
|   5   |   5    | 0    | 5    | 5    | 9    | 9    | 9    | 12   | 12   | 14   | 14   |        |
|   2   |   1    | 2    | 5    | 7    | 9    | 11   | 11   | 12   | 14   | 14   | 16   |        |

### 3.100个坐飞机，

第一个人随机坐，其余按顺序，第100个人坐到自己座位的概率是1/2.

等价于2-99个人遇到第一个人让其换位，第一个人和第100个人抢位置，和人数无关。

### 4.两端取数，先取的最大值

```C++
#include <iostream>
#include <vector>
using namespace std;

int getFirst(int &n, vector<int> &arr) {
    vector<int> sum(n + 1, 0);
    vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
    for (int  i = 1; i <= n; i++) {
        sum[i] = sum[i - 1] + arr[i];
    }
    for (int i = n -1; i > 0; i--) {
        dp[i][2] = max(arr[i], arr[i + 1]);
        for (int j = 3; i + j <= n + 1; j++) {
            dp[i][j] = max(sum[i + j - 1] - sum[i] - dp[i + 1][j - 1], sum[i + j - 1] - sum[i] - dp[i][j - 1]);
        }
    }
    return dp[1][n];
}

int main() {
    int n;
    cin >> n;
    vector<int> arr(n, 0);
    for (int i = 0; i < n; i++) {
        cin >> arr[i];
    }
    int ans = getFirst(n, arr);
    cout << ans << endl;
    return 0;
}

```

