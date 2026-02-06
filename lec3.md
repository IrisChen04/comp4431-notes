---
layout: page
title: Lecture 3
permalink: /lec3/
nav_order: 4
---

# Expert System and Games

#   Knowledge based agent (KBA)
## Knowledge-based Agent

**Definition**

An intelligent agent needs knowledge about the world for taking decisions and reasoning to act efficiently.

**Knowledge-based agents (KBA)** are those agents who have the capability of:

- Maintaining an internal state of knowledge
- Reasoning over that knowledge
- Updating their knowledge after observations
- Taking action

These agents can represent the world with some formal representation and act intelligently.

## Architecture of KBAs

KBAs are composed of **2 main parts**:

1. **Knowledge Base**
2. **Inference Engine**


![KBA Architecture](/assets/images/kba-architecture.png){:style="width: 100%;"}


The diagram shows the interaction flow:
- Environment provides input to the Inference Engine
- Inference Engine queries and updates the Knowledge Base
- Learning component updates the KB based on new information
- Inference Engine produces output actions

## Knowledge Base

**Knowledge base**: A central component/repository of all knowledge in the KBA

Key characteristics:

- A collection of sentences (here 'sentence' is a technical term and it is not identical to sentence in natural language like English)
- These sentences are expressed in a so-called **knowledge representation language**

**Construction Methods**:

In practice, the knowledge bases are built based on human/expert experience.

It can also be learned by the agent itself, but this requires more complex techniques like machine learning or reinforcement learning.
{: .note }

##Inference Rules

**One common way of knowledge base representation**

Also referred to as **Production Rules**
{: .label .label-blue }

**Structure**:

A production rule consists of (condition, decision) pairs which mean **"if condition then decision"**

The "decision" can also be the condition for another rule, not necessarily a real action to perform.
{: .note }

**Rule Firing**:

We say a rule is **fired** if its condition is fulfilled.

##### Example: Bus Riding Rules

A KB can have many rules, e.g.:

| Rule | Condition | Decision |
|:-----|:----------|:---------|
| R1 | IF (at bus stop AND bus arrives) | THEN (get into the bus) |
| R2 | IF (on the bus AND paid AND empty seat) | THEN (sit down) |
| R3 | IF (on bus AND unpaid) | THEN (pay charges) |
| R4 | IF (on the bus AND sit down) | THEN (play mobile phone) |
| R5 | IF (bus arrives at destination) | THEN (get down from the bus) |

**Inference Chain**:

As one rule is fired, it may trigger another rule to be fired. There can be a **chain reaction** (referred to as **inference chain**)!
{: .important }

For example:
1. At bus stop + bus arrives → R1 fires → get into bus
2. On bus + unpaid → R3 fires → pay charges
3. On bus + paid + empty seat → R2 fires → sit down
4. On bus + sit down → R4 fires → play mobile phone
5. Bus arrives at destination → R5 fires → get down from bus

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



**Backward Chaining** is a **goal-driven** inference method that starts with a hypothesis (goal) and works backward to find evidence (facts) that support it.


### **1. Goal-Driven Reasoning**

**What it means**: The inference process is **triggered by a query/goal**, not by available data.

**Process**:
```
Goal: "Does patient have influenza?"
    ↓
Ask: "What rules conclude influenza?"
    ↓
Find: Rule 1 (IF nassal_congestion AND viremia THEN influenza)
    ↓
New Sub-goals: "nassal_congestion?" AND "viremia?"
    ↓
Recursively prove each sub-goal...
```

---

### **2. Top-Down Approach**

**Visualization** (from your medical example):

```
Level 0:  [GOAL: Influenza?] ← START HERE
              ↓
Level 1:  [nassal_congestion?] [viremia?]
              ↓                    ↓
Level 2:  [runny_nose?]     [fever? achiness? cough?]
              ↓                    ↓
Level 3:  (Check facts)     [temperature>100? headache?]
              ↓                    ↓
Level 4:                    (Check facts)
```

The system **works downward** from goal to facts.

---

### **3. Depth-First Search Strategy**

**Critical distinction**: Backward chaining explores **one path completely** before trying alternatives.

**Example from your slides**:

**Knowledge Base**:
```
R1: IF nassal_congestion AND viremia THEN influenza
R2: IF runny_nose THEN nassal_congestion
R3: IF body_aches THEN achiness
R4: IF temperature > 100 THEN fever
R5: IF headache THEN achiness
R6: IF fever AND achiness AND cough THEN viremia
```

**Goal**: Prove influenza

**DFS Path**:
1. Try to prove influenza → need nassal_congestion AND viremia
2. **First**, fully explore nassal_congestion path:
   - Try R2 → need runny_nose
   - Check fact: runny_nose ✅ FOUND
   - nassal_congestion ✅ PROVED
3. **Then**, explore viremia path:
   - Try R6 → need fever AND achiness AND cough
   - Check cough ✅ FOUND
   - Try to prove fever → need temperature > 100
   - Check temperature = 101.7 ✅ FOUND
   - Try to prove achiness → need headache
   - Check headache ✅ FOUND
   - viremia ✅ PROVED
4. Both sub-goals proved → influenza ✅ PROVED

**NOT breadth-first** (which would check all sub-goals at same level simultaneously)

---

### **4. Possibility of Infinite Loops**

**Critical problem**: Backward chaining can get stuck in cycles!

**Example of problematic rules**:
```
R1: IF A THEN B
R2: IF B THEN C
R3: IF C THEN A  ← Creates cycle!

Goal: Prove A
→ Try R3: need C
  → Try R2: need B
    → Try R1: need A  ← LOOP! Back to original goal
```

**Solution strategies**:
1. **Track visited goals** (goal stack)
2. **Limit recursion depth**
3. **Detect cycles** and backtrack

---

### 🔄 **Backward Chaining Algorithm (Formal)**

 **Pseudocode**:

```python
function BACKWARD_CHAINING(KB, goal, facts, visited=set()):
    """
    KB: Knowledge Base (set of rules)
    goal: Goal to prove
    facts: Known facts
    visited: Goals already being explored (cycle detection)
    """
    
    # Base case 1: Goal is already a known fact
    if goal in facts:
        print(f"✅ {goal} is a known fact")
        return True
    
    # Base case 2: Cycle detection
    if goal in visited:
        print(f"⚠️ Cycle detected: {goal} already being explored")
        return False
    
    # Add goal to visited set
    visited.add(goal)
    
    # Find all rules that can prove this goal
    candidate_rules = [r for r in KB if r.conclusion == goal]
    
    if not candidate_rules:
        print(f"❌ No rules can prove {goal}")
        visited.remove(goal)
        return False
    
    # Try each rule (DFS)
    for rule in candidate_rules:
        print(f"🔍 Trying {rule} to prove {goal}")
        
        # Try to prove all premises
        all_premises_satisfied = True
        
        for premise in rule.premises:
            # Recursively prove each premise
            if not BACKWARD_CHAINING(KB, premise, facts, visited):
                all_premises_satisfied = False
                break
        
        if all_premises_satisfied:
            print(f"✅ {goal} proved using {rule}")
            visited.remove(goal)
            return True
    
    # No rule succeeded
    print(f"❌ Failed to prove {goal}")
    visited.remove(goal)
    return False
```

---

### 📊 **Detailed Analysis of Your Slide Examples**

 **Example 1: Medical Diagnosis (Backward Chaining)**

Let me trace through your slide's example in complete detail:

**Knowledge Base**:
```
R1: IF nassal_congestion AND viremia THEN influenza
R2: IF runny_nose THEN nassal_congestion
R3: IF body_aches THEN achiness
R4: IF temperature > 100 THEN fever
R5: IF headache THEN achiness
R6: IF fever AND achiness AND cough THEN viremia
```

**Facts**:
```
- runny_nose = True
- temperature = 101.7
- headache = True
- cough = True
```

**Goal**: Prove influenza

---

 **Execution Trace (Matching Your Slide)**:

**Cycle 1**:
- **Goal resolving**: influenza
- **Rule fired**: R1
- **Goals created**: nassal_congestion, viremia
- **Premise satisfied**: nil (none yet)
- **Action**: Push nassal_congestion and viremia onto goal stack

---

**Cycle 2**:
- **Goal resolving**: nassal_congestion (first sub-goal)
- **Rule fired**: R2
- **Goals created**: runny_nose, viremia (viremia still pending)
- **Premise satisfied**: runny_nose ✅ (found in facts)
- **Result**: nassal_congestion ✅ PROVED

---

**Cycle 3**:
- **Goal resolving**: viremia (second sub-goal)
- **Rule fired**: R6
- **Goals created**: fever, achiness, cough
- **Premise satisfied**: cough ✅ (found in facts)
- **Action**: Push fever and achiness onto goal stack

---

**Cycle 4**:
- **Goal resolving**: fever
- **Rule fired**: R4
- **Goals created**: temperature > 100, achiness (still pending)
- **Premise satisfied**: temperature > 100 ✅ (101.7 > 100)
- **Result**: fever ✅ PROVED

---

**Cycle 5** (First attempt):
- **Goal resolving**: achiness
- **Rule fired**: R3
- **Goals created**: body_aches, achiness
- **Premise satisfied**: nil ❌ (body_aches not in facts)
- **Result**: R3 FAILED, try alternative rule

---

**Cycle 5** (Second attempt):
- **Goal resolving**: achiness
- **Rule fired**: R5
- **Goals created**: headache
- **Premise satisfied**: headache ✅ (found in facts)
- **Result**: achiness ✅ PROVED

---

**Cycle 6**:
- **All sub-goals satisfied**:
  - nassal_congestion ✅
  - viremia ✅ (fever ✅, achiness ✅, cough ✅)
- **Conclusion**: influenza ✅ **PROVED**

---

 **Key Insights from Medical Example**:

1. **Depth-first exploration**: Fully resolved nassal_congestion before moving to viremia
2. **Backtracking**: When R3 failed to prove achiness, system tried R5
3. **Efficient**: Only checked rules relevant to the goal (didn't check irrelevant rules)
4. **Goal-directed**: Never derived facts that don't contribute to proving influenza

---

 📊 **Detailed Analysis: Query Example (Q2)**

Now let's trace both Forward and Backward Chaining for your query example:

**Knowledge Base**:

![Forward and Backward Chaining](/assets/images/forward-back.png){:style="width: 50%;"}


```
1. A (fact)
2. B (fact)
3. C (fact)
4. A ∧ B ⇒ D
5. B ∧ C ⇒ E
6. A ∧ C ⇒ F
7. A ∧ F ⇒ G
8. D ∧ F ⇒ K
9. G ∧ K ⇒ Q1
10. E ⇒ H
11. H ∧ C ⇒ Q2
```

**Query**: Q2

---

### **Forward Chaining Trace** (from your slide):

#### **Iteration 1** (Image a):
- **Check all rules with satisfied premises**
- R4: A ✅ AND B ✅ → **Derive D**
- R5: B ✅ AND C ✅ → **Derive E**
- R6: A ✅ AND C ✅ → **Derive F**
- **New facts**: {D, E, F}
- **Working memory**: {A, B, C, D, E, F}

#### **Iteration 2** (Image b):
- R7: A ✅ AND F ✅ → **Derive G**
- R8: D ✅ AND F ✅ → **Derive K**
- R10: E ✅ → **Derive H**
- **New facts**: {G, K, H}
- **Working memory**: {A, B, C, D, E, F, G, K, H}

#### **Iteration 3** (Image c):
- R9: G ✅ AND K ✅ → **Derive Q1**
- R11: H ✅ AND C ✅ → **Derive Q2** ✅ **QUERY FOUND!**
- **New facts**: {Q1, Q2}
- **Final working memory**: {A, B, C, D, E, F, G, K, H, Q1, Q2}

**Result**: Q2 found after 3 iterations
**Rules fired**: R4, R5, R6, R7, R8, R10, R9, R11 (8 rules)
**Facts derived**: D, E, F, G, K, H, Q1, Q2 (8 facts)

---

### **Backward Chaining Trace** (from your slide):

#### **Cycle 1** (Image a):
- **Goal**: Q2
- **Find rule**: R11 (H ∧ C ⇒ Q2)
- **Sub-goals**: H, C
- **Check C**: C ✅ in facts
- **New goal**: H

#### **Cycle 2** (Image b):
- **Goal**: H
- **Find rule**: R10 (E ⇒ H)
- **Sub-goal**: E
- **New goal**: E

#### **Cycle 3** (Image c):
- **Goal**: E
- **Find rule**: R5 (B ∧ C ⇒ E)
- **Sub-goals**: B, C
- **Check B**: B ✅ in facts
- **Check C**: C ✅ in facts
- **Result**: E ✅ PROVED

#### **Cycle 4** (Image d):
- **Backtrack to H**: E ✅ proved → H ✅ PROVED
- **Backtrack to Q2**: H ✅ AND C ✅ → Q2 ✅ **PROVED**

**Result**: Q2 proved in 4 cycles
**Rules fired**: R11, R10, R5 (3 rules only!)
**Facts derived**: E, H, Q2 (3 facts only!)

---

### **Comparison: Forward vs Backward for Q2**

| Metric | Forward Chaining | Backward Chaining |
|:--------|:------------------|:-------------------|
| **Rules fired** | 8 rules | 3 rules |
| **Facts derived** | 8 facts | 3 facts |
| **Iterations** | 3 iterations | 4 cycles |
| **Efficiency** | 37.5% (3/8 relevant) | 100% (all relevant) |
| **Irrelevant facts** | D, F, G, K, Q1 | None |
| **Best for** | "What can I conclude?" | "Is Q2 true?" |

**Key Insight**: For goal-directed queries, **backward chaining is much more efficient**! ✅

---

### 🎓 **Advanced Exercises**

 **Exercise 1: Trace Backward Chaining with Backtracking** ⭐⭐⭐⭐

**Knowledge Base**:
```
R1: IF A AND B THEN G
R2: IF C THEN G
R3: IF D THEN A
R4: IF E THEN A
R5: IF F THEN B
```

**Facts**: {C, E}

**Goal**: Prove G

**Tasks**:
1. **Trace backward chaining** showing ALL attempts (including failed paths)
2. **Show backtracking** when a path fails
3. **Draw the search tree** with successful and failed branches
4. **Count total rule checks** (including failed attempts)

<details>
<summary>💡 Click for Solution</summary>

**Trace**:

```
Goal: G
├─ Try R1: need A AND B
│  ├─ Sub-goal: A
│  │  ├─ Try R3: need D
│  │  │  └─ Check D: ❌ NOT in facts → FAIL
│  │  ├─ Try R4: need E
│  │  │  └─ Check E: ✅ in facts → A PROVED
│  │  └─ A ✅
│  ├─ Sub-goal: B
│  │  ├─ Try R5: need F
│  │  │  └─ Check F: ❌ NOT in facts → FAIL
│  │  └─ B ❌ FAILED
│  └─ R1 FAILED (B cannot be proved)
│
├─ Try R2: need C
│  └─ Check C: ✅ in facts → G PROVED ✅
│
└─ G ✅ PROVED via R2
```

**Search Tree**:
```
                G (goal)
               / \
              /   \
            R1     R2
           / \      |
          A   B     C ✅
         /|   |
       R3 R4  R5
        | |   |
        D E✅  F
        ❌    ❌
```

**Statistics**:
- Total rule checks: 6 (R1, R3, R4, R5, R2)
- Successful path: R2 → C
- Failed attempts: R1 (via R5), R3
- Backtracking points: 2 (after R3 fails, after R5 fails)

**Key Insight**: Backward chaining explores multiple paths and **backtracks** when a path fails. The order of rules matters for efficiency!

</details>

---

 **Exercise 2: Cycle Detection** ⭐⭐⭐⭐⭐

**Knowledge Base with cycles**:
```
R1: IF A THEN B
R2: IF B THEN C
R3: IF C THEN D
R4: IF D THEN B  ← Creates cycle B→C→D→B
R5: IF E THEN A
```

**Facts**: {E}

**Goal**: Prove D

**Tasks**:
1. **Trace backward chaining WITHOUT cycle detection** - what happens?
2. **Implement cycle detection** using a goal stack
3. **Show how the algorithm detects and handles the cycle**
4. **Modify KB to break the cycle** - what's the minimal change?

<details>
<summary>💡 Click for Solution</summary>

**Without Cycle Detection**:
```
Goal: D
├─ Try R3: need C
│  ├─ Try R2: need B
│  │  ├─ Try R1: need A
│  │  │  ├─ Try R5: need E
│  │  │  │  └─ Check E: ✅ → A PROVED
│  │  │  └─ A ✅
│  │  └─ B ✅
│  └─ C ✅
└─ D ✅ PROVED

Alternative path (if we tried R4):
Goal: D
├─ Try R4: need B
│  ├─ Try R1: need A ... (same as above)
│  └─ OR Try R4: need B  ← INFINITE LOOP!
```

**With Cycle Detection**:
```python
Goal Stack: []
Visited: {}

1. Push D onto stack: [D]
   Try R3: need C
   
2. Push C onto stack: [D, C]
   Try R2: need B
   
3. Push B onto stack: [D, C, B]
   Try R1: need A
   
4. Push A onto stack: [D, C, B, A]
   Try R5: need E
   Check E: ✅
   Pop A: [D, C, B]
   
5. B proved, pop B: [D, C]
6. C proved, pop C: [D]
7. D proved, pop D: []

If we tried R4 at step 3:
3. Push B onto stack: [D, C, B]
   Try R4: need B
   Check: B already in stack! ⚠️ CYCLE DETECTED
   Skip R4, try next rule (R1)
```

**Minimal Fix**:
Remove R4 or add a condition to break the cycle:
```
R4: IF D AND NOT(visited_B) THEN B
```

</details>

---

 **Exercise 3: Compare Efficiency** ⭐⭐⭐⭐

**Knowledge Base** (same as your Q2 example):
```
1. A, 2. B, 3. C
4. A ∧ B ⇒ D
5. B ∧ C ⇒ E
6. A ∧ C ⇒ F
7. A ∧ F ⇒ G
8. D ∧ F ⇒ K
9. G ∧ K ⇒ Q1
10. E ⇒ H
11. H ∧ C ⇒ Q2
```

**Tasks**:
1. **Trace backward chaining for Q1** (instead of Q2)
2. **Compare with forward chaining** for Q1
3. **Which is more efficient for Q1? Why?**
4. **Generalize**: When is backward chaining more efficient?

<details>
<summary>💡 Click for Solution</summary>

**Backward Chaining for Q1**:
```
Goal: Q1
├─ R9: need G AND K
   ├─ Sub-goal: G
   │  ├─ R7: need A AND F
   │  │  ├─ Check A: ✅
   │  │  ├─ Sub-goal: F
   │  │  │  ├─ R6: need A AND C
   │  │  │  │  ├─ Check A: ✅
   │  │  │  │  └─ Check C: ✅
   │  │  │  └─ F ✅
   │  │  └─ G ✅
   │  └─ G ✅
   ├─ Sub-goal: K
   │  ├─ R8: need D AND F
   │  │  ├─ Sub-goal: D
   │  │  │  ├─ R4: need A AND B
   │  │  │  │  ├─ Check A: ✅
   │  │  │  │  └─ Check B: ✅
   │  │  │  └─ D ✅
   │  │  └─ F already proved ✅
   │  └─ K ✅
   └─ Q1 ✅ PROVED
```

**Rules fired**: R9, R7, R6, R8, R4 (5 rules)
**Facts derived**: F, G, D, K, Q1 (5 facts)

**Forward Chaining for Q1** (from your slide):
- Rules fired: R4, R5, R6, R7, R8, R10, R9, R11 (8 rules)
- Facts derived: D, E, F, G, K, H, Q1, Q2 (8 facts)
- Irrelevant: E, H, Q2 (not needed for Q1)

**Efficiency Comparison**:

| Query | Forward (rules) | Backward (rules) | Winner |
|-------|----------------|------------------|--------|
| Q1 | 8 rules | 5 rules | Backward (62.5%) |
| Q2 | 8 rules | 3 rules | Backward (37.5%) |

**Generalization**:
- **Backward chaining is more efficient when**:
  - Goal is specific and well-defined
  - Many rules exist that don't lead to the goal
  - KB is large with many irrelevant rules
  
- **Forward chaining is more efficient when**:
  - Multiple goals need to be found
  - Most facts are relevant
  - System is reactive (monitoring, triggering)

</details>

---

 **Exercise 4: Real-World Application** ⭐⭐⭐⭐⭐

**Scenario**: Design a **Loan Approval System** using backward chaining.

**Goal**: Approve_Loan

**Requirements**:
- 15+ rules covering: credit score, income, debt ratio, employment
- Handle multiple paths to approval
- Show complete backward chaining trace
- Compare efficiency with forward chaining

**Example Rules**:
```
R1: IF Good_Credit AND Stable_Income THEN Approve_Loan
R2: IF Excellent_Credit THEN Approve_Loan
R3: IF Credit_Score > 700 THEN Good_Credit
R4: IF Credit_Score > 800 THEN Excellent_Credit
R5: IF Employment_Years > 2 AND Salary > 50K THEN Stable_Income
R6: IF Debt_Ratio < 0.3 AND Salary > 40K THEN Stable_Income
...
```

**Test Case**:
```
Facts:
- Credit_Score = 750
- Employment_Years = 3
- Salary = 60K
- Debt_Ratio = 0.25
```

**Tasks**:
1. **Design complete rule set** (15+ rules)
2. **Trace backward chaining** for Approve_Loan goal
3. **Show all backtracking** when paths fail
4. **Compare with forward chaining**: Which derives fewer facts?
5. **Handle conflicts**: What if multiple rules approve but with different terms?

<details>
<summary>💡 Click for Hints</summary>

**Rule Categories**:
- **Credit rules**: Score thresholds, history, defaults
- **Income rules**: Salary levels, stability, sources
- **Debt rules**: Ratios, types, payment history
- **Employment rules**: Years, industry, stability
- **Collateral rules**: Assets, value, liquidity

**Expected Challenges**:
- Multiple paths to approval (which to try first?)
- Failed paths requiring backtracking
- Conflicting evidence (good credit but high debt)
- Efficiency: backward chaining should check fewer rules

**Optimization Ideas**:
- Order rules by likelihood (try most common approvals first)
- Cache sub-goal results (if Good_Credit proved once, don't re-prove)
- Early termination (stop at first approval path)

</details>

---

### 🔬 **Deep Comparison: Forward vs Backward Chaining**

 **Comprehensive Comparison Table**:

| Aspect | Forward Chaining | Backward Chaining |
|--------|------------------|-------------------|
| **Direction** | Data → Goal | Goal → Data |
| **Trigger** | New facts arrive | Query is asked |
| **Search** | Breadth-first | Depth-first |
| **Efficiency** | Derives all possible facts | Only derives goal-relevant facts |
| **Best for** | Monitoring, reactive systems, multiple goals | Specific queries, theorem proving |
| **Memory** | High (stores all derived facts) | Lower (only goal-relevant facts) |
| **Loops** | No loops (monotonic) | Possible loops (needs cycle detection) |
| **Backtracking** | No backtracking | Yes, when path fails |
| **Completeness** | Finds all conclusions | Finds proof for specific goal |
| **Example** | "What diseases could this patient have?" | "Does this patient have influenza?" |

---

### **When to Use Each**:

**Use Forward Chaining when**:
✅ You want to find **all possible conclusions** from given facts
✅ System is **reactive** (monitoring, alerts, automation)
✅ Facts arrive **incrementally** over time
✅ Multiple goals need to be checked
✅ Example: Smart home automation, network monitoring, production systems

**Use Backward Chaining when**:
✅ You have a **specific goal** to prove
✅ KB is **large** with many irrelevant rules
✅ You want **efficient goal-directed reasoning**
✅ Explanation is important (trace shows proof path)
✅ Example: Medical diagnosis, loan approval, legal reasoning, theorem proving

---


# Game

Here's the content transcribed into Just the Docs markdown format:

### What Makes Games Special in AI?

**Multiagent Environment**:

- Multiple agents (players) interact
- Each agent's actions affect others' outcomes
- Strategic reasoning required: "If I do X, opponent will do Y, so I should do Z"

**Why Games are Ideal for AI**:

| Property | Explanation | Advantage for AI |
|:---------|:------------|:-----------------|
| **Easy to represent** | Discrete states (board positions) | Simple data structures |
| **Limited actions** | Small action space per state | Tractable search |
| **Precise rules** | Deterministic outcomes | No ambiguity |
| **Clear objectives** | Win/lose/draw | Easy to evaluate |

Real-world analogy: Games are like **controlled experiments** for AI - they have all the complexity of strategic reasoning without the messiness of real-world uncertainty.
{: .note }

### Formulating Games as Search Problems

#### Game Formulation Components

##### 1. Initial State $$S_0$$

**Definition**: The starting configuration of the game.

**Examples**:

- **Chess**: Standard starting position with all pieces
- **Tic-Tac-Toe**: Empty 3×3 board
- **Go**: Empty 19×19 board

**Representation**:

```python
# Tic-Tac-Toe initial state
initial_state = [
    [' ', ' ', ' '],
    [' ', ' ', ' '],
    [' ', ' ', ' ']
]
```

##### 2. Player(s) Function

**Definition**: Returns which player has the move in state $$s$$.

**Why it matters**: Games alternate between players (usually), so we need to track whose turn it is.

**Implementation**:

```python
def player(state):
    """Returns 'X' or 'O' depending on whose turn it is"""
    x_count = count_marks(state, 'X')
    o_count = count_marks(state, 'O')
    
    if x_count == o_count:
        return 'X'  # X goes first
    else:
        return 'O'
```

Key insight: In Tic-Tac-Toe, if X and O have equal counts, it's X's turn. Otherwise, it's O's turn.
{: .note }

##### 3. Actions(s) Function

**Definition**: Returns the set of **legal moves** available in state $$s$$.

**Example - Tic-Tac-Toe**:

```python
def actions(state):
    """Returns set of (row, col) tuples for empty cells"""
    possible_actions = set()
    
    for i in range(3):
        for j in range(3):
            if state[i][j] == ' ':
                possible_actions.add((i, j))
    
    return possible_actions

# Example:
# State:  X | O |  
#        -----------
#           |   |  
#        -----------
#           |   |  
# Actions: {(0,2), (1,0), (1,1), (1,2), (2,0), (2,1), (2,2)}
```

**Chess**: Much more complex! Must consider piece movement rules, checks, pins, castling rights, en passant, promotion, etc.

##### 4. Result(s, a) Function

**Definition**: The **transition model** - returns the new state after taking action $$a$$ in state $$s$$.

**Critical property**: Must be **deterministic** (no randomness in classic games).

**Implementation**:

```python
def result(state, action):
    """Returns new state after making action"""
    # Create a deep copy to avoid modifying original state
    new_state = copy.deepcopy(state)
    
    row, col = action
    current_player = player(state)
    
    # Make the move
    new_state[row][col] = current_player
    
    return new_state
```

Important: Always create a **new state** - never modify the original! This allows backtracking in search.
{: .important }

##### 5. Terminal-Test(s) Function

**Definition**: Returns `True` if the game is over in state $$s$$.

**Game-ending conditions for Tic-Tac-Toe**:

```python
def terminal_test(state):
    """Check if game is over"""
    # Check for winner
    if winner(state) is not None:
        return True
    
    # Check for draw (board full)
    if all(cell != ' ' for row in state for cell in row):
        return True
    
    return False

def winner(state):
    """Returns 'X', 'O', or None"""
    # Check rows
    for row in state:
        if row[0] == row[1] == row[2] != ' ':
            return row[0]
    
    # Check columns
    for col in range(3):
        if state[0][col] == state[1][col] == state[2][col] != ' ':
            return state[0][col]
    
    # Check diagonals
    if state[0][0] == state[1][1] == state[2][2] != ' ':
        return state[0][0]
    if state[0][2] == state[1][1] == state[2][0] != ' ':
        return state[0][2]
    
    return None
```

##### 6. Utility(s, p) Function

**Definition**: Returns the **payoff** for player $$p$$ when game ends in terminal state $$s$$.

**Also called**: Objective function, Payoff function, Value function

**Standard values**:

| Outcome | Utility for Winner | Utility for Loser | Utility for Draw |
|:--------|:-------------------|:------------------|:-----------------|
| **Win** | +1 | -1 | 0 |
| **Loss** | -1 | +1 | 0 |
| **Draw** | 0 | 0 | 0 |

**Implementation**:

```python
def utility(state, player):
    """Returns utility value for player in terminal state"""
    w = winner(state)
    
    if w == player:
        return 1  # Win
    elif w is None:
        return 0  # Draw
    else:
        return -1  # Loss
```

**Key property**: **Zero-sum game** - one player's gain is another's loss:

$$\text{Utility}(s, \text{MAX}) + \text{Utility}(s, \text{MIN}) = 0$$

### Tic-Tac-Toe Example - Deep Dive

**Current State**:

```
X | O |  
---------
  |   |  
---------
  |   |  
```

**Evaluation Function** (for non-terminal states):

$$e(p) = (\text{open lines for player}) - (\text{open lines for opponent})$$

Where "open line" = row, column, or diagonal that could still result in three-in-a-row.

#### Detailed Calculation

**For X (the player)**:

**Rows**:
- Row 0: `X | O | _` → ❌ Blocked by O
- Row 1: `_ | _ | _` → ✅ Open for X
- Row 2: `_ | _ | _` → ✅ Open for X

**Columns**:
- Col 0: `X | _ | _` → ✅ Open for X
- Col 1: `O | _ | _` → ❌ Blocked by O
- Col 2: `_ | _ | _` → ✅ Open for X

**Diagonals**:
- Main diagonal: `X | _ | _` → ✅ Open for X
- Anti-diagonal: `_ | _ | _` → ✅ Open for X

**Total for X**: 2 + 2 + 2 = **6 open lines**

**For O (the opponent)**:

**Rows**:
- Row 0: `X | O | _` → ❌ Blocked by X
- Row 1: `_ | _ | _` → ✅ Open for O
- Row 2: `_ | _ | _` → ✅ Open for O

**Columns**:
- Col 0: `X | _ | _` → ❌ Blocked by X
- Col 1: `O | _ | _` → ✅ Open for O
- Col 2: `_ | _ | _` → ✅ Open for O

**Diagonals**:
- Main diagonal: `X | _ | _` → ❌ Blocked by X
- Anti-diagonal: `_ | _ | _` → ✅ Open for O

**Total for O**: 2 + 2 + 1 = **5 open lines**

**Final Evaluation**:

$$e(X) = 6 - 5 = 1$$

**Interpretation**: X has a **slight advantage** (one more winning opportunity than O).

#### Why This Heuristic Works

1. **Captures strategic value**: More open lines = more winning chances
2. **Simple to compute**: $$O(1)$$ for 3×3 board
3. **Admissible**: Reflects actual game position
4. **Symmetric**: Works for both players

**Limitation**: Doesn't consider **threats** (lines with 2 marks) which are more valuable than lines with 1 mark.

**Better heuristic**:

$$e(p) = 3 \times (\text{lines with 2 marks}) + 1 \times (\text{lines with 1 mark}) - \text{same for opponent}$$

### Game Playing Strategy - Minimax Algorithm

#### Core Idea

"Maximize your winning possibility assuming the opponent will try to minimize it"
{: .text-green-300 }

This is the essence of **adversarial search**.

#### Minimax Principle

**Two types of players**:

- **MAX player**: Tries to maximize utility (you)
- **MIN player**: Tries to minimize utility (opponent)

**Recursive definition**:

$$\text{Minimax}(s) = \begin{cases}
\text{Utility}(s) & \text{if Terminal-Test}(s) \\
\max_{a \in \text{Actions}(s)} \text{Minimax}(\text{Result}(s, a)) & \text{if Player}(s) = \text{MAX} \\
\min_{a \in \text{Actions}(s)} \text{Minimax}(\text{Result}(s, a)) & \text{if Player}(s) = \text{MIN}
\end{cases}$$

#### Minimax Algorithm (Formal)

```python
def minimax(state):
    """Returns the optimal move for current player"""
    
    if terminal_test(state):
        return utility(state, MAX_PLAYER)
    
    if player(state) == MAX_PLAYER:
        return max_value(state)
    else:
        return min_value(state)

def max_value(state):
    """MAX player tries to maximize utility"""
    if terminal_test(state):
        return utility(state, MAX_PLAYER)
    
    v = -infinity
    for action in actions(state):
        v = max(v, min_value(result(state, action)))
    
    return v

def min_value(state):
    """MIN player tries to minimize utility"""
    if terminal_test(state):
        return utility(state, MAX_PLAYER)
    
    v = +infinity
    for action in actions(state):
        v = min(v, max_value(result(state, action)))
    
    return v
```

#### Minimax Decision

To actually **choose a move**, we need to track which action leads to the best value:

```python
def minimax_decision(state):
    """Returns the best action for current player"""
    
    if player(state) == MAX_PLAYER:
        best_value = -infinity
        best_action = None
        
        for action in actions(state):
            value = min_value(result(state, action))
            if value > best_value:
                best_value = value
                best_action = action
        
        return best_action
    
    else:  # MIN player
        best_value = +infinity
        best_action = None
        
        for action in actions(state):
            value = max_value(result(state, action))
            if value < best_value:
                best_value = value
                best_action = action
        
        return best_action
```

#### Minimax Example - Tic-Tac-Toe

**Current state (X to move)**:

```
X | O | X
---------
O | X |  
---------
  |   | O
```

**Available actions**: (2, 0), (2, 1)

**Minimax tree**:

```
                    Current State (MAX)
                    /              \
            Action (2,0)        Action (2,1)
                /                    \
        X | O | X            X | O | X
        O | X |              O | X |  
        X |   | O            O |   | O
        (MIN's turn)         (MIN's turn)
            /|\                  /|\
          ...                  ...
```

**Evaluation**:
- Action (2, 0): Leads to X winning → Utility = +1
- Action (2, 1): Leads to draw or O winning → Utility ≤ 0

**Minimax decision**: Choose (2, 0) ✅

#### Properties of Minimax

| Property | Description |
|:---------|:------------|
| **Complete** | Yes (if tree is finite) |
| **Optimal** | Yes (against optimal opponent) |
| **Time Complexity** | $$O(b^m)$$ where $$b$$ = branching factor, $$m$$ = max depth |
| **Space Complexity** | $$O(bm)$$ for DFS implementation |

**Problem**: Exponential time complexity!
{: .label .label-red }

**Example**:
- Chess: $$b \approx 35$$, $$m \approx 100$$ → $$35^{100}$$ positions!
- Go: $$b \approx 250$$, $$m \approx 150$$ → Completely intractable!

**Solution**: Alpha-Beta Pruning ✂️

### Alpha-Beta Pruning

#### Core Idea

"Ignore the unwanted portion of the search tree"
{: .text-green-300 }

If you already know a move is worse than a previously examined move, you don't need to fully evaluate it!

#### Key Concepts

**Alpha (α)**: The best value MAX can guarantee so far (lower bound for MAX)

**Beta (β)**: The best value MIN can guarantee so far (upper bound for MIN)

**Pruning conditions**:
- **At MAX node**: If $$v \geq \beta$$, prune (MIN won't let us get here)
- **At MIN node**: If $$v \leq \alpha$$, prune (MAX has better options elsewhere)

#### Alpha-Beta Algorithm

```python
def alpha_beta_search(state):
    """Returns best action using alpha-beta pruning"""
    
    def max_value(state, alpha, beta):
        if terminal_test(state):
            return utility(state, MAX_PLAYER)
        
        v = -infinity
        for action in actions(state):
            v = max(v, min_value(result(state, action), alpha, beta))
            
            if v >= beta:
                return v  # β cutoff (prune)
            
            alpha = max(alpha, v)
        
        return v
    
    def min_value(state, alpha, beta):
        if terminal_test(state):
            return utility(state, MAX_PLAYER)
        
        v = +infinity
        for action in actions(state):
            v = min(v, max_value(result(state, action), alpha, beta))
            
            if v <= alpha:
                return v  # α cutoff (prune)
            
            beta = min(beta, v)
        
        return v
    
    # Start search
    best_value = -infinity
    best_action = None
    alpha = -infinity
    beta = +infinity
    
    for action in actions(state):
        value = min_value(result(state, action), alpha, beta)
        if value > best_value:
            best_value = value
            best_action = action
        alpha = max(alpha, value)
    
    return best_action
```

#### Alpha-Beta Example

**Game tree**:

```
                    MAX
                   /   \
                  /     \
               MIN       MIN
              / | \     / | \
             3  12 8   2  4  6
```

**Without pruning** (Minimax):
- Evaluate all 6 leaf nodes
- MIN left chooses 3
- MIN right chooses 2
- MAX chooses max(3, 2) = 3

**With alpha-beta pruning**:

```
Step 1: MAX node, α=-∞, β=+∞
  ├─ Explore left MIN child
  
Step 2: MIN left, α=-∞, β=+∞
  ├─ Evaluate first child: 3
  │  β = min(+∞, 3) = 3
  │  
  ├─ Evaluate second child: 12
  │  v = min(3, 12) = 3
  │  β still = 3
  │  
  ├─ Evaluate third child: 8
  │  v = min(3, 8) = 3
  │  Return 3 to MAX
  
Step 3: Back at MAX, α = max(-∞, 3) = 3
  ├─ Explore right MIN child
  
Step 4: MIN right, α=3, β=+∞
  ├─ Evaluate first child: 2
  │  v = 2
  │  Check: v (2) <= α (3)? YES! ✂️ PRUNE!
  │  Don't evaluate remaining children (4, 6)
  │  Return 2 to MAX
  
Step 5: MAX chooses max(3, 2) = 3
```

**Result**: Only evaluated 4 nodes instead of 6! Saved 33% of work.

#### Alpha-Beta Pruning Efficiency

**Best case** (perfect move ordering):
- Time complexity: $$O(b^{m/2})$$
- Effectively **doubles the search depth**!
- Example: If you could search 4 plies with minimax, you can search 8 plies with alpha-beta

**Worst case** (terrible move ordering):
- Time complexity: $$O(b^m)$$ (no better than minimax)

**Average case** (random ordering):
- Time complexity: $$O(b^{3m/4})$$

Key insight: **Move ordering matters!**
{: .important }

**Move ordering heuristics**:
1. **Killer moves**: Moves that caused cutoffs in sibling nodes
2. **History heuristic**: Moves that caused cutoffs frequently
3. **MVV-LVA** (chess): Most Valuable Victim - Least Valuable Aggressor
4. **Principal Variation**: Follow the best line from previous search

### Evaluation Function Deep Dive

#### Why We Need Evaluation Functions

**Problem**: Can't search to terminal states in complex games!
- Chess: $$10^{120}$$ possible games
- Go: $$10^{170}$$ possible games

**Solution**: Use **evaluation function** to estimate utility of non-terminal states.

#### Properties of Good Evaluation Functions

| Property | Description | Example |
|:---------|:------------|:--------|
| **Correlation** | Should correlate with winning probability | Material advantage in chess |
| **Efficiency** | Fast to compute | $$O(1)$$ or $$O(n)$$ |
| **Monotonicity** | Better positions have higher values | Consistent ordering |
| **Granularity** | Distinguish between similar positions | Fine-grained differences |

#### Evaluation Function Examples

**Chess - Simple material count**:

$$e(s) = 9Q + 5R + 3B + 3N + 1P - (\text{opponent's material})$$

Where: Q=Queen, R=Rook, B=Bishop, N=Knight, P=Pawn

**Chess - Advanced evaluation** (used in Stockfish):

```python
def evaluate_chess(state):
    score = 0
    
    # Material
    score += material_balance(state)
    
    # Piece-square tables (positional value)
    score += piece_square_value(state)
    
    # Pawn structure
    score += evaluate_pawns(state)
    
    # King safety
    score += king_safety(state)
    
    # Mobility (number of legal moves)
    score += mobility(state)
    
    # Control of center
    score += center_control(state)
    
    return score
```

**Tic-Tac-Toe**:

$$e(p) = (\text{open lines for player}) - (\text{open lines for opponent})$$

**Implementation**:

```python
def evaluate_tictactoe(state, player):
    """Evaluation function for Tic-Tac-Toe"""
    
    def count_open_lines(state, mark):
        count = 0
        
        # Check rows
        for row in state:
            if opponent_mark not in row:
                count += 1
        
        # Check columns
        for col in range(3):
            column = [state[row][col] for row in range(3)]
            if opponent_mark not in column:
                count += 1
        
        # Check diagonals
        main_diag = [state[i][i] for i in range(3)]
        if opponent_mark not in main_diag:
            count += 1
        
        anti_diag = [state[i][2-i] for i in range(3)]
        if opponent_mark not in anti_diag:
            count += 1
        
        return count
    
    opponent = 'O' if player == 'X' else 'X'
    
    player_lines = count_open_lines(state, player)
    opponent_lines = count_open_lines(state, opponent)
    
    return player_lines - opponent_lines
```

**Go - Territory estimation**:

```python
def evaluate_go(state, player):
    score = 0
    
    # Captured stones
    score += captured_stones(state, player) * 1.0
    
    # Territory (controlled empty intersections)
    score += estimate_territory(state, player) * 0.5
    
    # Influence (potential territory)
    score += calculate_influence(state, player) * 0.2
    
    # Eye space (living groups)
    score += count_eyes(state, player) * 2.0
    
    return score
```

#### Cutoff Test

Instead of terminal test, use **cutoff test** to decide when to stop searching and apply evaluation function:

```python
def cutoff_test(state, depth):
    """Decide whether to stop searching"""
    
    # Always stop at terminal states
    if terminal_test(state):
        return True
    
    # Stop at maximum depth
    if depth >= MAX_DEPTH:
        return True
    
    # Quiescence search: don't stop in "unstable" positions
    if is_quiet_position(state):
        return True
    
    return False

def is_quiet_position(state):
    """Check if position is stable (no captures, checks, etc.)"""
    # In chess: no checks, no captures available
    # In go: no ko fights, no groups in atari
    return not has_tactical_threats(state)
```

### Key Takeaways

**1. Game Formulation**
- Games are perfect for AI: discrete, deterministic, clear objectives
- Formal components: Initial state, Player, Actions, Result, Terminal-Test, Utility

**2. Minimax Algorithm**
- Optimal strategy: assume opponent plays optimally
- Recursive: MAX maximizes, MIN minimizes
- Complete and optimal, but exponential complexity

**3. Alpha-Beta Pruning**
- Prunes branches that can't affect final decision
- Best case: $$O(b^{m/2})$$ - doubles search depth!
- Move ordering is critical for efficiency

**4. Evaluation Functions**
- Estimate utility of non-terminal states
- Must be fast, accurate, and monotonic
- Domain-specific knowledge is key

**5. Practical Considerations**
- Quiescence search for stable positions
- Iterative deepening for time management
- Transposition tables for repeated positions
- Opening books and endgame databases
```

This format maintains all the technical content while using Just the Docs styling conventions including proper tables, code blocks, callout boxes, and LaTeX math formatting.
## MiniMax Algorithm


Alpha beta Pruning
