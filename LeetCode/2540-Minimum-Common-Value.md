# 2540. Minimum Common Value

## Problem Statement

Given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, return the minimum integer common to both arrays. If there is no common integer, return `-1`.

A common integer is an integer that appears in both arrays.

---

# Approach 1: Hash Set

## Intuition

The brute force solution would use nested loops to compare every element of `nums1` with every element of `nums2`.

That would take:

O(n \times m)

which is inefficient.

Instead, we can use a **hash set** for constant-time lookup.

### Key Idea

* Store all elements of `nums1` in a hash set.
* Traverse `nums2`.
* The first element found in the set is the minimum common value because `nums2` is sorted.

---

## Algorithm

1. Insert all elements of `nums1` into a hash set.
2. Traverse `nums2`.
3. If an element exists in the hash set, return it.
4. If no common element exists, return `-1`.

---

## C++ Code

```cpp
class Solution {
public:
    int getCommon(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> set1(nums1.begin(), nums1.end());

        for (int num : nums2) {
            if (set1.contains(num)) {
                return num;
            }
        }

        return -1;
    }
};
```

---

## Complexity Analysis

### Time Complexity

* Building hash set: `O(n)`
* Traversing `nums2`: `O(m)`

Total:

O(n + m)

---

### Space Complexity

Hash set stores up to `n` elements:

O(n)

---

# Set Intersection Alternative

Another approach is:

* Convert both arrays into sets.
* Find the intersection.
* Return the minimum value from the intersection.

## Python Example

```python
class Solution:
    def getCommon(self, nums1: List[int], nums2: List[int]) -> int:
        set1 = set(nums1)
        set2 = set(nums2)

        common = set1.intersection(set2)

        if common:
            return min(common)

        return -1
```

---

# Approach 2: Two Pointers (Optimal)

## Intuition

Since both arrays are already sorted, we can efficiently traverse them using two pointers.

### Key Observation

The first common element encountered while traversing from left to right will automatically be the minimum common value.

---

## Two Pointer Strategy

* `first` points to `nums1`
* `second` points to `nums2`

### Cases

### Case 1

```text
nums1[first] == nums2[second]
```

We found the minimum common value.

---

### Case 2

```text
nums1[first] < nums2[second]
```

Move `first` forward because we need a larger value.

---

### Case 3

```text
nums1[first] > nums2[second]
```

Move `second` forward because we need a larger value.

---

## Why This Works

We always move the pointer with the smaller value.

This guarantees:

* elements are processed in sorted order
* no possible match is skipped
* first common element found is minimum

---

## Algorithm

1. Initialize two pointers:

   * `first = 0`
   * `second = 0`
2. Compare elements at both pointers.
3. Move the smaller pointer forward.
4. If equal, return the element.
5. If traversal completes, return `-1`.

---

## C++ Code

```cpp
class Solution {
public:
    int getCommon(vector<int>& nums1, vector<int>& nums2) {
        int first = 0;
        int second = 0;

        while (first < nums1.size() && second < nums2.size()) {

            if (nums1[first] < nums2[second]) {
                first++;
            }
            else if (nums1[first] > nums2[second]) {
                second++;
            }
            else {
                return nums1[first];
            }
        }

        return -1;
    }
};
```

---

## Complexity Analysis

### Time Complexity

Each pointer moves at most once through its array.

Total operations:

O(n + m)

---

### Space Complexity

Only a few variables are used:

O(1)

---

# Approach 3: Binary Search

## Intuition

Since arrays are sorted, binary search can efficiently search for an element.

### Key Idea

* Iterate through the smaller array.
* Binary search each element in the larger array.

---

# Binary Search Refresher

Binary search works by repeatedly dividing the search space in half.

### Variables

* `left`
* `mid`
* `right`

### Mid Formula

Use:

```cpp
mid = left + (right - left) / 2;
```

instead of:

```cpp
mid = (left + right) / 2;
```

to avoid integer overflow.

---

## Algorithm

1. Ensure binary search is performed on the larger array.
2. Traverse the smaller array.
3. Binary search each element in the larger array.
4. Return the first common value found.
5. Otherwise return `-1`.

---

## C++ Code

```cpp
class Solution {
public:
    int getCommon(vector<int>& nums1, vector<int>& nums2) {

        // Ensure nums1 is the smaller array
        if (nums1.size() > nums2.size()) {
            return getCommon(nums2, nums1);
        }

        for (int num : nums1) {

            if (binarySearch(num, nums2)) {
                return num;
            }
        }

        return -1;
    }

private:
    bool binarySearch(int target, vector<int>& nums) {

        int left = 0;
        int right = nums.size() - 1;

        while (left <= right) {

            int mid = left + (right - left) / 2;

            if (nums[mid] > target) {
                right = mid - 1;
            }
            else if (nums[mid] < target) {
                left = mid + 1;
            }
            else {
                return true;
            }
        }

        return false;
    }
};
```

---

## Complexity Analysis

Let:

* `n` = size of smaller array
* `m` = size of larger array

### Time Complexity

Binary search takes:

O(\log m)

Performed for all `n` elements:

O(n \log m)

---

### Space Complexity

Only constant extra variables are used:

O(1)

---

# Comparison of Approaches

| Approach      | Time Complexity | Space Complexity |
| ------------- | --------------- | ---------------- |
| Hash Set      | `O(n + m)`      | `O(n)`           |
| Two Pointers  | `O(n + m)`      | `O(1)`           |
| Binary Search | `O(n log m)`    | `O(1)`           |

---

# Best Approach

## Recommended: Two Pointers

Why?

* Optimal time complexity
* Constant space
* Simple implementation
* Takes advantage of sorted arrays

---

# Key Interview Takeaways

* Use **hashing** when fast lookup is needed.
* Use **two pointers** when arrays are sorted.
* Use **binary search** when repeated searching in sorted data is required.
* Always consider whether sorted order can eliminate extra space usage.
