---
marp: true
theme: default
paginate: true
backgroundColor: #f8f7f4
color: #1e1e2e
style: |
  section {
    font-family: 'Segoe UI', sans-serif;
    padding: 50px 60px;
    background-color: #f8f7f4;
  }
  h1 {
    color: #7c3aed;
    font-size: 2em;
    border-bottom: 3px solid #7c3aed;
    padding-bottom: 10px;
  }
  h2 {
    color: #2563eb;
    font-size: 1.5em;
  }
  code {
    background: #ede9fe;
    border-radius: 6px;
    padding: 2px 6px;
  }
  pre {
    background: #ede9fe;
    border-left: 4px solid #7c3aed;
    border-radius: 8px;
    padding: 20px;
  }
  pre code {
    background: transparent;
    padding: 0;
    color: #1e1e2e;
    font-size: 0.82em;
  }
  .highlight {
    color: #16a34a;
    font-weight: bold;
  }
  section.lead h1 {
    font-size: 2.8em;
    text-align: center;
    border: none;
  }
  section.lead p {
    text-align: center;
    font-size: 1.2em;
    color: #2563eb;
  }
  section.question h1 {
    font-size: 1.6em;
    text-align: center;
    border: none;
    color: #b45309;
  }
  section.question {
    background: #f8f7f4;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
  }
  section.question p {
    font-size: 1.15em;
    color: #2563eb;
    max-width: 75%;
  }
  section.divider h1 {
    font-size: 2.4em;
    text-align: center;
    border: none;
    color: #7c3aed;
  }
  section.divider {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
  }
  section.divider p {
    color: #2563eb;
    font-size: 1.2em;
  }
  ul li {
    margin-bottom: 10px;
    line-height: 1.5;
  }
  .two-col {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 30px;
  }
  .box {
    background: #ede9fe;
    border-radius: 10px;
    padding: 20px;
  }
  .callout {
    background: #fef9f0;
    border-left: 4px solid #f43f5e;
    border-radius: 6px;
    padding: 15px 20px;
    margin-top: 20px;
  }
  .success {
    background: #f0fdf4;
    border-left: 4px solid #16a34a;
    border-radius: 6px;
    padding: 15px 20px;
    margin-top: 20px;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 10px;
  }
  th {
    background: #7c3aed;
    color: #ffffff;
    font-weight: bold;
    padding: 10px 16px;
    text-align: left;
  }
  td {
    background: #ede9fe;
    color: #1e1e2e;
    padding: 9px 16px;
    border-bottom: 1px solid #ddd6fe;
  }
  tr:last-child td { border-bottom: none; }
  tr:nth-child(even) td { background: #e0d9fb; }
  blockquote {
    background: #fef9f0;
    border-left: 4px solid #f43f5e;
    border-radius: 6px;
    padding: 15px 20px;
    margin-top: 20px;
    color: #1e1e2e;
  }
  blockquote p { margin: 0; }
---

<!-- _class: lead -->

# Unit 9: Graph Algorithms (Week 7)

**CPTR 242 — Sequential and Parallel Data Structures and Algorithms**
Walla Walla University

---

<!-- _class: divider -->

# Part 1: Breadth-First Search

Section 9.1

---

<!-- _class: question -->

# 🤔 You want to find the shortest path between two people in a social network.

"Shortest" means fewest hops, not fastest computation.

**How would you systematically explore outward from a source, layer by layer, without missing anyone or revisiting anyone?**

---

# 9.1 Breadth-First Search

**BFS** explores a graph level by level from a source vertex, using a queue.

```
Graph:  A - B - D
        |   |
        C - E

BFS from A:  Level 0: A
             Level 1: B, C
             Level 2: D, E
```

Every vertex is visited exactly once. The order guarantees that when vertex v is first reached, the path taken is the **shortest path by hop count**.

---

# 9.1 BFS Algorithm

```cpp
void bfs(int src) {
    vector<int> dist(V, -1);
    queue<int> q;
    dist[src] = 0; q.push(src);
    while (!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : adj[u])
            if (dist[v] == -1) { dist[v] = dist[u]+1; q.push(v); }
    }
}
```

`dist[v] == -1` serves as the visited check. When BFS finishes, `dist[v]` holds the shortest hop-count distance from `src` to every reachable vertex.

---

# 9.1 BFS: Properties and Cost

| Property | Value |
|---|---|
| Time complexity | O(V + E) |
| Space (queue + visited) | O(V) |
| Shortest path? | Yes — by hop count |
| Works on weighted graphs? | No — use Dijkstra |
| Works on directed graphs? | Yes |

> BFS is also used for: checking connectivity, finding connected components, and level-order tree traversal. Any time you need "closest first," reach for BFS.

---

<!-- _class: divider -->

# Part 2: Depth-First Search

Section 9.3

---

<!-- _class: question -->

# 🤔 BFS fans out in all directions simultaneously.

What if you wanted to follow a single path as far as it goes before backtracking — like exploring a maze?

**What data structure would naturally support "go deep, then backtrack"?**

---

# 9.3 Depth-First Search

**DFS** follows each path to its end before backtracking, using a stack (or recursion).

```
Graph:  A - B - D
        |   |
        C - E

DFS from A (one possible order):  A → B → D → E → C
```

DFS does not guarantee shortest paths.

---

# 9.3 DFS Algorithm (Recursive)

```cpp
void dfs(int u, vector<bool>& visited) {
    visited[u] = true;
    for (int v : adj[u])
        if (!visited[v]) dfs(v, visited);
}
```

The call stack implicitly acts as the DFS stack. Each unvisited neighbor is explored immediately and fully before returning to the current vertex.

---

<!-- _class: divider -->

# Part 3: Dijkstra's Shortest Path

Section 9.5

---

<!-- _class: question -->

# 🤔 BFS finds shortest paths by hop count. But roads have lengths.

The path with fewest turns is rarely the fastest route.

**How would you always expand the lowest-cost frontier, rather than the earliest-discovered frontier?**

---

# 9.5 Dijkstra's Algorithm

**Dijkstra's** finds shortest weighted paths from a source to all vertices, using a min-priority queue.

**Core idea:** maintain a `dist[]` array initialized to ∞. Repeatedly extract the unvisited vertex with minimum known distance, then **relax** its edges.

**Relaxation:** if going through u improves the known distance to v, update it.

```
if dist[u] + weight(u,v) < dist[v]:
    dist[v] = dist[u] + weight(u,v)
```

---

# 9.5 Dijkstra: Trace

```
Graph: A --4-- B --2-- D
        \      |
         7     1
          \    |
            C--+  (C-B weight 3)

Source: A.  Initial dist: A=0, B=∞, C=∞, D=∞

Extract A(0): relax B→4, C→7
Extract B(4): relax C→min(7, 4+1)=5, D→4+2=6
Extract C(7): no improvement
Extract D(6): done
```

Final: dist = { A:0, B:4, C:5, D:6 }

**Critical constraint:** Dijkstra requires **non-negative edge weights**.


---

<!-- _class: divider -->

# Part 4: Topological Sort

Section 9.7

---

<!-- _class: question -->

# 🤔 You have a list of tasks where some must be completed before others.

Build before test. Install dependencies before running. Take CPTR 141 before CPTR 242.

**How do you find a valid ordering — and how do you know one exists?**

---

# 9.7 Topological Sort

A **topological ordering** of a DAG lists vertices so that every edge u → v has u appearing before v.

```
DAG:   CPTR141 → CPTR231 → CPTR242
                     ↓
                 CPTR332

Valid orderings:
  CPTR141, CPTR231, CPTR242, CPTR332  ✅
  CPTR141, CPTR231, CPTR332, CPTR242  ✅
  CPTR231, CPTR141, CPTR242, CPTR332  ❌  (231 before 141)
```

A topological ordering exists **if and only if** the graph is a DAG.

---

# We'll continue Wednesday! 😄 Have a great day!

---

<!-- _class: divider -->

# Part 5: Bellman-Ford

Section 9.9

---

# Bellman–Ford Algorithm

---

# What Bellman–Ford Does

Bellman–Ford finds the **shortest path** from one starting node to every other node.

Unlike Dijkstra's algorithm, it also works when edges can have **negative weights**.

---

# Core Idea

Shortest paths get discovered gradually.

* After 1 pass:

  * shortest paths using **1 edge** are correct
* After 2 passes:

  * shortest paths using **2 edges** are correct
* After 3 passes:

  * shortest paths using **3 edges** are correct

So we repeatedly improve distances until all shortest paths are found.

---

# Key Observation

In a graph with `V` vertices:

* any shortest path can use at most `V - 1` edges

So Bellman–Ford repeats its update process:

```text
V - 1 times
```

---

# Initial Setup

Start with:

```text
distance[source] = 0
distance[everything else] = ∞
```

Example:

```text
A = 0
B = ∞
C = ∞
D = ∞
```

---

# Relaxation

For an edge:

```text
u --(w)--> v
```

check whether going through `u` gives a shorter path to `v`.

If:

```text
dist[u] + w < dist[v]
```

then update:

```text
dist[v] = dist[u] + w
```

This process is called **relaxation**.

---

# Example Graph

```text
A --4--> B
A --5--> C
B --(-2)--> C
```

Initial distances:

```text
A = 0
B = ∞
C = ∞
```

---

# First Pass

Edge `A → B`

```text
0 + 4 < ∞
```

Update:

```text
B = 4
```

---

# Continue First Pass

Edge `A → C`

```text
0 + 5 < ∞
```

Update:

```text
C = 5
```

---

# Continue First Pass

Edge `B → C`

```text
4 + (-2) < 5
```

Update:

```text
C = 2
```

Final distances after pass:

```text
A = 0
B = 4
C = 2
```

---

# Why Multiple Passes?

Distance information spreads outward:

```text
Pass 1 → paths using 1 edge
Pass 2 → paths using 2 edges
Pass 3 → paths using 3 edges
```

Eventually all shortest paths become correct.

---


# Example:

```text

A → B → C → D

During the first pass:

maybe we discover B

During the second pass:

now we can improve C

```

---

# Negative Cycle Detection

After doing `V - 1` passes:

* check all edges one more time

If any distance can still improve:

```text
there is a negative cycle
```

because shortest paths should already be finalized.

---

# Time Complexity

Bellman–Ford processes:

* all edges
* `V - 1` times

Complexity:

```text
O(VE)
```

---

# Bellman–Ford in One Sentence

> “Keep relaxing every edge until no shorter paths exist.” 

---

<!-- _class: divider -->

# Part 6: Minimum Spanning Tree

Section 9.11

---

# Minimum Spanning Tree (MST)

---

# What Is an MST?

A **minimum spanning tree** is:

* a tree connecting all vertices
* with no cycles
* and the **smallest possible total edge weight**

---

# Example Graph

```text id="x0k1x4"
A --4-- B
|       |
7       2
|       |
C --3-- D
```

Edges:

```text id="4w4nmg"
A-B = 4
A-C = 7
B-D = 2
C-D = 3
```

---

# What Does “Spanning Tree” Mean?

A spanning tree must:

* connect all vertices
* contain no cycles

With `4` vertices, a tree must contain:

```text id="b5yx8j"
4 - 1 = 3 edges
```

So we must remove exactly one edge.

---

# Try Removing Edge A–C

Remove the edge with weight `7`:

```text id="m5kt7m"
A --4-- B
        |
        2
        |
C --3-- D
```

Everything is still connected:

```text id="yg1f0r"
A → B → D → C
```

Total weight:

```text id="ol3jif"
4 + 2 + 3 = 9
```

---

# Try Removing Edge B–D

```text id="wovdfj"
A --4-- B
|
7
|
C --3-- D
```

Total weight:

```text id="tf9hdu"
4 + 7 + 3 = 14
```

This is worse.

---

# The Minimum Spanning Tree

The best choice is:

```text id="mewpdc"
A-B = 4
B-D = 2
C-D = 3
```

Diagram:

```text id="a3y7zl"
A --4-- B
        |
        2
        |
C --3-- D
```

Total weight: 9

---

# Key Idea

An MST tries to:

```text id="c1m3ql"
Keep the graph connected
while using the cheapest possible edges
```

without creating cycles.


---

# Kruskal's Algorithm

**Kruskal's:** sort all edges by weight; greedily add the cheapest edge that doesn't create a cycle.

```
1. Sort edges by weight ascending
2. For each edge (u, v, w):
     if u and v are in different components:
         add edge to MST
         merge their components
3. Stop when V−1 edges are added
```

Total time: **O(E log E)** — dominated by the sort.

---

# Kruskal’s vs Prim’s Algorithm

---

# What Do They Both Do?

Both algorithms find a:

```text id="5e0y8h"
Minimum Spanning Tree (MST)
```

They connect all vertices with:

* no cycles
* minimum total edge weight

---

# Main Difference

## Kruskal

```text id="r2v1c7"
Builds the MST by choosing globally cheapest edges
```

---

# Main Difference

## Prim

```text id="tjlwm4"
Builds one growing tree outward from a starting vertex
```

---

# Kruskal’s Algorithm

Idea:

> “Take the cheapest edge available.”

Steps:

1. Sort all edges by weight
2. Add the smallest edge
3. Skip edges that create cycles
4. Continue until all vertices are connected

---

# Kruskal Visual

Start disconnected:

```text id="w0z7ua"
A   B   C   D
```

Add cheapest edges:

```text id="r5vw7m"
A-B   C-D
```

Merge components:

```text id="nyjlwm"
A-B-C-D
```

---

# Prim’s Algorithm

Idea:

> “Grow one tree outward.”

Steps:

1. Start from any vertex
2. Add the cheapest edge leaving the current tree
3. Repeat until all vertices are included

---

# Prim Visual

Start from one vertex:

```text id="frs7o4"
A
```

Grow outward:

```text id="c0onpp"
A-B
```

Continue growing:

```text id="nwmjlwm"
A-B-D
```

Eventually:

```text id="mf2n93"
A-B-D-C
```

---


# Time Complexity

| Algorithm   | Complexity   |
| ----------- | ------------ |
| Kruskal     | `O(E log E)` |
| Prim (heap) | `O(E log V)` |



# 9.11 Kruskal's vs. Prim's

| Property | Kruskal's | Prim's |
|---|---|---|
| Approach | Edge-centric | Vertex-centric |
| Better for | Sparse graphs | Dense graphs |

Both produce a valid MST. Kruskal's is simpler to implement for sparse graphs; Prim's is preferred when E ≈ V².

---

<!-- _class: divider -->

# Part 7: All-Pairs Shortest Path

Section 9.13

---

# Floyd–Warshall Algorithm

---

# What Does Floyd–Warshall Do?

Floyd–Warshall finds:

```text id="yjlwm0"
Shortest paths between EVERY pair of vertices
```

Not just from one source.

It computes:

```text id="5eq5yi"
distance[u][v]
```

for all vertices `u` and `v`.

---

# Core Idea

Ask this question repeatedly:

> “Would going through vertex `k` make the path shorter?”

For every pair `(i, j)`:

```text id="1h74s5"
Can i → k → j beat i → j ?
```

If yes, update the distance.

---

# Main Formula

For every intermediate vertex `k`:

```text id="jjlwmn"
dist[i][j] =
min(dist[i][j], dist[i][k] + dist[k][j])
```

This is the entire algorithm.

---

# What It Means

Current best path:

```text id="n2z6yk"
i → j
```

Possible better path:

```text id="qlxj6r"
i → k → j
```

If the second one is shorter:

```text id="o0x2lu"
update dist[i][j]
```


---

# Triple Nested Loop

Floyd–Warshall is basically:

```python
for k in vertices:
    for i in vertices:
        for j in vertices:
            dist[i][j] = min(
                dist[i][j],
                dist[i][k] + dist[k][j]
            )
```

---

# Why It Works

The algorithm gradually allows more intermediate vertices.

* First: paths using no intermediates
* Then: paths allowed to go through vertex 1
* Then: through vertices 1 and 2
* Eventually: through all vertices

At the end, all shortest paths are known.

---

# Time Complexity

There are 3 nested loops:

```text id="vh3rdu"
O(V³)
```

So Floyd–Warshall is slower on large graphs, but very simple.

---

# Negative Edges

Floyd–Warshall works with:

```text id="mjlwmq"
negative edge weights
```

as long as there are no negative cycles.

---

# Thanks! Quiz Tomorrow! 😄