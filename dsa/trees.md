# trees

## invert binary tree

> Given the root of a binary tree, invert the tree, and return its root.

- use dfs to traverse the tree, swapping children at each node

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        def dfs(node):
            if node is None:
                return

            dfs(node.left)
            dfs(node.right)

            node.left, node.right = node.right, node.left
            return

        dfs(root)
        return root
```

---

## max depth of binary tree

> Given the root of a binary tree, return its maximum depth.
>
> A binary tree's maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        def dfs(node):
            if node is None:
                return 0

            l = dfs(node.left)
            r = dfs(node.right)

            return 1 + max(l, r)

        return dfs(root)
```

## same tree

> Given the roots of two binary trees p and q, write a function to check if they are the same or not.
>
> Two binary trees are considered the same if they are structurally identical, and the nodes have the same value.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        def dfs(node1, node2):
            if node1 is None and node2 is None:
                return True
            elif node1 is None or node2 is None:
                return False

            if node1.val != node2.val:
                return False

            l = dfs(node1.left, node2.left)
            r = dfs(node1.right, node2.right)

            return l and r

        return dfs(p, q)
```

---

## lowest common ancestor of a bst

> Given a binary search tree (BST), find the lowest common ancestor (LCA) node of two given nodes in the BST.
>
> According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself).”

my naive approach:
```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        path_p = []
        path_q = []

        def search(node, path, target):
            if node is None:
                return

            path.append(node)
            if target > node.val:
                search(node.right, path, target)
            elif target < node.val:
                search(node.left, path, target)
            else:
                return

        search(root, path_p, p.val)
        search(root, path_q, q.val)

        union = set(path_p).intersection(set(path_q)) # intersection of their paths

        # then scan to find the lca
        ret = None
        for n in path_p:
            if n in union:
                ret = n
        return ret
```

- we can exploit the fact that we know we are at the lca when `p` and `q` are on different sides of the current node
- if they are both on one side, traverse to that side and continue the search

optimised solution (recursive):
```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        rval = max(p.val, q.val)
        lval = min(p.val, q.val)

        def search(node):
            nonlocal lval
            nonlocal rval

            if node is None:
                return None

            if lval <= node.val and rval >= node.val:
                return node
            elif lval <= node.val and rval <= node.val:
                return search(node.left)
            else:
                return search(node.right)

        return search(root)
```

---

## binary tree level order traversal

> Given the root of a binary tree, return the level order traversal of its nodes' values. (i.e., from left to right, level by level), with each level in its own subarray.

- bfs for level order traversal
- we encode the level of each node in the queue for separation of the values in the ret array

```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        q = deque()
        ret = []
        q.append((root, 0))
        prev = -1

        while q:
            cur = q.popleft()
            if cur[0] is None:
                continue

            if cur[1] != prev:
                prev = cur[1]
                ret.append([])

            ret[-1].append(cur[0].val)
            q.append((cur[0].left, cur[1] + 1))
            q.append((cur[0].right, cur[1] + 1))

        return ret
```

---

## validate binary search tree

> Given the root of a binary tree, determine if it is a valid binary search tree (BST).
>
> A valid BST is defined as follows:
>
> The left subtree of a node contains only nodes with keys less than the node's key.
> The right subtree of a node contains only nodes with keys greater than the node's key.
> Both the left and right subtrees must also be binary search trees.

- note that every time we traverse down the tree, we constrict the range of possible values for a valid bst
    - e.g. if we traverse to a right child, we note that the parent node's value now sets the minimum lower bound for any nodes in this subtree

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def dfs(node, lb, ub):
            if node is None:
                return True

            if node.val <= lb or node.val >= ub:
                return False

            return dfs(node.left, lb, node.val) and dfs(node.right, node.val, ub)

        return dfs(root, -float('inf'), float('inf'))
```

---

## k-th smallest element in bst

> Given the root of a binary search tree, and an integer k, return the `k-th` smallest value (1-indexed) of all the values of the nodes in the tree.

my naive approach:
```python
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        # produce a hashmap, which maps nodes to the number of nodes smaller than them
        d = {}

        def dfs(node, d):
            if node is None:
                return 0

            l = dfs(node.left, d)
            r = dfs(node.right, d)

            d[node] = l + 1
            return l + r + 1

        dfs(root, d)

        def search(node, d, k):
            if node is None:
                return

            lt = d[node]
            if lt == k:
                return node.val
            elif lt > k:
                return search(node.left, d, k)
            else:
                return search(node.right, d, k - lt)

        return search(root, d, k)
```

- we can take advantage of the fact that an in-order traversal of a bst gives elements in ascending order
- then just perform an in order traversal, decrementing k until 0, giving us the k-th smallest element

optimised solution:
```python
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        stack = []
        cur = root

        # iterative dfs rather than recursive - doesn't actually matter
        while stack or cur:
            # go as far left as possible
            while cur:
                stack.append(cur)
                cur = cur.left

            # pop from the stack - the next smallest element
            node = stack.pop()
            k -= 1

            if k == 0:
                return node.val

            # go right
            cur = node.right
```

---

## binary tree from preorder and postorder traversal

> Given two integer arrays `preorder` and `inorder` where `preorder` is the *preorder traversal* of a binary tree and `inorder` is the *inorder traversal* of the same tree, construct and return the binary tree.

- first value in pre-order traversal is **always** the root node
- then, the based on the position of the root node, everything to the left in the inorder array is in the left subtree, and vice versa for the right
- since we consider each node a root for its own subtree, we use a recursive approach

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None

        root = TreeNode(preorder[0])
        mid = inorder.index(preorder[0])
        root.left = self.buildTree(preorder[1:mid + 1], inorder[:mid])
        root.right = self.buildTree(preorder[mid + 1:], inorder[mid + 1:])
        return root
```

the above is unoptimised due to the use of `list.index(...)` and array splicing

a more optimised approach using a hashmap and index manipulation:
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None

        # Pre-compute inorder indices for O(1) lookup.
        inorder_map = {val: i for i, val in enumerate(inorder)}

        def build(p_start, p_end, i_start, i_end):
            # Base case: The range is empty, no node to create.
            if p_start >= p_end or i_start >= i_end:
                return None

            # The first element in the preorder range is the current root.
            root_val = preorder[p_start]
            root = TreeNode(root_val)

            # Find the root's index in the original inorder array.
            inorder_root_idx = inorder_map[root_val]

            # Calculate the number of nodes in the left subtree.
            left_subtree_size = inorder_root_idx - i_start

            # Recursively build the left subtree.
            # Preorder range: [p_start + 1, p_start + 1 + left_subtree_size)
            # Inorder range:  [i_start, inorder_root_idx)
            root.left = build(
                p_start + 1,
                p_start + 1 + left_subtree_size,
                i_start,
                inorder_root_idx,
            )

            # Recursively build the right subtree.
            # Preorder range: [p_start + 1 + left_subtree_size, p_end)
            # Inorder range:  [inorder_root_idx + 1, i_end)
            root.right = build(
                p_start + 1 + left_subtree_size,
                p_end,
                inorder_root_idx + 1,
                i_end,
            )

            return root

        return build(0, len(preorder), 0, len(inorder))
```
