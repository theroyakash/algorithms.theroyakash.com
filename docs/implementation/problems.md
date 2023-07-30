# Codeforces / CSES Implementation Algorithms

These problems are straight forward, has clear algorithms. Great starting point to initate revision when needed.

**Problems** discussed

- [Dalton the Teacher](#dalton-the-teacher)
- [Distinct Numbers](#distinct-numbers)


## Dalton the Teacher
Find the problem on [Codeforces](https://codeforces.com/contest/1855/problem/A)

### Problem Statement
Given array $a[n]$, find a permutation such that $a[i] \neq i \: \forall i \in [n]$ with minimum number of the following move

- Choose $i, j$ arbitarily, then $\textsf{swap}(a[i], a[j])$.

### Approach
- If the length of the number of pairs of bad elements $a[i] = i \mid\: \forall i \in \{\textsf{Bad Elements}\}$ is even then pairwise swap would be the minimum number of swap needed to achieve the desired permutation.
- Otherwise do a pairwise swap of the even element than one extra pair swap with the last one.

Hence the following is should get success

### Code
```cpp
#include <iostream>
using namespace std;

int main () {
    int testcases;
    cin >> testcases;
    
    while (testcases--) {
        int n;
        cin >> n;

        int p[n];

        for (int i = 0; i < n; i++) {
            cin >> p[i];
        }

        vector<int> sadStudentLocations;
        for (int i = 0; i < n; i++) {
            if (p[i] == i + 1) sadStudentLocations.push_back(i);
        }
        
        int totalSadStudents = sadStudentLocations.size();

        if (not totalSadStudents) {
            cout << 0 << endl;
        } else if (totalSadStudents % 2 == 0) {
            cout << totalSadStudents / 2 << endl;
        } else {
            cout << totalSadStudents / 2 + 1 << endl;
        }
    }
    return 0;
}
```

## Distinct Numbers
Find the problem in [CSES](https://cses.fi/problemset/task/1621).

You are given a list of $n$ integers, and your task is to calculate the number of distinct values in the list.

### Approach
- Use set to include them and return the size of the set. We are using `Splitmix64Hash` hash to speed up the `unordered_set` otherwise the testcases are designed to do $O(n^2)$ blow up of the set ([ref.](https://codeforces.com/blog/entry/62393)).

### Code

```cpp
using namespace std;

struct Splitmix64Hash {
    static uint64_t splitmix64(uint64_t x) {
        // http://xorshift.di.unimi.it/splitmix64.c
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

int main () {

    int n;
    cin >> n;

    unordered_set<int, Splitmix64Hash> s;

    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;

        s.insert(x);
    }

    cout << s.size() << endl;

    return 0;
}
```