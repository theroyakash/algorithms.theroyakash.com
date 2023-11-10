# Disjoint Set (Union Find) Data Structure

**Table of Contents**

1. [Naïve implementation of DSU](#naïve-implementation-of-dsu)
1. [APIs to support](#apis-to-support)
1. [Redundant Connection](#redundant-connection)
1. [News Distribution](#news-distribution)
1. [Building Roads](#building-roads)

## Naïve implementation of DSU
One of the naïve implementation for DSU is to use an array. Here for a Leetcode problem I've shown an implementation of DSU that do not use rank-path compression optimizations, just plain and simple implementation of DSU with array.

## APIs to support
- `find(int i)` to find the parent of some node `i`
- `makeUnion(int i, int j)` to create a union of two nodes `i` and `j`.
- The following is a simple implementation of DSU that implements the upper 2 APIs.

```cpp
class DSU {
private:
    int size;
    vector<int> dsuArray;
public:
    DSU(int n) {
        int size = n;
        for (int i=0;i<n+1;i++) {
            dsuArray.push_back(i);
        }
    }
    
    vector<int> getArray() {
        return dsuArray;
    }
    
    int find(int n) {
        return dsuArray[n];
    }
    
    bool makeUnion(int i, int j) {
        int parent_i = find(i);
        int parent_j = find(j);
        
        if (parent_i == parent_j) return false;
        
        // find all occurences of parent_j
        // set to parent_i to make the Union
        for (int i=1; i<dsuArray.size(); i++) {
            if (dsuArray[i] == parent_j) {
                dsuArray[i] = parent_i;
            }
        }
        
        return true;
    }
};
```

### Union by rank optimization
**If** you see the `makeUnion()` API we are setting the parents of `parent_j` to `parent_i`. Now if the number of elements in the group of `j` is higher then there is a chance to optimize the time complexity. We can update the `parent_i` to `parent_j` as number of times `parent_i` is to be updated is much lower.

This is called **union by rank optimization**. In order to do this we need to have some data structure that'll indicate the size of every group everytime we do an union. Then we can take the smaller group and updated its parents instead of updating the parent of the larger group size.


### Union by Size optimization
This is by far the easiest implementation of DSU. Here is an implementation using size optimization. Main goal of this optimization is we only update the parent of smaller group to the larger group if we are doing `UNION` between two different size groups.

```cpp
const int maxn = 1e5 + 10;
vector<int> adj[maxn];
int parent[maxn], size[maxn];
int n, m;

void init() {
    for (int i = 1; i <= n; i++) {
        parent[i] = i; size[i] = 1;
    }
}

int find(int x) {
    if (parent[x] == x) return x;
    return parent[x] = find(parent[x]);
}

bool UNION(int x, int y) {
    x = find(x); y = find(y);
    if (size[x] > size[y]) std::swap(x, y);

    if (x == y) return false;

    parent[x] = y;
    size[y] += size[x];

    return true;
}
```

# Problems (UF)

## Redundant Connection
[Find the problem on Leetcode](https://leetcode.com/problems/redundant-connection/description/)

### Problem Statement
In this problem, a tree is an undirected graph that is connected and has no cycles.

You are given a graph that started as a tree with n nodes labeled from 1 to n, with one additional edge added. The added edge has two different vertices chosen from 1 to `n`, and was not an edge that already existed. The graph is represented as an array edges of length `n` where `edges[i] = [ai, bi]` indicates that there is an edge between nodes ai and bi in the graph.

Return an edge that can be removed so that the resulting graph is a tree of n nodes. If there are multiple answers, return the answer that occurs last in the input.

### Example
```
Input: edges = [[1,2],[2,3],[3,4],[1,4],[1,5]]
Output: [1,4]
```

### Approach
- We'll use a DSU to add the edges in the DSU until we get a cycle.
- When we get a cycle, we'll add it to the answer, this will be the edge that we need to return.

### Code
```cpp
const int maxn = 1002;
int parent[maxn], sz[maxn];
int n = 1000;

void init() {
    for (int i = 0; i <= 1001; i++) {
        parent[i] = i;
        sz[i] = 1;
    }
}

int find(int x) {
    if (parent[x] == x) return x;
    return parent[x] = find(parent[x]);
}

bool UNION(int x, int y) {
    x = find(x);
    y = find(y);

    if (x == y) return false;

    if (sz[x] > sz[y]) std::swap(x, y);
    parent[x] = y; sz[y] += sz[x];

    return true;
}

class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        init();
        vector<int> ans;
        for (auto ed : edges) {
            if (not UNION(ed[0], ed[1])) {
                return ed;
            }
        }

        return ans;
    }
};
```

## News Distribution
[Codeforces Link](https://codeforces.com/problemset/problem/1167/C)
### Problem Statement
In some social network, there are $n$ users communicating with each other in $m$ groups of friends. Let's analyze the process of distributing some news between users.

Initially, some user $x$ receives the news from some source. Then he or she sends the news to his or her friends (two users are friends if there is at least one group such that both of them belong to this group). Friends continue sending the news to their friends, and so on. The process ends when there is no pair of friends such that one of them knows the news, and another one doesn't know.

For each user $x$ you have to determine what is the number of users that will know the news if initially only user $x$ starts distributing it.


```text title="Input Format"
7 5
3 2 5 4
0
2 1 2
1 1
2 6 7
```

```text title="Output format"
4 4 1 4 4 2 2
```

### Approach
- This is a simple disjoint set problem, however naively implementing will result in TLE.
- Another approach is the disjoint set union by size optimization,
- This problem can be solved using BFS as well. However to show the working of DSU we've implemented it in this way.

Optimally we can implement the solution using disjoint set with size optimization, following is the solution, (the highlighted portion finds the size of the each components).

```cpp hl_lines="42 43 44 45 46"
#include <string.h>

#include <climits>
#include <iostream>
#include <map>
#include <queue>
#include <set>
#include <stack>
#include <vector>

using namespace std;

class DSU {
public:
    int n;
    std::vector<int> parent;
    std::vector<unsigned long long int> size;
    DSU(int n) {
        this->n = n;
        for (int i = 0; i <= n; i++) {
            parent.push_back(i);
            size.push_back(1);
        }
    }

    int find(int x) {
        if (parent[x] == x)
            return x;
        return parent[x] = find(parent[x]);
    }

    void UNION(int a, int b) {
        a = find(a);
        b = find(b);

        if (size[a] < size[b]) swap(a, b);
        
        parent[b] = a;
        size[a] += size[b];
    }

    void make(std::vector<unsigned long long int>& ans) {
        for (int i = 0; i <= n; i++) {
            ans[i] = size[find(i)];
        }
    }
};

void solve() {
    int n, m;
    cin >> n >> m;

    DSU d(n);

    for (int i = 1; i <= m; i++) {
        int x;
        cin >> x;

        if (x == 0) continue;

        int prnt;
        cin >> prnt;

        for (int i = 1; i < x; i++) {
            int node;
            cin >> node;
            d.UNION(prnt, node);
        }
    }
    

    vector<unsigned long long int> ans(n + 1, 0);
    d.make(ans);

    for (int i = 1; i <= n; i++) {
        std::cout << ans[i] << " ";
    }
}

int main() {
    int testcases = 1;
    // cin >> testcases;

    while (testcases--) {
        solve();
    }
    return 0;
}
```

## Building Roads
[Find the problem here $\to$](https://cses.fi/problemset/task/1666)
### Problem Statement
Byteland has $n$ cities, and $m$ roads between them. The goal is to construct new roads so that there is a route between any two cities. Your task is to find out the minimum number of roads required, and also determine which roads should be built.

### Apporach
Same union find data structure implementation. The minimum number of roads should be $k - 1$ where k is the number of connected components.

### Solution
```cpp
// https://cses.fi/problemset/task/1666/

#include <algorithm>
#include <climits>
#include <iostream>
#include <vector>

using namespace std;

void fileIO() {
    freopen("input.txt", "r", stdin);
    freopen("output.txt", "w", stdout);
}

/*
 * Fast Disjoint Set Union Data Structure.
 * has implementation for fast Union and find operation
 */
class DisjointSetUnion {
public:
    int totalVertex;
    vector<int> parent;
    vector<int> size;

    DisjointSetUnion(int vertex) {
        this->totalVertex = vertex;
        for (int i = 0; i <= vertex; i++) {
            parent.push_back(i);
            size.push_back(1);
        }
    }

    bool UNION(int u, int v) {
        int parent_u = FIND(u);
        int parent_v = FIND(v);

        if (parent_u == parent_v) {
            return false;
        }

        if (size[parent_u] < size[parent_v]) {
            std::swap(parent_u, parent_v);
        }

        parent[parent_v] = parent_u;
        size[parent_u] += size[parent_v];

        return true;
    }

    int FIND(int i) {
        // recursive find call to find parent
        if (parent[i] == i) {
            return i;
        }

        return parent[i] = FIND(parent[i]);
    }

    vector<int> idConnectedComponents() {
        vector<int> components;
        for (int i = 1; i <= totalVertex; i++) {
            if (parent[i] == i) {
                components.push_back(i);
            }
        }

        return components;
    }
};

class City {
private:
    std::vector<std::pair<int, int>> allRequiredRoads;

    std::vector<std::pair<int, int>> roads;
    int numberOfCities, numberOfRoads;

public:
    void setRoads(std::vector<std::pair<int, int>> roads) {
        this->roads = roads;
    }

    void setNumberOfCities(int numberOfCities) {
        this->numberOfCities = numberOfCities;
    }

    void setNumberOfRoads(int numberOfRoads) {
        this->numberOfRoads = numberOfRoads;
    }

    void calculateMinimumRequiredRoads() {
        // MARK: Implemementation
        DisjointSetUnion dsu = DisjointSetUnion(this->numberOfCities);

        // the number of roads to be added to connect all cities is the number of the disjoint set - 1
        // connect each of the disjoint set by connecting one city from each of the disjoint set
        for (auto road : roads) {
            dsu.UNION(road.first, road.second);
        }

        // build roads from each one of the connected components.

        vector<int> idOfEachConnectedComponents = dsu.idConnectedComponents();
        int totalConnectedComponents = idOfEachConnectedComponents.size();

        if (totalConnectedComponents == 1) {
            return;
        } else {
            int index = 0;

            while (index < totalConnectedComponents - 1) {
                int city1 = idOfEachConnectedComponents[index];
                int city2 = idOfEachConnectedComponents[index + 1];
                this->allRequiredRoads.push_back({city1, city2});
                index += 1;
            }
        }
    }

    std::vector<std::pair<int, int>> getMinimumRequiredRoads() {
        return this->allRequiredRoads;
    }
};

int main() {

    // fileIO();

    int numberOfCities, numberOfRoads;

    // The goal is to construct new roads so that there is a route between any two cities.
    // total of n cities, and m roads between them
    // find out the minimum number of roads required, and also determine which roads should be built
    cin >> numberOfCities >> numberOfRoads;

    int roads = numberOfRoads;
    std::vector<std::pair<int, int>> allRoads;

    while (roads--) {
        int from, to;
        cin >> from >> to;

        allRoads.push_back({from,
                            to});
    }

    City city = City();

    city.setNumberOfCities(numberOfCities);
    city.setNumberOfRoads(numberOfRoads);
    city.setRoads(allRoads);

    // given all the roads, find out what is the minimum number of new roads required
    // to connect the city up such that there is a route between any two cities.
    city.calculateMinimumRequiredRoads();

    std::vector<std::pair<int, int>> minimumRoadSet = city.getMinimumRequiredRoads();
    cout << minimumRoadSet.size() << endl;

    for (auto city_pair : minimumRoadSet) {
        cout << city_pair.first << " " << city_pair.second << endl;
    }

    return 0;
}
```