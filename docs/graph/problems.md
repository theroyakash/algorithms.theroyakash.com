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

    void add_directed_edge(char from, char to) {
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
            adj_list.insert({
                i, l
            });
        }
    }

    void add_directed_edge(char from, char to){
        adj_list[from].push_front(to);
    }

    unordered_map<char, forward_list<char>> view(){
        return adj_list;
    }
};

// Driver Code
int main() {

    int number_of_vertices;
    vector<char> v;
    cin >> number_of_vertices;

    while (number_of_vertices){
        char vertex;
        cin >> vertex;
        v.push_back(vertex);
        number_of_vertices--;
    }

    Graph g = Graph(v);

    int number_of_edges;
    cin >> number_of_edges;

    while(number_of_edges){
        char from, to;
        cin >> from >> to;
        g.add_directed_edge(from, to);
        number_of_edges--;
    }

    auto view = g.view();

    for (auto vert:view){
        cout << vert.first << " -> ";
        for (auto nbr:vert.second)
            cout << nbr << " ";

        cout << endl;
    }

}
```