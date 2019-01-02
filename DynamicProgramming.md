198. House Robber

```java
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int[][] dp = new int[nums.length][2];
        dp[0][0] = 0;
        dp[0][1] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1]);
            dp[i][1] = dp[i - 1][0] + nums[i];
        }
        int len = nums.length;
        return Math.max(dp[len - 1][0], dp[len - 1][1]);            
    }
```

Two pointers to reduce space complexity.

```java
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int[] dp = new int[2];
        dp[0] = 0;
        dp[1] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            int skip = Math.max(dp[0], dp[1]);
            int stole = dp[0] + nums[i];
            dp[0] = skip;
            dp[1] = stole;
        }
        return Math.max(dp[0], dp[1]);            
    }
```