单调栈一般存index。

300. Longest Increasing Subsequence

dp solution, time O(n^2) space O(n^2).
Travers previous number, if smaller than itself, then update itself with the longest previouse subsesquence + 1.


```java
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int[] dp = new int[nums.length];
        dp[0] = 1;
        int max = 1;
        for (int i = 1; i < nums.length; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            max = Math.max(max, dp[i]);
        }
        return max;
    }
```

binary search   time O(nlogn) space O(n)

keep a tail array, each value in index i means the minimum tail value of a length i subsequence. Use binary search to find the first value in tail array which is larger than current value. If not found, add it to the end + 1 of tail array and update the pointer of end. The length is the pointer of end + 1. The out put array of tail is one subsequence, just remove all zeros(inf) in the end of tail array. Or we can keep index in tail, and use another parent array to store previous index. Parent initialized with -1.

```java
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int[] tail = new int[nums.length];
        int ptr = 0;
        tail[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            int pos = binarySearch(tail, 0, ptr, nums[i]);
            tail[pos] = nums[i];
            if (pos > ptr) {
                ptr = pos;
            }
        }
        return ptr + 1;
    }
    
    public int binarySearch(int[] tail, int start, int end, int target) {
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            if (tail[mid] < target) {
                start = mid;
            }
            else {
                end = mid;
            }
        }
        if (tail[start] >= target) {
            return start;
        }
        if (tail[end] >= target) {
            return end;
        }
        return end + 1;
    }
```