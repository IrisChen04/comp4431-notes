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

![My image description](/assets/images/bfs_eg1.png){:style="width: 110%;"}


##### Definition and Strategy

Breadth-First Search (BFS) is a systematic uninformed search algorithm that expands nodes in order of their depth from the root. The algorithm operates by:

1. Expanding the root node first
2. Expanding all successors of the root node
3. Expanding all successors of those nodes, proceeding level by level

The algorithm expands the **shallowest unexpanded node** first, ensuring complete exploration of each depth level before proceeding to the next.


##### Implementation

**Data Structure**: FIFO (First-In-First-Out) Queue

- Newly generated nodes (deeper levels) are added to the back of the queue
- Older nodes (shallower levels) at the front are expanded first
- This ensures level-by-level traversal of the search space

**Optimization Techniques**:

1. **Reached Set**: Maintains a set of reached states rather than a mapping, as any path to a previously reached state cannot be better when all actions have equal cost
2. **Early Goal Test**: Tests whether a node is a goal state immediately upon generation, rather than waiting until it is selected for expansion


##### Applicability

BFS is most appropriate for problems where:
- All actions have uniform cost
- The shallowest solution is desired
- Complete exploration is required


##### Complexity Analysis

Let $$b$$ denote the branching factor (average number of successors per state) and $$d$$ denote the depth of the shallowest goal node.

**Node Generation Pattern**:

For a uniform tree structure:
- Level 0: $$1$$ node (root)
- Level 1: $$b$$ nodes
- Level 2: $$b^2$$ nodes
- Level $$k$$: $$b^k$$ nodes

Total nodes generated to depth $$d$$:

$$N = 1 + b + b^2 + b^3 + \cdots + b^d = O(b^d)$$

**Formal Complexity**:

| Property            | Value          | Justification                                                                 |
|:--------------------|:---------------|:------------------------------------------------------------------------------|
| **Completeness**    | Yes            | Guaranteed to find a solution if one exists, even in infinite state spaces    |
| **Optimality**      | Yes*           | Finds solution with minimum number of actions when all actions have equal cost|
| **Time Complexity** | $$O(b^d)$$     | Must generate all nodes up to depth $$d$$                                     |
| **Space Complexity**| $$O(b^d)$$     | All generated nodes must be maintained in memory                              |

*Optimality holds only when all step costs are equal.
{: .note }


##### Practical Limitations

The exponential space complexity presents severe practical constraints:

**Example**: Consider a problem with:
- Branching factor $$b = 10$$
- Processing rate: $$10^6$$ nodes/second
- Memory requirement: 1 KB per node

| Depth $$d$$ | Time Required | Memory Required  |
|:------------|:--------------|:-----------------|
| 10          | ~3 hours      | ~10 terabytes    |
| 14          | ~3.5 years    | ~10 petabytes    |


##### Critical Observations

1. **Memory Bottleneck**: Space complexity is typically more restrictive than time complexity in practice
2. **Scalability**: Exponential complexity renders BFS impractical for large search spaces
3. **Motivation for Informed Search**: The limitations of BFS demonstrate the necessity for heuristic-guided search algorithms (e.g., A*) that can reduce the effective branching factor and make larger problem instances tractable


##### Conclusion

While BFS guarantees completeness and optimality (under uniform cost conditions), its exponential space requirements limit its applicability to small-scale problems. This fundamental limitation motivates the development of informed search strategies that leverage domain knowledge to guide exploration more efficiently.

---

#### Depth-First Search (DFS)

![Example 1 of DFS](/assets/images/dfs_eg1.png){:style="width: 50%;"}
![Example 2 of DFS](/assets/images/dfs_eg2.png){:style="width: 100%;"}



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
