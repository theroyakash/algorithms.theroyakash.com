# DP on strings
We'll discuss few DP on strings problem here, starting with the Longest Common 🗿 pattern. Then we'll move on to Edit distance and other similar basic and advanced DP on string problems.

**Problems** discussed

1. [Longest Common Subsequence](#longest-common-subsequence)
1. [Printing the Longest Common Subsequence](#printing-the-longest-common-subsequence)
1. [Longest common substring](#longest-common-substring)
1. [Longest palindromic sequence](#longest-palindromic-sequence)


## Longest Common Subsequence
Find the problem on [Leetcode $\to$](https://leetcode.com/problems/longest-common-subsequence/description/)
### Problem Statement
Given two strings `text1` and `text2`, return the length of their longest common subsequence. If there is no common subsequence, return `0`.

A subsequence of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.

- For example, "ace" is a subsequence of "abcde".

A common subsequence of two strings is a subsequence that is common to both strings.

### Examples
```
Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.
---

Input: text1 = "abc", text2 = "abc"
Output: 3
Explanation: The longest common subsequence is "abc" and its length is 3.
---

Input: text1 = "abc", text2 = "def"
Output: 0
Explanation: There is no such common subsequence, so the result is 0.
```

### Approach
- We formalize an approach to the solution for a DP problem into the following three steps
    - Find the base case (smallest viable input),
    - write the choice diagram, write the recursion for that choice diagram
    - memoize this recursion,
    - write bottom up approach solving the recursion.
- Here the base case is for a string with size $=0$. Hence we add the first condition for the recursion solution $= 0$ if $S_1 \text{.length or } S_2 \text{.length} = 0$. For this case the output is 0.
- Now following is the choice diagram

![choicediag0.png](./../images/choicediag0.png)

For this choice diagram we write the recursion and memoize it (see in code below).

### Code
```cpp
class Solution {
private:
    vector<vector<int>> dp;
    int subroutine(int n1, int n2, string &text1, string &text2) {
        if (n1 <= -1 or n2 <= -1) {
            return 0;
        }
        
        // base case
        if (dp[n1][n2] != -1) return dp[n1][n2];

        if (text1[n1] == text2[n2]) {
            return dp[n1][n2] = 1 + subroutine(n1-1, n2-1, text1, text2);
        }
        
        return dp[n1][n2] = std::max(
            subroutine(n1 - 1, n2, text1, text2),
            subroutine(n1, n2 - 1, text1, text2)
        );
    }

public:
    int longestCommonSubsequence(string text1, string text2) {
        dp = vector<vector<int>>(text1.size(), vector<int>(text2.size(), -1));
        return subroutine(text1.size() - 1, text2.size() - 1, text1, text2);
    }
};
```

**Converting** the code into a iterative DP approach

```cpp
class Solution {
public:
    vector<vector<int>> dp;

    int longestCommonSubsequence(string text1, string text2) {
        dp = vector<vector<int>>(text1.size() + 1, vector<int>(text2.size() + 1, 0));
        
        int n1 = text1.size();
        int n2 = text2.size();

        for (int i = 1; i <= n1; i++) {
            for (int j = 1; j <= n2; j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = std::max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp[n1][n2];
    }
};
```

## Printing the Longest Common Subsequence
Find the problem on [Coding Ninjas $\to$](https://www.codingninjas.com/studio/problems/print-longest-common-subsequence_8416383?leftPanelTab=1)
### Problem Statement
Same as above, but instead of the length we need the actual subsequence $(\textsf{LCS})$ as the output.
### Approach
- We know that output of $\textsf{LCS}$ is stored at $dp[n][m]$ if $n, m = \textsf{size}(S_1), \textsf{size}(S_2)$.
- We reach to $dp[n][m]$ by two ways
    - If $S_1[n] = S_2[m]$ then from $dp[n - 1][m- 1]$, that is $dp[n][m] = 1 + dp[n - 1][m- 1]$.
    - Otherwise we come from $max(dp[n -1][m], dp[n][m-1])$.
- We'll do a backtracking from $dp[n][m]$ to $dp[1][1]$ and add the characters whenever we encounter same characters in $S_1, S_2$.
- This is the way we build the `reverse` of $\textsf{LCS}$ string.
- We reverse the string and return.
### Code
```cpp
#include <string.h>
#include <iostream>

using namespace std;

string findLCS(int n, int m,string &s1, string &s2){
	n = s1.size();
	m = s2.size();

	int dp[n + 1][m + 1];
	memset(dp, 0, sizeof(dp));

	// build the dp table
	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= m; j++) {
			if (s1[i - 1] == s2[j - 1]) {
				dp[i][j] = 1 + dp[i - 1][j - 1];
			} else {
				dp[i][j] = std::max(dp[i-1][j], dp[i][j-1]);
			}
		}
	}

	// now do backtrack
	string LCS = "";
	int i = n;
	int j = m;

	while (i > 0 and j > 0) {
		if (s1[i - 1] == s2[j - 1]) {
			LCS += s1[i - 1];
			i--; j--;
		} else {
			if (dp[i - 1][j] > dp[i][j - 1]) {
				i--;
			} else {
				j--;
			}
		}
	}

	std::reverse(LCS.begin(), LCS.end());

	return LCS;
}
```

## Longest common substring
Find the problem on [GeeksForGeeks $\to$](https://practice.geeksforgeeks.org/problems/longest-common-substring1452/1)
### Problem Statement
Given two strings. The task is to find the length of the longest common substring. 

**Examples**
```
Input: S1 = "ABCDGH", S2 = "ACDGHR", n = 6, m = 6
Output: 4
Explanation: The longest common substring
is "CDGH" which has length 4.
---

Input: S1 = "ABC", S2 "ACB", n = 3, m = 3
Output: 1
Explanation: The longest common substrings
are "A", "B", "C" all having length 1.
```

### Approach
- The main difference from the last problem is that here we need to consider a _continuous_ substring.
- We define $dp[i][j]$ as same of length of $S_1[1 \dots i], S_2[1 \dots j]$. If $S_1[1 \dots i] \neq S_2[1 \dots j]$ then the common substring size we set to $0$. 
- We record every step if we find the biggest substring so far.

### Code
```cpp
class Solution {
public:
    int longestCommonSubstr (string S1, string S2, int n, int m) {
        int dp[n + 1][m + 1];
        memset(dp, 0, sizeof(dp));
        
        int maxLength = 0;
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (S1[i - 1] == S2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                    maxLength = std::max(maxLength, dp[i][j]);
                }
                /* else { dp[i][j] = 0; }
                 * if we don't see same element we reset the dp[i][j] = 0;
                 * but it is already 0 via memset.
                 */
            }
        }
        
        return maxLength;
    }
};
```
## Longest palindromic sequence
### Problem Statement
Given a string s, find the longest palindromic subsequence's length in s.

A **subsequence** is a sequence that can be derived from another sequence by deleting some or no elements without changing the order of the remaining elements.

### Approach
Longest palindromic subsequence has the following characteristics
1. It is pallindrome. Hence it reads same as front to back and back to front.
1. It is longest.

As it reads same as front to back and back to front, if we reverse the original string the longest palindromic subsequence should remain the same.

Hence if we find the longest common subsequence between the string and its reverse we find the longest palindromic subsequence for the string. Hence the following code.

### Code
```cpp
class Solution {
public:
    int LCS(string s1, string s2) {
        int n;
        n = s1.size();

        int dp[n + 1][n + 1];
        memset(dp, 0, sizeof(dp));

        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= n; j++) {
                if (not i or not j) {
                    dp[i][j] = 0;
                } else {
                    if (s1[i - 1] == s2[j - 1]) {
                        dp[i][j] = 1 + dp[i - 1][j - 1];
                    } else {
                        dp[i][j] = std::max(
                            dp[i - 1][j],
                            dp[i][j - 1]
                        );
                    }
                }
            }
        }

        return dp[n][n];
    }

    int longestPalindromeSubseq(string s) {
        string s2 = s;
        reverse(s2.begin(), s2.end());

        return LCS(s, s2);
    }
};
```