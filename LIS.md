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

739. Daily Temperatures

Monotonic Stack, time O(n) space O(n)

```java
    public int[] dailyTemperatures(int[] T) {
        int[] res = new int[T.length];
        Stack<Integer> st = new Stack<>();
        for (int i = 0; i < T.length; i++) {
            while(!st.isEmpty() && T[st.peek()] < T[i]) {
                res[st.peek()] = i - st.pop();
            }
            st.push(i);
        }
        return res;
    }
```

84. Largest Rectangle in Histogram

1. LIS to find first bar in right smaller than itself, and first bar in left smaller than itself, then get the maximum. Not pretty fast, three pass.


```java
    public int largestRectangleArea(int[] heights) {
        int[] left = findLeft(heights);
        int[] right = findRight(heights);
        int max = 0;
        for (int i = 0; i < heights.length; i++) {
            max = Math.max(max, (right[i] - left[i] + 1) * heights[i]);
        }
        return max;
    }
    
    public int[] findRight(int[] heights) {
        int[] res = new int[heights.length];
        Stack<Integer> st = new Stack<>();
        for (int i = 0; i < heights.length; i++) {
            while (!st.isEmpty() && heights[i] < heights[st.peek()]) {
                res[st.pop()] = i - 1;
            }
            st.push(i);
        }
        while (!st.isEmpty()) {
            res[st.pop()] = heights.length - 1;
        }
        return res;
    }
    
    public int[] findLeft(int[] heights) {
        int[] res = new int[heights.length];
        Stack<Integer> st = new Stack<>();
        for (int i = heights.length - 1; i >= 0; i--) {
            while (!st.isEmpty() && heights[i] < heights[st.peek()]) {
                res[st.pop()] = i + 1;
            }
            st.push(i);
        }
        while (!st.isEmpty()) {
            res[st.pop()] = 0;
        } 
        return res;
    }
```

2. increasing stack, if find a smaller one, pop out larger one before, tricky to calculate the area. Remember to deal with those left in the stack.

```java
    public int largestRectangleArea(int[] heights) {
        Stack<Integer> st = new Stack<>();
        int max = 0;
        for (int i = 0; i < heights.length; i++) {
            while(!st.isEmpty() && heights[st.peek()] > heights[i]) {
                max = Math.max(max, heights[st.pop()] * (i - (st.isEmpty() ? 0 : st.peek() + 1)));
            }
            st.push(i);
        }
        while (!st.isEmpty()) {
            max = Math.max(max, heights[st.pop()] * (heights.length - (st.isEmpty() ? 0 : st.peek() + 1)));
        }
        return max;
    }
```

85. Maximal Rectangle
Use a height array for height. On each row use stack to calculate maximum rectangle area like prev problem. O(n^2).

```java
    public int maximalRectangle(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }
        int m = matrix.length;
        int n = matrix[0].length;
        int[] height = new int[n + 1];
        height[n] = 0;
        int max = 0;
        for (int i = 0; i < m; i++) {
            Stack<Integer> st = new Stack<>();
            for (int j = 0; j <= n; j++) {
                if (j < n && matrix[i][j] == '1') {
                    height[j]++;
                }
                else {
                    height[j] = 0;
                }
                while (!st.isEmpty() && height[st.peek()] > height[j]) {
                    int area = height[st.pop()] * (j - (st.isEmpty() ? 0 : st.peek() + 1));
                    max = Math.max(max, area);
                }
                st.push(j);
            }
        }
        return max;
    }
```

334. Increasing Triplet Subsequence

1. Same as longest increasing subsequence

```java
    public boolean increasingTriplet(int[] nums) {
        if (nums == null || nums.length == 0) {
            return false;
        }
        int[] tail = new int[nums.length];
        int ptr = 0;
        tail[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            int pos = binarySearch(tail, 0, ptr, nums[i]);
            tail[pos] = nums[i];
            if (pos > ptr) {
                ptr++;
            }
            if (ptr >= 2) {
                return true;
            }
        }
        return false;
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

2. Two pointers for first min and second min. Use <= !!!!!, if not, secmin might be equal to first min.

```java
    public boolean increasingTriplet(int[] nums) {
        int min = Integer.MAX_VALUE;
        int secMin = Integer.MAX_VALUE;
        for (int num : nums) {
            if (num <= min) {
                min = num;
            }
            else if (num <= secMin) {
                secMin = num;
            }
            else if (num > secMin) {
                return true;
            }
        }
        return false;
    }
```

368. Largest Divisible Subset

Sort the array.
Use a parent array to retrive all integer.
O(n^2) like longest increasing subsequence. Find the largest with previous.

```java
    public List<Integer> largestDivisibleSubset(int[] nums) {
        if (nums == null || nums.length == 0) {
            return new ArrayList<Integer>();
        }
        int[] dp = new int[nums.length];
        int[] parent = new int[nums.length];
        Arrays.sort(nums);
        Arrays.fill(parent, -1);
        int max = 0;
        int maxInd = -1;
        for (int i = 0; i < nums.length; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j++) {
                if (nums[i] % nums[j] == 0 && dp[i] < dp[j] + 1) {
                    dp[i] = dp[j] + 1;
                    parent[i] = j;
                }
            }
            if (dp[i] > max) {
                max = dp[i];
                maxInd = i;
            }
        }
        List<Integer> res = new ArrayList<>();
        while (maxInd != -1) {
            res.add(nums[maxInd]);
            maxInd = parent[maxInd];
        }
        return res;
    }
```

354. Russian Doll Envelopes

dp solution, pretty slow, O(n^2)

```java
    public int maxEnvelopes(int[][] envelopes) {
        if (envelopes == null || envelopes.length == 0 || envelopes[0].length == 0) {
            return 0;
        }
        Arrays.sort(envelopes, new Comparator<int[]>() {
            @Override
            public int compare(int[] a1, int[] a2) {
                if (a1[0] < a2[0]) {
                    return -1;
                }    
                else if (a1[0] > a2[0]) {
                    return 1;
                }
                else {
                    if (a1[1] < a2[1]) {
                        return -1;
                    }
                    else if (a1[1] > a2[1]) {
                        return 1;
                    }
                }
                return 0;
            }
        });
        int[] dp = new int[envelopes.length];
        int max = 0;
        for (int i = 0; i < envelopes.length; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j++) {
                if (envelopes[j][0] < envelopes[i][0] && envelopes[j][1] < envelopes[i][1]) {
                    dp[i] = Math.max(dp[j] + 1, dp[i]);
                }
            }
            max = Math.max(max, dp[i]);
        }
        return max;
    }
```

binary search O(nlogn) for sorting + n * binarysearch
sort by [0] first decreasing then by [1] increasing
This is to make sure whenever we find a new larger [1], [0] is also larger,
so in tail array previous can be inside current one.

```java
    public int maxEnvelopes(int[][] envelopes) {
        if (envelopes == null || envelopes.length == 0 || envelopes[0].length == 0) {
            return 0;
        }
        Arrays.sort(envelopes, new Comparator<int[]>() {
            @Override
            public int compare(int[] a1, int[] a2) {
                if (a1[0] < a2[0]) {
                    return -1;
                }    
                else if (a1[0] > a2[0]) {
                    return 1;
                }
                else {
                    if (a1[1] < a2[1]) {
                        return 1;
                    }
                    else {
                        return -1;
                    }
                }
            }
        });
        int[] tail = new int[envelopes.length];
        int ptr = 0;
        tail[0] = envelopes[0][1];
        for (int i = 1; i < envelopes.length; i++) {
            int pos = binarySearch(tail, 0, ptr, envelopes[i][1]);
            tail[pos] = envelopes[i][1];
            if (pos > ptr) {
                ptr++;
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
