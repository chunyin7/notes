# sliding window

core idea:
- we use a left and right pointer, which we move across an array to mimic a *sliding window*
    - using this approach, we avoid having to rebuild a subarray at each iteration, increasing efficiency
- we consider all elements in between the pointers for some computation

*essentially* a 2 pointers approach, but the right pointer starts from the left, not the right

notes:
- try avoid complex state and updates as much as possible - only track what is strictly necessary
- 

## best time to buy and sell stock

> You are given an array prices where prices\[i\] is the price of a given stock on the ith day.
>
> You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.
>
> Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

- we send a right pointer out from the left pointer to compare the difference in prices

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        ret = 0
        i = 0
        r = 1
        while r < n:
            p = prices[i]

            if prices[r] > p:
                ret = max(ret, prices[r] - p) 
                r += 1
            else:
                i = r
                r = i + 1

        return ret
```

$O(n)$ solution as we consider each element once

---

## longest substring without repeating characters

> Given a string s, find the length of the longest substring without duplicate characters.

- we keep extending the window size until we run into a duplicate
    - in which case, we shrink the window until the duplicate is gone and then continue

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if s == "":
            return 0

        ret = 1
        n = len(s)
        l = 0
        r = 1

        chars = set()
        chars.add(s[l])
        while r < n:
            if s[r] not in chars:
                chars.add(s[r])
                ret = max(ret, r - l + 1)
                r += 1
            else:
                chars.remove(s[l])
                l += 1

        return ret
```

$O(n)$ solution, as each letter is checked at most twice

---

## longest repeating character replacement

> You are given a string s and an integer k. You can choose any character of the string and change it to any other uppercase English character. You can perform this operation at most k times.
>
> Return the length of the longest substring containing the same letter you can get after performing the above operations.

- we can always expand the window, while only conditionally shrinking it
- dynamically update the most frequent character on the spot, rather than recalculating
- we can use the size of the window to consider the count of the characters that are not the most frequent

```python
class Solution:
    def characterReplacement(self, s: str, k: int) -> int:
        l = 0
        ret = 1
        f = [0] * 26 # track frequencies of characters in the window
        most = 0 # track the most frequent character in the window

        for r in range(len(s)): # always expand the window
            cr = s[r]
            f[ord(cr) - ord('A')] += 1

            most = max(most, f[ord(cr) - ord('A')]) # update the most frequent character

            window_len = r - l + 1
            if window_len - most > k:
                cl = s[l]
                f[ord(cl) - ord('A')] -= 1
                l += 1

            window_len = r - l + 1
            ret = max(ret, window_len)

        return ret
```
