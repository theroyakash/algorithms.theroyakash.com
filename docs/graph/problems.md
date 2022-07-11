# Graph standard problems

**Questions Discussed**

- [Number of Islands](#number-of-islands)
- [Clone Graph](#clone-graph)
- [Max Area of Island](#max-area-of-island)
- [Pacific Atlantic Water Flow](#pacific-atlantic-water-flow)
- [Surrounded Regions](#surrounded-regions)

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
            for(auto nbr:current->neighbors) {
                if (map.find(nbr) == map.end()) {
                    map[nbr] = new Node(nbr->val);
                    q.push(nbr);
                }
                
                // add the newly created neighbor on the map[current] neighbors
                map[current]->neighbors.push_back(map[nbr]);
            }
        }
        
        return map[node];
    }
};
```

## Max Area of Island
You are given an $M \times N$ binary matrix grid. An island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

The area of an island is the number of cells with a value 1 in the island.

Return the maximum area of an island in grid. If there is no island, return $0$.

### Examples
<figure markdown>
![iamge](../images/maxarea1-grid.jpeg){ width="400" }
</figure>

```
Input:
Upper image as a 2-d grid

Output: 6
Explanation: The answer is not 11, because the island must be connected 4-directionally.
```

```
Input: grid = [[0,0,0,0,0,0,0,0]]
Output: 0
```

### Approach
- We'll modify the previous problem [Number of Islands](#number-of-islands) to count the size of the island during the DFS instead of just doing nothing.
- This way we can find the size of the Islands during the DFS, we'll keep track of the max-size we've seen so far.

### Code
```cpp
class Solution {
private:
    int dfs(vector<vector<int>>& grid, int i, int j, int r, int c) {
        if (i < 0 or j <0 or i >=r or j >=c or grid[i][j] == 0) {
            return 0;
        }
        
        grid[i][j] = 0;
        
        return 1 + dfs(grid, i+1, j, r, c) + dfs(grid, i-1, j, r, c) + dfs(grid, i, j + 1, r, c) + dfs(grid, i, j - 1, r, c);
    }
    
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int maxSize = 0;
        int numberOfIsland = 0;
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        for (int i=0; i<rows; i++) {
            for (int j=0; j<cols; j++) {
                if (grid[i][j] == 1) {
                    int size = dfs(grid, i, j, rows, cols);
                    maxSize = std::max(maxSize, size);
                    numberOfIsland++;
                }
            }
        }
        
        if (numberOfIsland == 0) return 0;
        return maxSize;
    }
};
```

## Pacific Atlantic Water Flow
[Find the problem on Leetcode $\to$](https://leetcode.com/problems/pacific-atlantic-water-flow/)
### Problem Statement
There is an $m \times n$ rectangular island that borders both the Pacific Ocean and Atlantic Ocean. The Pacific Ocean touches the island's left and top edges, and the Atlantic Ocean touches the island's right and bottom edges.

The island is partitioned into a grid of square cells. You are given an $m \times n$ integer matrix heights where `heights[r][c]` represents the height above sea level of the cell at coordinate $(r, c)$.

The island receives a lot of rain, and the rain water can flow to neighboring cells directly north, south, east, and west if the neighboring cell's height is less than or equal to the current cell's height. Water can flow from any cell adjacent to an ocean into the ocean.

Return a 2D list of grid coordinates result where `result[i] = [ri, ci]` denotes that rain water can flow from cell $(r_i, c_i)$ to both the Pacific and Atlantic oceans.

### Example
<figure markdown>
![waterflow-grid](../images/waterflow-grid.jpeg)
</figure>

```
Input: heights = [[1,2,2,3,5],[3,2,3,4,4],[2,4,5,3,1],[6,7,1,4,5],[5,1,1,2,4]]
Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
```

```
Input: heights = [[2,1],[1,2]]
Output: [[0,0],[0,1],[1,0],[1,1]]
```

### Approach
- We'll use DFS to solve this problem with a reverse approach.
- First instead of searching from where the water can flow to ocean we'll check from ocean what is the **reverse** of the path through which the water is flowing.
- Naturally water flows from top height to bottom height, here we'll do the opposite where we'll check if the neighbors height is greater than the current height we'll travel (because we're searching the reverse of the waterflow's path),
- Now all columns of row $0$ and all the rows of column $0$ has the pacific water, we'll draw a DFS to find where the water can go using our customized DFS algorithm.
- Similarly we'll do this for the atlantic ocean.
- Now we'll check where the water from both can reach, we'll return that in the answers array.

### Code
```cpp
class Solution {
private:
    vector<vector<int>> flowCoordinates;
    
    void dfs(vector<vector<int>>& heights, vector<vector<bool>> &visited, int i, int j, int r, int c) {
        
        visited[i][j] = true;
        
        if (i>0 and not visited[i-1][j] and heights[i][j] <= heights[i-1][j]) {
            dfs(heights, visited, i-1,j,r,c);
        }
        
        if (i<r-1 and not visited[i+1][j] and heights[i][j] <= heights[i+1][j]) {
            dfs(heights, visited, i+1,j,r,c);
        }
        
        if (j>0 and heights[i][j] <= heights[i][j-1] and not visited[i][j-1]) {
            dfs(heights, visited, i,j-1,r,c);
        }
        
        if (j<c-1 and heights[i][j] <= heights[i][j+1] and not visited[i][j+1]) {
            dfs(heights, visited, i,j+1,r,c);
        }
    }
    
public:
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
        int rows = heights.size();
        int cols = heights[0].size();
        
        vector<vector<bool>> pacific(rows, vector<bool>(cols));
        vector<vector<bool>> atlantic(rows, vector<bool>(cols));
        
        for (int i=0;i<rows;i++) {
            dfs(heights, pacific, i, 0, rows, cols);
            dfs(heights, atlantic, i, cols-1, rows, cols);
        }
        
        for (int i=0; i<cols; i++) {
            dfs(heights, pacific, 0, i, rows, cols);
            dfs(heights, atlantic, rows-1, i, rows, cols);
        }
        
        // now check for some (i, j) both pacific and altantic shows true
        
        for (int i=0; i<rows; i++) {
            for (int j=0;j<cols;j++) {
                if (pacific[i][j] and atlantic[i][j]) {
                    flowCoordinates.push_back({i, j});
                }
            }
        }
        
        return flowCoordinates;
    }
};
```

## Surrounded Regions
[Find the problem on Leetcode $\to$](https://leetcode.com/problems/surrounded-regions/)
### Problem Statement
Given an m x n matrix board containing 'X' and 'O', capture all regions that are 4-directionally surrounded by 'X'.

A region is captured by flipping all 'O's into 'X's in that surrounded region.

### Example
<figure markdown>
![xocapturegrid](../images/xocapturegrid.jpeg){ width="400" }
</figure>

```
Input: 
board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
```
**Explanation:** Surrounded regions should not be on the border, which means that any 'O' on the border of the board are not flipped to 'X'. Any 'O' that is not on the border and it is not connected to an 'O' on the border will be flipped to 'X'. Two cells are connected if they are adjacent cells connected horizontally or vertically.

### Approach
- We'll use the approach from the previous problem. We'll do a quick reverse thinking, see we should not capture regions that are on the border so what we'll do is use a DFS to quickly capture the regions that surrounds the border with some other alphabet "N" let's say.
- These can be achieved by running DFS on the "O" at the row $0$, **last row** and column $0$ and at the **last column**.
- So we'll run dfs at all the positions of "O" at the boundaries,
- then we'll capture all the regions that are left out **"not in the border"**.
- Then we'll convert the "N"s into "O"s.

### Code
```cpp
class Solution {
private:
    void dfs(vector<vector<char>>& board, int i, int j, int r, int c) {

        board[i][j] = 'N';
        
        if (i>0 and board[i-1][j] == 'O') {
            dfs(board, i-1, j, r, c);
        }
        
        if (i<r-1 and board[i+1][j] == 'O') {
            dfs(board, i+1, j, r, c);
        }
        
        if (j>0 and board[i][j-1] == 'O') {
            dfs(board, i, j-1, r, c);
        }
        
        if (j<c-1 and board[i][j+1] == 'O') {
            dfs(board, i, j+1, r, c);
        }
    }
    
public:
    void solve(vector<vector<char>>& board) {
        // capture everything except an unsurrounded region
        // if some O is there at the row 0, n-1 and col 0, n-1
        // we'll not be able to capture it.
        
        // run a dfs on each of the area in row 0, n-1 and col 0, n-1
        // to not capture it and marking it as N
        
        int rows = board.size();
        int cols = board[0].size();
        
        // vector<vector<bool>> visited(rows, vector<bool>(cols));
        
        // run dfs at all the positions of O at the boundaries
        // col wise
        for (int i=0; i<rows; i++) {
            // 0th column
            if (board[i][0] == 'O') {
                dfs(board, i, 0, rows, cols);
            }
            
            // cols-1th column
            if (board[i][cols-1] == 'O') {
                dfs(board, i, cols-1, rows, cols);
            }
        }
        
        // run row wise at col = 0 and rows-1
        for (int j=1; j<cols-1; j++) {
            // 1st row because 0,0 is already done
            if (board[0][j] == 'O') {
                dfs(board, 0, j, rows, cols);
            }
            
            if (board[rows-1][j] == 'O') {
                dfs(board, rows-1, j, rows, cols);
            }
        }
        
        for (int i=0; i<rows; i++) {
            for (int j=0; j<cols; j++) {
                if (board[i][j] == 'O') board[i][j] = 'X';
                if (board[i][j] == 'N') board[i][j] = 'O';
            }
        }
    }
};
```