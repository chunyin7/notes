# greedy

in **greedy** algorithms, we iteratively make locally optimal choices in hopes of producing a *globally optimal* solution

generally, a *good* greedy solution relies on a good heuristic of what is *best* at any local point

## jump game

> You are given an integer array `num`s. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.
>
> Return `true` if you can reach the last index, or `false` otherwise.

- we walk backwards through the array, greedily taking any jump that takes us forward

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        # we walk backwards
        cur = len(nums) - 1

        while cur > 0:
            i = 1
            found = False
            while cur - i >= 0:
                if cur - i + nums[cur - i] >= cur:
                    cur -= i
                    found = True
                    break

                i += 1

            if not found:
                return False

        return True
```

---

## jump game 2

> You are given a `0-indexed` array of integers `nums` of length n. You are initially positioned at `nums[0]`.
>
> Each element `nums[i]` represents the maximum length of a forward jump from index `i`. In other words, if you are at `nums[i]`, you can jump to any `nums[i + j]` where:
>
> `0 <= j <= nums[i]` and `i + j < n`
>
> Return the minimum number of jumps to reach `nums[n - 1]`. The test cases are generated such that you can reach `nums[n - 1]`.

- we can perform an augmented bfs based on a sliding window
    - note that standard bfs explores every edge not just every vertex

```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        ret = 0
        l, r = 0, 0

        while r < len(nums) - 1:
            farthest = 0
            for i in range(l, r + 1):
                farthest = max(farthest, i + nums[i])

            l = r + 1
            r = farthest
            ret += 1

        return ret
```

---

## gas station

> There are `n` gas stations along a circular route, where the amount of gas at the i-th station is `gas[i]`.
>
> You have a car with an unlimited gas tank and it costs `cost[i]` of gas to travel from the ith station to its next ``(i + 1)th`` station. You begin the journey with an empty tank at one of the gas stations.
>
> Given two integer arrays gas and cost, return the starting gas station's index if you can travel around the circuit once in the clockwise direction, otherwise return -1. If there exists a solution, it is guaranteed to be unique.

- first we confirm whether there is a solution or not by the sums of the gas and the cost array
- everytime we find that a position doesn't work, we greedily retry from the next position
    - this relies on the fact that we have guarranteed a solution's existance

```python
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        diff = []
        total = 0
        for i in range(len(gas)):
            total += gas[i] - cost[i]
            diff.append(gas[i] - cost[i])

        if total < 0:
            return -1

        start = 0
        g = 0
        for i in range(len(gas) - 1):
            g += diff[i]

            if g < 0:
                start = i + 1
                g = 0

        return start
```

---

## hand of straights

> Alice has some number of cards and she wants to rearrange the cards into groups so that each group is of size `groupSize`, and consists of `groupSize` consecutive cards.
>
> Given an integer array `hand` where `hand[i]` is the value written on the ith card and an integer groupSize, return `true` if she can rearrange the cards, or `false` otherwise.

- note that if the input array is not divisible by `groupSize`, it is obviously impossible
- we use a dict to keep track of frequencies, then simply take the min element in the dict, we continually check for if the next consecutive number is in the dict
- we can optimise with a min element retrieval with a min heap
    - note that when we completely remove an element from the dict, we remove it from the heap as well
    - if that element being removed is not the min element of the heap, the solution must be impossible, as then the next iteration will start with that min value and there will be some gap
