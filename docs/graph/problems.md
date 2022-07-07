# Graph standard problems

**Questions Discussed**
- [Number of Islands](#number-of-islands)
- [Clone Graph](#clone-graph)

## Number of Islands
[Find the problem on leetcode $\to$](https://leetcode.com/problems/number-of-islands/)
### Problem Statement
Given an m x n 2D binary grid grid which represents a map of '1's (land) and '0's (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

### Example
```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

### Approach
We'll use a simple DFS apporach to find the number of islands. The appoach is to when we touch a landmass, we'll mark all the connected landmass as '0' using DFS and increase the number of islands. Then travel through the space, until we find another landmass.

This way only new landmasses are considered in the number of islands.

**Here** is a code sample that uses DFS to find the number of islands.

### Code
```cpp
class Solution {
private:
    void dfs(vector<vector<char>> &grid, int i, int j, int r, int c) {
        if (i< 0 || i >= r || j<0 || j >= c || grid[i][j] == '0') {
            // if the dfs subroutine goes out of bounds,
            // or we find water during the traversal to find landmasses
            // we return
            return;
        }
        
        // if we find a grid[i][j] == "1" means this is a part of
        // the island so we set this position as "0" so that we
        // don't revisit this again
        grid[i][j] = '0';
        
        // run recursive dfs on all directions
        dfs(grid, i-1, j, r, c);
        dfs(grid, i+1, j, r, c);
        dfs(grid, i, j-1, r, c);
        dfs(grid, i, j+1, r, c);
    }

public:
    int numIslands(vector<vector<char>>& grid) {
        int islandCount = 0;
        int rows = grid.size();
        int cols = grid[0].size();
        
        for (int i=0; i<rows; i++) {
            for (int j=0; j<cols; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j, rows, cols);
                    islandCount++;
                }
            }
        }
        
        return islandCount;
    }
};
```

## Clone Graph
[Find the Problem on Leetcode $\to$](https://leetcode.com/problems/clone-graph/)
### Problem Statement
Given a reference of a node in a connected undirected graph. Return a deep copy (clone) of the graph.

### Approach
- We'll create a `unordered_map` to store the mapping between new and old nodes in the graph.
- Then we'll run a BFS algorithm on the graph to find new nodes and connect them to their corrospoding neighbors.

**The following** is a working code example for this apporach.

### Code
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/

class Solution {
public:
    Node* cloneGraph(Node* node) {
        if (not node) return nullptr;
        
        unordered_map<Node*, Node*> map;
        
        // create a node corrospoding to the starting node
        map[node] = new Node(node->val);
        
        queue<Node*> q;
        q.push(node);
        
        while (!q.empty()) {
            Node* current = q.front();
            q.pop();
            
            // for all neighbor of current check if we visited that or not?
            // if we don't visit we enter it in the map
            for (int i=0; i< current->neighbors.size(); i++) {
                auto neighbor = current->neighbors[i];
                
                if (map.find(neighbor) == map.end()) {
                    map[neighbor] = new Node(neighbor->val);
                    q.push(neighbor);
                }
                
                map[current]->neighbors.push_back(map[neighbor]);
            }
        }
        
        return map[node];
    }
};
```