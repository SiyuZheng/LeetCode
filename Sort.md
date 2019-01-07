[215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/description/)

Quick Select

```java
    public int findKthLargest(int[] nums, int k) {
        k = nums.length - k;
        return quickSelect(nums, k, 0, nums.length - 1);
    }
    
    public int quickSelect(int[] nums, int k, int start, int end) {
        if (start == end) {
            return nums[start];
        }
        int mid = start + (end - start) / 2;
        int pivot = nums[mid];
        int i = start, j = end;
        while (i <= j) {
            while (i <= j && nums[i] < pivot) {
                i++;
            }
            while (i <= j && nums[j] > pivot) {
                j--;
            }
            if (i <= j) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
                i++;
                j--;
            }
        }
        if (k <= j) {
            return quickSelect(nums, k, start, j);
        }
        if (k >= i) {
            return quickSelect(nums, k, i, end);
        }
        return nums[k];
    }
```