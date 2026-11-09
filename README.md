# Lab 08: Navigating Weighted Graphs with Dijkstra's Algorithm

Name(s): _Luke Perez_  

## Introduction
You are already familiar with Breadth-First Search (BFS) and Depth-First Search (DFS) for exploring graphs. However, what happens when all edges are not created equal? In this lab, we will explore Dijkstra's Algorithm, which allows us to find the shortest path between two vertices in a *weighted* graph.

---

## Part 1: BFS with Weights

BFS is excellent for finding the shortest path in an unweighted graph. Let's see why it fails when weights are introduced. Consider the following weighted graph:

![Part 1 Graph](./part1.png)

**Questions:**
1. If you run a standard BFS starting at Node $A$, looking for Node $D$, which path will the BFS algorithm discover first? Why?
   > It will discover the path straight from A to D because BFS goes wide instead of direct, so it'll start from A and go straight to any neighbors and since D is a neighbor, it'll go straight there. 

2. What is the total weight of the path BFS found? What is the total weight of the alternative path? 
   > The weight of the path BFS found is 100, but if it were to go from A to B to C to D which is the alternative path, it would be a weight of 9. 

3. How does the definition of "shortest path" change when moving from unweighted graphs to weighted graphs?
   > The 'shortest path' when talking about unweighted graphs is just the least amount of edges, but once weights are added to those edges, the 'shortest path' becomes whatever the least amount of weights possible is, even if its more edges. 

---

## Part 2: Dijkstra's Algorithm

Dijkstra's algorithm uses a greedy approach. It maintains a set of "visited" (finalized) nodes and an array of the shortest known distances to all other nodes. Consider the following weighted graph:

![Part 2 Graph](./part2.png)

**Instructions:**
Starting at Node $A$, trace Dijkstra's algorithm. Fill in the tracker table below. 
*   **Current Node:** The unvisited node with the smallest current distance.
*   **Neighbors to Check:** The neighbors of the current node whose distances might need updating.
*   **`dist` Array:** The current known shortest distance from $A$ to each node. Use $\infty$ for unreachable/unknown nodes.
*   **`visited` Set:** The set of nodes that have been finalized and whose shortest distances are known.

| Step | Current Node | Neighbors to Check | `dist` Map | `visited` Set | 
| :--- | :--- | :--- | :--- | :--- | 
| 0 | $A$ | $B$, $C$ | $A$:0, $B$:4, $C$:2 | { } | 
| 1 | $C$ | $B$, $D$ | $C$:0, $A$:2, $B$:1, $D$:8, $E$:10 | {$A$, $C$} | 
| 2 | $B$ | $D$ | $B$:0, $A$:4, $C$:1, $D$:5 | {$A$, $C$, $B$} | 
| 3 | $D$ | $E$ | $D$:0, $B$:5, $C$:8, $E$:2 | {$A$, $C$, $B$, $D$} | 
| 4 | $E$ | N/A | $E$:0, $C$:10, $D$:2 | {$A$, $C$, $B$, $D$, $E$} | 

**Conceptual Question:**
When we add a vertex to the "visited" set, why are we 100% certain we have found the absolute shortest path to that node? 
> When we add a vertex to the visited set, we can be certain that we found the shortest path to that vertex because we can always pick the locally best option until we open up the entirety of our graph to weigh out all the possible paths. For example, from A we can see C and B, but C is only 2 away while B is 4, so when we go to C we are already on the shortest path. Then we check the distance from C to B and see if its still shorter than from A straight to B, which it is (3 vs 4), so still on the shortest path. If A directly to B was shorter than A to C to B, then our path would switch to that to still be the shortest available option all the way until we hit the end of the path. 

---

## Part 3: Assumptions

Dijkstra's algorithm relies on an assumption about the weights. Let's see what happens when we break it. Consider the following weighted graph, which contains a negative edge:

![Part 3 Graph](./part3.png)

**Instructions:**
Trace Dijkstra's algorithm starting from Node $A$ to find the shortest path to Node $C$.

1. Which node does Dijkstra's algorithm visit first after $A$?
   > It would go to C straight from A, as A to C is 1 and A to B is 2, so the distance of 1 wins. 

2. What will Dijkstra's algorithm declare as the shortest path to $C$, and what is its weight?
   > Dijkstra's algorithm would declare A straight to C being a value of 1 the shortest.  

3. Look at the graph manually. What is the *actual* shortest path from $A$ to $C$, and what is its total weight? 
   > The actual shortest path mathematically would be A to B to C which is a distance of -3, which is a smaller traveling weight than positive 1.

4. **The Trap:** Explain exactly *why* Dijkstra's algorithm failed to find the correct shortest path in this scenario.
   > Dijkstra's algorithm failed here because it immediately values out the possible paths from A and chooses A to C ss a value of 1 because A to B is a value of 2, so it never goes that way, and because it fails at accounting the other path with a total weight of -3 being smaller than positive 1, it says positive 1 is better or shorter when, by numbers, its not. 

---

## Part 4: Implementing Dijkstra's Algorithm

In the real world, problems rarely look like neat mathematical graphs right away. You often have to translate real-world constraints into a standard graph format so that an algorithm like Dijkstra's can process it.

**The Scenario:**
You are building a navigation app for delivery robots on a college campus. 

* Moving between any two adjacent sidewalk intersections takes exactly $2$ minutes.

* Some paths have stairs. The robots *cannot* use stairs.

* Certain intersections are busy crosswalks. The robot must wait an additional $3$ minutes at these specific intersections before it can safely leave them and continue on its path.

**The Problem:**
Standard Dijkstra's algorithm only understands *edges* and *edge weights*. It does not natively understand "node weights" (like waiting at an intersection) or "impassable terrain".

**Questions:**

1. How should you represent the paths with stairs in your graph so the algorithm never tries to use them?

   > You could represent stair paths or impassable paths in a graph by simply not setting them or setting distances between them, so an algorithm never tries it because theres nothing there. 

2. How can you account for the $3$-minute waiting time at busy crosswalk intersections using *only* edge weights? (Hint: Think about how you could modify the weights of edges connecting to that intersection).

   > Add a value of 3, or the 3 minute wait to each edge that needs that wait time, so an algorithm now knows the real value of crossing that edge in the real world rather than just being able to cross it immediately. 