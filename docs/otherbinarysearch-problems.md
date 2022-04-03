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

    while (start <= end) {
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
We've seen the binary search algorithm for a sorted array, but if the array is reverse sorted then we can easily modify the binary search algorithm here `else if (v[middle] > target) { start = middle + 1; }` because the target element is lower and the array is reverse sorted so the required element should be at the end partition of the array.

So a little modification of the binary search algorithm is sufficient to solve the problem.

## Order agnostic search
If the array is sorted but we don't know the order of the elements whether it is ascending or descending? Then what we can do is the following

### Approach
- See if the array size is 1 or not then we return.
- If the array size is more than 1, then find if $\text{A[0]} > \text{A[1]}$ or not? If it is then the array is reverse sorted. If that is not the case then the array is sorted in ascending order.
- We run the previous or previous to previous algorithm accordingly.

## First and Last occurrence of an Element
In a sorted array if we have more than one element we may want to know what is the first occurrence of some element (assuming they exists).

### Approach
- We can use linear search but there is no fun in that. Instead we use a binary search, but how we'll do this?
- Let's employ a binary search subroutine, now this will find any occurrence of the **target** element,
- Now if this is not the first occurrence of the **target** element we will need to search the left subsection of the array, and in case we didn't find any we record this occurence as the first occurrence of the **target** element so far,
- Now we search for the **target** element again in the left subsection of the array by modifying `end = middle - 1`

### Code
```cpp
int firstOccurence(vector<int> &v, int target){
	int start = 0;
	int end = v.size() - 1;

	int middle = start + (end - start) / 2;
	int first = -1;

	while (start <= end) {

		if (v[middle] == target) {
            // modification to the general binary search
			first = middle;
			end = middle - 1;
		} else if (v[middle] < target) {
			start = middle + 1;
		} else {
			end = middle - 1;
		}

		middle = start + (end - start) / 2;
        
	}

	return first;
}
```

!!! danger "For finding the last occurence"
    Similarly we can find the last occurence of an element in the sorted array by modifying the previous problem slightly, by first finding the element with binary search, then instead of finding the element in the left side [for first occurence] we search the right sub array for the last occurence of the element.

    ```cpp
    int lastOccurence(vector<int> &v, int target){
        int start = 0;
        int end = v.size() - 1;

        int middle = start + (end - start) / 2;
        int last = -1;

        while (start <= end) {

            if (v[middle] == target) {
                // modification to the general binary search
                last = middle;
                start = middle + 1; // look in the right subarray for the last occurrence
            } else if (v[middle] < target) {
                start = middle + 1;
            } else {
                end = middle - 1;
            }

            middle = start + (end - start) / 2;

        }

        return last;
    }
    ```


## Count of an Element in a Sorted Array
This is a very simple problem, we can find the first occurrence of an element [index] and last occurence of an element and subtract. This will return the number of times the element was found in the array.

### Expected Time complexity
$O(\text{lg} N)$ for finding the first occurrence and $O(\text{lg} N)$ for finding the last occurrence. Total of $O(\text{lg} N)$.

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