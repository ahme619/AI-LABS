# Lab 4: Hill-Climbing Search with the N-Queens Problem
## Task 1: Create and Print the State

```python
state = [1, 3, 0, 2]

print("State:", state)
```
### Output:
State: [1, 3, 0, 2]
## Task 2: Run the experiment several times and record the results.
### Solution
| N | Run | Final Conflicts | Iterations | Solved? |
| :--- | :--- | :--- | :--- | :--- |
| 4 | 1 | 0 | 2 | True |
| 4 | 2 | 1 | 2 | false |
| 8 | 1 | 1 | 6 | false |
| 8 | 2 | 2 | 4 | false |
| 8 | 3 | 2 | 3 | false |
| 10 | 1 | 1 | 2 | false |
| 10 | 2 | 1 | 4 | false |
| 10 | 3 | 1 | 6 | false |

### Discussion:

- The same board size does not always give the same result.
- This happens because hill-climbing starts from a random initial state.
- A different starting point can lead to:
- a successful solution,
- or a local maximum with remaining conflicts.
- Larger boards are generally harder because there are more possible local maxima and more states to explore.
- This shows why random restarts are useful: repeated starts improve the chance of finding a valid solution.
## Task 3: Compare the results of basic Hill-Climbing and Random-Restart Hill-Climbing.
### Code used:
  
  ```python
  print("Basic Hill-Climbing")
print("-------------------")

for i in range(5):

    state, scores = hill_climbing(8)
    conflicts = count_conflicts(state)

    print(
        f"Run {i + 1}: "
        f"Conflicts = {conflicts}, "
        f"Iterations = {len(scores)}, "
        f"Solved = {conflicts == 0}"
    )


print("\nRandom-Restart Hill-Climbing")
print("----------------------------")

for i in range(5):

    state, restarts, best_scores = (
        random_restart_hill_climbing(8, max_restarts=100)
    )

    conflicts = count_conflicts(state)

    print(
        f"Run {i + 1}: "
        f"Conflicts = {conflicts}, "
        f"Restarts = {restarts}, "
        f"Solved = {conflicts == 0}"
    )
```
### Sample interpretation of the results:
#### 1.Did basic Hill-Climbing find a solution in every run?
- No.
- Basic Hill-Climbing can get stuck at a local maximum.
- Some runs may solve the problem, while others may stop with remaining conflicts.
#### 2.Did Random-Restart Hill-Climbing improve the results?
- Usually yes.
- Because it tries multiple random starting states, it increases the chance of finding a better solution than a single run.
#### 3.How many restarts were needed to find a solution?
- This varies from run to run.
- Sometimes a solution is found in 1 or 2 restarts.
- Sometimes it takes more.
- In some runs, it may not find a solution within the restart limit.
#### 4.Did any random-restart experiment reach its restart limit without finding a solution?
- It is possible, yes.
- This happens because random restarts do not guarantee success within a fixed number of attempts.
#### 5.Why can Random-Restart improve the probability of success without guaranteeing success within a fixed number of attempts?
- Each restart starts from a new random state.
- If one random start fails, another may succeed.
- So the chance of success grows as you increase the number of restarts.
- However, success is still probabilistic, not guaranteed.
- The algorithm may still fail in all attempts within the chosen restart limit.
#### Example of a possible result:
```text
Basic Hill-Climbing
Run 1: Conflicts = 0, Iterations = 10, Solved = True
Run 2: Conflicts = 2, Iterations = 7, Solved = False
Run 3: Conflicts = 0, Iterations = 13, Solved = True
Run 4: Conflicts = 4, Iterations = 9, Solved = False
Run 5: Conflicts = 0, Iterations = 11, Solved = True

Random-Restart Hill-Climbing
Run 1: Conflicts = 0, Restarts = 2, Solved = True
Run 2: Conflicts = 0, Restarts = 5, Solved = True
Run 3: Conflicts = 0, Restarts = 3, Solved = True
Run 4: Conflicts = 2, Restarts = 100, Solved = False
Run 5: Conflicts = 0, Restarts = 4, Solved = True
```
### Conclusion:
- Basic Hill-Climbing is simple but unreliable.
- Random-Restart Hill-Climbing improves performance by trying several starts.
- It improves the chance of success, but it still cannot guarantee success in a fixed number of restarts
## Task 4: 

## Section 17: Questions 1–10
### Question 1
#### What is the purpose of the evaluation function in Hill-Climbing?

The evaluation function measures how good a state is. In the N-Queens problem, it tells us how many queen conflicts exist. We want to minimize conflicts, so we define the evaluation score as negative conflicts. A higher evaluation score means a better state. Hill-Climbing uses this value to decide whether moving to a neighboring state is an improvement.

### Question 2
#### Why is an evaluation score of zero the best result in our N-Queens implementation?

A valid N-Queens solution has no attacking queens, so the number of conflicts is zero. In our implementation:

evaluation(state) = -count_conflicts(state)

Therefore:
- if the number of conflicts is 0, the evaluation score is 0
- 0 is the highest possible score
- so 0 represents the best possible state, meaning a valid solution has been found

### Question 3
#### What is the difference between a local maximum and a global maximum?

A local maximum is a state that is better than all of its immediate neighbors, but it may still not be the best state in the entire search space. A global maximum is the best possible state overall, with the highest evaluation score anywhere in the search space.

In Hill-Climbing, it is possible to stop at a local maximum even when a global maximum (a valid solution) exists elsewhere.

### Question 4
#### Why can basic Hill-Climbing stop without finding a solution even when a solution exists?

Because Hill-Climbing is greedy. It only moves to a better neighboring state if that state improves the current score. If the search reaches a local maximum, none of its neighbors are better, so the algorithm stops.

A valid solution may still exist elsewhere in the state space, but the algorithm never explores beyond the local maximum. This is why basic Hill-Climbing is not guaranteed to find a solution from every starting state.

### Question 5
#### Does reaching a local maximum always mean that the algorithm has failed? Explain.

No, it does not always mean failure.

A local maximum may also be the global maximum. In the N-Queens problem, a local maximum with zero conflicts is actually a valid solution. In that case, the algorithm has succeeded. However, if the local maximum still has conflicts, then the algorithm has stopped before reaching the goal.

So a local maximum is not always a failure, but it can block progress if the state is not a solution.

### Question 6
#### For N = 8, how many neighbors does each state have in our implementation?

Each queen can move to any of the other 7 rows in its own column. There are 8 columns, so the total number of neighbors is:

8 × 7 = 56

So each state has 56 neighbors.

### Question 7
#### What does the evaluation-score graph tell you about the behavior of Hill-Climbing?

The graph shows how the evaluation score changes during the search. In this implementation, the score is non-decreasing because the algorithm only accepts moves that improve the score.

If the score keeps increasing, the algorithm is improving. If the graph levels off, the algorithm has probably reached a local maximum or a state with no better neighbor. If the final score is zero, a valid solution has been found. If the final score is negative, the algorithm stopped before finding a solution.

### Question 8
#### Explain probabilistic completeness in your own words.

Probabilistic completeness means that if we repeat the algorithm many times with different random starting states, the chance of eventually finding a solution becomes very high as the number of attempts increases.

It does not guarantee success in one attempt or after a fixed number of attempts. Instead, it says that repeated independent random attempts make success more likely over time.

### Question 9
#### Why does Random-Restart Hill-Climbing improve the chance of finding a solution?

Random-Restart Hill-Climbing starts from many different random initial states. If one start leads to a local maximum, another start may lead to a valid solution.

This increases the chance of success because each restart gives the algorithm a new opportunity to explore a different part of the search space. More restarts means a better chance of finding a goal state.

### Question 10
#### Does probabilistic completeness guarantee that a solution will be found within 100 restarts? Why or why not?

No, probabilistic completeness does not guarantee success within 100 restarts.

It only says that as the number of attempts increases, the probability of success approaches 1. But with a fixed number of restarts, there is still a nonzero chance of failure. So random restarts improve the probability of success, but they do not guarantee success in any specific number of attempts.
 ## Section 18: Extension Task
### Modify the experiment to solve the N-Queens problem for N = 20.

#### Record the following:

- Number of conflicts in the final state.
- Number of iterations in a basic Hill-Climbing run.
- Number of restarts required by Random-Restart Hill-Climbing.
- Whether a valid solution was found.
- Compare the results of basic and Random-Restart Hill-Climbing.
#### Discuss how the number of queens and the random initial state affect the search process.
## Code used
```python
n = 20

# Basic Hill-Climbing
basic_state, basic_scores = hill_climbing(n)
basic_conflicts = count_conflicts(basic_state)

print("Basic Hill-Climbing")
print("Final conflicts:", basic_conflicts)
print("Iterations:", len(basic_scores))
print("Solved:", basic_conflicts == 0)


# Random-Restart Hill-Climbing
restart_state, restarts, best_scores = random_restart_hill_climbing(
    n,
    max_restarts=100
)
restart_conflicts = count_conflicts(restart_state)

print("\nRandom-Restart Hill-Climbing")
print("Final conflicts:", restart_conflicts)
print("Restarts:", restarts)
print("Solved:", restart_conflicts == 0)
```
## Comparing Basic and Random-Restart Hill-Climbing

#### The algorithms were tested five times using the N-Queens problem with a board size of 8.

### Results

#### Basic Hill-Climbing

| Run | Final Conflicts | Iterations | Solved? |
|---:|---:|---:|:---:|
| 1 | 2 | 3 | No |
| 2 | 2 | 4 | No |
| 3 | 0 | 5 | Yes |
| 4 | 1 | 4 | No |
| 5 | 1 | 4 | No |

#### Random-Restart Hill-Climbing

| Run | Final Conflicts | Restarts | Solved? |
|---:|---:|---:|:---:|
| 1 | 0 | 3 | Yes |
| 2 | 0 | 2 | Yes |
| 3 | 0 | 3 | Yes |
| 4 | 0 | 2 | Yes |
| 5 | 0 | 9 | Yes |

### Discussion

Basic Hill-Climbing found a solution in 1 out of 5 runs. In the other runs, it stopped with conflicts remaining, showing that it can get stuck at a non-solution local maximum.

Random-Restart Hill-Climbing found a solution in all 5 runs. It needed between 2 and 9 restarts. Trying new random starting states improved the chance of finding a solution, although it does not guarantee success within a fixed restart limit.

These results demonstrate that Random-Restart Hill-Climbing performed better than basic Hill-Climbing in these five trials. Results may differ in other runs because the starting states are random.
