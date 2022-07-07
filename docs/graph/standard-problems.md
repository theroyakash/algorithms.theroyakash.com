# More Graph Problems

## Cycle Detection in Undirected Graph
Using simple traversal techniques we can detect cycles in undirected graphs. Here first we'll implement this using breath first search then we'll also show how to do this using depth first search.
```cpp
// UNDIRECTED GRAPH DEFINITION
class Graph {
private:
    unordered_map<char, forward_list<char>> adj_list;

public:
    Graph(vector<char> vertexSet) {
        for (auto i : vertexSet) {
            forward_list<char> l;
            adj_list.insert({i, l});
        }
    }

    void add_edge(char from, char to) {
        // undirected graph
        adj_list[from].push_front(to);
        adj_list[to].push_front(from);
    }

    unordered_map<char, forward_list<char>> view() {
        return adj_list;
    }
};
```

### Approach using breath first search

- The apporach is very simple, if during traversal if you find any one of the neighbor of some vertex previously visited other than the immediate parent/neighbor means there is a cycle.
- So put the vertex and it's immediate parent inside a Queue (to run BFS).

```cpp
bool CYCLE_DETECTABLE_SEARCH(Graph &g, char startingVertex) {
    // For BFS we need a queue and this queue has vertex and it's
    // immediate parent as a pair<char, char>
    queue<pair<char, char>> q;

    unordered_map<char, bool> visited;
    auto graphView = g.view();

    for (auto vertexName : graphView)
        visited[vertexName.first] = false;

    // for starting vertex parent = 'N' for NULL;
    q.push({startingVertex, 'N'});
    visited[startingVertex] = true;

    while (!q.empty()) {
        pair<char, char> front = q.front();
        q.pop();
        char thisvertex = front.first;
        char parent = front.second;

        // for all the neighbors for thisvertex insert into queue
        // mark thisvertex as their immediate parents
        // if we see some visited vertex other than it's immediate neighbor/parent
        // return true

        for (char nbr : graphView[thisvertex]) {
            if (visited[nbr] == false) {
                visited[nbr] = true;
                q.push({nbr, thisvertex});
            } else if (visited[nbr] == true and nbr != parent) {
                // this neighbor of the vertex is already visited
                // and this neighbor is not the immediate parent
                // meaning there must be a cycle.
                return true;
            }
        }
    }

    return false;
}

bool hasCycle(Graph &g) {
    // assuming multiple connected components are there
    unordered_map<char, bool> visited;
    auto graphView = g.view();

    for (auto vertexName : graphView)
        visited[vertexName.first] = false;

    bool hasCycleForThisComponent;

    for (auto vertex : graphView) {
        if (!visited[vertex.first]) {
            hasCycleForThisComponent = CYCLE_DETECTABLE_SEARCH(g, vertex.first);
        }

        if (hasCycleForThisComponent) {
            return true;
        }
    }

    return false;
}
```

## Bipartite Graph Check
### Problem Statement
Given a graph G find out if this is a bipartite graph? For a bipartite graph, the graph has 2 set of vertices, in each set vertices are not connected to each other.
### Approach
- Bi-partite graph is a 2 colorable graph, one approach could be if we check a graph if it's 2 colorable then it's bipartite. Bipartite is equivalent to 2-colorable. Here we'll traverse through the graph and color every vertex. Now if we find some vertex that takes other than the 2 color considered first we'll say it is not bipartite. Else if the entire graph is 2 colorable then we'll return true.
- Also we can approach this problem as this $\to$ any odd length cycle containing graph is not a bipartite graph.

```cpp
// Any 2 Colorable graph is a bipartite graph Approach is much simpler to go through.
enum class Color { black,
                   red,
                   gray }; // gray is to indicate that the vertex is not visted yet

bool isBipartite(Graph &g) {
    // set all the graph vertex color as gray
    auto graph = g.view();
    unordered_map<char, Color> color;

    for (auto vertex : graph) {
        color[vertex.first] = Color::gray;
    }

    // set a starting index where to start the journey
    char startingVertex = graph.begin()->first;

    // create a queue for bfs
    queue<char> q;
    q.push(startingVertex);
    color[startingVertex] = Color::red;

    // run a bfs
    while (!q.empty()) {
        char thisvertex = q.front();
        q.pop();

        for (char nbr : graph[thisvertex]) {

            if (color[nbr] == Color::gray) {
                // if not visited then only process
                if (color[thisvertex] == Color::red) {
                    color[nbr] = Color::black;
                } else if (color[thisvertex] == Color::black) {
                    color[nbr] = Color::red;
                }

                // push into the queue for bfs
                q.push(nbr);

            } else {
                // check for termination condition
                if (color[nbr] == color[thisvertex]) {
                    return false;
                }
            }
        }
    }

    return true;
}
```

## Directed Graph Problems
In this section the given graph has directed edges, below are some standard directed graph problems that is the building block for many other graph problems in general.

- Topological Sort Problem
- Cycle Detection
- Strongly connected components (Kosaraju's algorithm).

### Topological Sorting of a Directed graph
#### Problem Statement
Topological sorting for Directed Acyclic Graph is a linear ordering of vertices such that for every directed $(u, v) \in \mathbf{V}$, $u$ comes before $v$. In other words in order to reach $v$ you must go through $u$.

#### Approach
- In order to start our topological sorting we must indentify a node that has no dependencies. We'll start from one of such nodes. This can be automated with the algorithm or can be relied on the user to give as an input.
- In this implementation we'll assume no such event. User will only give the Graph `G` and we'll find some node to start on on our own.


```cpp
// Directed Graph Varient
#include <iostream>
#include <unordered_map>
#include <forward_list>
#include <vector>

using namespace std;

class Graph{
private:
    unordered_map<char, forward_list<char>> adj_list;

public:
    Graph(vector<char> vertexSet){
        for (auto i:vertexSet){
            forward_list<char> l;
            adj_list.insert({i, l});
        }
    }

    void add_directed_edge(char from, char to){
        adj_list[from].push_front(to);
    }

    unordered_map<char, forward_list<char>> view(){
        return adj_list;
    }
};
```

## Minimum spanning tree problem
### [Minimum Cost to Connect all the points](https://leetcode.com/problems/min-cost-to-connect-all-points/)

we are given an array points representing integer coordinates of some points on a 2D-plane, where $\text{points[i]} = (x_i,y_i)$. The cost of connecting two points $(x_i,y_i)$ and $(x_j,y_j)$ is the manhattan distance between them: $\lvert x_i - x_j \rvert + \lvert y_i - y_j \rvert$. 

Return the minimum cost to make all points connected. All points are connected if there is exactly one simple path between any two points.

