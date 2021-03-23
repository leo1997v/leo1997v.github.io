---
layout:     post   				    # 使用的布局（不需要改）
title:      Self-use Algorithm and Data Structure Templates			# 标题 
subtitle:   Some self-use data structure and algorithm templates  #副标题
date:       2021-03-23 				# 时间
author:     Leo 						# 作者
header-img: img/post-bg-bloodborne.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Data Structure
    - Algorithm
    - Go
---

# Data Structure

## Hash 

Using built-in type `map`.

## Stack

Using built-in type `slice`  FILO

## Queue

Using built-in type `slice`  FIFO

## Linked List

```go
type struct ListNode{
    Val int
    *ListNode Next
}
```

## BInary Tree

```go
type sruct Tree{
    Val int
    *Tree Left
    *Tree Right
}
```

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
//return the kth biggest element
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
