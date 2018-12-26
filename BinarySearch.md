74. Search a 2D Matrix

Not very good solution, use template to avoid endless loop. O(log(m * n))

```java
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        int m = matrix.length, n = matrix[0].length;
        int s = 0, e = m * n - 1;
        while (s + 1 < e) {
            int mid = s + (e - s) / 2;
            int row = mid / n;
            int col = mid % n;
            if (matrix[row][col] < target) {
                s = mid;
            }
            else {
                e = mid;
            }
        }
        int row = s / n;
        int col = s % n;
        if (matrix[row][col] == target) {
            return true;
        }
        row = e / n;
        col = e % n;
        if (matrix[row][col] == target) {
            return true;
        }
        return false;
    }
```
240. Search a 2D Matrix II

Start from right top corner, then traverse with O(m + n).

```java
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        int m = matrix.length;
        int n = matrix[0].length;
        int x = n - 1, y = 0;
        while (x >= 0 && y < m) {
            if (matrix[y][x] == target){
                return true;
            }
            else if (matrix[y][x] < target){
                y++;
            }
            else {
                x--;
            }
        }
        return false;
    }
```

278. First Bad Version


```java
    public int firstBadVersion(int n) {
        int s = 1, e = n;
        while (s + 1 < e) {
            int mid = s + (e - s) / 2;
            if (isBadVersion(mid)) {
                e = mid;
            }
            else {
                s = mid;
            }
        }
        if (isBadVersion(s)) {
            return s;
        }
        return e;
    }
```
33. Search in Rotated Sorted Array

check mid with the left with equal.


```java
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0){
            return -1;
        }
        int left = 0, right = nums.length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] >= nums[left]) {
                if (target <= nums[mid] && target >= nums[left]) {
                    right = mid;
                }
                else {
                    left = mid;
                }
            }
            else{
                if (target >= nums[mid] && target <= nums[right]) {
                    left = mid;
                }
                else {
                    right = mid;
                }
            }
        }
        if (nums[left] == target) {
            return left;
        }
        if (nums[right] == target) {
            return right;
        }
        return -1;
    }
```

81. Search in Rotated Sorted Array II

check mid with the left, if equals, increment left to remove duplicate.

```java
    public boolean search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return false;
        }
        int left = 0, right = nums.length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[left]) {
                if (target >= nums[left] && target <= nums[mid]) {
                    right = mid;
                }
                else {
                    left = mid;
                }
            }
            else if (nums[mid] < nums[left]){
                if (target >= nums[mid] && target <= nums[right]) {
                    left = mid;
                }
                else {
                    right = mid;
                }
            }
            else {
                left++;
            }
        }
        return nums[left] == target || nums[right] == target;
    }
```

153. Find Minimum in Rotated Sorted Array

Compare with the right to avoid no rotated situation.

```java
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[right]) {
                right = mid;
            }
            else {
                left = mid;
            }
        }
        return Math.min(nums[left], nums[right]);
    }
```

154. Find Minimum in Rotated Sorted Array II

If mid equals to right, decrement right to remove duplicate.

```java
    public int findMin(int[] nums) {
        if(nums.length == 1){
            return nums[0];
        }
        int left = 0, right = nums.length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid;
            }
            else if (nums[mid] < nums[right]) {
                right = mid;
            }
            else {
                right--;
            }
        }
        return Math.min(nums[left], nums[right]);
    }
```

162. Find Peak Element

Compare with mid - 1 and mid + 1, since there will be more than three element if we use left + 1 < right, So we will not encounter situation where mid - 1 out of bound.

```java
    public int findPeakElement(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[mid - 1] && nums[mid] > nums[mid + 1]) {
                return mid;   
            }
            else if (nums[mid] <= nums[mid - 1] && nums[mid] >= nums[mid + 1]){
                right = mid;
            }
            else {
                left = mid;
            }
        }
        return nums[left] < nums[right] ? right : left;
    }
```

Find Peak Element II

[link](https://www.lintcode.com/problem/find-peak-element-ii)

O(mlog(n)) or O(nlog(m)). Use binary in one direction, and find whether the max on this row/col is larger than it's neighbors on the other direction.

```java
    public List<Integer> findPeakII(int[][] A) {
        int m = A.length;
        int n = A[0].length;
        int left = 1, right = m - 2;
        List<Integer> lst = new ArrayList<>();
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int col = findMaxCol(A, mid);
            if (A[mid][col] < A[mid - 1][col]) {
                right = mid - 1;
            }
            else if (A[mid][col] < A[mid + 1][col]) {
                left = mid + 1;
            }
            else {
                lst.add(mid);
                lst.add(col);
                return lst;
            }
        }
        return lst;
    }
    
    public int findMaxCol(int[][] A, int row) {
        int max = A[row][0];
        int maxInd = 0;
        for (int i = 1; i < A[0].length - 1; i++) {
            if (A[row][i] > max) {
                max = A[row][i];
                maxInd = i;
            }
        }
        return maxInd;
    }
```




4. Median of Two Sorted Arrays

helper means find the kth element in two arrays.
Compare the element at start + k / 2 - 1, remove the smaller one with previous element.

```java
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int len = nums1.length + nums2.length;
        if (len % 2 == 1) {
            return (double)helper(nums1, nums2, 0, 0, len / 2 + 1);
        }
        else {
            return (helper(nums1, nums2, 0, 0, len / 2) + helper(nums1, nums2, 0, 0, len / 2 + 1)) / 2.0;
        }
    }
    
    public int helper(int[] nums1, int[] nums2, int start1, int start2, int k) {
        if (start1 >= nums1.length) {
            return nums2[start2 + k - 1];
        }
        if (start2 >= nums2.length) {
            return nums1[start1 + k - 1];
        }
        if (k == 1) {
            return Math.min(nums1[start1], nums2[start2]);
        }
        int key1 = (start1 + k / 2 - 1 < nums1.length) ? nums1[start1 + k / 2 - 1] : Integer.MAX_VALUE;
        int key2 = (start2 + k / 2 - 1 < nums2.length) ? nums2[start2 + k / 2 - 1] : Integer.MAX_VALUE;
        if (key1 < key2) {
            return helper(nums1, nums2, start1 + k / 2, start2, k - k / 2);
        }
        else {
            return helper(nums1, nums2, start1, start2 + k / 2, k - k / 2);
        }
    }
```





