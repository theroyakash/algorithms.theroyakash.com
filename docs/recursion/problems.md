# :material-light-recessed: Recursion, Backtracking and Subset Problems

These pattern of questions include subset formation, backtracking and recursive calls. Almost all of the questions are leetcode medium level questions. Solving these following problems will help people gain a better understanding of

1. Backtracking,
2. Recursion calls,
3. Subset pattern of questions.

## Find all the subsets
[Problem on Leetcode $\to$](https://leetcode.com/problems/subsets/)

Given an integer array nums of unique elements, return all possible subsets (the power set). The solution set must not contain duplicate subsets. Return the solution in any order.

### Approach
With basic recursive approach, we first include an element and don't include an element. This is how we can generate all the unique subsets from a given set. We push the subset to the answer only at the last step of the recursion tree when the index reaches to the last element.

### Code
```cpp
class Solution {
private:
    vector<vector<int>> answer;

    void recurse(vector<int>& v, int index) {
        static vector<int> b;

        if (index > v.size() - 1) {
            
            // Push the subset only at the last step of the recursion tree
            
            vector<int> c(b); // create a copy of b and add it into answer
            answer.push_back(c);

            return;
        }

        // with the element
        b.push_back(v[index]);
        recurse(v, index + 1);

        // without the current element
        b.pop_back();
        recurse(v, index + 1);
    }

public:
    vector<vector<int>> subsets(vector<int>& nums) {
        recurse(nums, 0);
        return answer;
    }
};

```

## Subsets II
Subsets II is a little bit different, given an integer array nums **that may contain duplicates**, return all possible subsets (the power set).

The solution set must not contain duplicate subsets. Return the solution in any order.

### Approach
- The approach should be similar to the subset approach, now in order to avoid duplicates in the power set what we can do is the following
    - We can first sort the input array 
    - then skip duplicates while traversing through the array in the recursive calls by instead of skipping 1 index, skip multiple indexes until we can't find a new value.
    - This specific modification helps us avoid duplicates
        ```cpp
        while(index < nums.size() - 1 and nums[index] == nums[index + 1]){
            index++;
        }
        ```
- Now we can avoid duplicates in the power set.
### Code

```cpp
class Solution {
private:
    
    vector<vector<int>> answer;
    
    void recurse(vector<int>& nums, int index){
        
        static vector<int> b;
        
        if (index >= nums.size()) {
            // at the end of tree
            vector<int> c(b);
            answer.push_back(c);
            return;
        }
        
        b.push_back(nums[index]);
        recurse(nums, index + 1);
        
        b.pop_back();
        while(index < nums.size() - 1 and nums[index] == nums[index + 1]){
            index++;
        }
        recurse(nums, index + 1);
    }
    
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        std::sort(nums.begin(), nums.end());
        recurse(nums, 0);
        return answer;
    }
};
```

## Combination Sum
[Problem on Leetcode $\to$](https://leetcode.com/problems/combination-sum/)

Given an array of distinct integers candidates and a target integer target, return a list of all unique combinations of candidates where the chosen numbers sum to target. You may return the combinations in any order.

The same number may be chosen from candidates an unlimited number of times. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

It is guaranteed that the number of unique combinations that sum up to target is less than 150 combinations for the given input.

### Examples
```
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
Explanation:
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
7 is a candidate, and 7 = 7.
These are the only two combinations.


Input: candidates = [2,3,5], target = 8
Output: [[2,2,2,2],[2,3,3],[3,5]]


Input: candidates = [2], target = 1
Output: []
```

### Constraints:

- $1 \leq$ `candidates.length` $\leq 30$
- $1 \leq$ <= `candidates[i]` $\leq 200$
- All elements of candidates are distinct.
- $1 \leq$ `target` $\leq 500$

### Approach
- We'll create a recursive subroutine called `recurse` which will recursively find all the sub-sequences that sums up to `target`
- Recall a similar problem in the [intro section](/recursion/intro/) where we solved Find all the sub-sequences that sums up to K. But here the problem is little different, here one element can be added multiple times.
- So we create a modification in the first recursive call: `recurse(candidates, target, sum, index);`. In this case we are again calling with the same index so that we can check if multiple times we can add the same element.
- For the case where we are not considering a particular element [as] we are not considering a particular element, we'll simply call the recursive call `recurse(candidates, target, sum, index + 1;` with $\text{Index} + 1$.

### Code

```cpp
class Solution {
public:
    
    vector<vector<int>> answer;
    
    void recurse(vector<int> &candidates, int target, int sum, int index) {
        
        static vector<int> b; // shared data structure
        
        if (index > candidates.size() - 1) return;
        
        if (sum < target) {
            
            // check with the current index
            b.push_back(candidates[index]);
            sum = sum + candidates[index];
            recurse(candidates, target, sum, index); // can take the same index multiple times
            
            // remove the current index and check again
            b.pop_back();
            sum = sum - candidates[index];
            recurse(candidates, target, sum, index+1); // index+1 bcz we are no longer interested with that index
        }
        
        if (sum == target) {
            vector<int> bk(b);
            answer.push_back(bk);
        }
        
    }
    
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        recurse(candidates, target, 0, 0);
        return answer;
    }
};
```

## Combination Sum III

[Find the problem on Leetcode $\to$](https://leetcode.com/problems/combination-sum-iii/)

This is a slight modification of the previous problem, find all valid combinations of k numbers that sum up to n such that the following conditions are true:

- Only numbers 1 through 9 are used.
- Each number is used at most once.

Return a list of all possible valid combinations. The list must not contain the same combination twice, and the combinations may be returned in any order.