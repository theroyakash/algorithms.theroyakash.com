# Binary Search on Answer

Let's think about the following problem we have a pattern on the number line $[0,0,0,0,0,0,0, \dots, 1, 1,1,1,1,1,\dots]$ that is bunch of $0$s and followed by bunch of 1s or the other way around. Then we can implement a binary search algorithm to find the first occurence of $1$ or $0$. This is called binary search on answer.

Suppose $f(x)$ is a continious strictly increasing function defined in between $\left[0,10^9 + 7\right)$) and we need to find minimum $x$ such that $f(x) > 0$. We consider the lower bound on $x = 0$ and upper bound on $x = 10^9 + 7$. We'll search for an $x$ in this range.

```cpp
void solve() {
    int left = 0;
    int right = 1e9 + 7;

    while (right > left + 1) {
        int mid = (left >> 1) + (right >> 1);
        if (f(mid) > 0) right = mid;
        else left = mid;
    }

    cout << "[first x > 0] : " << left << endl;
}
```

**Problems**

1. [Packing Rectangles](#packing-rectangles)
1. [Ropes](#ropes)
1. [Very Easy Task](#very-easy-task)


## Packing Rectangles
Codeforces [Link](https://codeforces.com/edu/course/2/lesson/6/2/practice/contest/283932/problem/A)
### Problem Statement
There are n rectangles of the same size: w in width and h in length. It is required to find a square of the smallest size into which these rectangles can be packed. Rectangles cannot be rotated.

### Approach
Simple binary search on answer (= size into which these rectangles can be packed). We write a feasability function $f(x)$, returns true if $(x, x)$ size rectangle can hold $n$ many $(w, h)$ size rectangles.

### Code
```cpp
#include <iostream>
#include <vector>
#include <string.h>
#include <climits>
#include <math.h>

using namespace std;
long long int w, h, n; 

bool f(long long int x) {
    return (floor(x / w) * floor(x / h)) >= n;
}

void solve() {
    cin >> w >> h >> n;
    long long int start = 0, end = LLONG_MAX;
    
    while (end > start + 1) {
        long long int mid = (start >> 1) + (end >> 1);
        if (f(mid)) {
            end = mid;
        } else {
            start = mid;
        }
    }

    std::cout << end << "\n";
}

int main () {
    int testcases = 1;
    // cin >> testcases;

    while (testcases--) {
        solve();
    }
    return 0;
}
```

## Ropes
Codeforces [Link](https://codeforces.com/edu/course/2/lesson/6/2/practice/contest/283932/problem/B)

### Problem Statement
There are n ropes, you need to cut k pieces of the same length from them. Find the maximum length of pieces you can get.

### Approach
Same as above, we write feasability function and then search for answer

### Code
```cpp
#include <iostream>
#include <vector>
#include <string.h>
#include <iomanip>  
#include <climits>
#include <set>
#include <map>
#include <queue>
#include <math.h>

using namespace std;

const int maxn = 1e5 + 10;
int a[maxn];
long long int n, k;

bool f(long double x) {
    if (x == 0) return true;
    
    int total = 0;
    for (int i = 0; i < n; i++) {
        total += (a[i] / x);
    }

    return total >= k;
}

void solve() {
    cin >> n >> k;
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    long double l = 0, r = 1e18;
    long double mid = 0;

    int lp = 100;
    std::cout << setprecision(7);

    while (lp--) {
        mid = l + ((r - l) / 2);
        if (f(mid)) {
            l = mid;
        } else {
            r = mid;
        }
    }

    std::cout << l << "\n";
}

int main () {
    int testcases = 1;
    // cin >> testcases;

    while (testcases--) {
        solve();
    }
    return 0;
}
```

## Very Easy Task
Codeforces [Link](https://codeforces.com/edu/course/2/lesson/6/2/practice/contest/283932/problem/C)
### Problem Statement
This morning the jury decided to add one more, Very Easy Problem to the problemset of the olympiad. The executive secretary of the Organizing Committee has printed its statement in one copy, and now they need to make n more copies before the start of the olympiad. They have two copiers at his disposal, one of which copies a sheet in x seconds, and the other in y seconds. (It is allowed to use one copier or both at the same time. You can copy not only from the original, but also from the copy.) Help them find out what is the minimum time they need to make n copies of the statement.

### Approach
Same we try to write the feasability function $f(x)$ and do a binary search on $x$ to find the minimum $x$ such that $f(x) =$ `true`.

### Code
```cpp

```