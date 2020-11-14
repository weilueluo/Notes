# Algorithm Design & Application

## Ch. 13 Graphs & Traversals

### Definition

A graph groups edges in collection not a set, it allows two undirected edges to have the same start and end vertices, these edges are called parallel edges or multiple edges. An example use case is flight network where two planes operates on the same route at different time of the day.

**simple**
No parallel edges or self-loop.

**path **
A sequence of alternating e and v that start and end at some v.

**cycle **
A path that starts and ends at the same v.

**acyclic**
No cycle

**spanning subgraph** 
A subgraph that contains all v but may not be all e.

**connect/reachable**
v1 and v2 are reachable if there is a path between them.

**connected component**
Maximal connected subgraph

**strongly connected**
If all v are reachable from each other



- forest = graph without cycle (simple  = forest)

- tree = connected forest (simple = tree) (aka free tree)

- spanning tree = spanning subgraph that is a tree (no cycle and connected)

- connected undirected: e >= v-1

- connected undirected no cycle (tree): e = v-1

- undirected no cycle:  e <= v-1

- adjacency list 

  - collection of e (each e is simply a pair of v)
  - collection of v
    - each v store a list (aka adjacency list) of e that connects to that v
    - if it is directed there usually two lists for both direction
  - e+v space

  ![image-20201006210113869](C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201006210113869.png)

- dfs

  - examine once on each v, twice on e.
  - the graph is represented by a structure that allow us to find the neighbor e of a v.
    - adjacency list yes but adjacency matrix no
  - we can do:
    - Testing whether G is connected
    - Computing a spanning forest of G
    - Computing the connected components of G
    - Computing a path between two vertices of G, or reporting that no such path
      exists
    - Computing a cycle in G, or reporting that G has no cycles.
  - bfs
    - edges that used to discover new edges = discovery edge
      - it forms a spanning tree aka bfs tree (of the connected component)
    - other edges = cross edge
    - we can do:
      - Testing whether G is connected
      - Computing a spanning forest of G
      - Computing the connected components of G
      - Given a start vertex s of G, computing, for every vertex v of G, <u>a path with</u>
        <u>the minimum number of edges</u> between s and v, or reporting that no such
        path exists
      - Computing a cycle in G, or reporting that G has no cycles.

- diff dfs and bfs

  - if edges are not weighted, first encounter of a v is the shortest path to that v in bfs
  - bfs can compute the path with min edges

- transitive closure of $\overset{\rightarrow}{G}$ is $\overset{\rightarrow}{G}^*$ where an e between v1 and v2 if there is a directed path between them.

- dfs on an v in direct graph can find all other reachable v from v, labeled as V
  - Proof, consider there is a v that is reachable from initial v but not in V, since it is reachable that means this v has an directed path from initial v to this v, for any (u, v) in this directed path, all daughter of u is explored thus v must be explored, since every v on this path is explored this means the end v must be in V, thus contradiction.
  - we can also use this theorem to find transitive closure.
- The following problems can be solved in O(v(v+e)) time:
  - Computing, for each vertex v of  G, the subgraph reachable from v
  - Testing whether G is strongly connected
    - this can be done using 2 dfs
      - dfs on a v, if any v is not reached, it is not strongly connected
      - then we reverse all edges' direction
      - dfs on the same v again, if all v is reached again, then it is strongly connected
  - Computing the transitive closure Gâˆ— of  G.

- back edges, which connect a vertex to an ancestor in the DFS tree
- forward edges, which connect a vertex to a descendant in the DFS tree
- cross edges, which connect a vertex to a vertex that is neither its ancestor nor
  its descendant.

## Ch 16

- N = flow network
- cut = split V in N to be (V+, V-)
  - forward flow = V+ -> V-
  - backward flow = V- -> V+
  - capacity of cut = sum of capacity of edges in forward flow
  - value of flow / flow across cut = sum of capacity of edges in forward flow - sum of capacity of edges in backward flow

