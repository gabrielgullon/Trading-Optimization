# Trading-Optimization
A general summary of how I optimize my Trading Strategies.

# Optmization Steps

## #1 Results Distribution

**Objectives**

- Show all capital curves (x: #trades, y: profit)

- Display following results:

  - Profit
  - Optimization Function (PROM, or other)
  - Drawdown

- Display following metrics for each result above:
  - Max, avg ($\mu$), min
  - Positive curves (_final result > 0_)
  - Std dev ($\sigma$)
  - Z-Score ($\mu / \sigma$)

## #2 Parameters Distribution

**Objectives**

For each parameter optimized, display a chart where x: Parameter value, y: Optimization Function

## #3 Walk Forward Analysis

### Part 1:

### 1-a) Walk Forward

References for WFA:

- [O guia definitivo do Walk Forward Analysis (WFA)](https://medium.com/devtrader/o-guia-definitivo-do-walk-forward-analysis-wfa-e755c2c33542?source=collection_home---4------2-----------------------)

### 1-b) Walk Forward Matrix

**Objectives**
- Define the sets for In-Sample (IS), Out-of-Sample(OoS) and Iterations (I) for WFM.
- Display a table containing: the normalized (avg per month) for IS, OoS and WFE for each period of WFA: 
    1) Profit
    1) Optimization Function
    1) Drawdown
- 

**Guidelines**

- OoS periods $\ge$ 3 months
- At least 6 steps
- ChatGPT:

Let:

- \(N\) be the total number of months in your historical dataset.
- \(W\) be the size of each walk forward window (in months).
- \(S\) be the step size (in months).
- \(I\) be the total number of iterations.

1. **In-Sample Period (IS) Formula:**

   - The in-sample period for each iteration can be defined as follows:
     \[ IS_i = \text{Range}(i, W) \]
     where \(i\) is the iteration number, and \(\text{Range}(i, W)\) is a function that returns a range of months starting from \(i\) and spanning \(W\) months.

2. **Out-of-Sample Period (OOS) Formula:**

   - The out-of-sample period for each iteration can be defined as follows:
     \[ OOS_i = \text{Range}(i + W, S) \]
     where \(i\) is the iteration number, \(W\) is the size of the walk forward window, and \(S\) is the step size. \(\text{Range}(i + W, S)\) returns a range of months starting from \(i + W\) and spanning \(S\) months.

3. **Number of Iterations (I) Formula:**
   - The total number of iterations can be defined as:
     \[ I = \left\lceil \frac{N - W}{S} \right\rceil \]
     where \(\lceil x \rceil\) represents rounding \(x\) to the nearest integer greater than or equal to \(x\).

Using these formulas, you can systematically define the in-sample and out-of-sample periods for each iteration, ensuring that you cover the entire historical dataset while simulating the ongoing adaptation of your trading strategy to changing market conditions. Adjust the values of \(W\) and \(S\) based on your specific requirements.

- PARDO, p. 249:

  > "In general, the faster-paced a trading strategy, the more likely it is to
  > benefit from **shorter optimization and walk-forward windows**. Start with
  > windows from one to two years in length. Conversely, the slower that strategy,
  > the longer the window needed. Start with windows from three to six
  > years in length. These values, of course, are highly variable because of a
  > number of factors.

  > The size of the walk-forward window is typically a function of the
  > size of optimization window. Typically, a walk-forward window should be
  > somewhere in the area of 25 to 35 percent of the optimization window."

- PARDO, p. 142:

  > "A good rule of thumb for the determination of the size of the trading window is to set it at between one-eighth and one-third of the test window size."

Given all that, I define that:

- for T < 12:

  - in_sample = [2, 3, 4, 5, 6]
  - out_of_sample = [1, 2, 3]

    **Eg 1**

        - History lenght = 10 months (M)
        - IS = [2, 3, 4, 5, 6]
        - OoS = [1, 2, 3]
        - WFM = [lenght: 14]
          [{'in': 2, 'out': 1, 'steps': 8},
          {'in': 2, 'out': 2, 'steps': 4},
          {'in': 3, 'out': 1, 'steps': 7},
          {'in': 3, 'out': 2, 'steps': 4},
          {'in': 3, 'out': 3, 'steps': 2},
          {'in': 4, 'out': 1, 'steps': 6},
          {'in': 4, 'out': 2, 'steps': 3},
          {'in': 4, 'out': 3, 'steps': 2},
          {'in': 5, 'out': 1, 'steps': 5},
          {'in': 5, 'out': 2, 'steps': 2},
          {'in': 5, 'out': 3, 'steps': 2},
          {'in': 6, 'out': 1, 'steps': 4},
          {'in': 6, 'out': 2, 'steps': 2},
          {'in': 6, 'out': 3, 'steps': 1}]

- for T >= 12:

  - **IS**: $0.2 \le IS \le 0.4$
  - **OoS**: $0.25 * IS_i \le OoS_i \le 0.35 * IS_i$
  - $WFA_i \subset WFM$, if $OoS \lt IS$ and $ 0.25 \le \frac{OoS_i}{IS_i} \le 0.35$

    **Eg 2**

        - History lenght = 60 months (M)
        - IS = [12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24]
        - OoS = [3, 4, 5, 6, 7, 8]
        - WFM = [lenght: 21]
          [{'in': 12, 'out': 3, 'steps': 16},
          {'in': 12, 'out': 4, 'steps': 12},
          {'in': 13, 'out': 4, 'steps': 12},
          {'in': 14, 'out': 4, 'steps': 12},
          {'in': 15, 'out': 4, 'steps': 11},
          {'in': 15, 'out': 5, 'steps': 9},
          {'in': 16, 'out': 4, 'steps': 11},
          {'in': 16, 'out': 5, 'steps': 9},
          {'in': 17, 'out': 5, 'steps': 9},
          {'in': 18, 'out': 5, 'steps': 8},
          {'in': 18, 'out': 6, 'steps': 7},
          {'in': 19, 'out': 5, 'steps': 8},
          {'in': 19, 'out': 6, 'steps': 7},
          {'in': 20, 'out': 5, 'steps': 8},
          {'in': 20, 'out': 6, 'steps': 7},
          {'in': 20, 'out': 7, 'steps': 6},
          {'in': 21, 'out': 6, 'steps': 6},
          {'in': 21, 'out': 7, 'steps': 6},
          {'in': 22, 'out': 6, 'steps': 6},
          {'in': 23, 'out': 6, 'steps': 6},
          {'in': 24, 'out': 6, 'steps': 6}]

### Part 2: Walk Forward Analysis

**Objectives**


## #4 Monte Carlo

**Objectives**

- Display the same chart, metrics and results as in the "**Results Distribution**".
- The input Series here is the combined OoS from the WFA with the highest WFE
