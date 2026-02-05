---
layout: page
title: Lecture 3
permalink: /lec3/
nav_order: 4
---

# Expert System and Games

#   Knowledge based agent (KBA)
##  Knowledge base and representation

# Inference Engine
##  Forward Chaining

### **What is Forward Chaining?**

**Forward Chaining** is a **data-driven** inference method that starts with known facts and applies inference rules to derive new facts until a goal is reached or no more inferences can be made.


### 🎯 **Core Concepts Deep Dive**

#### **1. Data-Driven Reasoning**

**What it means**: The inference process is **triggered by available data**, not by a specific goal.

**Contrast with Backward Chaining**:
| Forward Chaining | Backward Chaining |
|:-----------------|:------------------|
| **Data → Goal** | **Goal → Data** |
| "What can I conclude from these facts?" | "What facts do I need to prove this goal?" |
| Bottom-up | Top-down |
| Breadth-first | Depth-first |

**Real-world analogy**:
- **Forward**: You have ingredients (facts) → figure out what dishes you can make (conclusions)
- **Backward**: You want pizza (goal) → figure out what ingredients you need (facts)


#### **2. Bottom-Up Approach**

**Visualization**:

```
Level 3:  [Thunderstorms likely] ← GOAL REACHED
              ↑
Level 2:  [Weather is hot] [Atmosphere is humid]
              ↑                    ↑
Level 1:  [Temp = 92°F]    [Humidity = 70%]
          (Initial Facts)
```

The system **builds upward** from base facts to higher-level conclusions.


#### **3. Breadth-First Search Strategy**

**Important distinction**: Forward chaining explores rules **level by level**, not depth-first.

**Example**:
```
Facts: A, B

Rules:
R1: IF A THEN C
R2: IF B THEN D
R3: IF C AND D THEN E

Execution Order (BFS):
1. Apply R1 → derive C
2. Apply R2 → derive D (same level as C)
3. Apply R3 → derive E (next level)
```

**NOT depth-first** (which would be: R1 → try to use C immediately before checking R2)


#### **4. Possibility of Irrelevant Rules**

**Critical insight**: Forward chaining can be **inefficient** because it may derive facts that don't contribute to any useful goal.

**Example**:
```
Goal: Determine if it will rain

Facts: Temperature = 92°F, Humidity = 70%, Wind = 5mph

Rules:
R1: IF Temp > 90 THEN Hot
R2: IF Humidity > 65 THEN Humid
R3: IF Hot AND Humid THEN Rain
R4: IF Wind < 10 THEN Calm  ← IRRELEVANT!
R5: IF Calm THEN Good_for_sailing ← IRRELEVANT!
```

Forward chaining will apply R4 and R5 even though they don't help determine rain! This is the **inefficiency** mentioned in your slides.

---

### 🔄 **Forward Chaining Algorithm (Formal)**

#### **Pseudocode**:

```python
function FORWARD_CHAINING(KB, facts):
    """
    KB: Knowledge Base (set of rules)
    facts: Initial facts (working memory)
    """
    inferred = set()  # Track what we've derived
    
    while True:
        new_facts = set()
        
        # Check each rule in KB
        for rule in KB:
            # If rule's conditions are satisfied by current facts
            if rule.conditions ⊆ facts and rule not in inferred:
                # Add conclusion to facts
                new_facts.add(rule.conclusion)
                inferred.add(rule)
                print(f"Applied {rule}: derived {rule.conclusion}")
        
        # If no new facts derived, stop
        if new_facts is empty:
            break
        
        # Add new facts to working memory
        facts = facts ∪ new_facts
    
    return facts
```

---

#### Detailed Weather Example Walkthrough

**Knowledge Base**:
- R1: IF Temp > 90°F THEN Hot
- R2: IF Humidity > 65% THEN Humid
- R3: IF Hot AND Humid THEN Thunderstorms

**Initial Facts**:
- F1: Temp = 92°F
- F2: Humidity = 70%

##### Execution Trace

**Iteration 1**

Working Memory: `{Temp=92°F, Humidity=70%}`

| Rule | Condition | Evaluation | Action | Result |
|:-----|:----------|:-----------|:-------|:-------|
| **R1** | Temp > 90°F? | 92 > 90 ✅ TRUE | Add "Hot" | **Fire R1** ✅ |
| **R2** | Humidity > 65%? | 70 > 65 ✅ TRUE | Add "Humid" | **Fire R2** ✅ |
| **R3** | Hot AND Humid? | Hot ❌, Humid ❌ | None | Skip |

New Facts Derived: `{Hot, Humid}`

Updated Working Memory: `{Temp=92°F, Humidity=70%, Hot, Humid}`

**Iteration 2**

Working Memory: `{Temp=92°F, Humidity=70%, Hot, Humid}`

| Rule | Condition | Evaluation | Action | Result |
|:-----|:----------|:-----------|:-------|:-------|
| **R1** | Already fired | Skip | - | - |
| **R2** | Already fired | Skip | - | - |
| **R3** | Hot AND Humid? | Hot ✅, Humid ✅ | Add "Thunderstorms" | **Fire R3** ✅ |

New Facts Derived: `{Thunderstorms}`

Updated Working Memory: `{Temp=92°F, Humidity=70%, Hot, Humid, Thunderstorms}`

**Iteration 3**

Working Memory: `{Temp=92°F, Humidity=70%, Hot, Humid, Thunderstorms}`

All rules already fired, no new conditions match.

New Facts Derived: `{}` (empty)

**STOP** - No new inferences possible
{: .label .label-red }

**Final Conclusion**: Thunderstorms are likely to develop ✅
{: .text-green-300 }



---

### 🎓 **Advanced Exercises for A-Grade Students**

#### **Exercise 1: Medical Diagnosis System** ⭐⭐⭐

**Knowledge Base**:
```
R1: IF Fever AND Cough THEN Flu_Suspected
R2: IF Fever AND Rash THEN Measles_Suspected
R3: IF Flu_Suspected AND Negative_Flu_Test THEN Cold
R4: IF Measles_Suspected AND Spots THEN Measles
R5: IF Fever THEN Check_Temperature
R6: IF Temperature > 102°F THEN High_Fever
R7: IF High_Fever AND Cough THEN Pneumonia_Risk
```

**Initial Facts**:
```
Fever = True
Cough = True
Temperature = 103°F
Negative_Flu_Test = True
```

**Tasks**:
1. **Trace the forward chaining process** step-by-step (show each iteration)
2. **List all derived facts** in order
3. **Identify which rules were fired** and in what order
4. **Which facts are irrelevant** to the final diagnosis?
5. **Draw the inference graph** showing fact dependencies

<details>
<summary>💡 Click for Solution</summary>

**Iteration 1**:
- R1 fires: Fever ✅ AND Cough ✅ → **Flu_Suspected**
- R5 fires: Fever ✅ → **Check_Temperature**
- New facts: {Flu_Suspected, Check_Temperature}

**Iteration 2**:
- R3 fires: Flu_Suspected ✅ AND Negative_Flu_Test ✅ → **Cold**
- R6 fires: Temperature > 102°F ✅ → **High_Fever**
- New facts: {Cold, High_Fever}

**Iteration 3**:
- R7 fires: High_Fever ✅ AND Cough ✅ → **Pneumonia_Risk**
- New facts: {Pneumonia_Risk}

**Iteration 4**:
- No new rules fire → **STOP**

**Final Facts**: {Fever, Cough, Temperature=103°F, Negative_Flu_Test, Flu_Suspected, Check_Temperature, Cold, High_Fever, Pneumonia_Risk}

**Rules Fired**: R1, R5, R3, R6, R7 (in this order)

**Irrelevant Facts**: R2 and R4 never fired (no Rash or Spots)

**Key Insight**: The system derived **both Cold AND Pneumonia_Risk** - this shows forward chaining can produce **multiple conclusions**!

</details>

---

#### **Exercise 2: Conflict Resolution** ⭐⭐⭐⭐

**Scenario**: Multiple rules can fire simultaneously. How do we choose?

**Knowledge Base**:
```
R1: IF A THEN B (Priority: 3)
R2: IF A THEN C (Priority: 1)
R3: IF B THEN D (Priority: 2)
R4: IF C THEN E (Priority: 4)
```

**Initial Facts**: {A}

**Tasks**:
1. **Apply forward chaining with priority-based conflict resolution** (highest priority first)
2. **Compare with FIFO conflict resolution** (first rule in KB fires first)
3. **Compare with recency-based resolution** (rules matching most recent facts fire first)
4. **Explain how different strategies affect efficiency**

<details>
<summary>💡 Click for Solution</summary>

**Strategy 1: Priority-Based** (Highest first)
- Iteration 1: R1 fires (priority 3) → B
- Iteration 2: R3 fires (priority 2) → D
- Result: {A, B, D}
- R2 and R4 never fire!

**Strategy 2: FIFO** (Order in KB)
- Iteration 1: R1 fires → B
- Iteration 2: R2 fires → C, R3 fires → D
- Iteration 3: R4 fires → E
- Result: {A, B, C, D, E}

**Strategy 3: Recency-Based**
- Iteration 1: R1 and R2 match A (most recent), R1 fires first → B
- Iteration 2: R3 matches B (most recent) → D
- Iteration 3: R2 matches A → C
- Iteration 4: R4 matches C (most recent) → E
- Result: {A, B, D, C, E}

**Key Insight**: Conflict resolution strategy **dramatically affects** what gets inferred!

</details>

---

#### **Exercise 3: Efficiency Analysis** ⭐⭐⭐⭐⭐

**Knowledge Base** (20 rules):
```
R1: IF A THEN B
R2: IF B THEN C
R3: IF C THEN D
...
R10: IF J THEN K
R11: IF X THEN Y (irrelevant branch)
R12: IF Y THEN Z (irrelevant branch)
...
R20: IF P THEN Q (irrelevant branch)
```

**Initial Facts**: {A, X, P}

**Tasks**:
1. **Calculate the number of rule matches** checked in forward chaining
2. **Compare with backward chaining** if goal is K
3. **Propose an optimization** to avoid irrelevant rules
4. **Implement a "relevance filter"** that only considers rules that could lead to a specific goal

<details>
<summary>💡 Click for Solution</summary>

**Forward Chaining**:
- Checks ALL 20 rules in each iteration
- Fires: R1→R2→R3→...→R10 (10 rules)
- Also fires: R11→R12→...→R20 (10 irrelevant rules)
- Total rule checks: 20 × 10 iterations = **200 checks**
- Efficiency: **50%** (half the rules are irrelevant)

**Backward Chaining** (Goal: K):
- Start with K, work backwards
- Only checks: R10→R9→...→R1 (10 rules)
- Total rule checks: **10 checks**
- Efficiency: **100%** (all rules relevant)

**Optimization: Goal-Directed Forward Chaining**
```python
def relevant_rules(KB, goal):
    """Filter rules that could contribute to goal"""
    relevant = set()
    to_check = {goal}
    
    while to_check:
        fact = to_check.pop()
        for rule in KB:
            if rule.conclusion == fact:
                relevant.add(rule)
                to_check.update(rule.conditions)
    
    return relevant
```

**Key Insight**: Pure forward chaining is **inefficient for goal-directed reasoning**. Hybrid approaches (like **RETE algorithm**) are used in production systems.

</details>

---

#### **Exercise 4: Cycle Detection** ⭐⭐⭐⭐

**Knowledge Base with potential cycles**:
```
R1: IF A THEN B
R2: IF B THEN C
R3: IF C THEN A  ← Creates cycle!
R4: IF C THEN D
```

**Initial Facts**: {A}

**Tasks**:
1. **Trace forward chaining** - does it terminate?
2. **Explain why cycles don't cause infinite loops** in standard forward chaining
3. **Modify the algorithm** to detect when a cycle is encountered
4. **What if rules can retract facts?** (non-monotonic reasoning)

<details>
<summary>💡 Click for Solution</summary>

**Trace**:
- Iteration 1: R1 fires (A → B), new fact: B
- Iteration 2: R2 fires (B → C), new fact: C
- Iteration 3: R3 fires (C → A), but A already exists! No new fact.
- Iteration 3: R4 fires (C → D), new fact: D
- Iteration 4: No new facts → **STOP**

**Why it terminates**:
- Forward chaining only adds facts, never removes them (**monotonic**)
- R3 tries to derive A, but A is already in working memory
- No **new** facts = termination condition met

**Cycle Detection**:
```python
if rule.conclusion in facts:
    print(f"Cycle detected: {rule} derives existing fact")
    continue  # Skip this rule
```

**Non-Monotonic Reasoning**:
If rules can retract facts (e.g., "IF C THEN NOT A"), cycles CAN cause infinite loops:
- A → B → C → NOT A → (A is removed) → B is removed → C is removed → A is added again → ...

This requires **Truth Maintenance Systems (TMS)** to handle properly.

</details>

---

#### **Exercise 5: Real-World Application** ⭐⭐⭐⭐⭐

**Scenario**: Design a **Smart Home Automation System** using forward chaining.

**Requirements**:
- 15+ rules covering: security, energy, comfort
- Handle conflicting rules (e.g., "turn on AC" vs "save energy")
- Implement rule priorities
- Show complete trace for a scenario

**Example Rules**:
```
R1: IF Motion_Detected AND Night THEN Turn_On_Lights
R2: IF No_Motion_For_10min THEN Turn_Off_Lights
R3: IF Temperature > 75°F THEN Turn_On_AC
R4: IF Away_Mode AND Temperature > 80°F THEN Turn_On_AC
R5: IF Away_Mode THEN Energy_Saving_Mode
R6: IF Energy_Saving_Mode THEN Disable_AC
...
```

**Tasks**:
1. **Design complete rule set** (15+ rules)
2. **Implement conflict resolution** (what if R3 and R6 conflict?)
3. **Trace a scenario**: User leaves home at 2 PM, temperature rises to 82°F
4. **Analyze efficiency**: How many rules fire? Any irrelevant ones?
5. **Compare with backward chaining**: If goal is "Minimize_Energy_Cost"

<details>
<summary>💡 Click for Hints</summary>

**Conflict Resolution Strategies**:
- **Priority-based**: Safety > Comfort > Energy
- **Specificity**: More specific rules override general ones
- **Recency**: Most recent facts trigger rules first

**Optimization Ideas**:
- Group rules by domain (security, energy, comfort)
- Use **rule salience** (activation strength)
- Implement **rule inhibition** (one rule can block another)

**Expected Challenges**:
- Conflicting goals (comfort vs. energy saving)
- Cascading effects (one rule triggers many others)
- Determining when to stop (no clear "goal" in reactive systems)

</details>

---

### 🔬 **Comparison: Forward vs Backward Chaining**

| Aspect | Forward Chaining | Backward Chaining |
|:--------|:------------------|:-------------------|
| **Direction** | Facts → Conclusions | Goal → Facts |
| **Strategy** | Breadth-first | Depth-first |
| **Efficiency** | Can derive irrelevant facts | Only derives relevant facts |
| **Use Case** | Monitoring, diagnosis, reactive systems | Query answering, planning, theorem proving |
| **Example** | "What can I conclude?" | "Is this true?" |
| **Termination** | When no new facts can be derived | When goal is proved/disproved |
| **Memory** | Stores all derived facts | Stores only goal-relevant facts |

---


### 🎯 **Exam-Style Questions**

#### **Question 1** (10 points):
Given the following KB and facts, trace forward chaining and list all derived facts in order:

```
KB:
R1: IF A AND B THEN C
R2: IF C THEN D
R3: IF D AND E THEN F
R4: IF A THEN G

Facts: {A, B, E}
```

---

#### **Question 2** (15 points):
Explain why forward chaining may be inefficient for goal-directed reasoning. Provide an example with at least 5 rules where forward chaining derives facts that don't contribute to the goal.

---

#### **Question 3** (20 points):
Design a forward chaining system for a **traffic light controller**:
- Input: Sensor data (cars waiting, pedestrian button, emergency vehicle)
- Output: Light states (red/yellow/green for each direction)
- Requirements: 10+ rules, handle conflicts, ensure safety

---

### 📚 **Key Takeaways for A-Grade Understanding**

1. ✅ **Forward chaining is data-driven**: Starts with facts, derives conclusions
2. ✅ **Uses breadth-first search**: Explores all rules at each level before moving deeper
3. ✅ **Can be inefficient**: May derive irrelevant facts
4. ✅ **Best for**: Monitoring, reactive systems, diagnosis
5. ✅ **Optimization needed**: RETE algorithm, relevance filtering, conflict resolution
6. ✅ **Contrast with backward chaining**: Different use cases, efficiency trade-offs



## Backward Chaining
Game
MiniMax Algorithm
Alpha beta Pruning
