# arrays and hashing

notable techniques:

- we can use hash an arrray into a set for O(1) lookup
- for counting frequency of elements we can use a hashmap, with the (element, frequency) key value pair
- we can break up a problem into halves and precompute the halves

## group anagrams

> Given an array of strings `strs`, group the **anagrams** together. You can return the answer in any order.

- for each string, produce a frequency map of its characters with an array
- then we can group the frequency maps together in a hashmap

```python
class Solution(object):
    def groupAnagrams(self, strs):
        """
        :type strs: List[str]
        :rtype: List[List[str]]
        """
        ret = []
        hashmap = defaultdict(list)
        for s in strs:
            count = [0] * 26
            for c in s:
                count[ord(c) - ord('a')] += 1
            count = str(count)
            hashmap[count].append(s)

        for key in hashmap.keys():
            ret.append(hashmap[key])
        return ret
```

---

## top k frequent elements

> Given an integer array `nums` and an integer `k`, return the `k` most frequent elements. You may return the answer in any order.

- we count occurrences of each number in a hashmap
- then we group letters by frequncy into buckets, then walk backwards in the buckets adding the most frequent chars to the return value

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        f = {}

        for n in nums:
            f[n] = f.get(n, 0) + 1

        # bucket sort by frequency
        maxf = max(f.values())
        buckets = [[] for _ in range(maxf)]

        for n in f:
            buckets[f[n] - 1].append(n)

        # now walk backwards in the buckets
        ret = []
        count = 0
        for i in range(maxf - 1, -1, -1):
            if count == k:
                return ret

            for n in buckets[i]:
                ret.append(n)
                count += 1

        return ret
```

---

## product of array except self

> Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`.
>
> The product of any prefix or suffix of nums is guaranteed to fit in a 32-bit integer.
>
> You must write an algorithm that runs in `O(n)` time and *without using the division operation*.

- we can precompute the product of all elements to the left and right of a specific index
- then the final product, is just the product of the 2 given entries of the left and right array

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        # compute the product of all elements before the i-th element
        before = [1]
        n = len(nums)

        for i in range(0, n - 1):
            before.append(nums[i] * before[i])

        # compute the product of all elements after the i-th element
        after = [0] * n
        after[n - 1] = 1

        for i in range(n - 1, 0, -1):
            after[i - 1] = after[i] * nums[i]


        ret = []
        for i in range(n):
            ret.append(before[i] * after[i])

        return ret
```
