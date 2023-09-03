# :material-matrix: Matrix Chain Multiplication Pattern

**Problems Discussed**

1. [:material-matrix: MCM Pattern](#material-matrix-mcm-pattern)
1. [Partition Array for Maximum Sum](#partition-array-for-maximum-sum)
1. [Palindrome Partitioning (MCM)](#palindrome-partitioning-mcm)


## :material-matrix: MCM Pattern
### Problem Statement
Given a sequence of matrices, find the most efficient way to multiply these matrices together. The efficient way is the one that involves the least number of multiplications.

### Approach
In this pattern / series of questions we will look create a left and right bound that is $i, j$. Then we break array / string two way, one part $i \to k$ and other part $k + 1 \to j$. This may vary depending upon the specifics of problems. Now we vary $k \in [i \to j]$ and for different-different breaks of array / string we find answers. Then out of all the possible answers we take minimum or maximum.

Here in this case we calculate the minimum possible operation for a sequence of matrix chain multiplication. Hence we follow from the pattern

1. Find suitable base case `if (i >= j) return 0;`. For single or zero matrix there is nothing to multiply, hence $0$.
1. We break the array into two parts $A, B$, then recursively find what is the min cost for $A$ and min cost for $B$ and total cost $= min_A + min_B+ \textsf{COST}(A*B)$. 
1. Then out of all the possible $A, B$ combination we output the minimum one.

### Code
```cpp
class Solution {
public:
    int solve(int arr[], int i, int j, vector<vector<int>> &dp) {
        // base case
        if (i >= j) return 0;
        
        int minOps = INT_MAX;
        
        if (dp[i][j] != -1) return dp[i][j];
        
        for (int k = i; k <= j - 1; k++) {
            dp[i][k] = solve(arr, i, k, dp);
            dp[k + 1][j] = solve(arr, k + 1, j, dp);
            
            int total = dp[i][k] +                    // min to calculate A
                        dp[k + 1][j] +                // min to calculate B
                        arr[i - 1] * arr[k] * arr[j]; // cost for A * B
                        
            minOps = std::min(minOps, total);
        }
        
        return dp[i][j] = minOps;
    }

    int matrixMultiplication(int N, int arr[]) {
        vector<vector<int>> dp(N, vector<int> (N, -1));
        return solve(arr, 1, N-1, dp);
    }
};
```

## Partition Array for Maximum Sum
Find problem on [Leetcode $\to$](https://leetcode.com/problems/partition-array-for-maximum-sum/description/)
### Problem Statement
Given an integer array `arr`, partition the array into (contiguous) subarrays of length at most $k$. After partitioning, each subarray has their values changed to become the maximum value of that subarray.

Return the largest sum of the given array after partitioning. Test cases are generated so that the answer fits in a 32-bit integer.

### Example
```
Input: arr = [1,15,7,9,2,5,10], k = 3
Output: 84
Explanation: arr becomes [15,15,15,9,10,10,10]
---

Input: arr = [1,4,1,5,7,3,6,1,9,9,3], k = 4
Output: 83
```

### Approach
- Similar to matrix chain multiplication we need to find a breaking point $k$ from start to finish but the size must be within a certain limit.
- So for each $i$ `max_` will locate the maximum in array $A[i \dots i + k]$ such that $i + k \leq \textsf{limit}$.
- Each candidate solutions would be $max * (k + 1)$ as we are replacing 

### Code
```cpp
class Solution {
public:
    int solve(int i, int bound, int n, vector<int>& arr, vector<int>& dp) {
        if (i >= n) return 0;
        if (dp[i] != -1) return dp[i];

        int max_ = 0;
        int solution = 0;

        for (int k = 0; k < bound; k++) {
            if (i + k >= n) break;
            max_ = std::max(arr[i + k], max_);
            solution = std::max(solution, max_ * (k + 1) + solve(i + k + 1, bound, n, arr, dp));
        }

        return dp[i] = solution;
    }

    int maxSumAfterPartitioning(vector<int>& arr, int k) {
        vector<int> dp(arr.size(), -1);
        return solve(0, k, arr.size(), arr, dp);
    }
};
```

## Palindrome Partitioning (MCM)
Find the problem on [Leetcode $\to$](https://leetcode.com/problems/palindrome-partitioning-ii/)

### Problem statement
Given a string `s`, partition `s` such that every substring of the partition is a palindrome. Return the minimum cuts needed for a palindrome partitioning of `s`.

### Example
```
Input: s = "aab"
Output: 1
Explanation: The palindrome partitioning ["aa","b"] could be produced using 1 cut.
---

Input: s = "a"
Output: 0
---

Input: s = "ab"
Output: 1
```

### Approach
- This is a MCM pattern question, because like MCM we need to break and check all possible partition and find the minimum breaking, we discuss couple of apporaches below

**(ONE)** possible approach but raises TLE for very high input size.

- Similar to MCM we check every partition in $i \to k$ and $k + 1 \to j$ where $k \in [n - 1]$. Then we find the minimum number of cuts required for the sub-problem.
- In each sub-problem `recursive_subroutine(i, j)` we first look whether $s[i \dots j]$ is a palindrome or not? If it is a palindrome we return `0`. To check if some substring $s[i \dots j]$ is palindrome we write this `buildDPTableForPalindromicSubstring` method. This can be done by reusing the question discussed here [Count Number Of Palindromic Substring $\to$](https://algorithms.theroyakash.com/dp/problems/#palindromic-substrings)
- Next we write the recursive sub-routine and the function `minCut` computes the final value.
- But shows `TLE` for larger input size.

### Code (TLE)
```cpp
class Solution {
public:
    void buildDPTableForPalindromicSubstring(string s, vector<vector<int>>& pallindromeLookup) {
        int size = s.size();

        for (int i=0; i<size; i++) {
            dp[i][i] = 1;
        }

        // go over the dp array to get the pallindromeLookup matrix
        for (int diff = 1; diff<size; diff++) {
            for(int i=0, j=i+diff; j<size; i++, j++) {
                if (s[i] == s[j]) {
                    if (std::abs(i - j) == 1) {
                        dp[i][j] = 2;
                    } else {
                        if (dp[i+1][j-1] != 0) dp[i][j] = dp[i+1][j-1] + 2;
                        else dp[i][j] = 0;
                    }
                } else {
                    dp[i][j] = 0;
                }
            }
        }
    }

    int recursive_subroutine(string s, int i, int j, vector<vector<int>> &pallindromeLookup, vector<vector<int>> &dp) {
        if (pallindromeLookup[i][j]) return 0;
        if (i >= j) return 0;

        if (dp[i][j] != -1) return dp[i][j];

        int minCuts = INT_MAX;
        for (int k = i; k < j; k++) {
            dp[i][k] = recursive_subroutine(s, i, k, pallindromeLookup, dp);
            dp[k + 1][j] = recursive_subroutine(s, k + 1, j, pallindromeLookup, dp);

            int total = 1 + dp[i][k] + dp[k + 1][j];
            minCuts = std::min(minCuts, total);
        }

        return dp[i][j] = minCuts;
    }

    int minCut(string s) {
        int size = s.size();
        vector<vector<int>> pallindromeLookup(size, vector<int>(size, 0));
        buildDPTableForPalindromicSubstring(s, pallindromeLookup);

        vector<vector<int>> dp(size, vector<int>(size, -1));

        return recursive_subroutine(s, 0, s.size() - 1, pallindromeLookup, dp);
    }
};
```

### Optimized approach
