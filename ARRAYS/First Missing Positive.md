# LeetCode 41 — First Missing Positive

**Difficulty:** Hard

---

# 1. Problem Statement

## First Missing Positive

Given an unsorted integer array `nums`, return the smallest missing positive integer.

You must implement an algorithm that runs in **O(n)** time and uses **O(1)** extra space.

### Example 1

```text
Input: nums = [1,2,0]
Output: 3
```

### Example 2

```text
Input: nums = [3,4,-1,1]
Output: 2
```

### Example 3

```text
Input: nums = [7,8,9,11,12]
Output: 1
```

---

## Constraints

* `1 <= nums.length <= 10^5`
* `-2^31 <= nums[i] <= 2^31 - 1`

---

# 2. Brute Force Approach

## Idea

The smallest missing positive must lie between **1** and **n+1**, where **n** is the array length.

A simple solution is:

1. Store every positive number in a `HashSet`.
2. Starting from `1`, check whether each integer exists.
3. The first missing integer is the answer.

### Algorithm

1. Create a `HashSet<Integer>`.
2. Insert every positive number.
3. Check numbers from `1` to `n`.
4. Return the first missing number.
5. If all exist, return `n + 1`.

### Example

```text
nums = [3,4,-1,1]

Set = {1,3,4}

Check:
1 ✓
2 ✗

Answer = 2
```

### Complexity

* **Time:** O(n)
* **Space:** O(n)

---

# 3. Optimal Approach (Index Placement / Cyclic Positioning)

## Key Observation

For an array of length **n**:

* Negative numbers are useless.
* Zero is useless.
* Numbers greater than **n** are also useless.

Only numbers in the range

```text
1 ... n
```

can affect the answer.

---

## Core Idea

Every number belongs to its correct index.

```text
Value -> Index

1 -> 0
2 -> 1
3 -> 2
...
n -> n-1
```

So repeatedly swap elements until every valid number reaches its correct position.

---

## Example Walkthrough

### Input

```text
nums = [3,4,-1,1]
```

Length = 4

Desired arrangement

```text
1 at index 0
2 at index 1
3 at index 2
4 at index 3
```

---

### Step 1

```text
[3,4,-1,1]

3 belongs to index 2

Swap

[-1,4,3,1]
```

---

### Step 2

Current index still 0

```text
[-1,4,3,1]

-1 is invalid

Move ahead
```

---

### Step 3

Index = 1

```text
[-1,4,3,1]

4 belongs to index 3

Swap

[-1,1,3,4]
```

---

### Step 4

Still index 1

```text
[-1,1,3,4]

1 belongs to index 0

Swap

[1,-1,3,4]
```

---

### Final Arrangement

```text
[1,-1,3,4]
```

Scan from left:

```text
Index 0 -> 1 ✓

Index 1 -> Expected 2

Found -1

Answer = 2
```

---

## Why Does This Work?

Every valid number is moved to exactly one correct location.

After rearrangement:

```text
index i should contain i+1
```

The first place where this rule breaks gives the smallest missing positive.

---

## Algorithm

### Step 1

Traverse array.

While

```text
current number is valid
AND
not already in correct place
```

swap it into its correct index.

---

### Step 2

Traverse again.

If

```text
nums[i] != i + 1
```

return

```text
i + 1
```

---

### Step 3

If every position is correct

return

```text
n + 1
```

---

## Correctness

After the swapping phase:

* Every value between `1` and `n` is either:

  * placed at its correct index, or
  * absent from the array.

During the second scan:

* If index `i` does not contain `i + 1`, then `i + 1` is missing.
* If every position is correct, then all numbers `1...n` exist, so the answer is `n + 1`.

Hence the algorithm always returns the smallest missing positive.

---

## Complexity

### Time

Each element is swapped at most once into its correct position.

```text
O(n)
```

### Space

Only constant extra variables are used.

```text
O(1)
```

This satisfies the problem requirements.

---

# 4. Java Code (Optimal Solution)

```java
class Solution {

    public int firstMissingPositive(int[] nums) {

        int n = nums.length;

        // Place each valid number in its correct position.
        for (int i = 0; i < n; i++) {

            while (nums[i] >= 1 &&
                   nums[i] <= n &&
                   nums[i] != nums[nums[i] - 1]) {

                int correctIndex = nums[i] - 1;

                // Swap nums[i] with its correct position
                int temp = nums[i];
                nums[i] = nums[correctIndex];
                nums[correctIndex] = temp;
            }
        }

        // Find the first position where value != index + 1
        for (int i = 0; i < n; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }

        // All numbers 1..n are present
        return n + 1;
    }
}
```

---

# Dry Run

### Input

```text
nums = [3,4,-1,1]
```

After placement

```text
[1,-1,3,4]
```

Scanning

```text
Index 0 -> 1 ✓

Index 1 -> Expected 2

Found -1
```

Return

```text
2
```

---

# Edge Cases

| Input           | Output |
| --------------- | ------ |
| `[1,2,0]`       | `3`    |
| `[3,4,-1,1]`    | `2`    |
| `[7,8,9,11,12]` | `1`    |
| `[1]`           | `2`    |
| `[2]`           | `1`    |
| `[-1,-2]`       | `1`    |
| `[1,1]`         | `2`    |
| `[2,2]`         | `1`    |
| `[1,2,3]`       | `4`    |

---

# Complexity Summary

| Approach                  | Time     | Space    |
| ------------------------- | -------- | -------- |
| HashSet (Brute Force)     | **O(n)** | **O(n)** |
| Index Placement (Optimal) | **O(n)** | **O(1)** |

---

# Key Interview Takeaways

* The answer always lies in the range **1 to n+1**.
* Ignore numbers `<= 0` or `> n`.
* Use the array itself as a hash table by placing each value at index `value - 1`.
* Each element is moved at most once, giving **O(n)** time.
* The algorithm uses **constant extra space**, satisfying the problem's strict constraints.
