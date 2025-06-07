# stacks

stacks are a *first in last out* data structure

they are especially powerful for problems with a **nested structure** or **sequential dependency**

core idea:
- we continue adding elements to the stack
- conditionally, when we find a certain element, we begin the process of popping from the stack and processing some information

## valid parentheses

> Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.
>
> An input string is valid if:
>
> 1. Open brackets must be closed by the same type of brackets.
> 2. Open brackets must be closed in the correct order.
> 3. Every close bracket has a corresponding open bracket of the same type.

- we add opening brackets to the stack
- when we find a closing bracket, we pop the last element from the stack
    - this **must** be the corresponding opening bracket for the string to be valid

```python
class Solution(object):
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """

        stack = []
        d = {'(' : ')', '[' : ']', '{' : '}'} # use a dictionary that maps parentheses types to avoid sequences of if statements
        for c in s:
            if c in d.keys():
                stack.append(c)
            elif len(stack) < 1 or c != d[stack.pop()]:
                return False

        if len(stack) > 0:
            return False
        else:
            return True
```

---

## daily temperatures

> Given an array of integers temperatures represents the daily temperatures, return an array answer such that `answer[i]` is the number of days you have to wait after the ith day to get a warmer temperature. If there is no future day for which this is possible, keep `answer[i]` == 0 instead.

- we maintain the stack to be loosely decreasing - stores tuples of temperature, index pairs
- when we find a warmer day, we continue popping from the stack while that day is warmer

```python
class Solution(object):
    def dailyTemperatures(self, temperatures):
        """
        :type temperatures: List[int]
        :rtype: List[int]
        """
        stack = [(temperatures[0], 0)]
        ret = [0] * len(temperatures)

        for i in range(1, len(temperatures)):
            t = temperatures[i]
            while stack and t > stack[-1][0]:
                last = stack.pop()
                ret[last[1]] = i - last[1]

            stack.append((t, i))

        return ret
```
