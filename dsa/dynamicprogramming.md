# dynamic programming

essentially a *memoized* recursive approach

## min cost climbing stairs

```python
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        dp = [0] * max(len(cost) + 1, 3)
        dp[0] = dp[1] = 0

        for i in range(2, len(cost) + 1):
            dp[i] = min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2])

        return dp[len(cost)]
```

---

## house robber

> You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and it will automatically contact the police if *two adjacent houses were broken into on the same night*.
>
> Given an integer array `nums` representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.

- we note that we only need to consider 2 or 3 houses down, as past then it is redundant to skip a house

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        dp = [0] * (len(nums) + 2)
        dp[2] = nums[0]

        for i in range(3, len(nums) + 2):
            dp[i] = max(dp[i - 2] + nums[i - 2], dp[i - 3] + nums[i - 3])

        return dp[-1]
```

---

## house robber 2

- the only difference is that the start and end house in the array are now adjacent
- we can do the same as house robber 1, for the subarrays excluding the first element, and the subarray excluding the last element

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) == 1:
            return nums[0]

        def helper(nums: List[int]) -> int:
            rob1, rob2 = 0, 0

            for n in nums:
                newRob = max(rob1 + n, rob2)
                rob1 = rob2
                rob2 = newRob

            return rob2

        return max(helper(nums[:len(nums) - 1]), helper(nums[1:]))
```
