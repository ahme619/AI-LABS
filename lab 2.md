# Lab 2: Uninformed Search — BFS and DFS on a 2D Maze

8. Compare BFS and DFS results.
9. Change the neighbor order and observe the effect on DFS.
10. Change the start and goal positions and test multiple cases.
11. Explain the difference between FIFO and LIFO frontier behavior.
12. Explain why BFS guarantees the shortest path in an unweighted maze.

---

## 1. Which algorithm found the shorter path?

BFS found the shorter path.

This is because BFS explores the maze level by level. In an unweighted maze, the first time BFS reaches the goal, it has found the shortest route from the start to the goal.

DFS may also find a path, but it is not guaranteed to be the shortest one because it explores one branch deeply before backtracking.

---

## 2. Which algorithm had the larger maximum frontier?

BFS usually had the larger maximum frontier.

Why?
- BFS stores multiple nodes at the same depth in the queue.
- DFS follows one branch at a time and usually keeps fewer nodes in memory at once.

So, BFS generally has a larger frontier and uses more memory than DFS.

---

## 3. Why did the two algorithms produce different paths on the same maze?

Because BFS and DFS expand nodes in different orders.

- BFS uses a queue (FIFO)
  - oldest discovered node is expanded first
- DFS uses a stack (LIFO)
  - most recently discovered node is expanded first

Since they process neighbors in different orders, they can reach the goal through different paths. The search order changes the path chosen.

---

## 4. Does DFS always produce the shortest path? Why or why not?

No, DFS does not always produce the shortest path.

DFS is not optimal because it keeps going deep into one branch before checking alternatives. It may find a valid path quickly, but that path may not be the shortest one.

This is why BFS is preferred when shortest path is required in an unweighted graph or maze.

---

## 5. Does BFS always produce the shortest path when every movement costs 1? Why?

Yes, BFS always produces the shortest path when every move costs the same amount (1).

Reason:
- BFS explores nodes in increasing order of path length.
- The first time BFS reaches the goal, it has found the minimum number of steps needed.

Since each move has equal cost, the path with the fewest moves is also the cheapest path.

---

## 6. Did the DFS path change after changing neighbor order? Why?

Yes, the DFS path can change when we change the neighbor order.

This happens because DFS examines neighbors in the exact order they are listed. If the order changes, DFS may take a different route first and therefore follow a different branch before reaching the goal.

So DFS depends strongly on neighbor ordering.

---

## 7. Did BFS’s path change in the same way? Why or why not?

BFS usually does not change in the same way.

BFS is based on level-order exploration and shortest-path logic. Even if the neighbor order changes, BFS still searches by depth. If multiple shortest paths exist, BFS may choose a different shortest path depending on the order of discovery, but it will still remain optimal.

So:
- DFS is highly sensitive to neighbor order
- BFS is more stable because it tends to prioritize shortest depth

---

## 8. Does BFS path length ever change if you re-run it with the same start/goal? Does DFS’s?

For the same maze, same `start`, same `goal`, and same neighbor order:

- BFS will usually return the same path length every time
- DFS will also usually return the same path length every time

This is because both algorithms are deterministic when the input and ordering are unchanged.

---

## 9. As the distance between start and goal grows, what happens to the maximum frontier size for each algorithm?

As the distance between the start and goal increases:

- BFS frontier usually grows larger because it explores many nodes at each level
- DFS frontier may also grow, but usually it stays relatively smaller because it follows one branch at a time

So:
- BFS typically has a larger maximum frontier
- DFS typically uses less memory

---

## 10. Can you find a `(start, goal)` pair where BFS and DFS return the same path?

Yes, it is possible.

If the maze structure between the two points is simple, or if there is only one practical path, both BFS and DFS may return the same route.

This tells us that the maze has little branching or only one clear route between those positions.

---

## 11. What happens if the goal is unreachable from the start?

If the goal is unreachable, then both BFS and DFS will not find a path.

In this implementation, they return:

```python
return None, max_frontier
