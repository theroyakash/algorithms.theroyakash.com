# CSES Problem Set
The CSES Problem Set contains a collection of algorithm programming practice problems. These problems are identified to be one of the best set of problems to build algorithmic thinking in the CP community. This can be one of the best resources for learning algorithmic thinking. 

**Below** are few solutions for the **CSES problem set**. Find the CSES problem set [here $\to$](https://cses.fi/problemset/)

**Problems and solution list**

- [Download Speed](#download-speed)

## [Download Speed](https://cses.fi/problemset/task/1694)
Consider a network consisting of n computers and m connections. Each connection specifies how fast a computer can send data to another computer. Kotivalo wants to download some data from a server. What is the maximum speed he can do this, using the connections in the network?

Please see [here $\to$](https://cses.fi/problemset/task/1694/) for full explaination of the problem and the input and output structure.

### Example I/O
```
Input:
4 5
1 2 3
2 4 2
1 3 4
3 4 5
4 1 3

Output:
6
```

### Approach
Use Edmond-Karp algorithm for finding max-flow in the flow-network. Make the internet mesh as a flow network and make source set to $1$ and destination set to $n$. Then run Edmond-Karp algorithm to find the maximum flow. This maximum flow is the maximum download speed possible.

### Code
```cpp
#include <climits>
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

class Graph {
public:
    vector<vector<int>> adj_list;
    vector<vector<long long int>> capacity;
    int source;
    int sink;

    int getSink() {
        return sink;
    }

    void setSink(int id) {
        sink = id;
    }

    int getSource() {
        return source;
    }

    void setSource(int id) {
        source = id;
    }

    Graph(int n) {
        for (int i = 0; i < n; i++) {
            vector<int> v;
            adj_list.push_back(v);

            vector<long long int> caprow(n, 0);
            capacity.push_back(caprow);
        }
    }

    void add_directed_edge(int from, int to, long long int cap) {
        adj_list[from].push_back(to);
        capacity[from][to] += cap;
    }

    long long int bfs(vector<int> &parent) {
        // find an augmenting path from s->t and return the path in
        // parent vector
        std::fill(parent.begin(), parent.end(), -1);
        parent[source] = -2;

        queue<pair<int, long long int>> q;
        q.push({source, LONG_MAX});

        while (not q.empty()) {
            int front = q.front().first;
            long long int flow = q.front().second;

            q.pop();

            for (int nbr : adj_list[front]) {
                // if there is no parent discovered before and there exists a flow
                if (parent[nbr] == -1 and capacity[front][nbr]) {
                    // then set parent and add it to augmenting path
                    parent[nbr] = front;
                    long long int new_flow = std::min(flow, capacity[front][nbr]);
                    if (nbr == sink)
                        return new_flow;

                    // otherwise push onto queue with updated flow
                    q.push({nbr, new_flow});
                }
            }
        }

        // there exists no s->t path so return 0 flow
        return 0;
    }
 
    long long int findMaxFlow() {
        long long int flow = 0;
        vector<int> parent(adj_list.size(), 0);
        long long int newflow = 0;

        // backtrace to s via the parent array and use the
        // augmenting path to augment the flow

        while (true) {
            newflow = bfs(parent);
            if (not newflow)
                break;

            int current = sink;
            flow += newflow;

            // otherwise through the augmenting path do the flow
            while (current != source) {
                int prev = parent[current];
                // augment the flow
                capacity[prev][current] -= newflow;
                capacity[current][prev] += newflow;

                current = prev;
            }
        }

        return flow;
    }
};

int main() {
    int vertex, edges;
    cin >> vertex >> edges;
    Graph g(vertex + 1);

    g.setSource(1);
    g.setSink(vertex);

    while (edges--) {
        int from, to;
        long long int capacity;
        cin >> from >> to >> capacity;

        g.add_directed_edge(from, to, capacity);
        g.add_directed_edge(to,  from, 0);
    }

    long long int maxflow = g.findMaxFlow();

    cout << maxflow << endl;
}
```