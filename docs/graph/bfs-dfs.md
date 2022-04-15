# Breath first and depth-first search
BFS and DFS are two of the most common graph as well as tree traversal techniques that ever existed. You should always understand the basic understanding behind these traversals.

## Things to remember about
- BFS uses a queue and FIFO ordering is something that it uses.
- DFS uses recursion, and recursively goes in-depth until the element is found.

## Standard Graph implementation
We'll use our standard implementation for graphs and will write BFS and DFS to work on that standard graph.

The following graph implementation may not be the very best implementation that you'll find, but it is good enough and most importantly you'll be able to implement this in an interview setting.

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

Now on top of this custom graph [adjacency list] representation, we'll implement BFS and DFS the 2 most common algorithms in graphs and trees ever.

## BFS with standard graph
Implementation follows the CLRS textbook for reference although is not a blind copy.
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

## DFS on standard Graph

Implementation of DFS is recursive, it recursively goes into the graph then backtracks once there is nowhere to go. But in this implementation, I'll strictly avoid recursion because the graph is a little hard to grasp, and doing recursion on a graph may seem $\text{BLACK MAGIC}$. Instead, I'll implement DFS using a stack data structure to mimic recursion. It's an iterative implementation that is much easier to grasp.

```cpp
vector<char> DFS(Graph &g, char startFromVertex){

    vector<char> stack;
    unordered_map<char, bool> visited;
    vector<char> dfsOrder;

    auto graphView = g.view();

    // initialize all the visited == false
    for (auto vertex:graphView)
        visited[vertex.first] = false;

    // push whatever with you are starting with
    stack.push_back(startFromVertex);

    while(!stack.empty()){
        char tos = stack.back();
        stack.pop_back();

        if (visited[tos] == false){
            // if this top of the stack is not visited then mark it
            // visited and push into the answer array [dfsOrder].
            visited[tos] = true;
            dfsOrder.push_back(tos);
        }

        for (auto adj_list:graphView[tos]){    // look at the adj_list of tos vertex
            if (visited[adj_list.first] == false){
                stack.push_back(adj_list.first);
            }
        }
    }

    return dfsOrder;
}
```