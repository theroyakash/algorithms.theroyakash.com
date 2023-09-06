# DP On Stocks

**Problems discussed**

1. [Buy and sell stocks I](#buy-and-sell-stocks-i)
1. [Buy and sell stocks II](#buy-and-sell-stocks-ii)
1. [Buy and sell stocks III](#buy-and-sell-stocks-iii)
1. [Buy and sell stocks IV](#buy-and-sell-stocks-iv)
1. [Buy and sell stocks with cooldown](#buy-and-sell-stocks-with-cooldown)

## Buy and sell stocks I
Find the problem on [leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

### Problem Statement
You are given an integer array prices where `prices[i]` is the price of a given stock on the $i^{\text{th}}$ day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

### Approach
- Pretty simple approach we can see for this,
    - We only sell with the minimum buy price to the left.
    - Now we record the max profit possible.

Hence the following code

### Code

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int min_ = INT_MAX;
        int max_profit = INT_MIN;
         
        for (int i = 0; i < prices.size(); i++) {
            min_ = std::min(min_, prices[i]);
            max_profit = std::max(max_profit, prices[i] - min_);
        }

        return max_profit;
    }
};
```

## Buy and sell stocks II
Find the problem on [Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii)
### Problem Statement
Problem statement is same as the last one but here you can buy multiple times (but not before you sell off first). You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

You are given an integer array prices where `prices[i]` is the price of a given stock on the $i^{\text{th}}$ day. Find maximum profit possible.

### Approach
- This problem can be solved by greedy strategy, but requires a proof why that works.
- However we don't need any argument as to why `DP` based solution is correct, because it explores all possibility of buy and sell choices.
    - If we look closely, we have 4 choices, if we bought previously then we can sell or not sell (2 choices)
    - if we have sold earlier on a given day we have 2 choices (buy or sell).
    - Based on that we write the recurrence and memoize to solve this problem.
- In the following implementation `B` is indication for buy order availability and `N` for not availability.

### Code
```cpp
class Solution {
public:
    int subroutine(vector<int>& prices, int index, char order,
                   map<int, map<char, int>>& dp) {

        if (index >= prices.size()) return 0;
        
        int profit = 0;

        if (dp.find(index) != dp.end()) {
            if ((*dp.find(index)).second.find(order) != (*dp.find(index)).second.end()) {
                return dp[index][order];
            }
        }

        if (order == 'B') {
            // we can buy now
            dp[index + 1]['N'] = subroutine(prices, index + 1, 'N', dp),
            dp[index + 1]['B'] = subroutine(prices, index + 1, 'B', dp);
            
            profit = std::max(
                dp[index + 1]['N'] - prices[index], // buy today
                dp[index + 1]['B'] // don't buy
            );
        } else {
            // we have choice to sell
            dp[index + 1]['B'] = subroutine(prices, index + 1, 'B', dp);
            dp[index + 1]['N'] = subroutine(prices, index + 1, 'N', dp);
            
            profit = std::max(
                prices[index] + dp[index + 1]['B'], // sell today
                dp[index + 1]['N']
            );
        }

        return dp[index][order] = profit;
    }

    int maxProfit(vector<int>& prices) {
        // 'B' enter the day with 'B'(buy) order available. 'N' not available
        map<int, map<char, int>> dp;
        return std::max(subroutine(prices, 0, 'B', dp), 0);
    }
};
```

Converting the code into **tabulation**, to reduce recursion space.

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        // 'B' enter the day with 'B'(buy) order available. 'N' not available
        int n = prices.size();
        int dp[n + 1][2];

        // 1 -> buy order available
        // 0 -> buy order not available
        
        // base cases
        dp[n][1] = 0;
        dp[n][0] = 0;
        
        for (int index = n - 1; index >= 0; index--) {
            for (int order = 0; order <= 1; order++) {
                int profit = 0;
                if (order == 1) {
                    profit = std::max(
                        dp[index + 1][0] - prices[index], // buy today
                        dp[index + 1][1] // don't buy
                    );
                } else {
                    profit = std::max(
                        prices[index] + dp[index + 1][1], // sell today
                        dp[index + 1][0]
                    );
                }

                dp[index][order] = profit;
            }
        }

        return dp[0][1];
    }
};
```

**Even more optimization**

We are using only one index up from the currently exploring index in the `dp` table. So no need to store `index + 1` once we done exploring `index` for some `index` $\in [1, n-1]$. We can replace current `index + 1` entries with `index` entries before we reach `index - 1`.

Hence we first set `dp` as a `pair<int, int>`. Then we say `dp.first` is same as `dp[index + 1][0]` and `dp.second` is same as `dp[index + 1][1]`, then replace `dp.first` and `dp.second` accordingly.

```cpp
class Solution {
public:
    pair<int, int> dp;

    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        
        // base cases
        dp.first = 0; // same as dp[idx + 1][1]
        dp.second = 0;
        
        for (int index = n - 1; index >= 0; index--) {
            for (int order = 0; order <= 1; order++) {
                int profit = 0;
                if (order == 1) {
                    profit = std::max(
                        dp.first - prices[index], // buy today
                        dp.second // don't buy
                    );

                    dp.second = profit;
                } else {
                    profit = std::max(
                        prices[index] + dp.second, // sell today
                        dp.first
                    );

                    dp.first = profit;
                }
            }
        }

        return dp.second;
    }
};
```

## Buy and sell stocks III
Find the problem on [Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii)
### Problem Statement
You are given an integer array prices where `prices[i]` is the price of a given stock on the $i^{\text{th}}$ day.

Find the maximum profit you can achieve. You may complete at most two transactions.

Note: You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again)

### Approach
The approach will be pretty much the same but we'll introduce a `cap` variable in the recursion, whenever a sell happens we can reduce this `cap` to `cap - 1`. Then stop the recursion when we reach `cap`.

### Code
```cpp
class Solution {
public:

    int subroutine(vector<int>& prices, int index, int order,
                   vector<vector<vector<int>>>& dp, int cap) {

        if (cap <= 0) return 0;
        if (index >= prices.size()) return 0;
        
        int profit = 0;

        if (dp[index][order][cap] != -1) return dp[index][order][cap];

        if (order) {
            // we can buy now
            dp[index + 1][0][cap] = subroutine(prices, index + 1, 0, dp, cap),
            dp[index + 1][1][cap] = subroutine(prices, index + 1, 1, dp, cap);
            
            profit = std::max(
                dp[index + 1][0][cap] - prices[index], // buy today
                dp[index + 1][1][cap] // don't buy
            );
        } else {
            // we have choice to sell
            dp[index + 1][1][cap - 1] = subroutine(prices, index + 1, 1, dp, cap - 1);
            dp[index + 1][0][cap] = subroutine(prices, index + 1, 0, dp, cap);
            
            profit = std::max(
                prices[index] + dp[index + 1][1][cap - 1], // sell today
                dp[index + 1][0][cap]
            );
        }

        return dp[index][order][cap] = profit;
    }

    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(2, vector<int>(3, -1)));
        return subroutine(prices, 0, 1, dp, 2);
    }
};
```

## Buy and sell stocks IV
Find the problem on [Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv)
### Problem statement
You are given an integer array prices where `prices[i]` is the price of a given stock on the $i^{\text{th}}$ day.

Find the maximum profit you can achieve. You may complete at most $k$ transactions.

Note: You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again)

### Approach
Once simple modification we need to do, that is update the initial call from $2$ to $k$ and update the `cap` size in the `dp` table from $3$ to $k + 1$ to accomodate $0 \dots k$.

### Code
```cpp
class Solution {
public:
    int subroutine(vector<int>& prices, int index, int order,
                   vector<vector<vector<int>>>& dp, int cap) {

        if (cap <= 0) return 0;
        if (index >= prices.size()) return 0;
        
        int profit = 0;

        if (dp[index][order][cap] != -1) return dp[index][order][cap];

        if (order) {
            // we can buy now
            dp[index + 1][0][cap] = subroutine(prices, index + 1, 0, dp, cap),
            dp[index + 1][1][cap] = subroutine(prices, index + 1, 1, dp, cap);
            
            profit = std::max(
                dp[index + 1][0][cap] - prices[index], // buy today
                dp[index + 1][1][cap] // don't buy
            );
        } else {
            // we have choice to sell
            dp[index + 1][1][cap - 1] = subroutine(prices, index + 1, 1, dp, cap - 1);
            dp[index + 1][0][cap] = subroutine(prices, index + 1, 0, dp, cap);
            
            profit = std::max(
                prices[index] + dp[index + 1][1][cap - 1], // sell today
                dp[index + 1][0][cap]
            );
        }

        return dp[index][order][cap] = profit;
    }

    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(2, vector<int>(k + 1, -1)));
        return subroutine(prices, 0, 1, dp, k);
    }
};
```

## Buy and sell stocks with cooldown
Find the problem on [leetcode $\to$](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/submissions/)

### Problem Statement
Same as above, find the maximum profit you can achieve. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times, same as [Buy and sell stocks II](#buy-and-sell-stocks-ii)) with the following restrictions:

- After you sell your stock, you cannot buy stock on the next day (i.e., cooldown one day).

You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

### Approach
We write the same exact code as [Buy and sell stocks II](#buy-and-sell-stocks-ii) and we change the sell to continue from `index + 2` because after a sell we go have one day cooldown.

### Code

```cpp
class Solution {
public:
    int subroutine(vector<int>& prices, int index, char order,
                   map<int, map<char, int>>& dp) {

        if (index >= prices.size()) return 0;
        
        int profit = 0;

        if (dp.find(index) != dp.end()) {
            if ((*dp.find(index)).second.find(order) != (*dp.find(index)).second.end()) {
                return dp[index][order];
            }
        }

        if (order == 'B') {
            // we can buy now
            dp[index + 1]['N'] = subroutine(prices, index + 1, 'N', dp),
            dp[index + 1]['B'] = subroutine(prices, index + 1, 'B', dp);
            
            profit = std::max(
                dp[index + 1]['N'] - prices[index], // buy today
                dp[index + 1]['B'] // don't buy
            );
        } else {
            // we have choice to sell
            dp[index + 2]['B'] = subroutine(prices, index + 2, 'B', dp);
            dp[index + 1]['N'] = subroutine(prices, index + 1, 'N', dp);
            
            profit = std::max(
                prices[index] + dp[index + 2]['B'], // sell today
                dp[index + 1]['N']
            );
        }

        return dp[index][order] = profit;
    }

    int maxProfit(vector<int>& prices) {
        // 'B' enter the day with 'B'(buy) order available. 'N' not available
        map<int, map<char, int>> dp;
        return subroutine(prices, 0, 'B', dp);
    }
};
```