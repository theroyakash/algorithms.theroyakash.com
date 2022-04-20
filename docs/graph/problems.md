# More Graph Problems
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

# Driver Code
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