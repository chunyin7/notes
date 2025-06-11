# two pointers

core idea:

- we use 2 pointers, usually a left and right pointer to traverse an array more effeciently
- we can conditionally shift either of the pointers if needed

## 3sum

> Given an integer array nums, return all the triplets \[nums\[i\], nums\[j\], nums\[k\]\] such that i != j, i != k, and j != k, and nums\[i\] + nums\[j\] + nums\[k\] == 0.
>
> Notice that the solution set must not contain duplicate triplets.

- first sort `nums`, such that we can control the increase/decrease of the sum when shifting
- we iterate across `nums` in an outer loop, considering each element
    - then we scan the rest of the array with a left and right pointer, shifting the corresponding pointer to help us get closer to our solution
- we use a set `used` to track when we use a member of nums, avoiding duplicate triplets
    - ensures we don't start the 2sum search with the same starting number again
    - due to the array being sorted, we also avoid duplicates as the outer loop iterates forward

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        n = len(nums)

        ret = []
        used = set()
        for i, num in enumerate(nums):
            if num in used:
                continue

            used.add(num)
            l = i + 1
            r = n - 1
            target = -1 * num
            while l < r:
                nl = nums[l]
                nr = nums[r]

                if nl + nr < target:
                    l += 1
                elif nl + nr > target:
                    r -= 1
                else:
                    ret.append([num, nl, nr])
                    l += 1
                    while l < r and nums[l] == nl:
                        l += 1

        return ret
```

---

## container with most water

> You are given an integer array height of length n. There are n vertical lines drawn such that the two endpoints of the ith line are (i, 0) and (i, height\[i\]).
> Find two lines that together with the x-axis form a container, such that the container contains the most water.
> Return the maximum amount of water a container can store.

- bottlenecked by minimum height
- we shift the pointer that is the bottleneck
    - shifting inwards causes the x axis size to decrease, so we must try to optimise the smaller height
    - a greedy approach where we systematically remove the worst of the 2 current options

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        n = len(height)
        ret = 0

        l = 0
        r = len(height) - 1

        while l < r:
            lh = height[l]
            rh = height[r]
            d = r - l

            h = min(lh, rh)
            ret = max(ret, h * d)

            if lh <= rh:
                l += 1
            else:
                r -= 1

        return ret
```
