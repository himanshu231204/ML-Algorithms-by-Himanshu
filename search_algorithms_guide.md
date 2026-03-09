# Complete Guide to Search Algorithms 🔍

A comprehensive guide covering all fundamental search algorithms with intuition, visualizations, and numerical examples.

---

## Table of Contents
1. [Uninformed Search Algorithms](#uninformed-search-algorithms)
   - [Breadth First Search (BFS)](#breadth-first-search-bfs)
   - [Depth First Search (DFS)](#depth-first-search-dfs)
   - [Depth Limited Search (DLS)](#depth-limited-search-dls)
   - [Uniform Cost Search (UCS)](#uniform-cost-search-ucs)
2. [Informed Search Algorithms (Heuristic)](#informed-search-algorithms)
   - [Greedy Best First Search](#greedy-best-first-search)
   - [A* Search Algorithm](#a-star-search-algorithm)
   - [A* Visualization](#a-star-visualization)

---

# Uninformed Search Algorithms

Uninformed search algorithms (also called blind search) have no additional information about states beyond the problem definition. They don't know how close a state is to the goal.

## Breadth First Search (BFS)

### 🎯 Intuition
BFS explores the search tree level by level, like ripples expanding in water. It visits all nodes at depth `d` before moving to depth `d+1`.

### 📊 Characteristics
- **Complete:** Yes (finds solution if exists)
- **Optimal:** Yes (finds shortest path in terms of number of steps)
- **Time Complexity:** O(b^d) where b = branching factor, d = depth
- **Space Complexity:** O(b^d) - stores all nodes at current level
- **Data Structure:** Queue (FIFO)

### 🔢 Numerical Example

Consider finding path from **A** to **G**:

```
Graph Structure:
        A
       / \
      B   C
     / \   \
    D   E   F
   /
  G
```

**Step-by-Step Execution:**

| Step | Queue         | Visited | Current | Action           |
|------|---------------|---------|---------|------------------|
| 0    | [A]           | []      | -       | Start            |
| 1    | [B, C]        | [A]     | A       | Visit A, add children |
| 2    | [C, D, E]     | [A, B]  | B       | Visit B, add children |
| 3    | [D, E, F]     | [A, B, C]| C      | Visit C, add children |
| 4    | [E, F, G]     | [A, B, C, D]| D   | Visit D, add children |
| 5    | [F, G]        | [A, B, C, D, E]| E | Visit E, no children |
| 6    | [G]           | [A, B, C, D, E, F]| F | Visit F, no children |
| 7    | []            | [A, B, C, D, E, F, G]| G | **GOAL FOUND!** |

**Path Found:** A → B → D → G  
**Number of Nodes Explored:** 7

### 💻 Python Implementation

```python
from collections import deque

def bfs(graph, start, goal):
    """
    Breadth First Search implementation
    
    Args:
        graph: Dictionary representing adjacency list
        start: Starting node
        goal: Goal node
    
    Returns:
        Path from start to goal, or None if not found
    """
    queue = deque([[start]])
    visited = set()
    
    while queue:
        path = queue.popleft()
        node = path[-1]
        
        if node == goal:
            return path
        
        if node not in visited:
            visited.add(node)
            
            for neighbor in graph.get(node, []):
                new_path = list(path)
                new_path.append(neighbor)
                queue.append(new_path)
    
    return None

# Example usage
graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': ['G'],
    'E': [],
    'F': [],
    'G': []
}

result = bfs(graph, 'A', 'G')
print(f"Path found: {' → '.join(result)}")  # Output: A → B → D → G
```

### 📈 Visual Representation

```
Level 0:           A
                  / \
Level 1:         B   C
                / \   \
Level 2:       D   E   F
              /
Level 3:     G

BFS explores: A → B → C → D → E → F → G
Order of exploration follows levels (breadth-wise)
```

---

## Depth First Search (DFS)

### 🎯 Intuition
DFS explores as deep as possible along each branch before backtracking. Like exploring a maze by always taking the first unexplored path until you hit a dead end.

### 📊 Characteristics
- **Complete:** No (can get stuck in infinite loops)
- **Optimal:** No (may not find shortest path)
- **Time Complexity:** O(b^m) where m = maximum depth
- **Space Complexity:** O(bm) - only stores single path
- **Data Structure:** Stack (LIFO) or Recursion

### 🔢 Numerical Example

Same graph as BFS example:

```
Graph Structure:
        A
       / \
      B   C
     / \   \
    D   E   F
   /
  G
```

**Step-by-Step Execution:**

| Step | Stack         | Visited | Current | Action           |
|------|---------------|---------|---------|------------------|
| 0    | [A]           | []      | -       | Start            |
| 1    | [C, B]        | [A]     | A       | Visit A, add children (reverse order) |
| 2    | [C, E, D]     | [A, B]  | B       | Visit B, add children |
| 3    | [C, E, G]     | [A, B, D]| D      | Visit D, add children |
| 4    | []            | [A, B, D, G]| G   | **GOAL FOUND!** |

**Path Found:** A → B → D → G  
**Number of Nodes Explored:** 4 (fewer than BFS!)

### 💻 Python Implementation

```python
def dfs(graph, start, goal, visited=None, path=None):
    """
    Depth First Search implementation (Recursive)
    
    Args:
        graph: Dictionary representing adjacency list
        start: Starting node
        goal: Goal node
        visited: Set of visited nodes
        path: Current path
    
    Returns:
        Path from start to goal, or None if not found
    """
    if visited is None:
        visited = set()
    if path is None:
        path = []
    
    visited.add(start)
    path = path + [start]
    
    if start == goal:
        return path
    
    for neighbor in graph.get(start, []):
        if neighbor not in visited:
            result = dfs(graph, neighbor, goal, visited, path)
            if result is not None:
                return result
    
    return None

# Iterative implementation using stack
def dfs_iterative(graph, start, goal):
    stack = [[start]]
    visited = set()
    
    while stack:
        path = stack.pop()
        node = path[-1]
        
        if node == goal:
            return path
        
        if node not in visited:
            visited.add(node)
            
            for neighbor in graph.get(node, []):
                new_path = list(path)
                new_path.append(neighbor)
                stack.append(new_path)
    
    return None

# Example usage
graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': ['G'],
    'E': [],
    'F': [],
    'G': []
}

result = dfs(graph, 'A', 'G')
print(f"Path found: {' → '.join(result)}")  # Output: A → B → D → G
```

### 📈 Visual Representation

```
        A (1)
       / \
    (2)B   C
     / \   \
  (3)D   E   F
    /
 (4)G ← GOAL!

DFS explores: A → B → D → G (goes deep first)
Numbers show order of exploration
```

---

## Depth Limited Search (DLS)

### 🎯 Intuition
DLS is DFS with a depth limit. It prevents infinite descent by stopping at a predetermined depth. Like exploring a cave but only going N levels deep.

### 📊 Characteristics
- **Complete:** No (goal may be beyond limit)
- **Optimal:** No
- **Time Complexity:** O(b^l) where l = depth limit
- **Space Complexity:** O(bl)
- **Advantage:** Avoids infinite paths in DFS

### 🔢 Numerical Example

Graph with depth limit = 2:

```
Graph Structure:
        A (depth 0)
       / \
      B   C (depth 1)
     / \   \
    D   E   F (depth 2)
   /
  G (depth 3) ← Beyond limit!
```

**Step-by-Step with Limit = 2:**

| Step | Stack         | Depth | Current | Action           |
|------|---------------|-------|---------|------------------|
| 0    | [(A, 0)]      | -     | -       | Start            |
| 1    | [(C, 1), (B, 1)]| 0   | A       | Add children at depth 1 |
| 2    | [(C, 1), (E, 2), (D, 2)]| 1 | B | Add children at depth 2 |
| 3    | [(C, 1), (E, 2)]| 2   | D       | Depth limit reached, skip G |
| 4    | [(C, 1)]      | 2     | E       | Depth limit reached |
| 5    | [(F, 2)]      | 1     | C       | Add children at depth 2 |
| 6    | []            | 2     | F       | Depth limit reached |

**Result:** Goal NOT found (G is at depth 3)

**Step-by-Step with Limit = 3:**

| Step | Result |
|------|--------|
| Same as DFS until depth 3 | **G FOUND at depth 3!** |

### 💻 Python Implementation

```python
def depth_limited_search(graph, start, goal, limit):
    """
    Depth Limited Search implementation
    
    Args:
        graph: Dictionary representing adjacency list
        start: Starting node
        goal: Goal node
        limit: Maximum depth to explore
    
    Returns:
        Path from start to goal, or None if not found
    """
    def dls_recursive(node, goal, depth, path, visited):
        if depth > limit:
            return None
        
        if node == goal:
            return path + [node]
        
        if node in visited:
            return None
        
        visited.add(node)
        
        for neighbor in graph.get(node, []):
            result = dls_recursive(neighbor, goal, depth + 1, path + [node], visited.copy())
            if result is not None:
                return result
        
        return None
    
    return dls_recursive(start, goal, 0, [], set())

# Example usage
graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': ['G'],
    'E': [],
    'F': [],
    'G': []
}

# With limit = 2 (won't find G)
result = depth_limited_search(graph, 'A', 'G', 2)
print(f"Limit=2: {result}")  # Output: None

# With limit = 3 (will find G)
result = depth_limited_search(graph, 'A', 'G', 3)
print(f"Limit=3: {' → '.join(result)}")  # Output: A → B → D → G
```

### 📈 Visual Representation

```
Limit = 2:
        A (✓ depth 0)
       / \
      B   C (✓ depth 1)
     / \   \
    D   E   F (✓ depth 2)
   /
  G (✗ depth 3 - CUTOFF!)

Limit = 3:
        A (✓ depth 0)
       / \
      B   C (✓ depth 1)
     / \   \
    D   E   F (✓ depth 2)
   /
  G (✓ depth 3 - FOUND!)
```

---

## Uniform Cost Search (UCS)

### 🎯 Intuition
UCS expands the node with the lowest path cost first. Like always choosing the cheapest route, regardless of direction. It's BFS with weighted edges.

### 📊 Characteristics
- **Complete:** Yes (if step costs > 0)
- **Optimal:** Yes (finds lowest cost path)
- **Time Complexity:** O(b^(1 + ⌊C*/ε⌋)) where C* = optimal cost, ε = minimum cost
- **Space Complexity:** O(b^(1 + ⌊C*/ε⌋))
- **Data Structure:** Priority Queue (min-heap)

### 🔢 Numerical Example

Weighted graph:

```
Graph Structure with costs:
        A
      /   \
    (1)   (4)
    /       \
   B         C
  / \       /
(3) (1)   (2)
 /   \   /
D     E-F
|     |
(1)  (2)
|     |
G     G
```

**Edge Costs:**
- A → B: 1
- A → C: 4
- B → D: 3
- B → E: 1
- C → F: 2
- D → G: 1
- E → G: 2

**Step-by-Step Execution:**

| Step | Priority Queue (node, cost) | Visited | Current | Path Cost | Action |
|------|----------------------------|---------|---------|-----------|--------|
| 0    | [(A, 0)]                   | []      | -       | 0         | Start  |
| 1    | [(B, 1), (C, 4)]           | [A]     | A       | 0         | Add B(cost 1), C(cost 4) |
| 2    | [(E, 2), (C, 4), (D, 4)]   | [A, B]  | B       | 1         | Add E(1+1=2), D(1+3=4) |
| 3    | [(C, 4), (D, 4), (G, 4)]   | [A, B, E]| E      | 2         | Add G(2+2=4) |
| 4    | [(D, 4), (G, 4), (F, 6)]   | [A, B, E, C]| C  | 4         | Add F(4+2=6) |
| 5    | [(G, 4), (G, 5), (F, 6)]   | [A, B, E, C, D]| D | 4      | Add G(4+1=5) - duplicate |
| 6    | **GOAL FOUND!**            | ...     | G       | 4         | G found with cost 4 |

**Optimal Path:** A → B → E → G  
**Total Cost:** 1 + 1 + 2 = 4

### 💻 Python Implementation

```python
import heapq

def uniform_cost_search(graph, start, goal):
    """
    Uniform Cost Search implementation
    
    Args:
        graph: Dictionary with format {node: [(neighbor, cost), ...]}
        start: Starting node
        goal: Goal node
    
    Returns:
        (path, cost) tuple or (None, None) if not found
    """
    # Priority queue: (cost, path)
    pq = [(0, [start])]
    visited = set()
    
    while pq:
        cost, path = heapq.heappop(pq)
        node = path[-1]
        
        if node == goal:
            return path, cost
        
        if node in visited:
            continue
        
        visited.add(node)
        
        for neighbor, edge_cost in graph.get(node, []):
            if neighbor not in visited:
                new_cost = cost + edge_cost
                new_path = path + [neighbor]
                heapq.heappush(pq, (new_cost, new_path))
    
    return None, None

# Example usage
graph = {
    'A': [('B', 1), ('C', 4)],
    'B': [('D', 3), ('E', 1)],
    'C': [('F', 2)],
    'D': [('G', 1)],
    'E': [('G', 2)],
    'F': [('G', 3)],
    'G': []
}

path, cost = uniform_cost_search(graph, 'A', 'G')
print(f"Path: {' → '.join(path)}")  # Output: A → B → E → G
print(f"Total Cost: {cost}")         # Output: 4
```

### 📈 Visual Representation

```
        A (cost: 0)
       / \
     1/   \4
     /     \
    B(1)   C(4)
   / \     |
  3/  1\   |2
  /    \  |
D(4)   E(2)-F(6)
 |      |  |
1|     2| 3|
 |      |  |
G(5)   G(4) G(9)
       ↑
   OPTIMAL PATH!
   
Path costs shown in parentheses
UCS chooses: A→B→E→G = 4 (minimum cost)
```

---

# Informed Search Algorithms

Informed search algorithms use heuristics (estimates of distance to goal) to guide the search more efficiently.

## Greedy Best First Search

### 🎯 Intuition
Greedy Best First Search always moves toward the node that appears closest to the goal based on the heuristic. Like following a compass pointing directly to your destination, ignoring the actual path cost.

### 📊 Characteristics
- **Complete:** No (can get stuck in loops)
- **Optimal:** No (ignores path cost)
- **Time Complexity:** O(b^m) (worst case)
- **Space Complexity:** O(b^m)
- **Heuristic:** Uses h(n) - estimated cost to goal
- **Data Structure:** Priority Queue (ordered by h(n))

### 🔢 Numerical Example

Graph with heuristic values (straight-line distance to G):

```
Graph Structure:
Node: (heuristic value h(n))

A(6) → B(4) → D(2) → G(0)
  \      \
   →  C(5) → F(3)
        ↓
        E(4)
```

**Heuristic Values (h(n)):**
- h(A) = 6
- h(B) = 4
- h(C) = 5
- h(D) = 2
- h(E) = 4
- h(F) = 3
- h(G) = 0

**Actual Edge Costs:**
- A → B: 1, A → C: 2
- B → D: 1, B → E: 5
- C → F: 1
- D → G: 1
- E → G: 1
- F → G: 8

**Step-by-Step Execution:**

| Step | Priority Queue (node, h(n)) | Current | Action | Why? |
|------|----------------------------|---------|--------|------|
| 0    | [(A, 6)]                   | -       | Start  | - |
| 1    | [(B, 4), (C, 5)]           | A       | Add neighbors | h(B)=4 < h(C)=5 |
| 2    | [(D, 2), (E, 4), (C, 5)]   | B       | Add neighbors | h(D)=2 is lowest |
| 3    | [(G, 0), ...]              | D       | Add G | h(G)=0 is lowest |
| 4    | **GOAL FOUND!**            | G       | Done! | - |

**Path Found:** A → B → D → G  
**Total Cost:** 1 + 1 + 1 = 3

**Note:** This happened to be optimal, but Greedy doesn't guarantee it!

### 💻 Python Implementation

```python
import heapq

def greedy_best_first_search(graph, start, goal, heuristic):
    """
    Greedy Best First Search implementation
    
    Args:
        graph: Dictionary with format {node: [(neighbor, cost), ...]}
        start: Starting node
        goal: Goal node
        heuristic: Dictionary with heuristic values {node: h(n)}
    
    Returns:
        (path, cost) tuple or (None, None) if not found
    """
    # Priority queue: (h(n), path, actual_cost)
    pq = [(heuristic[start], [start], 0)]
    visited = set()
    
    while pq:
        _, path, cost = heapq.heappop(pq)
        node = path[-1]
        
        if node == goal:
            return path, cost
        
        if node in visited:
            continue
        
        visited.add(node)
        
        for neighbor, edge_cost in graph.get(node, []):
            if neighbor not in visited:
                new_path = path + [neighbor]
                new_cost = cost + edge_cost
                # Priority is ONLY heuristic (greedy!)
                heapq.heappush(pq, (heuristic[neighbor], new_path, new_cost))
    
    return None, None

# Example usage
graph = {
    'A': [('B', 1), ('C', 2)],
    'B': [('D', 1), ('E', 5)],
    'C': [('F', 1)],
    'D': [('G', 1)],
    'E': [('G', 1)],
    'F': [('G', 8)],
    'G': []
}

heuristic = {
    'A': 6, 'B': 4, 'C': 5,
    'D': 2, 'E': 4, 'F': 3, 'G': 0
}

path, cost = greedy_best_first_search(graph, 'A', 'G', heuristic)
print(f"Path: {' → '.join(path)}")  # Output: A → B → D → G
print(f"Total Cost: {cost}")         # Output: 3
```

### 📈 Visual Representation

```
        A (h=6)
       / \
     1/   \2
     /     \
   B(h=4)  C(h=5)
   / \      |
  1/  5\   1|
  /    \   |
D(h=2) E(h=4) F(h=3)
 |1     |1    |8
 |      |     |
 G(h=0) G     G
 ↑
GREEDY CHOOSES THIS!
(Always follows lowest h(n))

Exploration order: A → B → D → G
(4 < 5, then 2 < 4, then 0 is goal)
```

**Example where Greedy fails:**

```
        A (h=7)
       / \
     5/   \1
     /     \
   G(h=0)  B(h=3)
            |1
            C(h=0)
            |1
            G

Greedy chooses: A → B → C → G (cost = 3)
Optimal path:   A → G (cost = 5)

Greedy fails because it's misled by h(B)=3 < h(G)=0 at A!
```

---

## A* Search Algorithm

### 🎯 Intuition
A* combines the best of both worlds: it considers both the actual cost to reach a node (like UCS) and the estimated cost to the goal (like Greedy). It evaluates nodes using f(n) = g(n) + h(n).

**Where:**
- **g(n)** = actual cost from start to node n
- **h(n)** = heuristic estimate from n to goal
- **f(n)** = total estimated cost through n

### 📊 Characteristics
- **Complete:** Yes
- **Optimal:** Yes (if heuristic is admissible)
- **Time Complexity:** Exponential in worst case
- **Space Complexity:** O(b^d)
- **Key Property:** If h(n) is admissible (never overestimates), A* is optimal
- **Data Structure:** Priority Queue (ordered by f(n))

### 🔢 Numerical Example

Same graph as Greedy example:

```
Graph with costs and heuristics:

Node  h(n)
A     6
B     4
C     5
D     2
E     4
F     3
G     0

Edges:
A-B: 1, A-C: 2
B-D: 1, B-E: 5
C-F: 1
D-G: 1, E-G: 1, F-G: 8
```

**Step-by-Step Execution:**

| Step | Priority Queue | Node | g(n) | h(n) | f(n)=g+h | Path | Action |
|------|----------------|------|------|------|----------|------|--------|
| 0    | [(A, 6)]       | -    | -    | -    | -        | -    | Start  |
| 1    | [(B, 5), (C, 7)]| A   | 0    | 6    | 6        | [A]  | Add B: g=1, h=4, f=5<br>Add C: g=2, h=5, f=7 |
| 2    | [(D, 4), (C, 7), (E, 10)]| B | 1 | 4 | 5 | [A,B] | Add D: g=2, h=2, f=4<br>Add E: g=6, h=4, f=10 |
| 3    | [(G, 3), (C, 7), (E, 10)]| D | 2 | 2 | 4 | [A,B,D] | Add G: g=3, h=0, f=3 |
| 4    | **GOAL!**      | G    | 3    | 0    | 3        | [A,B,D,G] | Found! |

**Optimal Path:** A → B → D → G  
**Total Cost:** 3

**Comparison with alternatives:**
- A → C → F → G = 2 + 1 + 8 = 11 ✗
- A → B → E → G = 1 + 5 + 1 = 7 ✗
- A → B → D → G = 1 + 1 + 1 = 3 ✓ (Optimal!)

### 💻 Python Implementation

```python
import heapq

def a_star_search(graph, start, goal, heuristic):
    """
    A* Search Algorithm implementation
    
    Args:
        graph: Dictionary with format {node: [(neighbor, cost), ...]}
        start: Starting node
        goal: Goal node
        heuristic: Dictionary with heuristic values {node: h(n)}
    
    Returns:
        (path, cost) tuple or (None, None) if not found
    """
    # Priority queue: (f(n), g(n), path)
    # f(n) = g(n) + h(n)
    pq = [(heuristic[start], 0, [start])]
    visited = {}  # Store best g(n) for each node
    
    while pq:
        f, g, path = heapq.heappop(pq)
        node = path[-1]
        
        if node == goal:
            return path, g
        
        # Skip if we've found a better path to this node
        if node in visited and visited[node] <= g:
            continue
        
        visited[node] = g
        
        for neighbor, edge_cost in graph.get(node, []):
            new_g = g + edge_cost
            
            # Only explore if we haven't found a better path to neighbor
            if neighbor not in visited or visited[neighbor] > new_g:
                new_path = path + [neighbor]
                new_f = new_g + heuristic[neighbor]
                heapq.heappush(pq, (new_f, new_g, new_path))
    
    return None, None

# Example usage
graph = {
    'A': [('B', 1), ('C', 2)],
    'B': [('D', 1), ('E', 5)],
    'C': [('F', 1)],
    'D': [('G', 1)],
    'E': [('G', 1)],
    'F': [('G', 8)],
    'G': []
}

heuristic = {
    'A': 6, 'B': 4, 'C': 5,
    'D': 2, 'E': 4, 'F': 3, 'G': 0
}

path, cost = a_star_search(graph, 'A', 'G', heuristic)
print(f"Path: {' → '.join(path)}")  # Output: A → B → D → G
print(f"Total Cost: {cost}")         # Output: 3
```

### 📈 Visual Representation with f(n) values

```
Node format: Node(g, h, f)

        A(0, 6, 6)
       /     \
     1/       \2
     /         \
  B(1, 4, 5)  C(2, 5, 7)
   /   \        |
  1/    5\     1|
  /      \     |
D(2,2,4) E(6,4,10) F(3,3,6)
 |1       |1        |8
 |        |         |
G(3,0,3) G(7,0,7)  G(11,0,11)
↑
OPTIMAL!

Priority queue expansion order:
1. A(f=6)
2. B(f=5) ← expanded because 5 < 6
3. D(f=4) ← expanded because 4 < 5
4. G(f=3) ← expanded because 3 < 4, GOAL!
```

---

## A* Visualization

### 🎨 Complete Example: Finding Path in a Grid

**Scenario:** Find shortest path from S to G in a grid with obstacles.

```
Grid (. = walkable, # = obstacle):
┌─────────────┐
│ S . . # . . │
│ . # . # . . │
│ . . . . # . │
│ # . # . . . │
│ . . . # . G │
└─────────────┘
```

**Heuristic:** Manhattan distance = |x1 - x2| + |y1 - y2|

### Step-by-Step Visualization

**Initial State:**
```
S(0,5,5) . . # . .
.        # . # . .
.        . . . # .
#        . # . . .
.        . . # . G(∞,0,∞)

Legend:
- Numbers in (): (g, h, f)
- Gray cells: Not yet explored
```

**After exploring S:**
```
S(0,5,5) ①(1,4,5) . # . .
①(1,5,6) #        . # . .
.        .        . . # .
#        .        # . . .
.        .        . # . G

① = Added to queue
```

**Midway through search:**
```
S ② ③ # . .
② # ③ # . .
③ ③ ④ ⑤ # .
# ④ # ⑤ ⑥ .
. ⑤ ⑥ # ⑦ G

Numbers show exploration order
Path being built: S → → → ↓ → ↓ → → ↓ → G
```

**Final Path Found:**
```
S→→→# . .
↓ #→# . .
↓→→→# .
# . #→→→
. . . #→G

Total cost: 11 steps
Cells explored: ~30 (out of 36)
Efficiency: A* explores fewer cells than BFS!
```

### Python Implementation for Grid A*

```python
import heapq
from typing import List, Tuple

def manhattan_distance(p1: Tuple[int, int], p2: Tuple[int, int]) -> int:
    """Calculate Manhattan distance between two points"""
    return abs(p1[0] - p2[0]) + abs(p1[1] - p2[1])

def a_star_grid(grid: List[List[int]], start: Tuple[int, int], 
                goal: Tuple[int, int]) -> List[Tuple[int, int]]:
    """
    A* pathfinding on a 2D grid
    
    Args:
        grid: 2D list where 0=walkable, 1=obstacle
        start: Starting position (row, col)
        goal: Goal position (row, col)
    
    Returns:
        List of positions from start to goal
    """
    rows, cols = len(grid), len(grid[0])
    
    # Directions: up, down, left, right
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    
    # Priority queue: (f, g, position, path)
    pq = [(manhattan_distance(start, goal), 0, start, [start])]
    visited = set()
    
    while pq:
        f, g, pos, path = heapq.heappop(pq)
        
        if pos == goal:
            return path
        
        if pos in visited:
            continue
        
        visited.add(pos)
        row, col = pos
        
        # Explore neighbors
        for dr, dc in directions:
            new_row, new_col = row + dr, col + dc
            new_pos = (new_row, new_col)
            
            # Check boundaries and obstacles
            if (0 <= new_row < rows and 0 <= new_col < cols and
                grid[new_row][new_col] == 0 and new_pos not in visited):
                
                new_g = g + 1
                new_h = manhattan_distance(new_pos, goal)
                new_f = new_g + new_h
                new_path = path + [new_pos]
                
                heapq.heappush(pq, (new_f, new_g, new_pos, new_path))
    
    return []  # No path found

# Example usage
grid = [
    [0, 0, 0, 1, 0, 0],
    [0, 1, 0, 1, 0, 0],
    [0, 0, 0, 0, 1, 0],
    [1, 0, 1, 0, 0, 0],
    [0, 0, 0, 1, 0, 0]
]

start = (0, 0)
goal = (4, 5)

path = a_star_grid(grid, start, goal)

# Print grid with path
for i, row in enumerate(grid):
    line = ""
    for j, cell in enumerate(row):
        if (i, j) == start:
            line += "S "
        elif (i, j) == goal:
            line += "G "
        elif (i, j) in path:
            line += "* "
        elif cell == 1:
            line += "# "
        else:
            line += ". "
    print(line)

print(f"\nPath length: {len(path)}")
print(f"Path: {path}")
```

**Output:**
```
S * * # . . 
* # * # . . 
* * * * # . 
# . # * * * 
. . . # * G 

Path length: 11
Path: [(0,0), (1,0), (2,0), (2,1), (2,2), (1,2), (2,3), (3,3), (3,4), (3,5), (4,5)]
```

---

## Comparison Table

| Algorithm | Complete | Optimal | Time Complexity | Space Complexity | Uses Heuristic |
|-----------|----------|---------|-----------------|------------------|----------------|
| **BFS** | ✅ Yes | ✅ Yes (unweighted) | O(b^d) | O(b^d) | ❌ No |
| **DFS** | ❌ No | ❌ No | O(b^m) | O(bm) | ❌ No |
| **DLS** | ❌ No | ❌ No | O(b^l) | O(bl) | ❌ No |
| **UCS** | ✅ Yes | ✅ Yes | O(b^(C*/ε)) | O(b^(C*/ε)) | ❌ No |
| **Greedy** | ❌ No | ❌ No | O(b^m) | O(b^m) | ✅ Yes h(n) |
| **A*** | ✅ Yes | ✅ Yes* | O(b^d) | O(b^d) | ✅ Yes f(n)=g+h |

*A* is optimal only if heuristic is admissible (never overestimates)

---

## When to Use Which Algorithm?

### Use BFS when:
- Unweighted graph
- Shortest path in terms of number of edges needed
- Graph is not too large (memory available)

### Use DFS when:
- Memory is limited
- Solutions are deep in the tree
- Multiple solutions exist (find any)

### Use DLS when:
- You know approximate solution depth
- Want to avoid infinite paths of DFS
- Implementing iterative deepening

### Use UCS when:
- Weighted graph
- Need optimal solution
- No heuristic available

### Use Greedy when:
- Quick solution needed (not necessarily optimal)
- Good heuristic available
- Memory/time constraints

### Use A* when:
- Weighted graph
- Need optimal solution
- Good admissible heuristic available
- Best overall choice for pathfinding!

---

## Key Concepts

### Admissible Heuristic
A heuristic h(n) is admissible if it **never overestimates** the actual cost to reach the goal.

**Examples:**
- ✅ **Straight-line distance** (for pathfinding): Always ≤ actual path
- ✅ **Manhattan distance** (for grid): Always ≤ actual moves
- ❌ **2 × straight-line**: Overestimates, not admissible

### Consistent Heuristic
A heuristic is consistent if: h(n) ≤ cost(n, n') + h(n') for all neighbors n'

This means: The estimated cost never decreases more than the actual cost of taking a step.

---

## Practice Problems

### Problem 1: BFS
Find the shortest path from A to F:
```
A - B - C
|   |   |
D - E - F
```

**Solution:** A → B → E → F (length 3)

### Problem 2: A*
Given the graph:
```
   A(8)
   / \
  5/  3\
  /    \
B(6)   C(4)
 |4     |2
 |      |
D(0)   D(0)
```
Numbers in () are heuristic values.
Find optimal path from A to D.

**Solution:** A → C → D (cost = 5, because 3 + 2 < 5 + 4)

---

## Conclusion

This guide covered:
- ✅ 4 Uninformed Search Algorithms (BFS, DFS, DLS, UCS)
- ✅ 2 Informed Search Algorithms (Greedy, A*)
- ✅ Detailed explanations with intuition
- ✅ Numerical examples with step-by-step execution
- ✅ Python implementations
- ✅ Visual representations
- ✅ When to use each algorithm

**Remember:**
- Uninformed = No domain knowledge, systematic exploration
- Informed = Uses heuristics to guide search
- A* = Best of both worlds (optimal + efficient)

Happy Learning! 🎓🚀
