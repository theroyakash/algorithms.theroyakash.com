---
title: Priority Queues and Binary Heaps
description: One of my favorite data structure is binary heaps.
tags:
	- tutorials
	- STL
	- Implementation
	- Introduction
---
# Priority Queues and Binary Heaps
## What are these priority queues?
A priority queue is a queue where the most important element is always at the front. The queue can be a max-priority queue (largest element first) or a min-priority queue (smallest element first).

So as a data structure designer you have the following options to design a priority queue:

- An max sorted array or min-sorted array, but downside is inserting new items is slow because they must be inserted in sorted order.
- or an binary heap (max heap or min heap)

Now the question arises what are heaps? The heap is a natural data structure for a priority queue. In fact, the two terms are often used as synonyms. A heap is more efficient than a sorted array because a heap only has to be partially sorted. All heap operations are in the order of $\text{log}$ or $O(\mathcal{N})$.

Examples of algorithms that can benefit from a priority queue implemented as heap

* Dijkstra's algorithm for graph searching uses a priority queue to calculate the minimum cost.
* A* pathfinding for artificial intelligence.
* Huffman coding for data compression. This algorithm builds up a compression tree. It repeatedly needs to find the two nodes with the smallest frequencies that do not have a parent node yet.
* Heap sorting algorithm.

## Identification

You should make and use heap for the problems with the following combination:

- K &
- largest or smallest,
- and solvable by sorting the input seq.

## `STL` Usage
A priority queue is a container adaptor that provides constant time lookup of the largest (by default) element, at the expense of logarithmic insertion and extraction. The default container is a `std::vector<T>` underneath.

A user-provided Compare can be supplied to change the ordering, e.g. using `std::greater<T>` would cause the smallest element to appear as the `top()` [Min Heap].


### Functions to remember
- `top()` to get the top of the heap. [either min or max element at the time]
- `empty()` returns false if the heap is not empty,
- `size()` number of elements in the heap,
- `push()` to add element takes $O(\log N)$ comparisons and then the time for `push_back()` method for the underlying container. By default the `std::vector<T>` container is used,
- `pop()` to remove the top element and build heap. $O(\log N)$ comparisons and then the time for `push_back()` method for underlying container.

## General Consensus on what heap to use?
- Given in the question `K` and `smallest` use `MaxHeap` of size K,
- Given in the question `K` and `largest` use `MinHeap` of size K.