# Lab-4 Local Search Optimization
  ## Hill climbing

Important note:
- The exact numerical results will vary because the starting state is random.
- The code below is the correct implementation for the lab.
- For Task 2 and Task 3, I include the expected format and an example of how to record observations, rather than pretending there is a single fixed answer.

## 1) Task 1:
### Create the state and print it

```python
state = [1, 3, 0, 2]

print("State:", state)
```

**Expected output:**
```python
State: [1, 3, 0, 2]
```

### 2) Random initial state

```python
import random

def random_state(n):
    return [random.randrange(n) for _ in range(n)]

state = random_state(8)
print("Random initial state:", state)
```

Example output:
```python
Random initial state: [2, 5, 1, 7, 0, 3, 6, 4]
```

### 3) Count conflicts and evaluation function

```python
def count_conflicts(state):
    conflicts = 0
    n = len(state)

    for i in range(n):
        for j in range(i + 1, n):
            # Same row
            if state[i] == state[j]:
                conflicts += 1
            # Same diagonal
            elif abs(state[i] - state[j]) == abs(i - j):
                conflicts += 1

    return conflicts


def evaluation(state):
    return -count_conflicts(state)


state = [1, 3, 0, 2]
print("Conflicts:", count_conflicts(state))
print("Evaluation score:", evaluation(state))
```

Expected output:
```python
Conflicts: 0
Evaluation score: 0
```

### 4) Generate neighboring states

```python
def get_neighbors(state):
    neighbors = []
    n = len(state)

    for col in range(n):
        for row in range(n):
            if row != state[col]:
                neighbor = state.copy()
                neighbor[col] = row
                neighbors.append(neighbor)

    return neighbors


state = [1, 3, 0, 2]
neighbors = get_neighbors(state)

print("Number of neighbors:", len(neighbors))
print("First five neighbors:")
for neighbor in neighbors[:5]:
    print(neighbor)
```

For N = 8:
- Each state has 8 × 7 = 56 neighbors

### 5) Basic Hill-Climbing implementation

```python
def hill_climbing(n):
    current = random_state(n)
    scores = []

    while True:
        current_score = evaluation(current)
        scores.append(current_score)

        neighbors = get_neighbors(current)
        best_neighbor = max(neighbors, key=evaluation)
        best_score = evaluation(best_neighbor)

        if best_score <= current_score:
            break

        current = best_neighbor

    return current, scores
```

### 6) Run Hill-Climbing for 8 queens

```python
solution, scores = hill_climbing(8)

print("Final state:", solution)
print("Final conflicts:", count_conflicts(solution))
print("Final evaluation score:", evaluation(solution))
print("Number of iterations:", len(scores))

if count_conflicts(solution) == 0:
    print("A valid solution was found.")
else:
    print("Hill-Climbing stopped without finding a solution.")
```

Example output:
```python
Final state: [0, 4, 7, 5, 2, 6, 1, 3]
Final conflicts: 0
Final evaluation score: 0
Number of iterations: 12
A valid solution was found.
```

Sometimes the algorithm stops before finding a solution; that is expected.

### 7) Display the board

```python
def print_board(state):
    n = len(state)

    for row in range(n):
        line = ""
        for col in range(n):
            if state[col] == row:
                line += "Q "
            else:
                line += ". "
        print(line)
```

Usage:
```python
print_board(solution)
```

### 8) Plot the evaluation-score graph

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(9, 5))
plt.plot(range(len(scores)), scores, marker="o")
plt.xlabel("Iteration")
plt.ylabel("Evaluation Score")
plt.title("Hill-Climbing Optimization: N-Queens")
plt.grid(True)
plt.show()
```

**Interpretation:**
- The score is non-decreasing because the algorithm only accepts improving moves.
- If the final score is 0, a valid solution was found.
- If the final score is negative, the search stopped at a non-goal state.

### 9) Experiment with different board sizes

```python
for n in [4, 8, 10, 12]:
    solution, scores = hill_climbing(n)
    print(
        f"N = {n:2d} | "
        f"Conflicts = {count_conflicts(solution):2d} | "
        f"Iterations = {len(scores):3d} | "
        f"Solved = {count_conflicts(solution) == 0}"
    )
```

Example output:
```python
N =  4 | Conflicts =  0 | Iterations =  4 | Solved = True
N =  8 | Conflicts =  0 | Iterations = 12 | Solved = True
N = 10 | Conflicts =  4 | Iterations = 32 | Solved = False
N = 12 | Conflicts =  6 | Iterations = 42 | Solved = False
```

This is just one example; your output will differ.

## 10) Task 2:
### Record observations in the table

Use this format:

```python
# Example table format
# You can fill this in manually after running the experiment several times
```

A sample table:

| N | Run | Final Conflicts | Iterations | Solved? |
|---:|---:|---:|---:|---|
| 4 | 1 | 0 | 5 | Yes |
| 4 | 2 | 0 | 4 | Yes |
| 8 | 1 | 0 | 12 | Yes |
| 8 | 2 | 2 | 18 | No |
| 8 | 3 | 0 | 10 | Yes |
| 10 | 1 | 4 | 29 | No |
| 10 | 2 | 0 | 40 | Yes |
| 10 | 3 | 6 | 33 | No |

**Discussion:**
- The same board size does not always produce the same result.
- This is because the algorithm starts from a random initial state.
- Some random starts are good and lead to a solution; others lead to local maxima.

### 11) Random-Restart Hill-Climbing

```python
def random_restart_hill_climbing(n, max_restarts=100):
    best_state = None
    best_score = float("-inf")
    best_scores = []

    for restart in range(max_restarts):
        state, scores = hill_climbing(n)
        score = evaluation(state)

        if score > best_score:
            best_state = state
            best_score = score

        best_scores.append(best_score)

        if best_score == 0:
            return best_state, restart + 1, best_scores

    return best_state, max_restarts, best_scores
```

Run it:

```python
solution, restarts, best_scores = random_restart_hill_climbing(8, max_restarts=100)

print("Final state:", solution)
print("Final conflicts:", count_conflicts(solution))
print("Restarts performed:", restarts)

if count_conflicts(solution) == 0:
    print("A valid solution was found.")
else:
    print("No solution was found within the restart limit.")

print_board(solution)
```

### 12) Plot Random-Restart progress

```python
plt.figure(figsize=(9, 5))
plt.plot(range(1, len(best_scores) + 1), best_scores, marker="o")
plt.xlabel("Restart")
plt.ylabel("Best Evaluation Score So Far")
plt.title("Random-Restart Hill-Climbing: N-Queens")
plt.grid(True)
plt.show()
```

**Interpretation:**
- The graph is non-decreasing because it stores the best score found so far.
- Once the score reaches 0, the algorithm has found a valid solution and stops.

## 13) Compare basic and random-restart Hill-Climbing

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
    state, restarts, best_scores = random_restart_hill_climbing(8, max_restarts=100)
    conflicts = count_conflicts(state)

    print(
        f"Run {i + 1}: "
        f"Conflicts = {conflicts}, "
        f"Restarts = {restarts}, "
        f"Solved = {conflicts == 0}"
    )
```

## Task 3
### answers:
### 1. Did basic Hill-Climbing find a solution in every run?
   - No, not always.
   - Some runs stop at local maxima.

### 2. Did Random-Restart Hill-Climbing improve the results?
   - Usually yes.
   - It gives the algorithm multiple chances to start from better random positions.

### 3. How many restarts were needed to find a solution?
   - This varies.
   - Sometimes 1 or 2; sometimes more.

### 4. Did any random-restart experiment reach its restart limit without finding a solution?
   - It is possible, especially if the restart limit is too small or unlucky random starts happen repeatedly.

### 5. Why can Random-Restart improve the probability of success without guaranteeing success within a fixed number of attempts?
   - Because each restart is an independent chance.
   - Even if the chance of success per try is not high, repeating the process increases the chance of eventually finding a solution.
   - It is not guaranteed in a fixed number of attempts.

## 17) Lab Questions and Answers

### Question 1: What is the purpose of the evaluation function in Hill-Climbing?
- The evaluation function measures how good a state is.
- In N-Queens, it tells us how many conflicts exist.
- We use the score as:
  - evaluation(state) = -count_conflicts(state)
- We want the highest possible score, because zero conflicts gives the best score of 0.

### Question 2: Why is an evaluation score of zero the best result in our N-Queens implementation?
- Because zero conflicts means no two queens attack each other.
- That is exactly the goal condition for N-Queens.
- Since evaluation = -conflicts, the maximum possible score is 0.

### Question 3: What is the difference between a local maximum and a global maximum?
- A local maximum is a state that is better than all its immediate neighbors, but not necessarily the best state in the entire search space.
- A global maximum is the best possible state in the whole state space.
- In this problem, a local maximum may still have some conflicts left, while the global maximum is a valid, conflict-free solution.

### Question 4: Why can basic Hill-Climbing stop without finding a solution even when a solution exists?
- Because the algorithm only looks at immediate neighbors and moves only when a better neighbor exists.
- It may reach a local maximum where every neighbor is worse, even though a better state exists elsewhere in the search space.
- It does not explore far enough away from the current state.

### Question 5: Does reaching a local maximum always mean that the algorithm has failed? Explain.
- No.
- If the local maximum is also the global maximum and satisfies the goal condition, then it is a successful solution.
- However, in N-Queens, a local maximum often still has conflicts; in that case the algorithm has failed to reach a valid state.

### Question 6: For N = 8, how many neighbors does each state have in our implementation?
- Each queen can move to any of the 7 other rows in its column.
- There are 8 columns, so the total number of neighbors is:
  - 8 × 7 = 56 neighbors

### Question 7: What does the evaluation-score graph tell you about the behavior of Hill-Climbing?
- The graph shows whether the algorithm is improving the state over time.
- The score rises only when the algorithm moves to a better neighbor.
- When no neighbor improves the current score, the search stops.
- If the final score is 0, the algorithm found a valid solution.

### Question 8: Explain probabilistic completeness in your own words.
- An algorithm is probabilistically complete if, with repeated independent attempts, the probability of finding a solution approaches 1 as the number of attempts increases.
- In simple words: if you keep trying from different random starts, your chance of eventually succeeding becomes very high.

### Question 9: Why does Random-Restart Hill-Climbing improve the chance of finding a solution?
- Because each restart begins from a different random state.
- This reduces the chance that the search always gets trapped in the same local maximum.
- By trying many different starts, the algorithm has more chances to encounter a successful path.

### Question 10: Does probabilistic completeness guarantee that a solution will be found within 100 restarts? Why or why not?
- No.
- Probabilistic completeness means the probability of success approaches 1 as the number of restarts grows, but it does not guarantee success in any fixed number of attempts.
- With 100 restarts, there may still be a nonzero probability of failure.

## 18) Extension Task: Solve N = 20

Use this code:

```python
solution, scores = hill_climbing(20)
print("Final state:", solution)
print("Conflicts:", count_conflicts(solution))
print("Iterations:", len(scores))
print("Solved:", count_conflicts(solution) == 0)
```

Random-restart version:

```python
solution, restarts, best_scores = random_restart_hill_climbing(20, max_restarts=200)
print("Final state:", solution)
print("Conflicts:", count_conflicts(solution))
print("Restarts:", restarts)
print("Solved:", count_conflicts(solution) == 0)
```

What to record:
- Number of conflicts in the final state
- Number of iterations in basic Hill-Climbing
- Number of restarts needed in Random-Restart Hill-Climbing
- Whether a valid solution was found

Discussion:
- As N increases, the search space becomes much larger.
- The chance of getting stuck at a local maximum increases.
- Random restarts help, but larger N often requires more restarts or can still fail within a fixed limit.

### 19) Final learning outcomes
- Hill-Climbing is a greedy, local search method.
- It is fast and simple, but it can get stuck at local maxima.
- Random-Restart Hill-Climbing improves the chance of success by trying multiple starting states.
- A valid N-Queens solution is a state with zero conflicts.
- The algorithm is not guaranteed to solve the problem in one run, but it often succeeds when restarted.

