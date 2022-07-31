# :material-train-car-passenger: Dynamic programming
There is a famous quote saying that "**those who don't remember the past are condemned to repeat it**". This quoute very much synchronize with the meaning of dynamic programming. What in dynamic programming you do is you calculate and remember the calculation, if and when the same calculation is encountered again, instead of recomputing it you fetch the data this reducing computing time.

There are several problems where you are required to calculate something multiple times and if you _cache_ the data then you can avoid computation multiple times. Let's see some of the problems where you need to cache some result to avoid multiple computations.

Table of Contents

- [Climbing Stairs](#climbing-stairs)
- [Frog Jump](#frog-jump)

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