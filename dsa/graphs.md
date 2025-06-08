# graphs


## no. of islands

> Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return the number of islands.
>
> An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

- we iterate over all entries in the grid
- if the current square is a piece of land, perform bfs on that piece of land
    - increment island counter and mark all connecting pieces of land as visited

initial naive approach:
```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        seen = set()
        ret = 0

        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if (i, j) in seen:
                    continue

                if grid[i][j] == "0":
                    seen.add((i, j))
                    continue

                # bfs on this current piece of land
                q = deque()
                q.append((i, j))
                ret += 1

                while q:
                    cur = q.popleft()
                    if cur in seen or cur[0] < 0 or cur[0] >= len(grid) or cur[1] < 0 or cur[1] >= len(grid[0]):
                        continue

                    seen.add(cur)
                    if grid[cur[0]][cur[1]] == "1":
                        q.append((cur[0], cur[1] + 1))
                        q.append((cur[0], cur[1] - 1))
                        q.append((cur[0] + 1, cur[1]))
                        q.append((cur[0] - 1, cur[1]))

        return ret
```

- inefficient due to `seen` set and redundant checks

more optimised solution:
```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        ret = 0

        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if grid[i][j] == "0":
                    continue

                # bfs on this current piece of land
                q = deque()
                q.append((i, j))
                ret += 1

                # mark entries as seen in place
                grid[i][j] = "0"
                while q:
                    row, col = q.popleft()
                    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]

                    for d in directions:
                        # check before adding to queue to avoid redundant queue space and inefficient checks later
                        if 0 <= row + d[0] < len(grid) and 0 <= col + d[1] < len(grid[0]) and grid[row + d[0]][col + d[1]] == "1":
                            grid[row + d[0]][col + d[1]] = "0" # IMPORTANT: mark as seen when adding to queue rather than when popping, avoids duplicate entries in queue
                            q.append((row + d[0], col + d[1]))

        return ret
```

---

## clone graph

> Given a reference of a node in a connected undirected graph.
>
> Return a deep copy (clone) of the graph.
>
> Each node in the graph contains a value (int) and a list (List[Node]) of its neighbors.

- use a hashmap to map original nodes to their clones
- any traversal should be fine

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val = 0, neighbors = None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []
"""

from typing import Optional
class Solution:
    def cloneGraph(self, node: Optional['Node']) -> Optional['Node']:
        if node is None:
            return None

        q = deque()
        new = Node(node.val, [])
        q.append(node)

        d = {node: new} # map original nodes to clones

        while q:
            cur = q.popleft()
            newnode = d[cur]

            for n in cur.neighbors:
                if n in d:
                    neighbor = d[n]
                else:
                    neighbor = Node(n.val, [])
                    d[n] = neighbor
                    q.append(n)

                newnode.neighbors.append(neighbor)

        return new
```

---

## pacific atlantic water flow

> There is an `m x n` rectangular island that borders both the Pacific Ocean and Atlantic Ocean. The Pacific Ocean touches the island's left and top edges, and the Atlantic Ocean touches the island's right and bottom edges.
>
> The island is partitioned into a grid of square cells. You are given an `m x n` integer matrix heights where `heights[r][c]` represents the height above sea level of the cell at coordinate `(r, c)`.
>
> The island receives a lot of rain, and the rain water can flow to neighboring cells directly north, south, east, and west if the neighboring cell's height is less than or equal to the current cell's height. Water can flow from any cell adjacent to an ocean into the ocean.
>
> Return a 2D list of grid coordinates result where `result[i] = [ri, ci]` denotes that rain water can flow from cell `(ri, ci)` to both the Pacific and Atlantic oceans.

- work inwards from the oceans, using any traversal
    - note that water can flow from a cell where there is an adjacent cell that water can flow from with a smaller or equal height
- use 2 hashsets, to track cells that can reach either ocean

```python
class Solution:
    def pacificAtlantic(self, heights: List[List[int]]) -> List[List[int]]:
        rows, cols = len(heights), len(heights[0])
        pac, atl = set(), set()

        def dfs(r, c, visit, prevh):
            if ((r, c) in visit or r < 0 or c < 0 or r >= rows or c >= cols or heights[r][c] < prevh):
                return

            visit.add((r,c))

            directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
            for d in directions:
                dfs(r + d[0], c + d[1], visit, heights[r][c])

        for c in range(cols):
            dfs(0, c, pac, 0)
            dfs(rows - 1, c, atl, 0)

        for r in range(rows):
            dfs(r, 0, pac, 0)
            dfs(r, cols - 1, atl, 0)

        return [[x, y] for x, y in pac.intersection(atl)]
```

---

## course schedule

> There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array prerequisites where `prerequisites[i] = [ai, bi]` indicates that you must take course `bi` first if you want to take course `ai`.
>
> For example, the pair `[0, 1]`, indicates that to take course 0 you have to first take course `1`.
> Return `true` if you can finish all courses. Otherwise, return `false`.

- treat each course as a graph vertex, then a prerequisite dependency defines a directed edge
- problem then boils down to whether there is a cycle or not
- we must distinguish between vertices we have *previously seen* and vertices that are part of the path being currently explored
    - finding a previously seen vertex is not necessarily implicit of a circular dependency, e.g. 2 courses depend on another, will lead to finding the dependency twice

```python
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        if not prerequisites:
            return True

        # make a hashmap representing edges
        edges = {}
        for p in prerequisites:
            prev = edges.get(p[0], [])
            prev.append(p[1])
            edges[p[0]] = prev

        visiting = set()
        visited = set() # we can also modify the graph in place to save space

        def dfs(v):
            if v not in edges or not edges[v]:
                return True

            visiting.add(v)
            ret = True
            for a in edges[v]:
                if a not in visiting and a not in visited:    
                    ret &= dfs(a)
                elif a in visiting:
                    return False

            visiting.remove(v)
            visited.add(v)
            return ret

        ret = True

        # handle when the graph is not connected
        for p in range(numCourses):
            if p not in visited:
                ret &= dfs(p)

        return ret
```

---

## graph valid tree

> Given n nodes labeled from `0` to `n - 1` and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

- essentially we must check for cycles
- for the hashmap of edge relations, store both sides of the relationship to save time

```python
class Solution:
    def validTree(self, n: int, edges: List[List[int]]) -> bool:
        if not edges:
            return True

        seen = set()
        adjmap = {i: [] for i in range(n)}

        for u, v in edges:
            adjmap[u].append(v)
            adjmap[v].append(u)

        def dfs(v, parent):
            if v in seen:
                return False

            ret = True
            seen.add(v)
            for a in adjmap[v]:
                if a != parent:
                    ret &= dfs(a, v)

            return ret

        res = dfs(edges[0][0], None)
        return res and len(seen) == n
```

## count components

> There is an undirected graph with `n` nodes. There is also an `edges` array, where `edges[i] = [a, b]` means that there is an edge between node `a` and node `b` in the graph.
>
> The nodes are numbered from `0` to `n - 1`.
>
> Return the total number of connected components in that graph.

```python
class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        adjmap = {i: [] for i in range(n)}

        for u, v in edges:
            adjmap[u].append(v)
            adjmap[v].append(u)

        seen = set()
        def dfs(v, parent):
            for a in adjmap[v]:
                if a not in seen:
                    seen.add(a)
                    dfs(a, v)

        ret = 0
        for i in range(n):
            if i not in seen:
                ret += 1
                dfs(i, None)

        return ret
```
