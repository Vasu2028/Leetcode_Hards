# LeetCode 68 — Text Justification

**Difficulty:** Hard

---

# 1. Problem Statement

## Text Justification

Given an array of words and an integer `maxWidth`, format the text so that:

* Every line has exactly `maxWidth` characters.
* Words are packed greedily (fit as many words as possible into each line).
* For every line except the last:

  * Spaces should be distributed as evenly as possible.
  * If the spaces cannot be divided equally, the **leftmost gaps receive one extra space**.
* The last line should be **left-justified**, meaning:

  * Words are separated by a single space.
  * Remaining spaces are appended at the end.

Return the fully justified text as a list of strings.

---

## Example 1

```text
Input:
words = ["This","is","an","example","of","text","justification."]
maxWidth = 16

Output:
[
"This    is    an",
"example  of text",
"justification.  "
]
```

---

## Example 2

```text
Input:
words = ["What","must","be","acknowledgment","shall","be"]
maxWidth = 16

Output:
[
"What   must   be",
"acknowledgment  ",
"shall be        "
]
```

---

## Constraints

* `1 <= words.length <= 300`
* `1 <= words[i].length <= 20`
* `words[i]` contains only printable non-space characters.
* `1 <= maxWidth <= 100`
* Every word length is at most `maxWidth`.

---

# 2. Brute Force Approach

## Idea

Build each line by repeatedly adding words until the next word no longer fits.

After collecting the words for a line:

* Count total letters.
* Compute remaining spaces.
* Distribute spaces one by one across the gaps.
* Handle the last line separately.

This approach directly simulates the formatting rules.

### Complexity

* **Time:** O(n × maxWidth)
* **Space:** O(maxWidth)

---

# 3. Optimal Approach

## Idea

Process the words once.

For each line:

1. Greedily collect as many words as fit.
2. If it is the last line or contains only one word:

   * Join words using one space.
   * Pad remaining spaces at the end.
3. Otherwise:

   * Compute

```text
totalSpaces = maxWidth - totalLetters
```

4. Compute

```text
gaps = number of spaces between words
```

5. Every gap gets

```text
baseSpaces = totalSpaces / gaps
```

6. The first

```text
extraSpaces = totalSpaces % gaps
```

gaps receive one additional space.

This satisfies the formatting rules while scanning the array only once.

---

## Example

```text
Words:
This is an

Letters = 8
Width = 16

Spaces = 8
Gaps = 2

Each gap = 4 spaces

Result:
This____is____an
```

(`_` represents a space.)

---

## Algorithm

1. Start from the current word.
2. Add words until the next word exceeds `maxWidth`.
3. Compute total letters.
4. If last line or one word:

   * Left justify.
5. Else:

   * Evenly distribute spaces.
6. Repeat until all words are processed.

---

## Correctness

The algorithm is correct because:

* Every line contains the maximum number of words that fit.
* Total spaces always make the line length exactly `maxWidth`.
* Extra spaces are assigned to the leftmost gaps.
* The final line follows the required left-justification rule.

---

## Complexity

| Metric | Complexity                         |
| ------ | ---------------------------------- |
| Time   | **O(n)**                           |
| Space  | **O(maxWidth)** (excluding output) |

where **n** is the number of words.

---

# 4. Java Code

```java
class Solution {

    public List<String> fullJustify(String[] words, int maxWidth) {

        List<String> result = new ArrayList<>();
        int i = 0;

        while (i < words.length) {

            int lineLength = words[i].length();
            int j = i + 1;

            // Greedily fit as many words as possible
            while (j < words.length &&
                   lineLength + 1 + words[j].length() <= maxWidth) {

                lineLength += 1 + words[j].length();
                j++;
            }

            int wordCount = j - i;
            int totalLetters = 0;

            for (int k = i; k < j; k++)
                totalLetters += words[k].length();

            int totalSpaces = maxWidth - totalLetters;

            StringBuilder line = new StringBuilder();

            // Last line OR single-word line
            if (j == words.length || wordCount == 1) {

                for (int k = i; k < j; k++) {

                    line.append(words[k]);

                    if (k != j - 1)
                        line.append(' ');
                }

                while (line.length() < maxWidth)
                    line.append(' ');

            } else {

                int gaps = wordCount - 1;

                int baseSpaces = totalSpaces / gaps;
                int extraSpaces = totalSpaces % gaps;

                for (int k = i; k < j; k++) {

                    line.append(words[k]);

                    if (k != j - 1) {

                        int spaces = baseSpaces;

                        if (extraSpaces > 0) {
                            spaces++;
                            extraSpaces--;
                        }

                        for (int s = 0; s < spaces; s++)
                            line.append(' ');
                    }
                }
            }

            result.add(line.toString());
            i = j;
        }

        return result;
    }
}
```

---

# Dry Run

```text
words =
["This","is","an","example","of","text","justification."]

maxWidth = 16
```

### Line 1

```text
This is an

Letters = 8
Spaces = 8
Gaps = 2

Result:
This____is____an
```

### Line 2

```text
example of text

Letters = 13
Spaces = 3
Gaps = 2

Gap sizes:
2 and 1

Result:
example__of_text
```

### Line 3

```text
justification.

Last line

Result:
justification.__
```

---

# Edge Cases

| Input                   | Expected                         |
| ----------------------- | -------------------------------- |
| Single word             | Pad spaces at the end            |
| Last line               | Left justified                   |
| Exactly fits width      | No extra padding except required |
| One word in middle line | Word followed by trailing spaces |
| Uneven spaces           | Leftmost gaps receive extras     |

---

# Complexity Summary

| Approach             | Time     | Space           |
| -------------------- | -------- | --------------- |
| Greedy Justification | **O(n)** | **O(maxWidth)** |

---

# Interview Tips

* Greedily collect words for each line.
* Compute total letters first.
* Handle **last line** separately.
* Handle **single-word line** separately.
* For normal lines:

  * `baseSpaces = totalSpaces / gaps`
  * `extraSpaces = totalSpaces % gaps`
* Assign extra spaces to the **leftmost** gaps.
* Use `StringBuilder` to efficiently construct each line.
