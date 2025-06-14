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

---

## decode ways

> You have intercepted a secret message encoded as a string of numbers. The message is decoded via the following mapping:
>
> `"1" -> 'A'`
>
> `"2" -> 'B'`
>
> `...`
>
> `"25" -> 'Y'`
>
> `"26" -> 'Z'`
>
> However, while decoding the message, you realize that there are many different ways you can decode the message because some codes are contained in other codes ("2" and "5" vs "25").
>
> For example, "11106" can be decoded into:
>
> "AAJF" with the grouping (1, 1, 10, 6)
> "KJF" with the grouping (11, 10, 6)
> The grouping (1, 11, 06) is invalid because "06" is not a valid code (only "6" is valid).
>
> Note: there may be strings that are impossible to decode.
>
> Given a string s containing only digits, return the number of ways to decode it. If the entire string cannot be decoded in any valid way, return 0.
>
> The test cases are generated so that the answer fits in a 32-bit integer.

- we can perform a decision tree dfs traversal, caching results we find at specific vertices
- note that if we take a iterative approach, where we iterate in reverse, it is possible to optimise space by using 2 variables rather than a full cache

```python
class Solution:
    def numDecodings(self, s: str) -> int:
        dp = {len(s) : 1}

        def dfs(i):
            if i in dp:
                return dp[i]

            if s[i] == '0':
                return 0

            ret = dfs(i + 1)
            if i < len(s) - 1 and ((s[i] == '1' and int(s[i + 1]) - int('0') < 10) or (s[i] == '2' and int(s[i + 1]) - int('0') < 7)):
                ret += dfs(i + 2)

            dp[i] = ret
            return ret

        return dfs(0)
```

memory optimised version:
```python
class Solution:
    def numDecodings(self, s: str) -> int:
        d1 = 1 # equivalent to dp[i + 1]
        d2 = 0 # equiavalent to dp[i + 2]

        for i in range(len(s) - 1, -1, -1):
            new = 0
            if s[i] != '0':
                new = d1

            if i < len(s) - 1 and ((s[i] == "1" and int(s[i + 1]) - int('0') < 10) or (s[i] == "2" and int(s[i + 1]) - int('0') < 7)):
                new += d2

            d2 = d1
            d1 = new

        return d1
```

---

## maximum product subarray

> Given an integer array `nums`, find a subarray that has the largest product, and return the product
>
> The test cases are generated so that the answer will fit in a 32-bit integer.

- we can incrementally solve the problem, i.e. max prod subarray of `[1, 2, 3]`, is just 1 * the max prod subarray of `[2, 3]`
- we can keep track of both the max and minimum product subarrays due to negative numbers causing inversions
- when we find a 0, we want to reset our ongoing counter

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        ret = max(nums)
        curMin, curMax = 1, 1

        for n in nums:
            tmp = curMax
            curMax = max(n, curMax * n, curMin * n)
            curMin = min(n, curMin * n, tmp * n)

            ret = max(ret, curMax)

        return ret
```

---

## word break

> Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s` can be segmented into a space-separated sequence of one or more dictionary words.
>
> Note that the same word in the dictionary may be reused multiple times in the segmentation.

- initial brute force idea:
    - try to construct `s` using a decision tree traversal of the words in wordDict
- then we can optimise by pruning obviously wrong results
- optimise by caching

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        dp = {}

        def dfs(ss):
            if ss in dp:
                return dp[ss]

            ret = False
            for word in wordDict:
                new = ss + word
                if len(new) < len(s) and new == s[:len(new)]:
                    r = dfs(new)
                    dp[new] = r

                    if r:
                        return True
                elif new == s:
                    return True

            return False

        return dfs("")
```

more spacially efficient iterative index based approach:
```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        dp = [False] * (len(s) + 1)
        dp[len(s)] = True

        for i in range(len(s) - 1, -1, -1):
            for w in wordDict:
                if (i + len(w)) <= len(s) and s[i : i + len(w)] == w:
                    dp[i] = dp[i + len(w)]
                if dp[i]:
                    break

        return dp[0]
```

---

## longest increasing sub sequence

- brute force approach
    - dfs on decision tree of inclusion of each number
- we can optimise by caching whether we can continue from a certain index for building subsequences
    - at each index, we can cache the length of the longest possible subsequence starting from that index

- instead of performing a dfs, we can walk through the array backwards, then check the longest increasing subsequence that starts at all indices greater than the current

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        dp = [1] * len(nums)

        for i in range(len(nums) - 1, -1, -1):
            for j in range(i + 1, len(nums)):
                if nums[j] > nums[i]:
                    dp[i] = max(dp[i], 1 + dp[j])

        return max(dp)
```

- the above approach runs in $O(n^2)$ but we can optimise for $O(n\log{n})$
- we can maintain an array, where each entry `tails[i]`, stores the smallest tail of all increasing subsequences of length `i + 1`
- when we walk through each `num` in `nums`, we have 2 cases:
    - `num` is greater than all elements in `tails`
        - this means that `num` can extend the longest increasing subsequence we've found so far, so we simply append num to tails
    - `num` is not greater than all elements in `tails`
        - this means that `num` cannot extend the current longest subsequence, but it might be able to form a *shorter* subsequence that is potentially useful in the future, as it would be easier to extend
        - in this case, we find the position in `tails`, where num would fit to maintain sorted order, which holds the smallest element greater than or equal to `num`, and replace that element with `num`

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        # 'tails' stores the smallest tail of all increasing
        # subsequences with length i+1
        tails = []

        for num in nums:
            if not tails or num > tails[-1]:
                # Case 1: 'num' is greater than all elements in 'tails'.
                # It extends the LIS, so we append it.
                tails.append(num)
            else:
                # Case 2: 'num' can replace an existing element to form
                # a more promising subsequence.
                l, r = 0, len(tails) - 1

                while l <= r:
                    m = l + ((r - l) // 2)

                    if tails[m] < num:
                        l = m + 1
                    else:
                        r = m - 1

                i = l

                tails[i] = num

        return len(tails)
```

---

## partition equal subset sum

> Given an integer array `nums`, return `true` if you can partition the array into two subsets such that the *sum of the elements in both subsets is equal* or `false` otherwise.

- we can note that we can rephrase this problem as for a target sum (sum of all elements divided by 2), can we find distinct elements in the array that add to this target
- brute force approach:
    - for each number in the list, we form a decision tree of whether to include it or not in our running sum
    - then we simply perform a dfs of this tree
- this can be better optimised by pruning branches that are obviously impossible, i.e. the running sum exceeds the target
- we can also cache our results at each step to optimise further

```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        s = sum(nums)

        if s % 2 != 0:
            return False

        target = s / 2

        dp = {}

        def dfs(a, i):
            if (a, i) in dp:
                return dp[(a, i)]
            if a == target:
                return True
            if i == len(nums) or a > target:
                dp[(a, i)] = False
                return False

            ret = dfs(a + nums[i], i + 1) or dfs(a, i + 1)

            dp[(a, i)] = ret

            return ret

        return dfs(0, 0)
```

an iterative approach that is more memory efficient:
```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        s = sum(nums)
        if s % 2 != 0:
            return False

        target = s / 2

        seen = {0}

        for n in nums:
            a = list(seen)
            for s in a:
                seen.add(n + s)

        return target in seen
```

in this case, we keep a set that tracks all possible sums, then if the target is in the set, we must have `true`
