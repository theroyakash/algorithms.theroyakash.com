# :material-bag-personal: Knapsack (0/1) Pattern

These problems discussed below conform to one knapsack pattern. Using solutions to the knapsack problem and a minor modification, you can solve almost all of the following problems. We'll discuss this as we explain the code and the approach to these problems.

**Problems** discussed

1. [Vanilla 0/1 Knapsack Problem](#vanilla-01-knapsack-problem)
1. [Subset Sum](#subset-sum)
1. [Partition Equal Subset Sum](#partition-equal-subset-sum)
1. [Count of Subset sum](#count-of-subset-sum)
1. [Minimum subset sum difference](#minimum-subset-sum-difference)
1. [Number of subsets with given difference](#number-of-subsets-with-given-difference)
1. [Target Sum](#target-sum)



## Vanilla 0/1 Knapsack Problem
### Problem statement
[Find the problem in GFG](https://practice.geeksforgeeks.org/problems/0-1-knapsack-problem0945/1).

It is a standard $0/1$ Knapsack Problem. Given a knapsack with $W$ bound on the weight, $p[n]$ profits for each item, $w[n]$ weights of each item, find $X \in \{0,1\}^n$ such that $p^TX$ is maximized and $w^T X \leq W$. $X$ is a solution to the $0/1$ knapsack problem.

### Approach
#### Recursive approach
Suppose $w= [1,3,4,5]$ and $p = [1,4,5,7]$. Now we have $4$ items, and we create a choice diagram. For each $i \in [n]$ we have a choice whether to include that element $i$ into the knapsack.

**Following is the choice diagram** for every element in the item list.

![choicediagram](./../images/choicediagram.png)

**Recursive design** should follow the syntax described below
```cpp linenums="1"
int knapsack(auto params) {
    /* BASE CONDITION */
    /* Call knapsack(params) on Choice Diagram */
}
```

Transforming the choice diagram into code

### Code
This is a simple recursive solution (not memoized).

```cpp linenums="1"
class Solution {
    public:
    //Function to return max value that can be put in knapsack of capacity W.

    int knapSack(int W, int wt[], int val[], int n) {
        /* BASE CONDITION [smallest value input] */
        if (n <= 0 or W <= 0) {
            return 0;
        }
        
        /* Choice Diagram */
        if (wt[n - 1] <= W) {
            return std::max(
                val[n - 1] + knapSack(W - wt[n-1], wt, val, n - 1),
                knapSack(W, wt, val, n - 1)
            );
        }
        
        // not include as capacity is lower
        return knapSack(W, wt, val, n - 1);
    }
};
```

We now memoize the solution to use the dynamic programming paradigm.

!!! note important "General approach to memoize a recursive solution" 
    - First, we need to identify what are the values that are changing,
    - Make a `vector<nested vector<int>>` 1D, 2D. 3D depending upon the number of elements changing.

```cpp linenums="1"
class Solution {
public:
    vector<vector<int>> dp;
    bool tableInitialized = false;
    
    //Function to return max value that can be put in knapsack of capacity W.
    int knapSack(int W, int wt[], int val[], int n) {
        // base condition [smallest value input]
        
        if (not tableInitialized) {
            dp = vector<vector<int>>(n + 1, vector<int>(W + 1, -1));
            tableInitialized = true;
        }
        
        if (dp[n][W] != -1) {
            return dp[n][W];
        }
        
        if (n <= 0 or W <= 0) {
            return 0;
        }

        if (wt[n - 1] <= W) {
            dp[n][W] = std::max(
                val[n - 1] + knapSack(W - wt[n-1], wt, val, n - 1),
                knapSack(W, wt, val, n - 1)
            );
            
            return dp[n][W];
        }
        
        // not include as capacity is lower
        dp[n][W] = knapSack(W, wt, val, n - 1);
        return dp[n][W];
    }
};
```


The following is a top-down approach for $0/1$ knapsack
```cpp linenums="1"
class Solution {
    public:
    //Function to return max value that can be put in knapsack of capacity W.
    int knapSack(int W, int wt[], int val[], int n) {
        int dp[n + 1][W + 1];
        memset(dp, -1, sizeof(dp));
        
        for (int i = 0; i < W + 1; i++) {
            dp[0][i] = 0;
        }
        
        for (int i = 0; i < n + 1; i++) {
            dp[i][0] = 0;
        }
        
        for (int i = 1; i < n + 1; i++) {
            for (int j = 1; j < W + 1; j++) {
                if (j >= wt[i - 1]) {
                    dp[i][j] = std::max(
                        dp[i-1][j],  // not considering the i th element
                        dp[i-1][j - wt[i-1]] + val[i-1] // considering the i th element
                    );
                } else {
                    dp[i][j] = dp[i-1][j]; // not considering the i th element
                }
            }
        }
        
        return dp[n][W];
    }
};
```

### Approach Identification
To identify a problem having a solution similar to knapsack is to find out if there are two of the following thing

- an upper bound of some integer $W$,
- An array where we can choose to include or not include certain elements


## Subset Sum
### Problem Statement
Given an array of non-negative integers and a value sum, determine if there is a subset of the given set with a sum equal to the given sum

### Example
```
Input: N = 6, arr[] = {3, 34, 4, 12, 5, 2}, sum = 9
Output: 1

Explanation: Here there exists a subset with sum = 9, 4+3+2 = 9.
```

### Recursive solution
```cpp linenums="1"
class Solution{   
public:
    bool subSetPossible = false;
    vector<vector<int>> dp;
    
    // Main function
    bool isSubsetSum(vector<int> arr, int sum) {
        int size = arr.size();
        dp = vector<vector<int>>(sum + 1, vector<int>(size + 1, -1)); // sum and index is changing
        
        recursiveSubroutine(arr, sum, 0, size);
        return subSetPossible;
    }
    
    void recursiveSubroutine(vector<int>& arr, int sum, int i, int size) {
        
        if (i == size) return;
        if (sum == 0) {
            subSetPossible = true;
            return;
        }
        
        if (arr[i] > sum) {
            // do not take the element and move on
            recursiveSubroutine(arr, sum, i + 1, size);
            return;
        } else {
            recursiveSubroutine(arr, sum - arr[i], i+1, size);  // take
            recursiveSubroutine(arr, sum, i+1, size);           // no take
        }
    }
};
```

Now all left is to memoize the solution

**My approach** for memoization of the code up-above.

- We find that `sum`, and `i` are two variable that is changing.
- Goal for memoization is not to compute any precomputed function calls.
- So we'll create a vector `dp` to store existing call results on a particular `sum, i`.
- We need to change the function signature by returning a `bool` if `dp[`sum][i]` is true. This will solve sub-problems, and using that, we can compute the subset sum optimally using overlapping subproblems.

```cpp linenums="1"
class Solution{   
public:
    bool subSetPossible = false;
    vector<vector<int>> dp;
    
    bool isSubsetSum(vector<int> arr, int sum) {
        int size = arr.size();
        dp = vector<vector<int>>(sum + 1, vector<int>(size + 1, -1)); // sum and index is changing
        
        return recursiveSubroutine(arr, sum, 0, size);
    }
    
    bool recursiveSubroutine(vector<int>& arr, int sum, int i, int size) {
        
        if (i >= size and sum) return false;
        
        if (sum == 0) {
            return true;
        }
        
        if (dp[sum][i] != -1) return dp[sum][i];
        
        if (arr[i] > sum) {
            // do not take the element and move on
            return dp[sum][i] = recursiveSubroutine(arr, sum, i + 1, size);
        }
        
        return dp[sum][i] = (recursiveSubroutine(arr, sum - arr[i], i+1, size) or recursiveSubroutine(arr, sum, i+1, size));
    }
};
```

### Iterative DP approach
```cpp linenums="1"
class Solution{   
public:
    vector<vector<bool>> dp;
    bool isSubsetSum(vector<int>arr, int sum) {
        int size = arr.size();
        
        dp = vector<vector<bool>>(sum + 1, vector<bool>(size + 1, -1));
        // if sum = 0; then always possible
        for (int i = 0; i <= size; i++) {
            dp[0][i] = true;
        }
        
        // > 0 sum and no element is not possible
        for (int i = 1; i <= sum; i++) {
            dp[i][0] = false;
        }
        
        for (int i = 1; i <= sum; i++) {
            for (int j = 1; j <= size; j++) {
                // dp[i][j] till jth element is it possible to 
                // get a subset with sum = i;
                if (arr[j - 1] > i) {
                    // not possible
                    dp[i][j] = dp[i][j - 1];
                } else {
                    dp[i][j] = dp[i][j - 1] or dp[i - arr[j - 1]][j - 1];
                }
            }
        }
        
        return dp[sum][size];
    }
};
```

## Partition Equal Subset Sum
Find the problem on [Leetcode](https://leetcode.com/problems/partition-equal-subset-sum/description/)

### Problem Statement
Given an integer array `nums`, return `true` if you can partition the array into two subsets such that the sum of the elements in both subsets is equal or `false` otherwise.

### Examples
```
Input: nums = [1,5,11,5]
Output: true
Explanation: The array can be partitioned as [1, 5, 5] and [11].
---
Input: nums = [1,2,3,5]
Output: false
Explanation: The array cannot be partitioned into equal sum subsets.
---
```

### Approach
- We have to divide the array into two parts to equal the total sum.
- If the total sum is odd, then no way we can separate the two arrays.
- Otherwise, we need to find if there exists a subset with target $= \frac{\text{sum}}{2}$.

### Code
```cpp linenums="1"
class Solution {
private:
    vector<vector<int>> dp;

    bool recursiveSubroutine(vector<int> &nums, int sum, int i) {
        int size = nums.size();
        if (i >= size and sum) return false;
        if (sum == 0) return true;

        if (dp[sum][i] != -1) return dp[sum][i];

        if (nums[i] > sum) {
            return dp[sum][i] = recursiveSubroutine(nums, sum, i + 1);
        }
        
        return dp[sum][i] = recursiveSubroutine(nums, sum, i + 1)
                        or recursiveSubroutine(nums, sum - nums[i], i + 1);
    }

public:
    bool canPartition(vector<int>& nums) {
        int target = 0;
        for (auto i : nums) target += i;
        
        if (target % 2 != 0) return false;
        else target /= 2;
        
        int size = nums.size();

        dp = vector<vector<int>>(target + 1, vector<int>(size + 1, -1));

        return recursiveSubroutine(nums, target, 0);
    }
};
```

## Count of Subset sum
Find the problem on [GFG](https://practice.geeksforgeeks.org/problems/perfect-sum-problem5633/1)

### Problem Statement
Given an array arr[] of non-negative integers and an integer sum, the task is to count all subsets of the given array with a sum equal to a given sum.

### Example
```
Input: N = 6, arr[] = {2, 3, 5, 6, 8, 10}
       sum = 10
Output: 3
Explanation: {2, 3, 5}, {2, 8}, {10}
```

### Approach
In the last problem we indicated if there exists a sub problem with `isSubsetSum = true`. Now for this problem we simply add true boolean outputs from subproblems. This way we can count how many times true is returned.

### Code
```cpp linenums="1"
class Solution{
public:
    int MOD = 1e9 + 7;
    vector<vector<int>> dp;
    
	int perfectSum(int arr[], int n, int sum) {
	    
	    dp = vector<vector<int>>(n + 1, vector<int>(sum + 1, 0));
	    
	    for (int i = 0; i <= n; i++) {
	        dp[i][0] = 1;
	    }
	    
		for (int i = 1; i <= n; i++) {
    		for (int j = 0; j <= sum; j++) { // TODO: if we set j = 1..sum there is a problem
    			if (j - arr[i-1] >= 0) {
    				dp[i][j] = (dp[i - 1][j] + dp[i - 1][j - arr[i-1]]) % MOD;
    			} else {
    				dp[i][j] = dp[i - 1][j]; // dont consider the element
    			}
    		}
    	}
    	
	    
	    return dp[n][sum];
    }
};
```

## Minimum subset sum difference
### Problem Statement
You are given an array `arr` containing `n` non-negative integers. Your task is to partition this array into two subsets such that the absolute difference between subset sums is minimum. You just need to find the minimum absolute difference considering any valid division of the array elements.

### Approach
- We need to divide the array into two parts $s_1, s_2$ such that $abs(\sum_{i} s_1[i] - \sum_{i} s_2[i])$ is minimized.
- For each subset $s_i$, the sum $\sum_{j} s_i[j] \in \left[0, \sum_{i} A[i]\right]$. Where $A$ is the main array.
- Hence the difference $s_1 - s_2$ becomes $abs(\sum_{i} A[i] - 2s_1)$.
- Now instead of finding $\displaystyle\min_{\forall s_1, s_2 \in 2^n} abs(s_1, s_2)$ we need to find $\displaystyle\min_{\forall s_1 \in 2^n} abs(2s_1 - \sum_i A[i])$.
- Now for each of the possible sum $\sum_i s_1[i]$ we find this breaking creates least value of $abs(2s_1 - \sum_i A[i])$.
- We return the least value.

### Code
```cpp linenums="1"
typedef struct Loc {
    int i, j;
} Loc;

int minSubsetSumDifference(vector<int> &arr, int n) {
    int minSum = 0;
    int maxSum = accumulate(arr.begin(), arr.end(), 0);

    int minimumSubsetSumDifference = INT_MAX;

    int size = arr.size();
    int sum = maxSum;

    vector<vector<bool>> dp(size + 1, vector<bool>(sum + 1, false));

    for (int i = 0; i <= size; i++) {
        dp[i][0] = true;
    }

    vector<Loc> locations; // store locations in the last array where
                           // there is a subset s_1 possible

    for (int i = 1; i <= size; i++) {
        for (int j = 1; j <= sum; j++) {

            if (j - arr[i - 1] >= 0) {
                dp[i][j] = dp[i - 1][j] or dp[i - 1][j - arr[i - 1]];
            } else {
                dp[i][j] = dp[i - 1][j];
            }

            if (i == size and dp[i][j] == 1) {
                Loc loc;
                loc.i = i, loc.j = j;
                locations.push_back(loc);
            }
        }
    }

    // for each of the s_i find out the difference.
    for (auto loc : locations) {
        int i = loc.i, j = loc.j;
        minimumSubsetSumDifference = std::min(minimumSubsetSumDifference, abs(2 * j - maxSum));
    }

    return minimumSubsetSumDifference;
}
```

## Number of subsets with given difference
### Problem Statement
Given an array $A$ and a difference `diff` = $d$, find the number of subsets that array can be divided into so that each the difference between the two subset is the given $d$.

### Approach
- We'll re-use the previous concepts in order to solve this problem at hand,
- We need to break the array into two parts $S_1, S_2$ such a way that $\sum_i S_1[i] - \sum_i S_2[i] = d$ where $d$ is the given difference. We need to find out the number of such pairs $S_1, S_2$.
- **Observation:** it is also known that $\sum_i S_1[i] + \sum_i S_2[i] = \sum_i A[i]$.
- If we add the two equation from up above we get the following $2* \sum_i S_1[i] = d + \sum_i A[i]$. Hence $\sum_i S_1[i] = \frac{d + \sum_i A[i]}{2}$
- Hence the question now becomes from How many $S_1, S_2$ pairs possible for the difference = $d$ to How many subsets possible with $S_1 = j$ for some number $j = \frac{d + \sum_i A[i]}{2}$.

### Code
```cpp linenums="1"
#include <bits/stdc++.h>

using namespace std;

class Solution {
public:
    int MOD = 1e9 + 7;
    int sumOfArray = 0;
    vector<vector<int>> dp;
    
    int countPartitions(int n, int d, vector<int>& arr) {
        sumOfArray = 0;
        for (int i : arr) sumOfArray+=i;
        
        int j = (d + sumOfArray) / 2;
        
        buildDPTable(n, arr, j);
        
        return dp[n][j];
    }
    
    // build the dp table
    void buildDPTable(int n, vector<int>& arr, int sum) {
        dp = vector<vector<int>>(n + 1, vector<int>(sum + 1, -1));
        
        for (int i = 1; i <= sum ; i++) {
            dp[0][i] = 0; // no solution for +ve sum and 0 element
        }
        
        for (int i = 0; i <= n; i++) {
            dp[i][0] = 1; // empty subset for sum = 0
        }
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= sum ; j++) {
                dp[i][j] = dp[i - 1][j];
                
                if (j - arr[i - 1] >= 0) {
                    dp[i][j] += dp[i - 1][j - arr[i - 1]];
                }
            }
        } 
    }
    
};

int main() {
    Solution s;
    int n, d;
    cin >> n >> d;
    
    vector<int> a(n, 0);
    
    for (int i = 0; i < n; i++) cin >> a[i];
    
    cout << s.countPartitions(n, d, a);
    
}
```

## Target Sum
[Find the problem on Leetcode $\to$](https://leetcode.com/problems/target-sum/)

### Problem Statement
You are given an integer array $\textsf{nums}$ and an integer $\textsf{target}$.

You want to build an expression out of nums by adding one of the symbols '+' and '-' before each integer in nums and then concatenate all the integers.

> For example, if $\textsf{nums} = [2, 1]$, you can add a '+' before 2 and a '-' before 1 and concatenate them to build the expression $+2-1$.

Return the number of different expressions that you can build, which evaluates to target.

### Approach

### Code
```cpp linenums="1"
class Solution {
private:
    int countSubsetSumDifference(vector<int>& nums, int difference) {
        int sum = accumulate(nums.begin(), nums.end(), 0);

        int target = (sum + difference) / 2;
        if ((sum + difference) % 2 or sum < difference) return 0;
        if (target < 0) return 0;

        return findNumberOfSubsetWithTarget(nums, target);
    }

    int findNumberOfSubsetWithTarget(vector<int>& nums, int sum) {
        int n = nums.size();
        vector<vector<int>> dp(n + 1, vector<int>(sum + 1, 0));

        // if sum = 0 then empty set is good
        for (int i = 0; i <= n; i++) {
            dp[i][0] = 1;
        }

        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= sum; j++) {  // starting from j = 0 because there can be element
            // 0 that can be added into the subsets to achieve 0 sum.

                if (j - nums[i - 1] >= 0) {
                    dp[i][j]=dp[i-1][j] + dp[i-1][j-nums[i-1]];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }

        return dp[n][sum];
    }

public:
    int findTargetSumWays(vector<int>& nums, int target) {
        if (nums.size() == 1) {
            // base case
            if (abs(nums[0]) == abs(target)) return 1;
            else return 0;
        }

        return countSubsetSumDifference(nums, target);
    }
};
```