# Breath first and depth first search
BFS and DFS are two of the most common graph as well as tree traversal techniques that ever existed. You should always understand the basic understanding behind these traversals.

## Things to remember about
- BFS uses a queue and FIFO ordering is something that it uses.
- DFS uses recursion, recursivly goes in depth until the element is found.

## Standard Graph implementation
We'll use our own standard implementation for graph and will write BFS and DFS to work on that standard graph.

This following graph implementation may not be the very best implementation that you'll find, but it is good enough and most importantly you'll be able to implement this in an interview setting.

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
public:

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

Now on top of this custom graph [adjacency list] representation we'll implement BFS and DFS the 2 most common algorithms in graphs and trees ever.

### BFS Implementation
Implementation follows CLRS text book for reference although not a blind copy.
```cpp
#include <iostream>
#include <list>
#include <unordered_map>
#include <vector>
#include <utility>

#include <queue>

vector<char> BFS(Graph &g, char startFromVertex){

	// create a queue
	queue<char> q;

	// result order for the bfs
	vector<char> bfstree;

	// the graph as a map
	unordered_map<char, list<pair<char, int>>> graphView = g.view();

	// visited map
	unordered_map<char, bool> visited;
	for (auto vertex:graphView)
		visited[vertex.first] = false;

	q.push(startFromVertex); // startFromVertex must be in graph g


	while(!q.empty()){
		char vert = q.front();
		if (visited[vert] == false){
			// if we did not visit this vertex please process
			bfstree.push_back(vert);
			
			auto neighbors = graphView[vert];
			for (auto neighbor:neighbors){
				q.push(neighbor.first);
			}

			visited[vert] = true;
		}
		
		// now remove the vertex
		q.pop();
	}

	return bfstree;
}
```