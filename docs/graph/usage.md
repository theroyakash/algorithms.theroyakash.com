# Graph Usage

The graph is one of the most common and important data structures. With C++ and STL I'll show you the best possible implementation for the graph that you'll be able to implement and analyze in your code at FAANG interviews within the time constraints.

## Graph Adjacency List vs Adjacency Matrix
Most of cases the List representation is good enough, if the graph is sparse then it will take less space, and if the graph is dense you should use the adjacency matrix representation.

In my opinion any graph with **less than 70% of the all possible edges**: $\text{Count(E)} \geq 0.7 * {n \choose 2}$ present can be considered to be implemented as adjacency list.

## C++ Graph representation
```cpp
#include <iostream>
#include <list>
#include <unordered_map>
#include <vector>
#include <utility>

using namespace std;

// Directed graph implementation
class Graph{ 
private:
    unordered_map<char, list<pair<char, int>>> adj_list;
    vector<pair<char, char>> E; // edge set
public:
    vector<pair<char, char>> edges(){
        return E;
    }

    void add_edge(char vertex1, char vertex2, int weight){
        adj_list[vertex1].push_front(make_pair(
            vertex2, weight
        ));
        
        E.push_back({vertex1, vertex2});
    }

    void register_vertex(vector<char> vertices){
        for (auto v:vertices){
            list<pair<char, int>> l;
            adj_list.insert({v, l});
        }
    }

    unordered_map<char, list<pair<char, int>>> view(){
        return adj_list;
    }
};
```

The following code shows how to make a graph and use it

```cpp
int main() {
    Graph g;
    vector<char> v = {'a', 'b', 'c'};
    g.register_vertex(v);
    g.add_edge('a', 'c', 32);
    g.add_edge('a', 'd', 2);
    g.add_edge('b', 'd', 12);
    g.add_edge('b', 'c', 98);
    g.add_edge('c', 'a', 1);
    
    unordered_map<char, list<pair<char, int>>> map = g.view();

    for (auto data:map){
        cout << data.first << " ";
        
        for (auto neighbor:data.second)
            cout << "[" << neighbor.first << ": " << neighbor.second << "]";

        cout << "\n";
    }

    // print all the edges
    auto edges = g.edges();
    for (auto edge:edges){
        cout << edge.first << "->" << edge.second << "\n";   
    }
}
```