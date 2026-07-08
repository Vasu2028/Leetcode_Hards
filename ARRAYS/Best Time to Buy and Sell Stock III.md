# LeetCode 123 — Best Time to Buy and Sell Stock III

**Difficulty:** Hard

---

# 1. Problem Statement

## Best Time to Buy and Sell Stock III

You are given an array `prices` where `prices[i]` is the price of a given stock on the `iᵗʰ` day.

Find the maximum profit you can achieve.

You may complete **at most two transactions**.

> **Note:**
>
> * You may not engage in multiple transactions simultaneously.
> * You must sell the stock before buying again.

Return the maximum profit.

---

## Example 1

```text
Input: prices = [3,3,5,0,0,3,1,4]

Output: 6

Explanation:

Buy on day 4 (price = 0)
Sell on day 6 (price = 3)
Profit = 3

Buy on day 7 (price = 1)
Sell on day 8 (price = 4)
Profit = 3

Total Profit = 6
```

---

## Example 2

```text
Input: prices = [1,2,3,4,5]

Output: 4
```

---

## Example 3

```text
Input: prices = [7,6,4,3,1]

Output: 0
```

---

## Constraints

* `1 <= prices.length <= 10^5`
* `0 <= prices[i] <= 10^5`

---

# 2. Brute Force Approach

## Idea

Try every possible first transaction.

For every possible first buy/sell pair:

* Calculate its profit.
* Try every possible second buy/sell pair after the first transaction.
* Keep the maximum total profit.

### Complexity

* **Time:** O(n³)
* **Space:** O(1)

Too slow for `n = 100000`.

---

# 3. Optimal Approach

## Dynamic Programming (State Machine)

Instead of checking every pair, keep track of the best profit after each state.

There are only four states.

```
First Buy
↓

First Sell
↓

Second Buy
↓

Second Sell
```

We maintain four variables.

| Variable | Meaning                          |
| -------- | -------------------------------- |
| `buy1`   | Maximum profit after first buy   |
| `sell1`  | Maximum profit after first sell  |
| `buy2`   | Maximum profit after second buy  |
| `sell2`  | Maximum profit after second sell |

---

## State Transitions

Initially

```
buy1 = -∞
sell1 = 0

buy2 = -∞
sell2 = 0
```

For every price:

### First Buy

```
buy1 = max(buy1, -price)
```

Either

* keep previous purchase
* buy today

---

### First Sell

```
sell1 = max(sell1, buy1 + price)
```

Either

* keep previous profit
* sell today

---

### Second Buy

```
buy2 = max(buy2, sell1 - price)
```

Either

* keep previous second purchase
* buy today using profit from first transaction

---

### Second Sell

```
sell2 = max(sell2, buy2 + price)
```

Either

* keep previous answer
* sell today

---

## Example

```
prices =

3 3 5 0 0 3 1 4
```

Initially

```
buy1 = -∞
sell1 = 0
buy2 = -∞
sell2 = 0
```

After processing all prices

```
buy1 = 0
sell1 = 4

buy2 = 2
sell2 = 6
```

Answer

```
6
```

---

## Why This Works

Each variable stores the **best possible profit** after reaching that state.

For example,

```
buy2
```

already includes the profit earned from the first completed transaction.

Thus, every transaction is considered exactly once while scanning the array only once.

---

## Algorithm

1. Initialize four variables.
2. Traverse the array once.
3. Update

   * first buy
   * first sell
   * second buy
   * second sell
4. Return `sell2`.

---

## Correctness

The DP states represent all valid transaction stages:

* `buy1` → after buying once
* `sell1` → after selling once
* `buy2` → after buying twice
* `sell2` → after selling twice

Each transition chooses the better of:

* keeping the previous state
* performing today's transaction

Since every day's price is processed exactly once and every valid state transition is considered, the final value of `sell2` is the maximum achievable profit using at most two transactions.

---

## Complexity

| Metric | Complexity |
| ------ | ---------- |
| Time   | **O(n)**   |
| Space  | **O(1)**   |

---

# 4. Java Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        int buy1 = Integer.MIN_VALUE;
        int sell1 = 0;

        int buy2 = Integer.MIN_VALUE;
        int sell2 = 0;

        for (int price : prices) {

            // First purchase
            buy1 = Math.max(buy1, -price);

            // First sale
            sell1 = Math.max(sell1, buy1 + price);

            // Second purchase
            buy2 = Math.max(buy2, sell1 - price);

            // Second sale
            sell2 = Math.max(sell2, buy2 + price);
        }

        return sell2;
    }
}
```

---

# Dry Run

### Input

```
prices = [3,3,5,0,0,3,1,4]
```

| Price | buy1 | sell1 | buy2 | sell2 |
| ----: | ---: | ----: | ---: | ----: |
|     3 |   -3 |     0 |   -3 |     0 |
|     3 |   -3 |     0 |   -3 |     0 |
|     5 |   -3 |     2 |   -3 |     2 |
|     0 |    0 |     2 |    2 |     2 |
|     0 |    0 |     2 |    2 |     2 |
|     3 |    0 |     3 |    2 |     5 |
|     1 |    0 |     3 |    2 |     5 |
|     4 |    0 |     4 |    2 |     6 |

Final Answer

```
6
```

---

# Edge Cases

| Input               | Output |
| ------------------- | ------ |
| `[1]`               | `0`    |
| `[1,2]`             | `1`    |
| `[2,1]`             | `0`    |
| `[1,2,3,4,5]`       | `4`    |
| `[7,6,4,3,1]`       | `0`    |
| `[3,3,5,0,0,3,1,4]` | `6`    |

---

# Complexity Summary

| Approach                   | Time      | Space    |
| -------------------------- | --------- | -------- |
| Brute Force                | **O(n³)** | **O(1)** |
| DP State Machine (Optimal) | **O(n)**  | **O(1)** |

---

# Interview Tips

* This is a **state machine DP** problem.
* Memorize the four states:

  * `buy1`
  * `sell1`
  * `buy2`
  * `sell2`
* Update them in exactly this order:

  1. `buy1`
  2. `sell1`
  3. `buy2`
  4. `sell2`
* `buy2` uses the profit from `sell1`, ensuring the second transaction starts only after the first has completed.
* This pattern generalizes to **Best Time to Buy and Sell Stock IV**, where DP tracks states for up to `k` transactions.
