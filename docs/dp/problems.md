# :material-train-car-passenger: Dynamic programming
There is a famous quote saying that "**those who don't remember the past are condemned to repeat it**". This quote very much synchronizes with the meaning of dynamic programming. What in dynamic programming you do is you calculate and remember the calculation, if and when the same calculation is encountered again, instead of recomputing it you fetch the data this reducing computing time.

There are several problems where you are required to calculate something multiple times and if you _cache_ the data then you can avoid computation multiple times. Let's see some of the problems where you need to cache some result to avoid multiple computations.

Table of Contents

- [Climbing Stairs](#climbing-stairs)
- [Frog Jump](#frog-jump)
- [House Robber](#house-robber)
- [House Robber II](#house-robber-ii)
- [Longest Palindromic Substring](#longest-palindromic-substring)
- [Palindromic Substrings](#palindromic-substrings)
- [Minimizing Coins](#minimizing-coins)
- [Coin Combinations I](#coin-combinations-i)
- [Coin Combinations II](#coin-combinations-ii)
- [Removing Digits](#removing-digits)
- [Grid Paths](#grid-paths)

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
        if (size == 3) return std::max(nums[0] + nums[2], nums[1]);
        
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
        if (size == 3) return std::max(nums[0] + nums[2], nums[1]);
        
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

## Palindromic Substrings
Given a string s, return the number of palindromic substrings in it. A string is a palindrome when it reads the same backward as forward. A substring is a contiguous sequence of characters within the string.

### Examples
```
Input: s = "abc"
Output: 3
Explanation: Three palindromic strings: "a", "b", "c".

Input: s = "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```

### Approach
- The solution is almost similar to the last problem where we were required to find the longest size of the pallindrome, for that we constructed a two dimensional array `dp` to store for all possible values of $(i, j)$ if there exists a pallindrome or not?
- Then we found what is the maximum of them and returned it.
- But where we are required to find the number of such palindromic sub-strings. So our `dp` array indicates for each $(i, j)$ such that $i<j$ $\exists$ a pallindrome between $s[i \to j]$. So if we count the number of non zero entries in the `dp` array we'll find the number of possible substrings in the string.

![iamge](../images/numberofsubstring.png)

As we can see every non zero entry in the array `dp` shows us a unique substring that is a pallindrome between $i \to j$.

### Code
```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int size = s.size();
        vector<vector<int>> dp(size, vector<int>(size, 0));
        
        for (int i=0; i<size; i++) {
            dp[i][i] = 1;
        }
        
        // go over the dp array to get the dp matrix sorted
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
        
        // check how many entries are non zero
        int counter = 0;
        for (int i=0; i<size; i++) {
            for (int j=0; j<size; j++) {
                if (dp[i][j] != 0) counter++;
            }
        }
        
        return counter;
    }
};
```

## Minimizing Coins
[Find the problem on CSES $\to$](https://cses.fi/problemset/task/1634/)

### Problem Statement
Consider a money system consisting of $n$ coins. Each coin has a positive integer value. Your task is to produce a sum of money $x$ using the available coins in such a way that the number of coins is minimal.

For example, if the coins are $\{1,5,7\}$ and the desired sum is $11$ an optimal solution is $5+5+1$ which requires $3$ coins.

**Input Format** The first input line has two integers n and x: the number of coins and the desired sum of money. The second line has n distinct integers $c_1,c_2,\dots,c_n$ the value of each coin.

**Output Format** Print one integer: the minimum number of coins. If it is not possible to produce the desired sum, print -1.

### Example

**Input:**
```
3 11
1 5 7
```
**Output:**
```
3
```

### Apporach
We need to build a $\text{dp}$ vector. $\text{dp[}i\text{]}$ will indicate minimum number of coins that will be required to get the value $i$. So trivially $\text{dp}[0] = 0$.

Now from $1 \to \text{target}$ we'll check what is the minimum number of coins that will be required to get that value. If we look closely then we'll find that for any given $i$: $\text{dp[}i\text{]} = \text{Min(dp[i], dp[i - coin])}$ for each of the coins in the coin bag. So mathematically we can write

$$
dp[i] =
\left\{
	\begin{array}{ll}
		\infty  & \mbox{if } i < 0 \\
		0 & \mbox{if } i = 0 \\
        \text{min}_{c \in \{c_1, c_2, \dots, c_n\}} dp[i - c] + 1 & \mbox{if } i > 0
	\end{array}
\right.
$$

Hence the solution below:

### C++ Code
```cpp
#include <algorithm>
#include <iostream>
#include <vector>

using std::cin;
using std::cout;
using std::endl;
using std::vector;

int findMinNumberOfCoins(vector<int> &coinStore, int target) {
    vector<int> dp(target + 1, 1e9); // dp[i] is the minimum number of coins to get target i
    dp[0] = 0;                       // to get 0 as target we need 0 coins <- base case

    for (int i = 1; i <= target; i++) {
        // loop over all the coins
        for (auto coin : coinStore) {
            if (i - coin < 0) {
                break;
            }
 
            dp[i] = std::min(dp[i], dp[i - coin] + 1);
        }
    }

    return (dp[target] == 1e9) ? -1 : dp[target];
}

int main() {
    // have coins, and a target
    // find what is the min coins to take for the target

    int coins, target;
    cin >> coins >> target;
    
    vector<int> coinStore;

    while (coins--) {
        int coin;
        std::cin >> coin;
        coinStore.push_back(coin);
    }

    std::sort(coinStore.begin(), coinStore.end());
    cout << findMinNumberOfCoins(coinStore, target) << endl;
    return 0;
}
```

## Coin Combinations I
[Find the problem on CSES $\to$](https://cses.fi/problemset/task/1635/)

Consider a money system consisting of n coins. Each coin has a positive integer value. Your task is to calculate the number of distinct ways you can produce a money sum x using the available coins.

For example, if the coins are $\{2,3,5\}$ and the desired sum is 9 then there are $8$ ways.

### Approach
Suppose $dp[i]$ is such that it defines to get $i$ value how many ways there is? So trivially $dp[0] = 1$ for any given set of coins $c_1, \dots c_n$. Now for any $i \in \mathbb{N}$, $dp[i]$ should be defined as follows

$$
dp[i] =
\left\{
	\begin{array}{ll}
		0  & \mbox{if } i < 0 \\
		1 & \mbox{if } i = 0 \\
        \displaystyle\sum_{c \in \{c_1, c_2, \dots, c_n\}} dp[i - c] & \mbox{if } i > 0 \text{ and }  i - c > 0
	\end{array}
\right.
$$

Let's see why $\displaystyle\sum_{c \in \{c_1, c_2, \dots, c_n\}} dp[i - c]$ holds. For the target $i$ and $i > 0$ the number of ways to get to $i$ is number of ways to get to $i-c$ for each of the coins in $\{c_1, c_2, \dots, c_n\}$. Hence we get the sum. The following is a code example working with this idea.

### Code
```cpp
#include <algorithm>
#include <iostream>
#include <vector>

using namespace std;

const int MOD = (int)1e9 + 7;

long long int findNumberOfWaysToGetTarget(int target, vector<long long int> &coinStore) {
    vector<long long int> dp(target + 1, 0); // set all to 0 first, dp[i] represents ways to get i target via coinStore
    dp[0] = 1;                               // base case only 1 way to get 0 with {coinStore}

    for (int i = 1; i <= target; i++) {
        for (int coin : coinStore) {
            if (i - coin >= 0) {
                dp[i] += dp[i - coin];
                dp[i] = dp[i] % MOD;
            }
        }
    }

    return dp[target];
}

void fileIO() {
    freopen("input.txt", "r", stdin);
    freopen("output.txt", "w", stdout);
}

int main() {

    int number_of_coins, target;
    vector<long long int> coins;

    cin >> number_of_coins >> target;

    while (number_of_coins--) {
        long long int coin;
        cin >> coin;
        coins.push_back(coin);
    }

    cout << findNumberOfWaysToGetTarget(target, coins) << endl;

    return 0;
}
```

## Coin Combinations II

[Find the problem on CSES $\to$](https://cses.fi/problemset/task/1636)

### Problem Statement
Same as the above problem statement but here we need to find unique solutions.

### Approach
In the **last problem** we were required to find all possible ways to get to the target, so $dp[i]$ was defining all the possible way we can get to the target $i$. Then we did a traversal over all the coin $c \in \{c_1, c_2, \dots, c_n\}$ and find out $dp[i]$ as $\displaystyle\sum_{c \in \{c_1, c_2, \dots, c_n\}} dp[i - c]$. This way considering coin $c$ we added all the possibilities from $dp[i - c]$.

But here we are required to find all possible ways but those should be unique, meaning $5 + 5 + 1$ is same as $1 + 5 + 5$ and $5 + 1 + 5$. These three are not distinct cases these three are unique cases.

To use only distinct combinations of coins what we can do is loop over all the coins $c \in \{c_1, c_2, \dots, c_n\}$, for each of the coin we set $dp[i]$ as $dp[i] + dp[i-c]$. Thus we'll loop over coins in order $c \in \{c_1,c_2, \dots, c_n\}$ thus using unqiue combinations of coins $\{c_i \mid i \in \{1, \dots, n\}\}$.

### Code
The code is the exact same as the last problem, with slight changes made to the nesting of the for loops. The outer for loop of target is now inside the loop of coins in the coin store.

```cpp
#include <algorithm>
#include <iostream>
#include <unordered_set>
#include <vector>

using namespace std;

const int MOD = (int)1e9 + 7;

long long int findNumberOfWaysToGetTarget(int target, vector<long long int> &coinStore) {
    vector<long long int> dp(target + 1, 0); // set all to 0 first, dp[i] represents ways to get i target via coinStore
    dp[0] = 1;                               // base case only 1 way to get 0 with {coinStore}

    for (int coin : coinStore) {
        for (int i = 1; i <= target; i++) {

            if (i - coin >= 0) {
                dp[i] += dp[i - coin];
                dp[i] = dp[i] % MOD;
            }
        }
    }

    return dp[target];
}

void fileIO() {
    freopen("input.txt", "r", stdin);
    freopen("output.txt", "w", stdout);
}

int main() {

    int number_of_coins, target;
    vector<long long int> coins;

    cin >> number_of_coins >> target;

    while (number_of_coins--) {
        long long int coin;
        cin >> coin;
        coins.push_back(coin);
    }

    cout << findNumberOfWaysToGetTarget(target, coins) << endl;

    return 0;
}
```

## Removing Digits
[Find the problem on CSES $\to$](https://cses.fi/problemset/task/1637)

### Problem Statement
You are given an integer n. On each step, you may subtract one of the digits from the number. How many steps are required to make the number equal to $0$.

### Example
Suppose the given number is 27, then the optimal number of digit removal is 5. The optimal solution is the following $27 \rightarrow 20 \rightarrow 18 \rightarrow 10 \rightarrow 9 \rightarrow 0$.

### Approach

<iframe width="560" height="315" src="https://www.youtube.com/embed/5JipxDAzD98" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

We'll do a recursive apporach on this problem, the intuition for the recursive solution is really easy to understand. For a given number $i$, we'll first find out what are the individual digits of this number. Then what we do is find out recursivly what is the minimum number of removals required accoss all the (number - digit) + 1.

For example what we do is for $27$ we'll find out what is minimum between $27 - 7$ and $27 - 2$ then add $1$. We have to make sure that we don't do this for digit $0$. Hence the solution is the following.

### Code
```cpp
#include <algorithm>
#include <iostream>
#include <unordered_map>
#include <vector>
#include <climits>

using namespace std;

vector<int> getIndividualDigits(int number) {
    vector<int> digits;

    while (number > 0) {
        digits.push_back(number % 10);
        number /= 10;
    }

    return digits;
}

int findMinimumNumberOfSubtractions(int number, unordered_map<int, int> &store) {
    // if already present then find and return
    if (number == 0) return 0;

    if (store.find(number) != store.end()) {
        return store[number];
    }

    vector<int> individualDigits = getIndividualDigits(number);
    int minimumNumberOfSubtractions = INT_MAX;

    for (auto digit:individualDigits) {
        if (digit) minimumNumberOfSubtractions = std::min(findMinimumNumberOfSubtractions(number - digit, store), minimumNumberOfSubtractions);
    }
    store[number] = minimumNumberOfSubtractions + 1;
    return store[number];
}

int main() {

    int number;
    cin >> number;
    unordered_map<int, int> store;

    cout << findMinimumNumberOfSubtractions(number, store);

    return 0;
}
```

## Grid Paths
[Find the problem on CSES $\rightarrow$](https://cses.fi/problemset/task/1638/)

### Problem Statement
Consider an $(n, n)$ grid whose squares may have traps. It is not allowed to move to a square with a trap. Your task is to calculate the number of paths from the upper-left square to the lower-right square. You can only move right or down.

### Example
We should print the number of paths modulo $10^9 + 7$

#### Input/Output example
```
Input:
4
....
.*..
...*
*...

Output: 3
```

### Approach
We find out a simple recursive equation to find out what is the number of ways to move to $(n, n)$ square.

Suppose we define $dp[i][j]$ as number of ways to move to $(n, n)$ from location $(i, j)$ in the grid. Then our solution is $dp[0][0]$. The base case here is at location $(n, n)$. Then $dp[n][n] = 1$. We can easily see that the solution for any location $(i, j)$ is

$$
dp[i][j] =
\left\{
	\begin{array}{ll}
		0  & \mbox{if } i, j \text{ is out of bounds of the grid}\\
        0  & \mbox{if } \text{ grid[i][j] is a * (obstacle)}\\
		1 & \mbox{if } (i, j) = (n, n) \\
        dp[i + 1][j] + dp[i][j + 1] & \mbox{Otherwise}
	\end{array}
\right.
$$

Here $dp[i + 1][j] + dp[i][j + 1]$ denotes number of ways to get from position $(i + 1, j)$ to $(n, n)$ and $(i, j + 1)$ to $(n, n)$ if $(i, j)$ is within the grid bounds, 0 otherwise.

### Code
```cpp
#include <iostream>
#include <string>

long long MOD = 1e9 + 7;

using std::cout;
using std::endl;

int main() {

    int size;
    std::cin >> size;

    int grid[size][size];

    int row = 0;
    while (row < size) {
        int col = 0;
        while (col < size) {
            char block;
            std::cin >> block;

            if (block == '.') {
                grid[row][col] = 0;
            } else {
                grid[row][col] = 1;
            }

            col++;
        }

        row++;
    }

    long long int dp[size][size];

    if (grid[size - 1][size - 1] == 0) {
        dp[size - 1][size - 1] = 1; // trivial to go from size:size to size:size
    } else {
        cout << 0 << endl;
        // exit program
        return 0;
    }

    for (int r = size - 1; r >= 0; r--) {
        for (int c = size - 1; c >= 0; c--) {
            if ((r == size - 1 and c == size - 1))
                continue;

            long long int down = (r + 1 >= size) ? 0 : dp[r + 1][c];
            long long int right = (c + 1 >= size) ? 0 : dp[r][c + 1];

            dp[r][c] = down + right;
            dp[r][c] = dp[r][c] % MOD;

            if (grid[r][c])
                dp[r][c] = 0;
        }
    }

    cout << dp[0][0] << endl;

    return 0;
}
```