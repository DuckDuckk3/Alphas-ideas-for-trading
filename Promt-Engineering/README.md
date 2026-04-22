# Scientific Prompt Engineering Guide

## 1. Core Principle

A good prompt is:
- **Clear** (no ambiguity)
- **Constrained** (defines boundaries)
- **Structured** (organized logically)
- **Goal-oriented** (explicit outcome)

> Think of prompting as writing a **specification**, not a question.

---

## 2. The Optimal Prompt Structure

Use this framework:


[ROLE]
[OBJECTIVE]
[CONTEXT]
[INPUT DATA]
[CONSTRAINTS]
[OUTPUT FORMAT]
[EVALUATION CRITERIA]


### Example Template


You are a [ROLE].

Your task is to [OBJECTIVE].

Context:
[BACKGROUND INFORMATION]

Input:
[DATA OR PROBLEM]

Constraints:

[Constraint 1]
[Constraint 2]

Output format:
[EXACT FORMAT REQUIRED]

Evaluation criteria:

[What defines a good answer]

---

## 3. Define the Role Explicitly

Bad:

Explain this concept


Good:

You are a quantitative researcher specializing in factor investing.
Explain the concept of value factor construction.


Why:
- Activates domain-specific reasoning
- Reduces generic responses

---

## 4. Specify the Objective Precisely

Bad:

Improve this


Good:

Improve this alpha by reducing turnover while keeping Sharpe stable.


Key rule:
> One prompt = one clear objective

---

## 5. Provide Sufficient Context

Include:
- Domain (finance, physics, NLP, etc.)
- Level (beginner, expert)
- Use case (research, production, learning)

Example:

This alpha is designed for WorldQuant Brain and must have low correlation with the pool.


---

## 6. Constrain the Solution Space

Without constraints → vague answers

Add constraints like:
Do not increase turnover significantly
Avoid using price-only signals
Use only fundamental data

This forces **focused reasoning**

---

## 7. Control the Output Format

Always define output format if you care about usability.

Example:

Output in a Markdown code block.
Use short explanations followed by formulas.


Or:

Return 3 alternative alpha formulas, each on a new line.


---

## 8. Use Decomposition (Step-by-Step Thinking)

Instead of:

Give me the best alpha


Use:
Analyze weaknesses of the current alpha
Propose improvements
Output final improved versions

This improves:
- logical consistency
- depth of reasoning

---

## 9. Use Iterative Prompting

Do NOT expect perfect output in one step.

Workflow:

Step 1: Generate ideas
Step 2: Filter ideas
Step 3: Refine best candidates


Example:

First generate 5 ideas.
Then select the best 2 based on stability.
Then refine them.


---

## 10. Reduce Hallucination

Techniques:
- Ask for assumptions explicitly
- Require justification

Example:

Explain why each modification improves the alpha.
If uncertain, state assumptions.


---

## 11. Use Comparison Prompts

Force better reasoning:


Compare these two alphas in terms of:

turnover
stability
correlation

---

## 12. Use Constraint + Optimization Prompts

Very powerful for quant tasks:


Optimize this alpha under:

low correlation (<0.3)
turnover < 30%
maintain Sharpe

---

## 13. Use “Failure Mode” Prompts


List scenarios where this alpha will fail.


This improves robustness understanding.

---

## 14. Common Mistakes

❌ Too vague  
❌ No output format  
❌ No constraints  
❌ Asking multiple unrelated tasks  
❌ No context  

---

## 15. Example: High-Quality Prompt (Quant)


You are a quantitative researcher.

Your task is to improve the following alpha:

rank(enterprise_value / cashflow)

Context:

Designed for cross-sectional equity trading
Must have low correlation with existing alphas

Constraints:

Keep turnover low
Avoid unstable ratios (e.g. division by near-zero values)
Prefer fundamental-based signals

Instructions:

Identify weaknesses
Propose 3 improved versions
Explain each improvement briefly

Output format:

Use a code block
Each alpha on a new line
Followed by 1–2 line explanation

---

## 16. Meta Insight

The best prompts:
- **Minimize ambiguity**
- **Maximize structure**
- **Guide reasoning without over-constraining creativity**

> Prompt engineering = designing the search space of the model.
