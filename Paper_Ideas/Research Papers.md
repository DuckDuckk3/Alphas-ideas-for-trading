# Research Papers for Alpha Research

This document consolidates the most relevant academic papers for developing and improving systematic equity alphas, with an emphasis on short-term signals, analyst information, market microstructure, machine learning, and low-correlation alpha construction.

The goal is not to reproduce published formulas. Instead, these papers should be used to extract:

* Economic mechanisms
* Signal construction principles
* Signal combinations
* Conditional and interaction effects
* Portfolio construction methods
* Transaction-cost considerations
* Robustness and out-of-sample testing
* Potentially orthogonal alpha families

---

## 1. Beyond Fama-French Factors: Alpha from Short-Term Signals

**Journal:** Financial Analysts Journal, 2023

**Paper:** [Beyond Fama-French Factors: Alpha from Short-Term Signals](https://www.tandfonline.com/doi/full/10.1080/0015198X.2023.2173492)

### Why this paper matters

This is the most directly relevant paper for systematic alpha research.

The authors construct a composite alpha using several short-term signals:

* Short-term reversal
* Short-term momentum
* Short-term analyst revisions
* Short-term risk
* Monthly seasonality

The important contribution is not any individual signal, but the methodology used to transform economic hypotheses into a robust composite alpha.

### Key concepts to extract

```text
Economic hypothesis
        ↓
Signal construction
        ↓
Signal combination
        ↓
Portfolio construction
        ↓
Transaction costs
        ↓
Out-of-sample testing
        ↓
Robustness analysis
```

### Relevance to BRAIN

This provides a useful framework for developing Regular Alphas and Power Pool candidates.

Particularly important topics:

* Combining weak but complementary signals
* Controlling correlation between alpha components
* Transaction-cost-aware construction
* Testing whether an alpha survives after publication
* Testing across different regions and market environments

### Priority

**Highest priority**

---

## 2. Analysts' Underreaction and Momentum Strategies

**Journal:** Journal of Economic Dynamics and Control, 2023

**Paper:** [Analysts' underreaction and momentum strategies](https://www.sciencedirect.com/science/article/pii/S0165188922002639)

### Core mechanism

The paper studies the possibility that analysts systematically underreact to information.

The authors construct a measure called **Analysts' Predictable Underreaction (APU)** and show that it predicts analyst forecast errors and subsequent stock returns.

The reported strategy generates approximately **0.85% monthly Fama-French six-factor alpha**, with a t-statistic of approximately **3.48** in the study.

### Key research directions

The important lesson is that analyst information should not simply be treated as:

```text
analyst signal → rank → alpha
```

Instead, investigate the dynamics of the information.

Potential signal families:

```text
Analyst Revision
        ├── Revision magnitude
        ├── Revision persistence
        ├── Revision acceleration
        ├── Revision reversal
        ├── Revision consistency
        └── Analyst disagreement
```

### Potential BRAIN fields

Relevant field families include:

```text
recommendation_change_component_short_term
recommendation_change_component_medium_term_2
recommendation_change_component_long_term
analyst_count_decreasing_*
```

The main research hypothesis should be:

> Analyst information may be incorporated into prices gradually rather than instantaneously.

### Priority

**Highest priority**

---

## 3. Short-Term Institutions, Analyst Recommendations, and Mispricing: The Role of Higher Order Beliefs

**Journal:** Journal of Accounting Research, 2021

**Paper:** [Short-Term Institutions, Analyst Recommendations, and Mispricing: The Role of Higher Order Beliefs](https://onlinelibrary.wiley.com/doi/full/10.1111/1475-679X.12352)

### Core mechanism

This paper studies the interaction between:

```text
Analyst Recommendations
        ×
Short-Term Institutional Trading
        ×
Past Stock Performance
```

Rather than assuming that a positive analyst recommendation directly predicts positive returns, the paper investigates how different market participants interpret and respond to the information.

The interaction can produce predictable reversal effects.

The effect is particularly interesting around:

* Earnings news
* High fundamental uncertainty
* Institutional trading activity

### Potential BRAIN alpha family

A promising conceptual structure is:

```text
Analyst signal
        ×
Institutional activity
        ×
Earnings information
```

This is attractive because it is fundamentally different from simply building another price momentum or reversal alpha.

### Research questions

* Does analyst recommendation information become more predictive when institutional activity is abnormal?
* Does the predictive effect change after strong prior returns?
* Does the relationship strengthen around earnings events?
* Is the effect stronger for firms with high fundamental uncertainty?

### Priority

**Highest priority**

---

## 4. Machine Forecast Disagreement

**Journal:** The Review of Financial Studies, 2026

**Paper:** [Machine Forecast Disagreement](https://academic.oup.com/rfs/advance-article-abstract/doi/10.1093/rfs/hhag042/8660474)

### Core concept

The paper introduces **Machine Forecast Disagreement (MFD)**, which measures the degree to which different machine-learning models disagree about expected stock returns.

The key idea is:

```text
Prediction
        ↓
Multiple models
        ↓
Forecast dispersion
        ↓
Disagreement
        ↓
Future return predictability
```

The study reports a value-weighted long-short spread of approximately **13% per year**.

### Why this is particularly interesting

This represents a different type of alpha signal.

Instead of asking:

> What does the model predict?

ask:

> How confident or uncertain is the prediction across different models?

### Potential BRAIN field families

Search for fields related to:

```text
forecast
prediction
expected_return
probability
confidence
model
disagreement
dispersion
```

Potential hypotheses:

```text
Prediction level
Prediction dispersion
Prediction stability
Prediction revision
Prediction disagreement
```

### Potential alpha structures

```text
forecast level × forecast disagreement
```

```text
forecast revision × forecast disagreement
```

```text
prediction stability × return reversal
```

The key research direction is **forecast uncertainty as information**, rather than prediction magnitude alone.

### Priority

**Highest priority**

---

## 5. Alpha Go Everywhere: Machine Learning and International Stock Returns

**Journal:** The Review of Asset Pricing Studies, 2025

**Paper:** [Alpha Go Everywhere: Machine Learning and International Stock Returns](https://academic.oup.com/raps/article-abstract/15/3-4/288/8172522)

### Main findings

The paper investigates machine-learning approaches to international stock return prediction.

Important findings include:

* Neural networks can outperform linear models in cross-sectional return prediction.
* Market-specific training is important.
* Information learned from U.S. characteristics can improve prediction in international markets.

### Relevance to BRAIN

This is particularly relevant for reducing production correlation.

Do not assume:

```text
Strong USA alpha
        ↓
Strong GLB alpha
```

Instead investigate:

```text
Signal
   ×
Market
   ×
Region
   ×
Regime
```

A signal can have different economic mechanisms and predictive strengths across markets.

### Potential application

When developing a new alpha:

```text
USA
GLB
EUR
ASI
```

should not automatically be treated as interchangeable environments.

The objective is to identify:

* Region-specific signal strength
* Region-specific decay
* Region-specific turnover
* Region-specific correlation
* Region-specific robustness

### Priority

**High priority**

---

## 6. What Drives Momentum and Reversal? Evidence from Day and Night Signals

**Journal:** The Review of Financial Studies, 2026

**Paper:** [What Drives Momentum and Reversal? Evidence from Day and Night Signals](https://academic.oup.com/rfs/advance-article-abstract/doi/10.1093/rfs/hhag036/8626980)

### Core idea

The paper decomposes stock returns into:

```text
Overnight return
+
Intraday return
```

and studies their different relationships with momentum and reversal.

The results indicate that:

* Intraday returns generate momentum.
* Overnight returns do not exhibit the same momentum behavior.
* The findings are consistent with investor underreaction to information revealed through trading.

### Direct relevance to current alpha research

This is particularly relevant to signals such as:

```text
overnight = open / ts_delay(close,1) - 1;
intraday = close / open - 1;
```

Instead of treating these variables as arbitrary features, they can be connected to a specific economic hypothesis:

> Information revealed during trading may be incorporated into prices gradually, creating intraday momentum.

### Research directions

Investigate separately:

```text
Overnight momentum
Overnight reversal
Intraday momentum
Intraday reversal
Overnight × intraday interaction
Volume × intraday return
VWAP deviation × intraday return
```

This provides a much stronger foundation for microstructure-based alpha construction than simply testing arbitrary return windows.

### Priority

**Highest priority for current research**

---

## 7. Bimodal Characteristic Returns and Predictability Enhancement via Machine Learning

**Journal:** Management Science, 2022

**Paper:** [Bimodal Characteristic Returns and Predictability Enhancement via Machine Learning](https://pubsonline.informs.org/doi/abs/10.1287/mnsc.2021.4189)

### Core idea

Rather than simply applying a linear factor score, the authors use machine learning to reclassify stocks according to predicted financial performance.

Conceptually:

```text
Traditional approach:

Characteristic
      ↓
Linear factor score
      ↓
Portfolio


Machine-learning approach:

Characteristics
      ↓
Nonlinear model
      ↓
Stock classification
      ↓
Conditional alpha
```

The reported value-weighted long-short portfolio generates approximately **2.4% monthly alpha**, with a t-statistic of approximately **6.63**, after controlling for major common factors.

### Relevance to BRAIN

The main lesson is not to implement machine learning directly inside BRAIN.

Instead, investigate nonlinear relationships using operators already available in the platform.

For example:

```text
Factor A × Factor B
```

```text
Factor A conditioned on Factor B
```

```text
Factor A × regime
```

```text
Factor A × uncertainty
```

This is a useful path toward **conditional alpha**.

### Priority

**High priority**

---

## 8. Estimating Stock Market Betas via Machine Learning

**Journal:** Journal of Financial and Quantitative Analysis, 2025

**Paper:** [Estimating Stock Market Betas via Machine Learning](https://www.cambridge.org/core/journals/journal-of-financial-and-quantitative-analysis/article/estimating-stock-market-betas-via-machine-learning/5D19DD38014A2C23E677F85BE5E7148A)

### Core idea

The paper demonstrates that machine learning can improve the estimation of time-varying stock market betas.

Important predictors include:

* Historical beta
* Turnover
* Size

Nonlinearities and interactions can improve beta prediction.

### Relevance to alpha research

Risk variables should not necessarily be treated only as things to neutralize.

Potential alpha hypotheses include:

```text
Beta instability
Beta acceleration
Idiosyncratic risk
Downside risk
Volatility regime
Factor exposure change
Risk shock
```

For example:

```text
Risk change
    ×
Momentum
```

or:

```text
Volatility regime
    ×
Reversal
```

may generate signals that are less correlated with conventional momentum factors.

### Priority

**High priority for orthogonal alpha research**

---

## 9. A Machine Learning View on Momentum and Reversal Trading

### Main topic

This paper investigates machine-learning approaches to momentum and reversal trading, including:

* Decision Trees
* Support Vector Machines
* Multilayer Perceptrons
* LSTM

The main methodological value is understanding how nonlinear models can capture conditional momentum/reversal behavior.

### Relevance

This paper should not be the first paper to read because its journal quality is lower than the papers above.

However, it is useful after understanding the economic mechanisms behind momentum and reversal.

The most useful concept is:

```text
Momentum/Reversal
        +
Market state
        ↓
Conditional signal
```

rather than:

```text
momentum → always trade
```

### Priority

**Secondary**

---

# Recommended Reading Order

The papers should not be read in publication order. A better sequence for current alpha research is:

```text
1. Beyond Fama-French Factors
        ↓
2. What Drives Momentum and Reversal?
        ↓
3. Analysts' Underreaction and Momentum Strategies
        ↓
4. Short-Term Institutions + Analyst Recommendations
        ↓
5. Machine Forecast Disagreement
        ↓
6. Bimodal Characteristic Returns + ML
        ↓
7. Alpha Go Everywhere
        ↓
8. Estimating Stock Market Betas via ML
        ↓
9. ML View on Momentum and Reversal
```

---

# Core Research Themes to Extract

After reading the papers, the objective should be to build a library of economic mechanisms rather than a library of copied formulas.

## Theme 1: Information Underreaction

```text
New information
      ↓
Investor underreaction
      ↓
Gradual price adjustment
      ↓
Predictable returns
```

Relevant papers:

* Analysts' Underreaction and Momentum Strategies
* What Drives Momentum and Reversal?
* Short-Term Institutions, Analyst Recommendations, and Mispricing

Potential BRAIN signals:

```text
Analyst revisions
Earnings surprises
Intraday returns
Recommendation changes
News information
```

---

## Theme 2: Signal Interaction

Instead of testing:

```text
Signal A
Signal B
Signal C
```

independently, investigate:

```text
Signal A × Signal B
```

Examples:

```text
Analyst × Momentum
Analyst × Institution
Analyst × Earnings
Momentum × Risk
Reversal × Volume
Forecast × Disagreement
```

This is one of the most important ideas for building orthogonal Regular Alphas.

---

## Theme 3: Signal Persistence

A signal should not necessarily be treated as a one-day observation.

For analyst-related fields, investigate:

```text
Level
Change
Acceleration
Persistence
Reversal
Consistency
Dispersion
```

For example:

```text
current_revision
```

is only one hypothesis.

A potentially more interesting hypothesis is:

```text
revision_t
    +
revision_t-1
    +
revision_t-2
```

or:

```text
revision_acceleration
```

---

## Theme 4: Disagreement and Uncertainty

A major emerging direction is:

```text
Prediction
+
Prediction disagreement
```

Potential variables:

```text
Forecast dispersion
Analyst dispersion
Model disagreement
Earnings uncertainty
Fundamental uncertainty
Risk uncertainty
```

The central question becomes:

> Does disagreement itself contain information about future returns?

This is potentially highly valuable for developing low-correlation alphas.

---

## Theme 5: Conditional Alpha

Instead of:

```text
Signal → Return
```

use:

```text
Signal
   ×
Market condition
   ↓
Conditional Return
```

Potential regimes:

```text
High volatility
Low volatility
High volume
Low volume
Strong momentum
Strong reversal
Earnings period
High uncertainty
Low liquidity
```

This can transform a weak standalone signal into a useful conditional component.

---

# Practical BRAIN Research Pipeline

The papers suggest the following workflow for future alpha development:

```text
Economic mechanism
        ↓
Identify BRAIN fields
        ↓
Construct primitive signal
        ↓
Test standalone Sharpe
        ↓
Test decay / persistence
        ↓
Test conditional interactions
        ↓
Test correlation against existing alphas
        ↓
Optimize turnover
        ↓
Neutralize unwanted exposures
        ↓
Transaction-cost analysis
        ↓
Region / universe robustness
        ↓
Production correlation
        ↓
Final Regular Alpha candidate
```

The important change in research philosophy is:

> Do not start from an operator and search for a profitable formula.

Instead:

> Start from an economic mechanism, identify observable variables representing that mechanism, construct the simplest possible signal, and then test whether the mechanism survives statistically and economically.

---

# Priority Matrix

| Paper                              | Main Topic                        | Relevance | Priority |
| ---------------------------------- | --------------------------------- | --------: | -------: |
| Beyond Fama-French Factors         | Composite short-term alpha        | Very High |        1 |
| What Drives Momentum and Reversal? | Overnight/intraday                | Very High |        2 |
| Analysts' Underreaction            | Analyst revisions                 | Very High |        3 |
| Short-Term Institutions + Analysts | Analyst × institution interaction | Very High |        4 |
| Machine Forecast Disagreement      | Forecast disagreement             | Very High |        5 |
| Bimodal Characteristic Returns     | Nonlinear factor prediction       |      High |        6 |
| Alpha Go Everywhere                | International ML alpha            |      High |        7 |
| Estimating Stock Betas via ML      | Dynamic risk                      |      High |        8 |
| ML View on Momentum/Reversal       | ML methodology                    |    Medium |        9 |

---

# The Most Important Alpha Families to Investigate

Based on these papers, the next research directions should be concentrated in five families:

### 1. Analyst Revision Alpha

```text
revision
revision persistence
revision acceleration
revision disagreement
analyst underreaction
```

### 2. Intraday / Overnight Alpha

```text
overnight return
intraday return
overnight × intraday
intraday × volume
intraday × VWAP
```

### 3. Analyst × Institutional Alpha

```text
analyst recommendation
×
institutional activity
×
past return
```

### 4. Forecast Disagreement Alpha

```text
forecast level
forecast dispersion
forecast revision
forecast stability
forecast disagreement
```

### 5. Conditional Risk Alpha

```text
risk regime
×
momentum
```

```text
risk regime
×
reversal
```

```text
beta instability
×
fundamental signal
```

---

# Final Research Principle

The strongest takeaway from these papers is that the next generation of alpha research should move away from isolated factor signals toward **conditional, interaction-based, economically motivated signals**.

The progression should be:

```text
Traditional factor
        ↓
Short-term signal
        ↓
Signal interaction
        ↓
Conditional signal
        ↓
Regime-dependent signal
        ↓
Orthogonal composite alpha
```

For the current BRAIN research, the highest-value areas are therefore:

1. **Analyst revision persistence and underreaction**
2. **Overnight/intraday decomposition**
3. **Analyst × institutional trading interactions**
4. **Forecast disagreement / dispersion**
5. **Risk-regime-conditioned alpha**
6. **Nonlinear interactions between fundamental and short-term signals**
7. **Region-specific alpha mechanisms**

These directions are preferable to generating more variations of generic momentum, reversal, or volume-rank formulas because they provide a stronger economic hypothesis and a better path toward genuinely orthogonal alpha.
