# 4. Median of Two Sorted Arrays

LeetCode: https://leetcode.com/problems/median-of-two-sorted-arrays/

## Python solution

```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        if not nums1:
            return self.getMedianFromList(nums2)
        if not nums2:
            return self.getMedianFromList(nums1)
        if len(nums1) == 1 and len(nums2) == 1:
            return (nums1[0] + nums2[0]) * 0.5

        l1, r1, l2, r2 = 0, len(nums1) - 1, 0, len(nums2) - 1
        m = r1-l1+1
        n = r2-l2+1
        m1, m2 = nums1[(l1+r1+1)//2], nums2[(l2+r2+1)//2]
        while l1 < r1 - 1 and l2 < r2 - 1 and m1 != m2:
            diff = (min(m,n)-1) // 2
            if m1 < m2:
                l1 += diff
                r2 -= diff
            else:
                l2 += diff
                r1 -= diff
            m = r1-l1+1
            n = r2-l2+1
            m1, m2 = nums1[(l1+r1+1)//2], nums2[(l2+r2+1)//2]

        if m1 == m2:
            if m % 2 == 0 and n % 2 == 0:
                candidates = [nums1[(l1+r1+1)//2 - 1], nums2[(l2+r2+1)//2 - 1], m1, m2]
                return self.getMedianFromList(sorted(candidates))
            return m1

        if m == 2 and n == 2:
            return self.getMedianFromList(sorted([nums1[l1], nums1[r1], nums2[l2], nums2[r2]]))
        
        if m < n:
            return self.baseHelper(nums1, l1, r1, nums2, l2, r2)
        # n < m
        return self.baseHelper(nums2, l2, r2, nums1, l1, r1)


    def getMedianFromList(self, nums):
        n = len(nums)
        return nums[n // 2] if n % 2 == 1 else (nums[n // 2] + nums[n//2 - 1]) * 0.5
    
    def baseHelper(self, nums1, l1, r1, nums2, l2, r2):
        # nums1 is the shorter array with 0 < m = r1 - l1 + 1 <= 2 elements
        m = r1 - l1 + 1
        n = r2 - l2 + 1
        med = (l2+r2+1)//2
        candidates = nums1[l1:r1+1] + [nums2[med], nums2[med-1]]
        if m == 1:
            if n % 2 == 1:
                candidates.append(nums2[med+1])
        else: # m == 2
            candidates.append(nums2[med+1])
            if n % 2 == 0:
                candidates.append(nums2[med-2])
        return self.getMedianFromList(sorted(candidates))
```

## Notes
* The core idea is to reduce the size of the two arrays (i.e. removing values that cannot be (part of) the median) using binary search.
* For each iteration, we can eliminate min(m/2, n/2) elements by comparing the median of the two arrays, where m and n are size of the current arrays.
* IMPORTANT: when m == 4 or n == 4, we can eliminate 1 element only (instead of 2)! Otherwise, we may be removing the real median candidate (since the median of an array with even size is the average of the middle two elements).
* IMPORTANT: we must stop once the size of one array reaches 2 (for same reason as above).
* The next important idea is that *Median(array, [k])* equals median of {*the middle 2 or 3 elements of array, k*} (2 elements if array has even length and 3 otherwise).
* Similarly, *Median(array, [s,t])* equals median of {*the middle 4 or 3 elements of array, s, t*} (4 elements if array has even length and 3 otherwise).
* To speed up, we may have early termination when median of the two arrays collide.
