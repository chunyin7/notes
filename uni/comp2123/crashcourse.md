# SYNCS CRASHCOURSE

## PROOFS

**invariant proofs**:
invariant - some condition (should be related to the question/problem at hand) that remains consistent on each recursive call / iteration

show that the invariant is maintained at each step - i.e. via induction
link invariant termination with producing the correct result

**exchange argument**:
we suppose that the algorithm is incorrect - provide a generic case where the result is trivially incorrect
then show contradiction by walking through the algorithm

## EXAMPLES

**finding the median of 2 sorted arrays in O(log(n+m))**:
using divide and conquer
- split both arrays down the middle
- compare the elements on both sides of the split e.g. l1 and l2, r1 and r2
- based on the comparison above and the count of elements on the left side of the splits (we want that to be more than (n+m)/2), shift one of the splits
    - we shift one of them left if the count is equal to (n+m)/2
    - otherwise we shift one of them right
- then, with the final splits, we compare l1,l2,r1,r2 to get the median

