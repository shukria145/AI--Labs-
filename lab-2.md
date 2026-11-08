# Lab-2- Problem Solving by Searching — Uninformed Search 
## Section 7: Compare BFS and DF

Here are the results from running the code:

| Algorithm | Path Length | Maximum Frontier |
| :--- | :--- | :--- |
| *BFS* | 8 | 13 |
| *DFS* | 14 | 9 |

### Answer these questions:

**1. Which algorithm found the shorter path?**  
  BFS found the shorter path.

 **2. Which algorithm had the larger maximum frontier?**  
BFS had the larger maximum frontier.

 **3. Why did the two algorithms produce different paths on the same maze?**  
 Because BFS explores level-by-level (FIFO) to find the shortest route
 while DFS dives deep into one branch as far as possible before backtracking (LIFO).

 **4. Does DFS always produce the shortest path? Why or why not?**  
No. DFS just finds a path; it doesn't check if it's the shortest because it stops as soon as it hits the goal, even if it took a long route.

 **5. Does BFS always produce the shortest path when every movement costs 1? Why?**  
 Yes. Because it explores all nodes at the current depth before moving deeper, it is guaranteed to find the goal in the fewest possible steps.

--- 
## Section 8: Experiment 1 — Change the Neighbor Order
Original DFS order:
- up, down, left, right

Changed order:
- right, down, left, up

### Did the DFS path change?
- Yes, the DFS path usually changes when the neighbor order changes.

### Why?
- DFS explores the next neighbor in the order it appears in the list.
- Different orders cause the algorithm to follow different branches first, so it can find a different route to the goal.

### Did BFS’s path depend on this ordering the same way?
- Not in the same way.
- BFS is still layer-by-layer and still guarantees a shortest path length.
- Neighbor ordering can change which shortest path is chosen if there are multiple equally short paths, but BFS still explores by distance, not by branch depth.
- DFS is much more sensitive to neighbor ordering because it chooses one branch and keeps going.

***Note***: In many mazes, BFS may still return the same path length even if the exact route changes, as long as there are multiple shortest routes.

---
 ## 9. Experiment 2 — Change `start` and `goal`

Here are the results for 3 different (start, goal) pairs:

| Start | Goal | BFS Length | BFS Max Frontier | DFS Length | DFS Max Frontier |
| :--- | :--- | :--- | :--- | :--- | :--- |
| (0, 0) | (4, 4) | 8 | 13 | 14 | 9 |
| (0, 0) | (2, 2) | 4 | 9 | 10 | 7 |
| (4, 4) | (0, 0) | 8 | 11 | 8 | 11 |

### Answer these questions:

**1. Does BFS's path length ever change if you re-run it with the same start/goal? Does DFS's?**

BFS will never change (it is guaranteed to find the absolute shortest path). DFS will stay the same as long as the `directions` list is the same, but if you change the order of directions, DFS's path length will change.

**2. As the distance between start and goal grows, what happens to the maximum frontier size for each algorithm?**

For BFS, the maximum frontier size grows significantly because it explores outwards in all directions like a circle. For DFS, the maximum frontier size grows much slower because it only keeps track of the single path it is currently exploring.

**3. Can you find a (start, goal) pair where BFS and DFS return the same path? What does that tell you about the maze structure?**

Yes, looking at the third row (Start 4,4 to Goal 0,0), both BFS and DFS found a path of length 8. This tells us that there is only one clear path between those two points (there are no loops or alternative routes).

**4. What happens if goal is unreachable from start (e.g. sealed off by walls)? Try it — what do bfs() and dfs() return?**

If the goal is completely sealed off, the `frontier` will eventually become empty because the algorithms will explore every possible open cell. When the `while` loop ends, the functions will return `None`.

