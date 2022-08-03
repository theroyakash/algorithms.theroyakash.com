# :material-train-car-passenger: Dynamic programming
There is a famous quote saying that "**those who don't remember the past are condemned to repeat it**". This quote very much synchronizes with the meaning of dynamic programming. What in dynamic programming you do is you calculate and remember the calculation, if and when the same calculation is encountered again, instead of recomputing it you fetch the data this reducing computing time.

There are several problems where you are required to calculate something multiple times and if you _cache_ the data then you can avoid computation multiple times. Let's see some of the problems where you need to cache some result to avoid multiple computations.

Table of Contents

- [Climbing Stairs](#climbing-stairs)
- [Frog Jump](#frog-jump)
- [House Robber](#house-robber)
- [House Robber II](#house-robber-ii)
- [Longest Palindromic Substring](#longest-palindromic-substring)

## Climbing Stairs
[Find the Problem on Leetcode $\to$](https://leetcode.com/problems/climbing-stairs/)
### Problem Statement
You are climbing a staircase. It takes n steps to reach the top. Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

### Example
```
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps


Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

### Approach
We are told that we can only reach to the top with once step or 2 step in the staircase. The number ways we can reach to the top is exactly the number of ways we can reach to the $n -1^{th}$ step $+$ number of ways we can reach to the $n -2^{nd}$ step. So we quickly write a recursive function and will only compute if not aready computed.

### Code
```cpp
class Solution {
private:
    unordered_map<int, int> mp;
public:
    int climbStairs(int n) {
        
        if (n <= 2) return n;
        
        if (mp.find(n) == mp.end()) {
            int ways = climbStairs(n-2) + climbStairs(n-1);
            mp.insert({n, ways});
        } 
        
        return mp[n];
    }
};
```

### Time Complexity
The algorithm runs in $O(N)$ time and with `O(N)` memory from the `unordered_map` and also takes extra auxiliary space due to recursive implementation.

## Frog Jump
[Find the Problem on Codestudio](https://www.codingninjas.com/codestudio/problems/frog-jump_3621012)
### Problem Statement
There is a frog on the 1st step of an $N$ stairs long staircase. The frog wants to reach the $N^{th}$ stair. `HEIGHT[]` is the height of the (i+1)th stair.If Frog jumps from ith to jth stair, the energy lost in the jump is given by `HEIGHT[i-1] - HEIGHT[j-1]`. 

In the Frog is on ith staircase, he can jump either to $(i+1)^{th}$ stair or to $(i+2)^{th}$ stair. Your task is to find the minimum total energy used by the frog to reach from 1st stair to $N^{th}$ stair.

### Approach
- We'll be using normal Dynamic Programming approach. We'll manually hard code the cost of moving for 0th (index which is 0) and 1st index (which is $\text{heights[0] - heights[1]}$).
- Then for each $i$ we'll see the result for $i-1$ and $i-2$ and select the step that uses less energy.
- We update the energy lost at step $i$ then go to step $i+1$.
- We don't have to store the $[0 \to i]$ step costs because we are using only $i-1$ and $i-2$ values at any given $i$. So a $2$ element array or a `pair<int, int>` is enough.

### Code
```cpp
#include <unordered_map>
int frogJump(int n, vector<int> &heights) {
    // Write your code here.
    int energy_lost = 0;
    
    pair<int, int> store;
    
    store.first = 0;
    store.second = std::abs(heights[1] - heights[0]);
    
    for (int i=2; i<n; i++) {
        int oneStepCost = store.second + std::abs(heights[i] - heights[i-1]);
        int twoStepCost = store.first + std::abs(heights[i] - heights[i-2]);
        
        int energyLostNow = std::min(oneStepCost, twoStepCost);
        store.first = store.second;
        store.second = energyLostNow;
    }
    
    return store.second;
}
```

### Time Complexity
The algorithm runs in $O(N)$ time and with no extra memory.

## House Robber
[Find the problem on Leetcode $\to$](https://leetcode.com/problems/house-robber/)
### Problem Statement
You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given an integer array nums representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.

### Examples
```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.

Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
Total amount you can rob = 2 + 9 + 1 = 12.
```

### Approach
We'll be using a DP apporach to solve this problem, the main objective is to come up with some recursive solution standing at the $i^{th}$ location. So let's build an intuition around that,

For location $i = 0$ you only can rob the $0^{th}$ house, and standing at the $1^{st}$ house you can either rob the $0^{th}$ house and skip the $1^{st}$ house or skip the $0^{th}$ house and rob the $1^{st}$ house whichever gives more profit. So we'll do the following, we'll indicate for index $i -1$ a map `m` which will show what could have been the maximum profit we could make till $i-1$ index.

Depending upon the choice at index $i$ we'll update the maximum profit we can make till this location $i$.

What are the decisions you need to make before you can make a move at location $i$? The question can be answered by looking at the $i-1^{th}$ location see what is the maximum you can make till $i-1$. Then you either skip this location and make a move at the next location or make a move at this location and skip the next location. Take whatever maximizes the profit.

- So profit till $0$ will be `nums[0]`
- profits till $1$ will be `std::max(nums[0], nums[1])`
- Profits at location $2$ will be the following
    - Either you take the profit from the $0^{th}$ index and rob the $2^{nd}$ location
    - or you take the profit till the $1^{st}$ location and skip the second location.
- $\forall x \in [0, \text{nums.size})$ we'll see what is maximum between the following two `profits[i-1]` and `profits[i-2] + nums[i]`. We'll take the maximum and put in the profits map or array.

### Code
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {

        int size = nums.size();

        // base cases are trivially solvable
        if (size == 1) return nums[0];
        if (size == 2) return std::max(nums[0], nums[1]);
        if (size == 3) return std::max(std::max(nums[0], nums[2]), nums[1]);
        
        pair<int, int> profits;
        profits.first = nums[0];
        profits.second = std::max(nums[0], nums[1]);
        
        for (int i=2; i<nums.size(); i++) {
            int max_profit_possible = std::max(profits.first + nums[i], profits.second);
            profits.first = profits.second;
            profits.second = max_profit_possible;
        }
        
        return profits.second;
    }
};
```

### Time Complexity
The algorithm runs in $O(N)$ time and with no extra memory.

## House Robber II
[Find the Problem on Leetcode $\to$](https://leetcode.com/problems/house-robber-ii)
### Problem Statement
You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are **arranged in a circle**. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have a security system connected, and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given an integer array nums representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.

### Approach
The approach is similar to the last problem, there the last house was not connected to the first house. What we'll do in the problem is considering the first ($0^{th}$) house we'll calculate the profits, and not considering the first ($0^{th}$) house we'll calculate the profits, the we'll return the maximum.

If we consider the first ($0^{th}$) house then we should never consider the last house so we ignore the last house. For the case when we do not consider the first ($0^{th}$) house, we must ignore the first ($0^{th}$) house explicitly. To do that we set `profits[0] = 0` and `profits[1] = nums[1]`, this forces the algorithm to drop the first ($0^{th}$) house.

We'll also force the algorithm to take the first ($0^{th}$) house by setting `profits[0] = nums[0]` and `profits[1] = nums[0]`.

### Code
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        
        int size = nums.size();
        
        // base cases trivially solvable
        if (size == 1) return nums[0];
        if (size == 2) return std::max(nums[0], nums[1]);
        if (size == 3) return std::max(std::max(nums[0], nums[2]), nums[1]);
        
        // forcing the algorithm to take the 0th house
        pair<int, int> profits;
        profits.first = nums[0];
        profits.second = nums[0];
        
        // profits considering 0th index
        // if we consider the 0th index then we can not rob the last index
        // so we run the algorithm till size - 2
        for (int i=2; i<=size-2; i++) {
            int max_profit_so_far = std::max(profits.first + nums[i], profits.second);
            int temp = profits.second;
            profits.second = max_profit_so_far;
            profits.first = temp;
        }
        
        // profits not considering the 1st index
        // forcing algorithm to not take the 0th house
        pair<int, int> profits2;
        profits2.first = 0;
        profits2.second = nums[1];
        
        // profits not considering 0th index
        // if we do not consider the 0th index then we can rob the last index
        // so we run the algorithm till the last index
        for (int i=2; i<size; i++) {
            int max_profit_so_far = std::max(profits2.first + nums[i], profits2.second);
            int temp = profits2.second;
            profits2.second = max_profit_so_far;
            profits2.first = temp;
        }
        
        return std::max(profits.second, profits2.second);
    }
};
```

### Time Complexity
The algorithm runs in $O(N)$ time and with no extra memory.

## Longest Palindromic Substring
[Find the problem on leetcode $\to$](https://leetcode.com/problems/longest-palindromic-substring/)
### Problem Statement
Given a string s, return the longest palindromic substring in s.
### Examples
```
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.

Input: s = "cbbd"
Output: "bb"
```

### Approach
- We'll make a 2D array called `dp`, now for each entry $(i, j)$ in `dp` shows whether from $i \to j$ is a palindromic substring or not and what is the size of that pallindrome?
- First for all entries $(i, j)$ in `dp` such that $i = j$ is a palindromic substring because one character word is always a pallindrome.
- Now we'll move diagonally right next from the main diagonal. Look at the below picture to understand how are we moving?
    ![image](../images/longest-pallindrome.png)
- So we need to design the for loops accordingly. If we look closely to the change of $(i, j)$ we'll see that each time $i$ start from $0$ and $j$ starts from $i + \text{some number}$, that some number is increasing by $1$ every iteration.
- First iteration $i$ starts from $0$ and $j$ starts from $i + 1$ and then in the next iteration $i$ again starts from $0$ and $j$ starts from $i + 2$ and so on. So we can design the for loops accordingly.
- Now standing at some location $(i, j)$ meaning looking at the string from $i \to j$ we need to decide whether it is a pallindrome or not? If the characters $s[i] = s[j]$ means it is a pallindrome if and only if the substring $s[i+1 \, \to \, j-1]$ is a palindrome. this item $s[i+1 \, \to \, j-1]$ can be reused to avoid further computation.

### Code
```cpp
class Solution {
private:
    int max_length = INT_MIN;
    pair<int, int> indexOfMax;
public:
    string longestPalindrome(string s) {
        
        int size = s.size();
        
        // base cases are trivially solvable
        if (size == 1) return s;
        if (size == 2) {
            if (s[0] == s[1]) return s;
        }
        
        if (size == 3) {
            if (s[0] == s[2]) return s;
        }
        
        // our storage is dp of size n x n
        vector<vector<int>> dp(size, vector<int>(size, 0));

        // all the size one character is a pallindrome so mark 1
        for (int i=0; i<size; i++) {
            dp[i][i] = 1;
        }
        
        // now we update the following pattern
        // 00 11 22 33 44 .. done
        // 10 21 32 43 54 .. 

        for (int diff = 1; diff<size; diff++) {
            for (int i=0, j=i+diff; j<size; i++, j++) {
                if (s[i] == s[j]) {
                    // means the last 2 elements are similar
                    if (std::abs(i - j) == 1) {
                        // means there is no extra element b/w
                        // s[i] and s[j] they are together
                        // so from i to j it is pallindrome
                        dp[i][j] = 2;

                        if (dp[i][j] > max_length) {
                            indexOfMax.first = i;
                            indexOfMax.second = j;     
                            max_length = dp[i][j];
                        }

                    } else {
                        // means there is some elements b/w i and j
                        // check they are pallindrome or not?
                        if (dp[i+1][j-1] != 0) {

                            dp[i][j] = dp[i+1][j-1] + 2;

                            if (dp[i][j] > max_length) {
                                indexOfMax.first = i;
                                indexOfMax.second = j;

                                max_length = dp[i][j];
                            }

                        } else {
                            dp[i][j] = 0;
                        }
                    }
                } else {
                    // means it is not pallindrome
                    dp[i][j] = 0;
                }
            }
        }        
        
        // max size of the pallindrome is stored at indexOfMax;
        // the pallindrome is starting from indexOfMax.first to indexOfMax.second;
        // return this
        
        vector<char> result;
        for (int i=indexOfMax.first; i<=indexOfMax.second; i++) {
            result.push_back(s[i]);
        }
        
        string s1(result.begin(), result.end());
        
        return s1;
    }
};
```