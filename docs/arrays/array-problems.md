---
title: Problems on arrays
tags:
    - Array problems
---

# Problems on arrays
## Dutch National Flag Partitioning

### Problem Statement
Implemented naively, quicksort has large run times and deep function call stacks on arrays with many duplicates because the subarrays may differ greatly in size. One solution is to reorder the array so that all elements less than the pivot appear first, followed by elements equal to the pivot, followed by elements greater than the pivot. This is known as Dutch national flag partitioning, because the Dutch national flag consists of three horizontal bands, each in a different color.

### Solution Pattern
- Main thing to do in this problem is to make groups of elements whose values are less, equal, more than the target.
- For $O(N)$ space solutions we can create three new arrays called `less, more, equal` and put values into it during the traversal.
- For $O(1)$ space solutions we can do these steps:
    - Do - inplace grouping of all the less than target elements during the first pass,
    - Do - inplace grouping of all the greater than target elements during the next pass. During this pass if we find one element that is greater than the target we can say that "Hey! we have entered into the `less` subarray", so we can break out of the loop.
    - Total time complexity is $O(N)$ for the first pass and on an avarage $O(\frac{N}{2})$ in the second pass. Total of $O(N)$.


```python
def dutch_flag_partition(pivot_index: int, A: list[int]) -> None:
    pivot_value: int = A[pivot_index]

    small: int = 0

    # Group all the smaller elements together at the start
    # This is in-place grouping of elements smaller than some target
    for i in range(len(A)):
        if A[i] < pivot_value:
            A[small], A[i] = A[i], A[small]
            small += 1

    # Group all the larger elements together at the end
    larger: int = len(A) - 1
    for i in reversed(range(len(A))):
        if A[i] < pivot_value:
            break
        elif A[i] > pivot_value:
            A[i], A[larger] = A[larger], A[i]
            larger -= 1
```

!!! note "Test Cases"
    - Test PASSED (204/204) [   8 ms]
    - Avarage running time:   65 us
    - Median running time:    13 us


!!! note "Similar problems"
    Given an array of n objects with keys that takes on of four values, reorder the array so that all the objects that have the same values appear together. $O(1)$ space and $O(N)$ time.


## String Compression Problem

### Problem Statement
Implement a method to perform basic string compression, like "aaaabbbcccaa" $\to$ "a4b3c3a2"

### Approach
- Make a one key dictionary. Add and count the subsequent characters, for C++ you'd make a `pair<int, int>` and destroy it once a new character is seen,
- Once you find a new element then drop the key, add the contents to the builder string and count again.

### Code
```python
def string_compressing(string: str) -> str:
    build_string: list[str] = []  # using character array instead of string for O(1) append
    dictionary: dict[str, int] = {}

    for character in string:
        if character in dictionary:
            dictionary[character] += 1
        elif character not in dictionary:
            if dictionary != {}:
                # If the dictionary is not empty means we have a new element to consider.
                # Dump the contents to the build_string and make the dictionary = {}
                build_string.append(list(dictionary.keys())[0])
                build_string.append(str(dictionary[list(dictionary.keys())[0]]))
            
            dictionary = {}
            # Set the new character count to 1
            dictionary[character] = 1

    build_string.append(list(dictionary.keys())[0])
    build_string.append(str(dictionary[list(dictionary.keys())[0]]))
    
    build_string = "".join(build_string)  # This does not take O(N^2), takes only O(N) to concatenate a character array to a string.
    build_string = build_string.replace("1", '')  # O(N)
    return build_string
```


```python
string_compressing("abbbcccaa")  # -> 'ab3c3a2'
```

### Time complexity
This solution take $O(N)$ time and constant space if you don't count the return string. It uses space to hold an **one key** dictionary and the return string.

## Best Time to Buy and Sell Stock
[Problem on Leetcode $\to$](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

### Problem Statement
You are given an array prices where `prices[i]` is the price of a given stock on the $\text{i}^{\text{th}}$ day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return $0$.

### Examples
#### Example 1
```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```
#### Example 2
```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

### Approach
- We start with the lowest profit possible which is $0$,
- We'll start the analysis from day $1$ not from day $0$,
- We indicate the dip is the day $0^{th}$ price. If we buy at that price then we analyse further.
- Now we analyze from day $1 ... N$
    - For day $1$ if we sell that stock the profit will be `profSLT = prices[1] - dip` at the **lowest dip so far**, we update the max profit by taking the `std::max` of previous maximum profit and todays profit.
    - If todays price is lower than the last day's price, it is possible that we can get a better solution (profit) if not we already have stored the last best profit.
    - We repeat this until we reach the last day.

### Code
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int mprof = 0; // maximum profit
        int dip = prices[0]; // should buy at lowest dip starting from day 1
        
        for (int i=1; i<prices.size(); i++) {
            // profit if sold today bought at the lowest dip so far;
            int profSLT = prices[i] - dip;
            // if this is the maxprofit then update
            mprof = std::max(mprof, profSLT);
            
            if (prices[i] < dip) dip = prices[i];
        }
        
        return mprof;
    }
};
```
## Best Time to Buy and Sell Stock II
[Problem on Leetcode $\to$](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)
### Problem Statement
Similar to the previous problem, you are given an integer array prices where `prices[i]` is the price of a given stock on the ith day.

On each day, you may decide to buy and/or sell the stock. You can only hold at most one share of the stock at any time. However, you can buy it then immediately sell it on the same day.

Find and return the maximum profit you can achieve.
### Approach
Similar to the previous problem we'll continue to implement the logic but, if we see a dip later in the future, we'll sell off and buy at tomorrows dip to avoid any potential loss.

!!! note
    Here `profitInCurrentWindow` in the code denotes the profit in the current window. An window is reset when we sell off the stock because there will be a dip in the next day. We start the window by buying the stock in the next dip.

### Code
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        
        if (prices.size() == 1) return 0;
        
        int totalProfit = 0;
        int dip = prices[0];
        int profitInCurrentWindow = 0;
        
        // start from day 1 and local dip so far is day 0's price
        for (int index = 1; index < prices.size(); index++) {
            // today's price = prices[index]
            int price = prices[index];
            // see if selling today entails profit?
            int profit = price - dip;
            
            // what is the max profit possible in the current window
            profitInCurrentWindow = std::max(profitInCurrentWindow, profit);
            
            // if it is profitable and tomorrow's price goes down don't hold, sell and exit
            // and reset the problem to next day and start again
            if (profit > 0 and index < prices.size() - 1) {
                if (prices[index + 1] < prices[index]) {
                    totalProfit = totalProfit + profitInCurrentWindow;
                    // also reset the dip to the index + 1
                    // for a new profit calculation for tomorrow
                    dip = prices[index + 1];
                    index++; // we reset the entire problem and recalculate everything from the next index
                    profitInCurrentWindow = 0; // reset the profit in the window
                }
            }
            
            if (price < dip) dip = price;
        }
        
        totalProfit += profitInCurrentWindow; // update the last profit window
        
        return totalProfit;
    }
};
```