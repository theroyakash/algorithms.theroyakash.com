# Breaking Arrays into subarrays meeting some condition

- [Codeforces Contest 882 Div 2, Problem A](#codeforces-contest-882-div-2-problem-a)


Few example problems

- [Codeforces Contest 882 Div 2, Problem A](https://codeforces.com/contest/1847/problem/A)
- [Codeforces Contest 882 Div 2, Problem B](https://codeforces.com/contest/1847/problem/B)
***

## [Codeforces Contest 882 Div 2, Problem A](https://codeforces.com/contest/1847/problem/A)
### Problem Statement
In [Codeforces Contest 882 Div 2, Problem A](https://codeforces.com/contest/1847/problem/A) they are asking to break the array into continuous parts such that sum of power for all segment is minimised. Suppose $A$ is the array then find a sequence $(a_i) \mid \forall i \in [n], a_i = A[i]$ such that $\sum_{i \in (a_i)} f(a_{i-1}, a_i)$ is minimised. Here $(a_{i-1}, a_i)$ is subsequence of the array $A$.

### Approach
- Power of a sub sequence of villagers from $a[l \dots r]$ is defined as $abs(a[l] - a[l + 1]) + abs(a[l+1] - a[l + 2]) + ... + abs(a[r-1] - a[r])$
- Our task is to break the villagers into exactly $k$ contiguous subgroups so that the sum of their power for each of the group is minimized and single member groups are worth $0$ power.
- **Simple Observation** is that if we break the continiousness of a group at some location $i$ then the sum of all the powers reduces by $abs(a[i - 1] - a[i])$. Hence we must find the biggest such drop and break the members there.
- At the end return the total sum.

### Code
```cpp
#include <iostream>
#include <math.h>
#include <vector>

using namespace std;

int main() {
    // ith guy has a[i] sus on Kars.
    // a sub sequence of villagers from a[l ... r] is defined as
    // abs(a[l] - a[l + 1]) + abs(a[l+1] - a[l + 2]) + ... + abs(a[r-1] - a[r]) is the power of a group

    // task break the villagers into exactly k
    // contiguous subgroups so that the sum of their power for each of the group is minimized.
    // single member groups are worth 0 power.

    int testcases;
    cin >> testcases;

    while (testcases--) {
        int n, k;
        cin >> n >> k;

        int a[n];
        for (int i = 0; i < n; i++) {
            cin >> a[i];
        }

        // solution for each of the testcases
        // first consider the entire array as a group
        int maxcost = 0;
        pair<int, int> diff[n]; // difference array stores all n-1 a[i-1] - a[i] computations

        diff[0] = {0, 0};

        for (int i = 1; i < n; i++) {
            int cost = abs(a[i] - a[i - 1]);
            maxcost += cost;
            diff[i] = {cost, i - 1};
        }

        // now sort the diff array based on decreasing differences
        std::sort(diff, diff + n, [](const auto &a, const auto &b) {
            return a.first > b.first;
        });

        for (int i = 0; i < k-1; i++) {
            maxcost -= diff[i].first;
        }

        cout << maxcost << endl;
    }

    return 0;
}
```