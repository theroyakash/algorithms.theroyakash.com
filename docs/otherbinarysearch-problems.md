---
title: Binary Search Problems
---

# Binary Search problems
Binary search problems are really useful because they run in sublinear time. We'll be solving the following problems in order to master this

## Binary Search implementation in C++
Binary search is really easy to implement. Main goal is to divide the search space into 2 partitions (`bi`) and then based on the criteria search only in one partition.

### Expected time complexity
Expected time complexity is $O(\log N)$ where N is the number of elements in the array.

### Code implementation
```cpp
// Return the index of the element in the array
// using binary search

int binary_search(std::vector<int> &v, int target) {
    int start = 0;
    int end = v.size() - 1;

    int middle = (end + start) / 2;
    int index = -1; // return -1 if not found

    while (start < end) {
        if (v[middle] < target) {
            start = middle + 1;
        } else if (v[middle] > target) {
            end = middle - 1;
        } else {
            return middle;
        }

        middle = (end + start) / 2;
    }

    return index;
}
```

## Binary Search on Reverse Sorted Array
## Order Not Known Search
## First and Last occurrence of an Element
## Count of an Element in a Sorted Array
## Number of Times a Sorted array is Rotated
## Find an Element in a Rotated Sorted Array
## Searching in a Nearly Sorted Array
## Find Floor of an element in a Sorted Array
## Ceil of an element in a Sorted Array
## Next alphabetical element
## Find position of an element in an Infinite Sorted Array
## Index of First 1 in a Binary Sorted Infinite Array
## Minimum Difference Element in a Sorted Array
## Binary Search on Answer Concept
## Peak Element
## Find maximum element in Bitonic Array
## Search An Element in Bitonic Array
## Allocate minimum number of pages