# divide and conquer

## maximum subarray

> Given an integer array `nums`, find the *subarray* with the largest sum, and return its sum.

- this can be done most efficiently with a simple linear loop, however it is also possible to achieve this with divide and conquer
- when we merge, we compute the max of the left max sum, the right max sum and some other sum which crosses the boundary

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        def merge(l, r):
            merged = l[0] + r[0]

            # compute the border sums
            lborder = 0
            lbordermax = -float('inf')
            for i in range(len(l[0]) - 1, -1, -1):
                lborder += l[0][i]
                lbordermax = max(lbordermax, lborder)

            rborder = 0
            rbordermax = -float('inf')
            for i in range(len(r[0])):
                rborder += r[0][i]
                rbordermax = max(rbordermax, rborder)

            return (merged, max(l[1], r[1], rbordermax + lbordermax))

        def divide(nums):
            if len(nums) == 1:
                return (nums, nums[0])

            m = len(nums) // 2
            l = divide(nums[:m])
            r = divide(nums[m:])

            return merge(l, r)

        return divide(nums)[1]
```

---

## convert sorted array to bst

> Given an integer array `nums` where the elements are sorted in ascending order, convert it to *height balanced* a binary search tree.

- we use the mid of the left and right of the divide to join and properly produce a bst subtree

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        def merge(l, m, r):
            # l and r are the roots of their respective subtrees
            # r will always be bigger as the array is sorted

            m.left = l
            m.right = r
            return m

        def divide(nums):
            if len(nums) == 0:
                return None

            m = len(nums) // 2
            l = divide(nums[:m])
            r = divide(nums[m + 1:])
            mid = TreeNode(nums[m])

            return merge(l, mid, r)

        return divide(nums)
```
