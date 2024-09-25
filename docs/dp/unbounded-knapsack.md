# :material-bag-checked: Knapsack (Unbounded) Pattern

These problems discussed below conform to one knapsack pattern called Unbounded Knapsack. Using solutions to the knapsack problem and a minor modification, you can solve almost all of the following problems. We'll discuss this as we explain the code and the approach to these problems.

**Problems Discussed**

1. [:material-bag-carry-on: The Unbounded Knapsack Problem](#material-bag-carry-on-the-unbounded-knapsack-problem)
1. [:material-pipe-disconnected: Rod cutting](#material-pipe-disconnected-rod-cutting)
1. [Coin Change II](#coin-change-ii)
1. [Coin Change I (Unbounded Knapsack Way)](#coin-change-i-unbounded-knapsack-way)
1. [Maximum Ribbon Cut](#maximum-ribbon-cut)


## :material-bag-carry-on: The Unbounded Knapsack Problem
:material-google: :fontawesome-brands-amazon: :material-microsoft: :material-apple: :fontawesome-brands-meta:

To recap in the $0/1$ knapsack case we either include and item or not. Each item has one instance in the universe. Either we take one of each item in the bag or not. **But** in the unbounded knapsack case we can take any number of same item as we wish. Once we make a decision that we don't or we would take some element, we don't go back and rethink about it.

Multiple occurences of same item is allowed. If at some time $t$ we don't choose some element then we don't consider that element again (we call it processed). In $0/1$ knapsack whether we consider it or not we call it processed (we don't reconsider). As here we can include that element multiple amount of time then we consider the element not processed if it is considered in the last decision.

### Code changes from $0/1$ knapsack
In $0/1$ knapsack once we reject or accept an element we move onto the next element. This is shown in the following code

```cpp linenums="1" hl_lines="3 4 9" title="Recursion of 0/1 Knapsack"
if (wt[n - 1] <= W) {
    return std::max(
        val[n - 1] + knapSack(W - wt[n-1], wt, val, n - 1),
        knapSack(W, wt, val, n - 1)
    );
}

// not include as capacity is lower
return knapSack(W, wt, val, n - 1);
```

Lines 4, 9 is when we don't consider the element. When we don't consider the element knapsack and unbounded knapsack behaves the same. Hence we don't need any code changes there.

In line $3$ we are considering the inclusion of the element. Here we change the code for unbounded knapsack. Following is the code for $0/1$ and unbounded knapsack solved with bottom up approach and their differences marked.

```cpp linenums="1" title="0/1 Knapsack Code (Bottom Up)" hl_lines="6"

/* for all dp[i][j] */

if (j >= wt[i - 1]) {
    dp[i][j] = std::max(
        dp[i-1][j],  // not considering the i th element
        dp[i-1][j - wt[i-1]] + val[i-1] // considering the i th element
    );
} else {
    dp[i][j] = dp[i-1][j]; // not considering the i th element
}
```

```cpp linenums="1" title="Unbounded Knapsack Code" hl_lines="6"

/* for all dp[i][j] */

if (j >= wt[i - 1]) {
    dp[i][j] = std::max(
        dp[i-1][j],  // not considering the i th element
        dp[i][j - wt[i-1]] + val[i-1] // considering the i th element
    );
} else {
    dp[i][j] = dp[i-1][j]; // not considering the i th element
}
```

Notice when including the element we do not change the `dp[i]`.

## :material-pipe-disconnected: Rod cutting
Asked in: :material-google: :fontawesome-brands-amazon: :fontawesome-brands-meta:

### Problem Statement
Given a rod of length $N$ inches and an array of prices. $\text{price}[i]$ denotes the value of a piece of length $i$. 

Determine the maximum value obtainable by cutting up the rod and selling the pieces.

### Approach
The approach is same as the unbounded knapsack problem. Think of the total bag weight as rod length $n$, then you can cut any length $i$ from $n$ as long as it is possible. Think of cutting length $i$ from $n$ and putting it in a bag. The bag is the storage for all pieces. The pieces of the bag will determine the total profit which we have to maximize.

### Code

```cpp linenums="1" title="Rod cutting problem (bottom up approach)"
class Solution{
public:
    int cutRod(int price[], int n) {
        
        // dp[i][j] = considering the ith sized rod
        // and j total rod size, what is the max profit.
        int dp[n + 1][n + 1];
        
        memset(dp, 0, sizeof(dp));
        
        // length of rod = 0; profit = 0;
        for (int i = 0; i <= n; i++) {
            dp[i][0] = 0;
            dp[0][i] = 0;
        }
        
        // dp table fillup
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (i == 1) {
                    // for required length = j
                    // with i = 1 lenth pieces
                    // total profit = j * {1st element price}
                    dp[i][j] = j * price[0];
                } else {
                    if (i <= j) {
                        // if the rod size is big enough to
                        // have a cut of length[i] then consider the
                        // ith element and reduce the rod size
                        dp[i][j] = std::max(dp[i - 1][j], price[i - 1] + dp[i][j - i]);
                    } else {
                        dp[i][j] = dp[i - 1][j];
                    }
                }
            }
        }
        
        return dp[n][n];
    }
};
```
## Coin Change II
Find the problem on [Leetcode $\to$](https://leetcode.com/problems/coin-change-ii/description/)

### Problem Statement
You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money.

Return the number of combinations that make up that amount. If that amount of money cannot be made up by any combination of the coins, return 0.

You may assume that you have an infinite number of each kind of coin. The answer is guaranteed to fit into a signed 32-bit integer.

### Approach
This is a problem of unbounded knapsack. We have to count number of ways we can place coins to the amount. Think of `amount` as the bag size, and coins are each element with $v$ profit, $v$ is the value of the coin. Similar to unbounded knapsack we can put multiple instances of same coin and fill the bag up i.e, sum up to `amount`.


### Code
```cpp linenums="1" title="Coin Change II"
class Solution {
public:
    int change(int amount, vector<int>& coins) {

        if (amount == 0) return 1;

        int size = coins.size();
        vector<vector<int>> dp(size + 1, vector<int>(amount + 1, 0));

        for (int i = 0; i <= size; i++) {
            for (int j = 0; j <= amount; j++) {
                if (j == 0) {
                    // one way to select 0 amount from coins [select null set]
                    dp[i][j] = 1;
                }

                if (i and j) {
                    dp[i][j] = dp[i - 1][j];
                    if (j - coins[i - 1] >= 0) {
                        dp[i][j] += dp[i][j - coins[i - 1]];
                    }
                }
            }
        }

        return dp[size][amount];
    }
};
```

## Coin Change I (Unbounded Knapsack Way)
To solve the problem using most optimal way you can look [🔗 here](https://algorithms.theroyakash.com/dp/problems/#coin-combinations-ii).

Find the problem on [leetcode 🔗](https://leetcode.com/problems/coin-change/description/) and [gfg 🔗](https://practice.geeksforgeeks.org/problems/coin-change2448/1).

### Problem Statement
You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money.

Return the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return `-1`.

You may assume that you have an infinite number of each kind of coin.

### Approach

## Maximum Ribbon Cut
