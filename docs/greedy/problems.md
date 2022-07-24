# Greedy Problems
Greedy algorithms are algorithms that follows the problem-solving heuristic of making the locally optimal choice at each stage. You can not go back on your decisions once you've taken it.

**Let's solve some greedy problems**

**Questions solved here**

- [Jump Game](#jump-game)

## Jump Game
[Find the problem on Leetcode $\to$](https://leetcode.com/problems/jump-game/)
### Problem Statement
You are given an integer array `nums`. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.

Return `true` if you can reach the last index, or false otherwise.

### Example
```
Input: nums = [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

```
Input: nums = [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.
```

### Approach
- We'll approach the problem in a reversed order. So we'll start from the end of the array and see if it is possible to reach that place, if we find any place from where we can reach the last index in the next iteration we'll see, that we can reach that index?
- We'll try to see the updated position is possible or not?

### Code
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int to_reach = nums.size() - 1;
        int index = nums.size() - 1;
        
        while (index >=0) {
            if (nums[index] + index >= to_reach) {
                to_reach = index;
            }
            
            index--;
        }
        
        return to_reach == 0;
    }
};
```