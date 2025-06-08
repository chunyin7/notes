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

---

## search in rotated sorted array

> There is an integer array nums sorted in ascending order (with distinct values).
>
> Prior to being passed to your function, nums is possibly rotated at an unknown pivot index k (1 <= k < nums.length) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (0-indexed). For example, `[0,1,2,4,5,6,7]` might be rotated at pivot index 3 and become `[4,5,6,7,0,1,2]`.
>
> Given the array nums after the possible rotation and an integer target, return the index of target if it is in `nums`, or `-1` if it is not in `nums`.
>
> You must write an algorithm with O(log n) runtime complexity.

- search based on which half the middle pointer is
- then based on the above, search by considering possible cases of where we need to move left or right

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        l, r = 0, len(nums) - 1

        while l <= r:
            m = l + ((r - l) // 2)
            if nums[m] == target:
                return m

            # check which half of the array the middle pointer is
            if nums[m] > nums[r]:
                # the middle pointer is in the left half

                if target > nums[m] or target < nums[l]:
                    # search right
                    l = m + 1
                else:
                    r = m - 1
            else:
                # the middle pointer is in the right half
                if target < nums[m] or target > nums[r]:
                    r = m - 1
                else:
                    l = m + 1

        return -1
```
