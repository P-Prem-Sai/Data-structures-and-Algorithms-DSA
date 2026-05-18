# 2553. Separate the Digits in an Array

Given an array of positive integers `nums`, return an array `answer` that consists of the digits of each integer in `nums` after separating them in the same order they appear in `nums`.

To separate the digits of an integer is to get all the digits it has in the same order.

For example, for the integer `10921`, the separation of its digits is `[1,0,9,2,1]`.

---

## Example 1

### Input
```cpp
nums = [13,25,83,77]
```

### Output
```cpp
[1,3,2,5,8,3,7,7]
```

### Explanation
- The separation of `13` is `[1,3]`.
- The separation of `25` is `[2,5]`.
- The separation of `83` is `[8,3]`.
- The separation of `77` is `[7,7]`.

`answer = [1,3,2,5,8,3,7,7]`.

Note that `answer` contains the separations in the same order.

---

## Example 2

### Input
```cpp
nums = [7,1,3,9]
```

### Output
```cpp
[7,1,3,9]
```

### Explanation
The separation of each integer in `nums` is itself.

`answer = [7,1,3,9]`.

---

## Constraints

```cpp
1 <= nums.length <= 1000
1 <= nums[i] <= 10^5
```

---

# Solution

# Approach 1: Simulation

## Intuition

The problem requires splitting each number in the array into its individual digits and then reassembling those digits in order to form a new array.

When splitting a number `x` digit by digit:

- Taking `x % 10` gives the digit in the units place.
- Dividing `x` by `10` removes the digit in the units place.
- We repeat this process until `x` becomes `0`.

This gives the digits in reverse order, so we store them in a temporary array and later traverse it in reverse order to maintain the correct sequence.

Since all values in the input are positive integers, we do not need to handle the case where `x` starts as `0`.

---

## Implementation

```cpp
class Solution {
public:
    vector<int> separateDigits(vector<int>& nums) {
        vector<int> res;

        for (auto x : nums) {
            vector<int> tmp;

            while (x > 0) {
                tmp.push_back(x % 10);
                x /= 10;
            }

            for (int i = tmp.size() - 1; i >= 0; i--) {
                res.push_back(tmp[i]);
            }
        }

        return res;
    }
};
```

---

## Complexity Analysis

Let `n` be the length of `nums`, and let `M` be the maximum value in `nums`.

### Time Complexity
```cpp
O(n log M)
```

### Space Complexity
```cpp
O(log M)
```

> The output array is not included in the space complexity.

---

# Approach 2: Reverse Traversal

## Intuition

In Approach 1, we use a temporary array to store the digits and then append them in reverse order to the result array.

Instead, we can:

- Traverse `nums` in reverse order.
- Directly append each digit of `x` to the result array while extracting it.
- Reverse the final result array at the end.

This removes the need for a temporary array.

---

## Implementation

```cpp
class Solution {
public:
    vector<int> separateDigits(vector<int>& nums) {
        vector<int> res;

        for (int i = nums.size() - 1; i >= 0; i--) {
            int x = nums[i];

            while (x > 0) {
                res.push_back(x % 10);
                x /= 10;
            }
        }

        reverse(res.begin(), res.end());

        return res;
    }
};
```

---

## Complexity Analysis

Let `n` be the length of `nums`, and let `M` be the maximum value in `nums`.

### Time Complexity
```cpp
O(n log M)
```

### Space Complexity
```cpp
O(1)
```
