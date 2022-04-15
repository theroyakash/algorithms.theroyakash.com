# Taking data in and writing data out to a file
In some of the problems in SDE 1 and SDE 2 online coding rounds, you have to take care of the program inputs.

Here is an example of how we can write all inputs to a text file and then run our algorithms.

You must add 2 files called "input.txt" and "output.txt" in the same directory and add these 2 lines of code in the `main()`

```cpp
freopen("input.txt", "r", stdin);
freopen("output.txt", "w", stdout);
```

### Input
Now in the input.txt file, you design as the inputs are given in the following case the input was

```
3
a
b
c
4
a b 12
c b 2
b a 99
c a 8
```

### Output
The corresponding output file is written as
```
c [a: 8][b: 2]
b [a: 99]
a [b: 12]

a->b
c->b
b->a
c->a
```

## Code

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


int main() {
    
    freopen("input.txt", "r", stdin);
    freopen("output.txt", "w", stdout);

    Graph g;

    int vertex;
    cin >> vertex;

    vector<char> v;

    while (vertex != 0){
        char vertex_label;
        cin >> vertex_label;
        v.push_back(vertex_label);
        vertex--;
    }

    g.register_vertex(v);

    int numberofedges;

    cin >> numberofedges;

    while(numberofedges != 0){
        char from, to;
        int weight;

        cin >> from >> to >> weight;
        g.add_edge(from, to, weight);
        numberofedges--;
    }

    unordered_map<char, list<pair<char, int>>> map = g.view();

    for (auto data:map){
        cout << data.first << " ";
        
        for (auto neighbor:data.second)
            cout << "[" << neighbor.first << ": " << neighbor.second << "]";

        cout << "\n";
    }

    cout << "\n";

    // print all the edges
    auto edges = g.edges();
    for (auto edge:edges){
        cout << edge.first << "->" << edge.second << "\n";
    }

    cout << "\n";
}

```