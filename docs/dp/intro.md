# :material-train-car-passenger: Dynamic programming
There is a famous quote saying that "**those who don't remember the past are condemned to repeat it**". This quoute very much synchronize with the meaning of dynamic programming. What in dynamic programming you do is you calculate and remember the calculation, if and when the same calculation is encountered again, instead of recomputing it you fetch the data this reducing computing time.

There are several problems where you are required to calculate something multiple times and if you _cache_ the data then you can avoid computation multiple times. Let's see some of the problems where you need to cache some result to avoid multiple computations.

Table of Contents

- [Climbing Stairs](#climbing-stairs)

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
