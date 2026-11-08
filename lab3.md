# Lab-3: Informed Search & Heuristics
### Cell 6 — Misplaced Tiles

```python name=misplaced_tiles.py
def misplaced_tiles(state):
    """Count out-of-place numbered tiles, excluding the blank."""
    return sum(
        1
        for i, tile in enumerate(state)
        if tile != 0 and tile != GOAL_STATE[i]
    )
```

### Cell 7 — Manhattan Distance

```python name=manhattan_distance.py
def manhattan_distance(state):
    """Sum each numbered tile's grid distance from its goal position."""
    distance = 0

    for current_index, tile in enumerate(state):
        if tile == 0:
            continue

        current_row, current_col = divmod(current_index, 3)
        goal_index = tile - 1
        goal_row, goal_col = divmod(goal_index, 3)

        distance += abs(current_row - goal_row) + abs(current_col - goal_col)

    return distance
```

### Cell 10 — A* Search

```python name=a_star_search.py
def a_star_search(start_state, heuristic_fn):
    """
    Return a dict containing the solution path, cost, and nodes expanded.
    """
    counter = itertools.count()
    frontier = []
    heapq.heappush(
        frontier,
        (heuristic_fn(start_state), next(counter), 0, start_state, [])
    )

    best_g = {start_state: 0}
    nodes_expanded = 0

    while frontier:
        f, _, g, state, path = heapq.heappop(frontier)

        # Ignore an outdated queue entry if a shorter route was found later.
        if g > best_g.get(state, float("inf")):
            continue

        nodes_expanded += 1

        if is_goal(state):
            return {
                "path": path,
                "cost": g,
                "nodes_expanded": nodes_expanded,
            }

        for neighbor_state, action in get_neighbors(state):
            new_g = g + 1

            if new_g < best_g.get(neighbor_state, float("inf")):
                best_g[neighbor_state] = new_g
                new_f = new_g + heuristic_fn(neighbor_state)
                heapq.heappush(
                    frontier,
                    (
                        new_f,
                        next(counter),
                        new_g,
                        neighbor_state,
                        path + [action],
                    ),
                )

    return {"path": None, "cost": None, "nodes_expanded": nodes_expanded}
```

The `nodes_expanded` count above excludes outdated entries that are popped and skipped; it counts valid nodes processed by the search.

## Written answers

## Why must both heuristics be zero at the goal? 
The true remaining cost at the goal is zero. An admissible heuristic cannot overestimate that cost, and these heuristics are nonnegative, so they must return zero there. If a heuristic returned a positive value at the goal, it would be inadmissible. More generally, an inadmissible heuristic can cause A* to choose a goal before finding the least-cost solution.

## Why does the gap between heuristics grow with scramble depth? 
Manhattan Distance uses how far each tile is from its goal, while Misplaced Tiles only records whether each tile is wrong. The extra distance information helps A* prioritize more promising states. As puzzles get harder, the weaker heuristic tends to leave many more states looking equally promising, so the search can expand substantially more nodes. The exact point where the gap becomes clear depends on the generated puzzles and trial results.

## Why is a higher admissible heuristic still admissible, and why can it reduce expansions? 
Admissibility means a heuristic never exceeds the true remaining cost. Manhattan Distance is at least Misplaced Tiles for every state, but it still does not exceed the true cost: each move shifts one numbered tile by one grid step, so it can reduce the total Manhattan distance by at most one. A higher admissible estimate is more informative and can rule out more states as unable to lead to a cheaper solution, usually reducing expansions. The precise number of expansions can depend on tie-breaking and the puzzle.

## Optional comparison:
Manhattan Distance will generally solve deeper puzzles with fewer expansions than Misplaced Tiles, though both may become slow as difficulty increases.

## For your results table

Run Cell 16 to produce the actual table and chart for your trials. The exact node counts depend on the randomly generated scrambles, so they can’t be filled in reliably in advance. You can also check that the heuristics return equal optimal solution costs for every trial by adding this assertion inside the loop in Cell 15:

```python name=cost_check.py
assert result_misplaced["cost"] == result_manhattan["cost"]
```

One caveat: `generate_scramble(num_moves)` makes a random walk of that many moves; it can undo previous moves. So `num_moves` is the walk length, not necessarily the puzzle’s optimal solution depth.
