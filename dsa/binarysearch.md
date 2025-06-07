# binary search

core idea:
- we use a divide and conquer binary search approach to effeciently find some element
- generally, we require a sorted input array

## find minimum in rotated sorted array

> Suppose an array of length n sorted in ascending order is rotated between 1 and n times. For example, the array nums = `[0,1,2,4,5,6,7]` might become:
>
> `[4,5,6,7,0,1,2]` if it was rotated 4 times.
> `[0,1,2,4,5,6,7]` if it was rotated 7 times.
> Notice that rotating an array `[a[0], a[1], a[2], ..., a[n-1]]` 1 time results in the array `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]`.
>
> Given the sorted rotated array nums of unique elements, return the minimum element of this array.
>
> You must write an algorithm that runs in O(log n) time.

- we first check if the current section is sorted
    - by nature of how we move the left and right pointers, if the section we find is sorted, the min must be in the window
- if not, then if the middle element is greater than the left, we note the min must be to the right

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        l, r = 0, len(nums) - 1
        ret = float('inf')

        while l <= r:
            if nums[l] < nums[r]: # if the current section is sorted
                ret = min(ret, nums[l])
                break

            m = l + ((r - l) // 2)
            ret = min(ret, nums[m])
            if nums[m] >= nums[l]: # search right
                l = m + 1
            else: # search left
                r = m - 1

        return ret
```
