---
layout:     post   				    # 使用的布局（不需要改）
title:      Self-used Algorithm and Data Structure Templates			# 标题 
subtitle:   Some self-used data structure and algorithm templates(keep updating) #副标题
date:       2021-03-23 				# 时间
author:     Leo 						# 作者
header-img: img/post-bg-bloodborne.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Data Structure
    - Algorithm
    - Go
---

# **Data Structure**

## Hash 

Using built-in type `map`.

## Stack

Using built-in type `slice`  FILO

## Queue

Using built-in type `slice`  FIFO

## Linked List

```go
type struct ListNode{
    //*ListNode Prior
    Val int
    *ListNode Next
}
```

## Binary Tree

```go
type sruct Tree{
    Val int
    *Tree Left
    *Tree Right
}
```

## Heap

Use package `container/heap` in Go standard library.

```go
// heap.Interface
type Interface interface {
    sort.Interface
    Push(x interface{}) // add x as element Len()
    Pop() interface{}   // remove and return element Len() - 1.
}

// sort.Interface
type Interface interface {
        Len()                     // return the sum of the elements
        Swap(i, j interface{})    // swap the element i and element j
        Less(i, j interface{})    // compare if element i is less than element j
}
```

Any type that implements this interface may be used as a min-heap.

An integer min-heap to implement a priority queue

```go
package main

import (
	"container/heap"
	"fmt"
	"sort"
)
// An IntHeap is a min-heap of ints.
type IntHeap []int

func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

// Push and Pop use pointer receivers because they modify the slice's length,
// not just its contents.
func(h *IntHeap) Push(x interface{}){
    *h=append(*h,x.(int))
}

func(h *IntHeap) Pop()interface{}{
    n:=len(*h)
    x:=(*h)[n-1]
    (*h)=(*h)[:n-1]
    return x

}
//return the k th biggest element
func findKthLargest(nums []int, k int) int {
    h:=IntHeap{}
    for _,v:=range nums{
        heap.Push(&h,v)
    }
    res:=0
    for i:=0;i<k;i++{
        res=heap.Pop(&h).(int)
    }
    return res
}
```

## Graph

in-Degree=out-Degree

# **Algorithm**

## UnionFind

```go
type unionFind struct {
    parent, size []int
}

func newUnionFind(n int) *unionFind {
    parent := make([]int, n)
    size := make([]int, n)
    for i := range parent {
        parent[i] = i
        size[i] = 1
    }
    return &unionFind{parent, size}
}

func (uf *unionFind) find(x int) int {
    if uf.parent[x] != x {
        uf.parent[x] = uf.find(uf.parent[x])
    }
    return uf.parent[x]
}

func (uf *unionFind) union(x, y int) {
    fx, fy := uf.find(x), uf.find(y)
    if fx == fy {
        return
    }
    if uf.size[fx] < uf.size[fy] {
        fx, fy = fy, fx
    }
    uf.size[fx] += uf.size[fy]
    uf.parent[fy] = fx
}

func (uf *unionFind) inSameSet(x, y int) bool {
    return uf.find(x) == uf.find(y)
}
```

## Binary Search

- **Default(searching for left bound)**

Using a *left closed and right open* searching region is more recommended.

```go
//searching region is [left,right)
func binarySearch（nums []int, target int）int{
    n:=len(nums)
    l,r:=0,n
    mid:=(l+r)/2
    for l<r{
        mid=(l+r)/2
        if nums[mid]==target{
            r=mid
        }else if target<nums[mid]{
            r=mid
        }else if target>nums[mid]{
            l=mid+1
        }
    }
    return l
    // nums[l] is the first element >= target
}
```

Actually, this is a left-approaching method(looking for the left bound), the value returned is the index of the first element which `>= target`. 

- Searching for right bound
  
```go
func binarySearch（nums []int, target int）int{
    n:=len(nums)
    l,r:=0,n
    mid:=(l+r)/2
    for l<r{
        mid=(l+r)/2
        if nums[mid]==target{
            l=mid+1
        }else if target<nums[mid]{
            r=mid
        }else if target>nums[mid]{
            l=mid+1
        }
    }
    return l-1
    // nums[l-1] is the last element <= target
}

```

[Chinese Reference](https://www.cnblogs.com/kyoner/p/11080078.html)

## Bit Manipulation

- **law of XOR `^`**

```
x ^ 0 = x
x ^ 11111……1111 = ~x
x ^ (~x) = 11111……1111
x ^ x = 0
a ^ x ^ x = a
a ^ b = c  => a ^ c = b  => b ^ c = a //law of commutation
a ^ b ^ c = a ^ (b ^ c) = (a ^ b）^ c //law of association
```

- **Some special operation---set special bit `0` or `1`**
  
>Poor English :(

```
将 x 最右边的 n 位清零， x & ( ~0 << n )

获取 x 的第 n 位值(0 或者 1)，(x >> n) & 1

获取 x 的第 n 位的幂值，x & (1 << (n - 1))

仅将第 n 位置为 1，x | (1 << n)

仅将第 n 位置为 0，x & (~(1 << n))

将 x 最高位至第 n 位(含)清零，x & ((1 << n) - 1)

将第 n 位至第 0 位(含)清零，x & (~((1 << (n + 1)) - 1)）
```

- **Some useful operation of `&`**

```  
X & 1 == 1，x是奇数

X & = (X - 1)，将最低位(LSB)的 1 清零

X & -X， 得到最低位(LSB)的 1

X & ~X = 0
```

## DFS

```go
res:=[]T
var dfs func (路径, 选择列表)
dfs=func (路径, 选择列表){
    if 满足结束条件{
        result.add(路径)
        return
    }
    if 不满足结束条件{return} //剪枝
​
    for 选择 in 选择列表{
        做选择  
        backtrack(路径, 选择列表)//进入下一层
        撤销选择  //状态重置
    }
}
```

## BFS

树的层序遍历，无权重图的最短路金

```go
queue:=[]*T{begin}
step:=0
var bfs=func(begin){
    for len(queue)>0{
        
        size:=len(queue)//当前层的元素数量
        
        for i:=0;i<size;i++{
            temp:=queue[0]//取出队首元素
            
            ...//对队首元素进行操作（取值或判断终点等）
            
            ...
            queue=append(queue,...)
            ...                         //下一层元素入队，并更新状态值
        }

        queue[size:]//弹出当前层

        step++//记录步数
    }
    
}
```

## Sort

### quickSort

average  O(nlogn)  , worst O(n<sup>2</sup>) ,unstable

```go
func sortArray(nums []int) []int {
    quickSort(nums,0,len(nums)-1)
    return nums
}
//快慢指针，i指向小于等于pivot的最后一个元素，j指向大于pivot的元素的下一个元素
func partition(nums []int,l int,r int)int{
    pivot:=nums[r]
    i:=l-1
    for j:=l;j<r;j++{
        if nums[j]<=pivot{
            i++
            nums[i],nums[j]=nums[j],nums[i]
        }
    }
    nums[i+1],nums[r]=nums[r],nums[i+1]
    return i+1
}

func quickSort(nums []int,l int,r int){
    if l>=r{
        return
    }
    q:=partition(nums,l,r)
    quickSort(nums,l,q-1)
    quickSort(nums,q+1,r)
}
```

### Bubble Sort

O(n<sup>2</sup>)~O(n), stable

```go
func sortArray(nums []int) []int {
    n:=len(nums)
    for i:=0;i<n;i++{
        for j:=0;j<n-i-1;j++{
            if nums[j]>nums[j+1]{
                nums[j],nums[j+1]=nums[j+1],nums[j]
            }
        }
    }
    return nums
}
```

### HeapSort

O(nlogn)~O(nlogn), unstable

```go
func heapify(nums []int, parent int,latestNode int){    //O(logn)
    left:=2*parent+1
    right:=2*parent+2
    largest:=parent
    if left<=latestNode&&nums[left]>nums[parent]{       //先检查节点是否存在
        largest=left
    }else{
        largest=parent
    }
    if right<=latestNode&&nums[right]>nums[largest]{
        largest=right
    }
    //如果父节点不变，下沉结束
    
    //存在大于父节点的子节点，交换
    if largest!=parent{        //对交换过后的子节点进行下沉
        nums[parent],nums[largest]=nums[largest],nums[parent]
        heapify(nums,largest,latestNode)
    }
}

func buildHeap(nums []int){
    n:=len(nums)
    for i:=(n-2)/2;i>=0;i--{   //从最后一个非叶节点开始下沉
        heapify(nums,i,n-1)
    }
}

func sortArray(nums []int) []int {
    buildHeap(nums)
    n:=len(nums)
    for i:=n-1;i>=1;i--{
        nums[0],nums[i]=nums[i],nums[0]
        heapify(nums,0,i-1)
    }
    return nums
}
```

### Insert Sort

O(n)~O(n<sup>2</sup>), average O(n<sup>2</sup>), stable

```go
func sortArray(nums []int) []int {
    n:=len(nums)
    for i:=1;i<n;i++{
        for j:=i;j>0;j--{
            if nums[j]<nums[j-1]{
                nums[j],nums[j-1]=nums[j-1],nums[j]
            }
        }
    }
    return nums
}
```

### Merge Sort

O(nlogn)~O(nlogn), stable

```go

func mergeSort(nums []int) []int {
    if len(nums)<2{
        return nums
    }
    mid:=len(nums)/2
    left:=mergeSort(nums[:mid])
    right:=mergeSort(nums[mid:])
    result:=merge(left,right)
    return result
}

func merge(left,right []int)[]int{
    l,r:=0,0 //index
    res:=[]int{}
    for l<len(left){
        if r==len(right){    //右数组遍历完，合并左剩余数组
            res=append(res,left[l:]...)
            break
        }
        if left[l]<right[r]{
            res=append(res,left[l])
            l++
        }else{
            res=append(res,right[r])
            r++
        }
    }
    res=append(res,right[r:]...)//左数组遍历完，合并剩余右数组
    return res
}
```

### Select Sort

O(n<sup>2</sup>)~O(n<sup>2</sup>), average O(n<sup>2</sup>), unstable

```go
func sortArray(nums []int) []int {
    n:=len(nums)
    for i:=0;i<n;i++{
        minIndex:=i
        for j:=i+1;j<n;j++{
            if nums[j]<nums[minIndex]{
                minIndex=j
            }
        }
        nums[i],nums[minIndex]=nums[minIndex],nums[i]
    }
    return nums
}
```






