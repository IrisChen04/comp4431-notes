---
layout: page
title: Lecture 2
permalink: /lec2/
nav_order: 3
---

# Lec 2 Heuristic Search

## 1. Agents in AI

**Agent**: An artificial entity that is capable of:
- **Perceiving** their surroundings using sensors (observe, like a processor)
- **Making decisions** based on observations
- **Taking actions** in response using actuators (do actions to the environment)

**Example**: Vacuum cleaner
- Perceives: which room, clean or dirty
- Decides: move right/left, suck the dust
- Acts: executes the movement and cleaning

---

## 2. Rational Agent

**Definition**: An agent that does the right thing!
- Acts to achieve the **best outcome**
- Different environments require different agents

### Characteristics of Rational Agents:

1. **Distinct from omniscience** (doesn't need to know everything)
2. **Autonomous** - behavior is determined by its own experience
3. Can perform actions to **modify future percepts** to obtain useful information

---

## 3. Search Strategies

### 3.1 Uninformed Search

**Definition**: Searches through all possible solutions **without additional knowledge** about the search space.

#### Breadth-First Search (BFS)
- Expands the **shallowest unexpanded node** first
- Explores level by level
- Uses a **queue** data structure

#### Depth-First Search (DFS)

![My image description](/assets/images/dfs_eg1.png){:style="width: 50%;"}



- Expands the **deepest unexpanded node** first
- Explores as far as possible along each branch before backtracking
- Uses a **stack** or recursion

### 3.2 Informed Search (Heuristic Search)

**Definition**: Uses **additional knowledge** (heuristics) to guide the search toward the goal more efficiently.

#### A* Search Algorithm

**Evaluation Function**: $$f(n) = g(n) + h(n)$$

Where:
- $$g(n)$$: **Actual cost** from start to node n (cost so far)
- $$h(n)$$: **Estimated cost** from node n to goal (heuristic)
- $$f(n)$$: **Total estimated cost** of path through node n to goal

**Key Concept - Admissible Heuristic**:
- A heuristic $$h(n)$$ is **admissible** if it **never overestimates** the actual cost
- Formally: $$h(n) \leq h^*(n)$$, where $$h^*(n)$$ is the true cost to reach the goal

---

## 4. Heuristics

**Definition**: A problem-solving method that uses shortcuts to produce good-enough solutions in limited time.

**Characteristics**:
- May not always find the **optimal solution**
- Ranks alternatives based on available information
- Based on intuition, experience, or common sense

### Issues with Heuristic Search:

1. **May lead to local minimum** (2nd or 3rd best solution)
2. **May reach dead ends** (no solution)
3. **Depends on heuristic function definition** - poor heuristics can mislead the search

**Example Issue**: If straight-line distance from B→G is underestimated as 10, the algorithm might expand node B instead of the better choice E.

---

## 5. Search Strategy Evaluation Criteria

1. **Completeness**: Does it always find a solution if one exists?
2. **Time complexity**: Number of nodes generated
3. **Space complexity**: Maximum number of nodes in memory
4. **Optimality**: Does it always find the least-cost solution?

### Comparison:

| Strategy | Type        | Optimal?                           |
|----------|-------------|------------------------------------|
| BFS      | Uninformed  | Yes (for unweighted graphs)        |
| DFS      | Uninformed  | No                                 |
| A*       | Informed    | Yes (with admissible heuristic)    |

---

## 6. Other Applications: 8-Puzzle

**Problem**: Sliding-tile puzzle where the empty space can move in 4 directions (Up, Down, Left, Right)

**Goal**: Rearrange tiles from initial state to goal state

**Can use**: BFS, DFS, or A* search strategies

---

## 7. Technological Trends in AI Agents

### Evolution Stages:

#### 1. Symbolic Agent Stage
- Uses logical rules and symbolic representations
- Example: Knowledge-based expert systems

#### 2. Reinforcement Learning-Based Agent Stage
- Agents learn through interaction with environment
- Goal: Maximize cumulative rewards
- Agent receives state $$s_t$$, takes action $$A_t$$, receives reward $$R_t$$

#### 3. Large Language Model (LLM)-Based Agent Stage
- Uses LLMs as the "brain" or controller
- Key capabilities:
  - **Reasoning and planning** (like symbolic agents)
  - **Interactive capabilities** with environment
  - **Few-shot and zero-shot generalization**

#### Key Components of LLM-Based Agents:
- **Memory**: Preserves knowledge, experience, and historical information
- **Planning**: Devises action sequences based on objectives and constraints
- **Tool use**: APIs, calculators, code interpreters, physical actions
