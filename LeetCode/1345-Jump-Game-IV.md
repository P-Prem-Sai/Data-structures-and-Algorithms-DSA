# 1345. Jump Game IV

## Problem Statement

Given an array of integers `arr`, you are initially positioned at index `0`.

In one step, you can jump from index `i` to:

* `i + 1` where `i + 1 < arr.length`
* `i - 1` where `i - 1 >= 0`
* Any index `j` where:
  `arr[i] == arr[j]` and `i != j`

Return the minimum number of steps to reach the last index of the array.

---

# Intuition

This problem can be modeled as a **graph traversal** problem.

Each index is treated as a node, and from one index we can move to:

1. Left neighbor (`i - 1`)
2. Right neighbor (`i + 1`)
3. Any index having the same value

Since we need the **minimum number of jumps**, the best algorithm is:

* **Breadth First Search (BFS)**

Because BFS always finds the shortest path in an unweighted graph.

---

# Approach 1: BFS (Optimal)

## Key Optimization

Without optimization, finding all indices with the same value would take:

```cpp
O(N) for every node
```

which becomes:

```cpp
O(N^2)
```

To avoid this:

We preprocess all indices having the same value using a hashmap.

Example:

```cpp
arr = [7,6,9,6,9,6,9,7]
```

Store as:

```cpp
7 -> [0,7]
6 -> [1,3,5]
9 -> [2,4,6]
```

Now we can directly access same-value neighbors in `O(1)` average time.

---

# Important Optimization

After visiting all nodes for a value:

```cpp
graph[arr[node]].clear();
```

Why?

Because revisiting the same-value list again is unnecessary and causes repeated work.

This optimization is what makes the solution truly `O(N)`.

---

# BFS Flow

1. Start BFS from index `0`
2. Store visited indices
3. Explore:

   * same value indices
   * left neighbor
   * right neighbor
4. Return steps when reaching `n - 1`

---

# BFS Solution (C++)

```cpp
class Solution {
public:
    int minJumps(vector<int>& arr) {
        int n = arr.size();

        if (n <= 1) {
            return 0;
        }

        // value -> list of indices
        unordered_map<int, vector<int>> graph;

        for (int i = 0; i < n; i++) {
            graph[arr[i]].push_back(i);
        }

        queue<int> q;
        q.push(0);

        vector<bool> visited(n, false);
        visited[0] = true;

        int steps = 0;

        while (!q.empty()) {

            int size = q.size();

            while (size--) {

                int node = q.front();
                q.pop();

                // Reached destination
                if (node == n - 1) {
                    return steps;
                }

                // Same value jumps
                for (int child : graph[arr[node]]) {

                    if (!visited[child]) {
                        visited[child] = true;
                        q.push(child);
                    }
                }

                // Prevent redundant processing
                graph[arr[node]].clear();

                // Right neighbor
                if (node + 1 < n && !visited[node + 1]) {
                    visited[node + 1] = true;
                    q.push(node + 1);
                }

                // Left neighbor
                if (node - 1 >= 0 && !visited[node - 1]) {
                    visited[node - 1] = true;
                    q.push(node - 1);
                }
            }

            steps++;
        }

        return -1;
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

* Every index is visited once
* Same-value lists are cleared after processing

---

## Space Complexity

```cpp
O(N)
```

Used for:

* hashmap
* queue
* visited array

---

# Approach 2: Bidirectional BFS

---

# Why Bidirectional BFS?

Normal BFS expands from only one side.

Sometimes the search space becomes huge.

Instead:

* Start BFS from:

  * beginning
  * ending

Whichever frontier is smaller gets expanded.

This reduces unnecessary traversal.

---

# Core Idea

We maintain:

* `beginSet` → BFS from start
* `endSet` → BFS from end

When both meet:

```cpp
Answer found
```

---

# Bidirectional BFS Solution (C++)

```cpp
class Solution {
public:
    int minJumps(vector<int>& arr) {

        int n = arr.size();

        if (n <= 1) {
            return 0;
        }

        unordered_map<int, vector<int>> graph;

        for (int i = 0; i < n; i++) {
            graph[arr[i]].push_back(i);
        }

        unordered_set<int> beginSet;
        unordered_set<int> endSet;
        unordered_set<int> visited;

        beginSet.insert(0);
        endSet.insert(n - 1);

        visited.insert(0);
        visited.insert(n - 1);

        int steps = 0;

        while (!beginSet.empty()) {

            // Always expand smaller set
            if (beginSet.size() > endSet.size()) {
                swap(beginSet, endSet);
            }

            unordered_set<int> nextSet;

            for (int node : beginSet) {

                // Same value jumps
                for (int child : graph[arr[node]]) {

                    if (endSet.count(child)) {
                        return steps + 1;
                    }

                    if (!visited.count(child)) {
                        visited.insert(child);
                        nextSet.insert(child);
                    }
                }

                // Prevent repeated processing
                graph[arr[node]].clear();

                // Right neighbor
                if (node + 1 < n) {

                    if (endSet.count(node + 1)) {
                        return steps + 1;
                    }

                    if (!visited.count(node + 1)) {
                        visited.insert(node + 1);
                        nextSet.insert(node + 1);
                    }
                }

                // Left neighbor
                if (node - 1 >= 0) {

                    if (endSet.count(node - 1)) {
                        return steps + 1;
                    }

                    if (!visited.count(node - 1)) {
                        visited.insert(node - 1);
                        nextSet.insert(node - 1);
                    }
                }
            }

            beginSet = nextSet;
            steps++;
        }

        return -1;
    }
};
```

---

# Complexity Analysis

## Time Complexity

```cpp
O(N)
```

In practice, usually faster than normal BFS because:

* search depth is reduced
* fewer nodes explored

---

## Space Complexity

```cpp
O(N)
```

Used for:

* hashmap
* visited set
* BFS sets

---

# Key Interview Points

## Why BFS?

Because we need:

```text
Minimum jumps
```

BFS guarantees shortest path in an unweighted graph.

---

## Why clear hashmap list?

```cpp
graph[arr[node]].clear();
```

Without this:

* Same indices get revisited repeatedly
* Causes TLE
* Complexity may become O(N²)

---

## Why Bidirectional BFS is faster?

Instead of exploring:

```text
Start -> End
```

We explore:

```text
Start -> Middle <- End
```

This significantly reduces traversal depth.

---

# Example

```cpp
arr = [100,-23,-23,404,100,23,23,23,3,404]
```

Possible shortest path:

```text
0 -> 4 -> 3 -> 9
```

Answer:

```cpp
3
```

---

# Takeaways

* Convert array problems into graph problems when jumps/transitions exist
* BFS = shortest path in unweighted graph
* Hashmap preprocessing is crucial
* Clearing processed adjacency lists is the key optimization
* Bidirectional BFS can further improve performance
