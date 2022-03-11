---
title: Heap Problems
description: Heap Problems that are asked for SDE 1 and SDE 2 interviews.
tags:
	- Problem Solving
	- Video Solutions
---

# Heap Problems

## Kth Largest Element in an Array

[Questions on leetcode $\to$](https://leetcode.com/problems/kth-largest-element-in-an-array/)

### Problem statement
Given an integer array nums and an integer k, return the kth largest element in the array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

### Examples
```text
Input: nums = [3,2,1,5,6,4], k = 2
Output: 5

Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

### Constraints
- $1 \leq$ `k` <= `nums.size()` $\leq$ $10^4$
- $- 10^4 \leq \text{nums[i]} \leq 10^4$

### Approach
This is a most obvious heap problem. Using a min heap we can solve this problem.