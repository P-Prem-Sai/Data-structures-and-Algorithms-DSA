# 2657. Find the Prefix Common Array of Two Arrays

## Problem Statement

You are given two arrays `A` and `B`, both of length `n`.

Both arrays are permutations of integers from `1` to `n`.

We need to construct an array `C` where:

```cpp
C[i]
```

represents the number of common elements between:

```cpp
A[0...i] and B[0...i]
```

---

# Example

## Input

```cpp
A = [1,3,2,4]
B = [3,1,2,4]
```

## Explanation

### i = 0

```cpp
A prefix = [1]
B prefix = [3]
```

No common elements.

```cpp
C[0] = 0
```

---

### i = 1

```cpp
A prefix = [1,3]
B prefix = [3,1]
```

Common elements:

```cpp
1, 3
```

```cpp
C[1] = 2
```

---

### i = 2

```cpp
A prefix = [1,3,2]
B prefix = [3,1,2]
```

Common elements:

```cpp
1, 2, 3
```

```cpp
C[2] = 3
```

---

### i = 3

```cpp
A prefix = [1,3,2,4]
B prefix = [3,1,2,4]
```

Common elements:

```cpp
1, 2, 3, 4
```

```cpp
C[3] = 4
```

---

## Output

```cpp
[0,2,3,4]
```

---

# Approach 1: Brute Force

# Intuition

The straightforward approach is:

For every prefix:

```cpp
A[0...i]
B[0...i]
```

compare every element of `A` with every element of `B`.

Whenever we find a match:

```cpp
A[aIndex] == B[bIndex]
```

we count it as a common element.

Since all numbers are unique:

* once matched, we can stop searching for that element.

---

# Algorithm

1. Iterate through every prefix index.
2. Compare all elements inside current prefixes.
3. Count matching elements.
4. Store result in answer array.

---

# Brute Force Solution (C++)

```cpp
class Solution {
public:
    vector<int> findThePrefixCommonArray(vector<int>& A, vector<int>& B) {

        int n = A.size();

        vector<int> prefixCommonArray(n);

        // Process every prefix
        for (int currentIndex = 0; currentIndex < n; ++currentIndex) {

            int commonCount = 0;

            // Compare all elements in current prefixes
            for (int aIndex = 0; aIndex <= currentIndex; ++aIndex) {

                for (int bIndex = 0; bIndex <= currentIndex; ++bIndex) {

                    if (A[aIndex] == B[bIndex]) {
                        ++commonCount;
                        break;
                    }
                }
            }

            prefixCommonArray[currentIndex] = commonCount;
        }

        return prefixCommonArray;
    }
};
```

---

# Complexity Analysis

## Time Complexity

```cpp
O(N^3)
```

Why?

* Outer loop runs `N` times
* Two nested loops also run up to `N`

Total:

```cpp
O(N × N × N)
```

---

## Space Complexity

```cpp
O(1)
```

Ignoring output array.

Only variables are used.

---

# Approach 2: Hash Set

# Intuition

We can improve the brute force solution using:

```cpp
unordered_set
```

Instead of repeatedly comparing all elements.

---

# Key Idea

Maintain:

```cpp
elementsInA
elementsInB
```

These sets store all elements seen so far.

For every index:

1. Insert current elements into sets
2. Count how many elements exist in both sets

---

# Algorithm

1. Create two sets.
2. Add elements from both arrays while traversing.
3. Count common elements using set lookup.
4. Store result.

---

# Hash Set Solution (C++)

```cpp
class Solution {
public:
    vector<int> findThePrefixCommonArray(vector<int>& A, vector<int>& B) {

        int n = A.size();

        vector<int> prefixCommonArray(n);

        unordered_set<int> elementsInA;
        unordered_set<int> elementsInB;

        for (int currentIndex = 0; currentIndex < n; ++currentIndex) {

            // Insert current elements
            elementsInA.insert(A[currentIndex]);
            elementsInB.insert(B[currentIndex]);

            int commonCount = 0;

            // Count common elements
            for (int element : elementsInA) {

                if (elementsInB.count(element)) {
                    ++commonCount;
                }
            }

            prefixCommonArray[currentIndex] = commonCount;
        }

        return prefixCommonArray;
    }
};
```

---

# Complexity Analysis

## Time Complexity

```cpp
O(N^2)
```

Why?

* Outer loop runs `N` times
* Inner loop iterates over set elements

---

## Space Complexity

```cpp
O(N)
```

Because of the two sets.

---

# Approach 3: Single Pass with Frequency Array (Optimal)

# Intuition

We can optimize further using a frequency array.

---

# Key Observation

Both arrays are permutations of:

```cpp
1 to N
```

So we can directly track occurrences using an array.

---

# Core Idea

Create:

```cpp
frequency[n + 1]
```

For every index:

```cpp
frequency[A[i]]++
frequency[B[i]]++
```

Whenever frequency becomes:

```cpp
2
```

it means:

```cpp
That number has appeared in BOTH arrays
```

So it contributes to the common count.

---

# Why Frequency == 2?

Example:

```cpp
A = [1,3]
B = [3,1]
```

Processing:

### Step 1

```cpp
frequency[1] = 1
frequency[3] = 1
```

No common elements yet.

---

### Step 2

```cpp
frequency[3] = 2
frequency[1] = 2
```

Now both numbers appeared in both arrays.

Common count increases.

---

# Algorithm

1. Create frequency array.
2. Traverse arrays once.
3. Increase frequency of current elements.
4. If frequency becomes 2:

   * increment common count
5. Store common count.

---

# Optimal Solution (C++)

```cpp
class Solution {
public:
    vector<int> findThePrefixCommonArray(vector<int>& A, vector<int>& B) {

        int n = A.size();

        vector<int> prefixCommonArray(n);

        vector<int> frequency(n + 1, 0);

        int commonCount = 0;

        for (int currentIndex = 0; currentIndex < n; ++currentIndex) {

            // Process A[currentIndex]
            if (++frequency[A[currentIndex]] == 2) {
                ++commonCount;
            }

            // Process B[currentIndex]
            if (++frequency[B[currentIndex]] == 2) {
                ++commonCount;
            }

            prefixCommonArray[currentIndex] = commonCount;
        }

        return prefixCommonArray;
    }
};
```

---

# Complexity Analysis

## Time Complexity

```cpp
O(N)
```

Why?

* Single traversal of arrays
* Constant time operations

---

## Space Complexity

```cpp
O(N)
```

Because of frequency array.

---

# Dry Run

## Input

```cpp
A = [1,3,2,4]
B = [3,1,2,4]
```

---

## Step 1

```cpp
frequency[1] = 1
frequency[3] = 1
common = 0
```

```cpp
ans[0] = 0
```

---

## Step 2

```cpp
frequency[3] = 2 -> common++
frequency[1] = 2 -> common++
```

```cpp
common = 2
ans[1] = 2
```

---

## Step 3

```cpp
frequency[2] = 2
common = 3
```

```cpp
ans[2] = 3
```

---

## Step 4

```cpp
frequency[4] = 2
common = 4
```

```cpp
ans[3] = 4
```

---

# Final Output

```cpp
[0,2,3,4]
```

---

# Key Interview Points

## Why does frequency == 2 work?

Because:

* every number appears exactly once in each array
* first occurrence → seen in one array
* second occurrence → now seen in both arrays

---

## Why is this optimal?

Because:

* only one pass
* no nested loops
* no expensive lookups

---

# Comparison of Approaches

| Approach        | Time Complexity | Space Complexity |
| --------------- | --------------- | ---------------- |
| Brute Force     | O(N³)           | O(1)             |
| Hash Set        | O(N²)           | O(N)             |
| Frequency Array | O(N)            | O(N)             |

---

# Takeaways

* Prefix problems often benefit from:

  * frequency arrays
  * hash sets
  * cumulative counting

* Since values are limited from:

```cpp
1 to N
```

frequency arrays become very powerful.

* The optimal solution avoids repeated comparisons completely.
