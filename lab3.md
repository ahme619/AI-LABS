# Week 4 Lab: A* Search for the 8-Puzzle

## Question 1: Implement the function misplaced_tiles(state) as described in the lab.

### The function should:

- Count the number of tiles that are not in their goal position.
- Ignore the blank tile (0).
- Compare each element of state with GOAL_STATE.
  ## Lab description:
  ```python
  def misplaced_tiles(state):
    """
    Count tiles that are not in their goal position.
    The blank (0) does NOT count as a misplaced tile.
    """
    # TODO: implement
    # Hint: compare state[i] to GOAL_STATE[i] for each i, skip where state[i] == 0
    pass
  ```
  ## Solution:
  ```python
  def misplaced_tiles(state):
    """
    Count tiles that are not in their goal position.
    The blank (0) does NOT count as a misplaced tile.
    """
    count = 0
    for i in range(len(state)):
        if state[i] != 0 and state[i] != GOAL_STATE[i]:
            count += 1
    return count
  ```
  ## Explanation:

- We loop through every position in the 8-puzzle.
- If the tile is not the blank and it is not equal to the tile in the same position of GOAL_STATE, then it is misplaced.
- Count all such tiles.
  ### Example:
  ```python
  test_state = (1, 2, 3, 4, 5, 6, 7, 0, 8)
print(misplaced_tiles(test_state))   # Output: 1


### Why this is correct:

- In the test state, only tile 8 is in the wrong place, so the number of misplaced tiles is 1.
- The blank tile is ignored.
  ## Question 2: Implement the function manhattan_distance(state) as described in the lab.

## The function should:

- Compute the Manhattan distance for every non-zero tile.
- Ignore the blank tile (0).
- Sum the horizontal and vertical distances from each tile’s current position to its goal position.
### Lab description:
```python
def manhattan_distance(state):
    """
    Sum of the horizontal + vertical distance of every tile
    (excluding the blank) from its goal position.
    """
    # TODO: implement
    # Hint: for each tile value, find its current (row, col) and its
    # goal (row, col), and sum abs(row_diff) + abs(col_diff)
    pass
```
## Solution:
```python
def manhattan_distance(state):
    """
    Sum of the horizontal + vertical distance of every tile
    (excluding the blank) from its goal position.
    """
    total = 0

    for i, tile in enumerate(state):
        if tile == 0:
            continue

        goal_index = GOAL_STATE.index(tile)
        current_row, current_col = divmod(i, 3)
        goal_row, goal_col = divmod(goal_index, 3)

        total += abs(current_row - goal_row) + abs(current_col - goal_col)

    return total
```
### Explanation:

- We loop through every tile in the current state.
- For each non-zero tile:
- Find its current index position.
- Find the tile’s goal index in GOAL_STATE.
- Compute row difference and column difference.
- Add the absolute values:
- abs(current_row - goal_row) + abs(current_col - goal_col)
- Add all tile distances together.
### Example:
```python
test_state = (1, 2, 3, 4, 5, 6, 7, 0, 8)
print(manhattan_distance(test_state))   # Output: 1
```
### Why this is correct:

- In the test state, only tile 8 is one step away from its goal position.
- So the Manhattan distance is 1.
- If you want, I can continue with:

## Question 3: Implement A* search
### The function should:

- Use a priority queue ordered by f(n) = g(n) + h(n)
- Keep track of the best known cost for each state
- Expand the lowest-cost node
- Stop when the goal state is reached
#### Return:
  - "path": list of actions from start to goal
  - "cost": number of moves in the solution
  - "nodes_expanded": total number of nodes popped from the frontier
  ### Lab description:
  ```python
def a_star_search(start_state, heuristic_fn):
    """
    Returns a dict with:
        "path": list of actions from start to goal
        "cost": number of moves in the solution
        "nodes_expanded": total number of nodes popped from the frontier
    """
    counter = itertools.count()  # tie-breaker for equal f-values
    frontier = []
    heapq.heappush(frontier, (heuristic_fn(start_state), next(counter), 0, start_state, []))
    # entry format: (f, tie_breaker, g, state, path_of_actions)

    best_g = {start_state: 0}
    nodes_expanded = 0

    while frontier:
        f, _, g, state, path = heapq.heappop(frontier)
        nodes_expanded += 1

        if is_goal(state):
            return {"path": path, "cost": g, "nodes_expanded": nodes_expanded}

        # TODO: skip this node if we've already found a better g for this state
        # (this handles the "explored with lower g" case from the A* pseudocode)

        for neighbor_state, action in get_neighbors(state):
            new_g = g + 1  # each move costs 1

            # TODO: if neighbor_state is new OR new_g is better than best_g.get(neighbor_state, inf):
            #   - update best_g[neighbor_state]
            #   - compute new_f = new_g + heuristic_fn(neighbor_state)
            #   - push (new_f, next(counter), new_g, neighbor_state, path + [action]) onto frontier
            pass

    return {"path": None, "cost": None, "nodes_expanded": nodes_expanded}  # no solution found
```
### Solution:
```python
def a_star_search(start_state, heuristic_fn):
    """
    Returns a dict with:
        "path": list of actions from start to goal
        "cost": number of moves in the solution
        "nodes_expanded": total number of nodes popped from the frontier
    """
    counter = itertools.count()
    frontier = []
    heapq.heappush(frontier, (heuristic_fn(start_state), next(counter), 0, start_state, []))

    best_g = {start_state: 0}
    nodes_expanded = 0

    while frontier:
        f, _, g, state, path = heapq.heappop(frontier)

        # skip stale entries: if we already found a better cost for this state
        if g > best_g.get(state, float("inf")):
            continue

        nodes_expanded += 1

        if is_goal(state):
            return {"path": path, "cost": g, "nodes_expanded": nodes_expanded}

        for neighbor_state, action in get_neighbors(state):
            new_g = g + 1

            if new_g < best_g.get(neighbor_state, float("inf")):
                best_g[neighbor_state] = new_g
                new_f = new_g + heuristic_fn(neighbor_state)
                heapq.heappush(frontier, (new_f, next(counter), new_g, neighbor_state, path + [action]))

    return {"path": None, "cost": None, "nodes_expanded": nodes_expanded}
```
### Explanation:

- frontier is a min-heap ordered by:
  - f = g + h
- best_g stores the lowest cost found so far for each state
- If a state is reached with a worse g than previously seen, it is skipped
- When the goal state is popped, we return the path and cost
- Each move costs 1
### Example smoke test:
```python
easy_state = (1, 2, 3, 4, 5, 6, 7, 0, 8)
result = a_star_search(easy_state, manhattan_distance)
print(result)
assert result["cost"] == 1
```
### Expected output:
{'path': ['Left'], 'cost': 1, 'nodes_expanded': 2}

### Why the tie-breaker counter is needed:

- Python compares heap tuples left to right
- If two states have the same f-value, Python would compare the next item
- The counter ensures that equal f-values are ordered consistently and do not compare whole state tuples
## Question 4: Why must h(n) = 0 whenever state == GOAL_STATE, for both heuristics? What would break in A* if this weren't true?
Answer: For the 8-puzzle, the goal state is already solved, so the true remaining cost to reach the goal is 0.

#### That means:

- If state == GOAL_STATE, then the remaining path cost is 0
- Therefore, the heuristic must satisfy:
  - h(GOAL_STATE) = 0
This is true for both heuristics:

- Misplaced Tiles:
  - At the goal state, no tile is misplaced
  - So misplaced_tiles(GOAL_STATE) = 0
- Manhattan Distance:

  - At the goal state, every tile is already in its final position
  - So the total Manhattan distance is 0
Why this matters in A*: A* uses:

- f(n) = g(n) + h(n)
If the goal state had h(goal) > 0, then the heuristic would claim there is still cost left even though the puzzle is already solved. That would violate admissibility, because admissibility requires:

- h(n) <= true remaining cost
And for a goal state:

- true remaining cost = 0
- so h(goal) must be 0
  
#### What would break:

- A* might fail to recognize that the goal has been reached optimally
- It could keep exploring even though the solution is already found
- The guarantee of optimality would be lost
- The algorithm would no longer correctly interpret the goal as a zero-cost terminal state

 #### So the key rule is:

- h(goal) = 0 is required because the goal is already solved, and a heuristic must never overestimate the remaining cost to reach that goal.
## Question 5: At what scramble depth does the gap between the two heuristics become clearly visible? Why does the gap widen as depth increases rather than staying constant?
## Answer
The exact depth depends on the scrambles generated and the results in your table. In general, the difference often becomes noticeable at moderate depths, around 10–15 moves. Use your Cell 16 results to identify where it becomes clear in your experiment.

The gap widens because Misplaced Tiles only counts how many tiles are out of place, while Manhattan Distance also measures how far each tile is from its goal position. As puzzles become harder, that distance information helps Manhattan Distance guide A* more effectively, so it typically expands fewer nodes than Misplaced Tiles.
