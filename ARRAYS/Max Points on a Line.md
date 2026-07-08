# LeetCode 149 — Max Points on a Line

**Difficulty:** Hard

---

# 1. Problem Statement

## Max Points on a Line

Given an array of points where `points[i] = [xi, yi]`, return the maximum number of points that lie on the same straight line.

---

## Example 1

```text
Input:
points = [[1,1],[2,2],[3,3]]

Output:
3
```

---

## Example 2

```text
Input:
points =
[[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]

Output:
4
```

---

## Constraints

* `1 <= points.length <= 300`
* `points[i].length == 2`
* `-10^4 <= xi, yi <= 10^4`
* All points are unique.

---

# 2. Brute Force Approach

## Idea

Every line can be uniquely determined by two points.

For every pair of points:

1. Form a line.
2. Check every remaining point.
3. Count how many lie on the same line.
4. Keep the maximum.

To avoid floating-point precision, use the collinearity condition:

```text
(x2-x1)(y3-y1) == (y2-y1)(x3-x1)
```

### Complexity

* **Time:** O(n³)
* **Space:** O(1)

Too slow for `n = 300`.

---

# 3. Optimal Approach

## Idea

Fix one point as the starting point.

Compute the slope from this point to every other point.

Points having the same slope lie on the same line passing through the fixed point.

Store the frequency of each slope using a `HashMap`.

Repeat for every point.

---

## Why Normalize Slopes?

Using `double` slopes causes precision errors.

Example:

```text
1 / 3
2 / 6
3 / 9
```

All represent the same slope but floating-point values may differ.

Instead store

```text
dy / dx
```

in reduced form.

Example

```text
dy = 6
dx = 9

gcd = 3

Slope = 2/3
```

Now every equivalent slope has the same representation.

---

## Example

```text
points

(1,1)
(2,2)
(3,3)
(4,5)
```

Fix

```text
(1,1)
```

Slopes

| Point | Slope |
| ----- | ----- |
| (2,2) | 1/1   |
| (3,3) | 1/1   |
| (4,5) | 4/3   |

Map

```text
1/1 → 2
4/3 → 1
```

Maximum through this point

```text
2 + 1 = 3 points
```

---

## Algorithm

For every point:

1. Create a new HashMap.
2. Compute `dx` and `dy`.
3. Reduce using GCD.
4. Create key

```text
dy/dx
```

5. Increase frequency.
6. Track maximum slope frequency.
7. Update global answer.

---

## Correctness

For a fixed point:

* Every point on the same line has the same normalized slope.
* The HashMap counts how many points share that slope.
* Repeating the process for every starting point ensures every possible line is considered.

Thus, the maximum frequency found is the maximum number of collinear points.

---

## Complexity

| Metric | Complexity |
| ------ | ---------- |
| Time   | **O(n²)**  |
| Space  | **O(n)**   |

---

# 4. Java Code

```java
class Solution {

    public int maxPoints(int[][] points) {

        int n = points.length;

        if (n <= 2)
            return n;

        int answer = 2;

        for (int i = 0; i < n; i++) {

            HashMap<String, Integer> map = new HashMap<>();
            int max = 0;

            for (int j = i + 1; j < n; j++) {

                int dx = points[j][0] - points[i][0];
                int dy = points[j][1] - points[i][1];

                int g = gcd(dx, dy);

                dx /= g;
                dy /= g;

                // Normalize sign
                if (dx < 0) {
                    dx = -dx;
                    dy = -dy;
                } else if (dx == 0) {
                    dy = 1;
                } else if (dy == 0) {
                    dx = 1;
                }

                String key = dy + "/" + dx;

                int count = map.getOrDefault(key, 0) + 1;
                map.put(key, count);

                max = Math.max(max, count);
            }

            answer = Math.max(answer, max + 1);
        }

        return answer;
    }

    private int gcd(int a, int b) {

        if (b == 0)
            return Math.abs(a);

        return gcd(b, a % b);
    }
}
```

---

# Dry Run

### Input

```text
points

(1,1)
(2,2)
(3,3)
```

Fix first point

```text
(1,1)
```

Compute slopes

```text
(2,2)

dy = 1
dx = 1

Slope = 1/1
```

```text
(3,3)

dy = 2
dx = 2

Reduce

1/1
```

HashMap

```text
1/1 → 2
```

Maximum

```text
2 + 1 = 3
```

Answer

```text
3
```

---

# Edge Cases

| Input             | Output                                    |
| ----------------- | ----------------------------------------- |
| `[[1,1]]`         | `1`                                       |
| `[[1,1],[2,2]]`   | `2`                                       |
| Vertical line     | Correctly handled                         |
| Horizontal line   | Correctly handled                         |
| Negative slope    | Correctly handled                         |
| Large coordinates | Correctly handled using GCD normalization |

---

# Complexity Summary

| Approach                    | Time      | Space    |
| --------------------------- | --------- | -------- |
| Brute Force                 | **O(n³)** | **O(1)** |
| HashMap + Normalized Slopes | **O(n²)** | **O(n)** |

---

# Interview Tips

* Never compare slopes using `double`.
* Store slopes as reduced fractions using **GCD**.
* Normalize signs so equivalent slopes have identical keys.
* Vertical lines should always map to the same key (e.g., `1/0`).
* Horizontal lines should always map to the same key (e.g., `0/1`).
* The standard interview solution is **Fix One Point + HashMap of Normalized Slopes**, giving **O(n²)** time.
