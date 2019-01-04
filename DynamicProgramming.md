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
            int steal = dp[0] + nums[i];
            dp[0] = skip;
            dp[1] = steal;
        }
        return Math.max(dp[0], dp[1]);            
    }
```

Circular array

```java
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }
        if (nums.length == 2) {
            return Math.max(nums[0], nums[1]);
        }
        int[] dp = new int[2];
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);
        for (int i = 2; i < nums.length; i++) {
            dp[i % 2] = Math.max(dp[(i - 2) % 2] + nums[i], dp[(i - 1) % 2]);
        }
        return Math.max(dp[0], dp[1]);
    }
```

213. House Robber II

Calculate skip first house or skip last house and get the maximum.

```java
    public int rob(int[] nums) {
        if(nums == null || nums.length == 0){
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }
        int skipFirst = helper(nums, 1, nums.length - 1);
        int skipLast = helper(nums, 0, nums.length - 2);
        return Math.max(skipFirst, skipLast);
    }
    
    public int helper(int[] nums, int start, int end) {
        int[] dp = new int[2];
        dp[0] = 0;
        dp[1] = nums[start];
        for (int i = start + 1; i <= end; i++) {
            int steal = dp[0] + nums[i];
            int skip = Math.max(dp[0], dp[1]);
            dp[0] = skip;
            dp[1] = steal;
        }
        return Math.max(dp[0], dp[1]);
    }
```

337. House Robber III

"optimal substructure": if we want to rob maximum amount of money from current binary tree (rooted at root), we surely hope that we can do the same to its left and right subtrees.

Max = rob Current + rob left.left + rob left.right + rob right.left + rob right.right   ||  rob left + rob right

Use a map for memorize. O(n).

```java
    public int rob(TreeNode root) {
        Map<TreeNode, Integer> map = new HashMap<>();
        return robHelper(root, map);
    }
    
    public int robHelper(TreeNode node, Map<TreeNode, Integer> map) {
        if (node == null) {
            return 0;
        }
        if (map.containsKey(node)) {
            return map.get(node);
        }
        int val = node.val;
        if (node.left != null) {
            val += robHelper(node.left.left, map) + robHelper(node.left.right, map);
        }
        if (node.right != null) {
            val += robHelper(node.right.left, map) + robHelper(node.right.right, map);
        }
        int res = Math.max(val, robHelper(node.left, map) + robHelper(node.right, map));
        map.put(node, res);
        return res;
    }
```


Or user a int array int[2]. int[0] means skip current room, int[1] means rob current room. null -> int[0] = 0 int[1] = 0.

```java
public int rob(TreeNode root) {
    int[] res = robSub(root);
    return Math.max(res[0], res[1]);
}

private int[] robSub(TreeNode root) {
    if (root == null) return new int[2];
    
    int[] left = robSub(root.left);
    int[] right = robSub(root.right);
    int[] res = new int[2];

    res[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
    res[1] = root.val + left[0] + right[0];
    
    return res;
}
```

276. Paint Fence

w(n) number of ways to paint n posts

p(n) color of the nth post

w(n) consists of two cases:

1.p(n) == p(n - 1)

2.p(n) != p(n - 1)

case 2 is easy. for every way of painting all previous (n - 1) posts, you have (k - 1) ways to paint p(n)
because you can choose k - 1 different color rather than the same color as p(n - 1)

so w(n - 1) * (k - 1)

notice that for p(n) == p(n - 1), p(n - 1) must be not equal to p(n - 2), this is equalvalent to replace n by n - 1
for the formular above, essentially the same as case2 but for a smaller n.
so w(n - 1 - 1) * (k - 1)

so w(n) = (k - 1) * (w(n - 1) + w(n - 2))

```java
    public int numWays(int n, int k) {
        if (n == 0 || k == 0) {
            return 0;
        }
        int[] dp = new int[3];
        dp[0] = 0;
        dp[1] = k;
        dp[2] = k * k;
        for (int i = 3; i <= n; i++) {
            dp[i % 3] = dp[(i - 2) % 3] * (k - 1) + dp[(i - 1) % 3] * (k - 1);
        }
        return dp[n % 3];
    }
```
256. Paint House

Circular array to keep track of three differnt colors.

```java
    public int minCost(int[][] costs) {
        if (costs == null || costs.length == 0) {
            return 0;
        }
        int[] red = new int[2];
        int[] blue = new int[2];
        int[] green = new int[2];
        red[0] = costs[0][0];
        blue[0] = costs[0][1];
        green[0] = costs[0][2];
        for (int i = 1; i < costs.length; i++) {
            red[i % 2] = Math.min(blue[(i - 1) % 2], green[(i - 1) % 2]) + costs[i][0];
            blue[i % 2] = Math.min(red[(i - 1) % 2], green[(i - 1) % 2]) + costs[i][1];
            green[i % 2] = Math.min(red[(i - 1) % 2], blue[(i - 1) % 2]) + costs[i][2];
        }
        int last = costs.length - 1;
        return Math.min(red[last % 2], Math.min(blue[last % 2], green[last % 2]));
    }
```

265. Paint House II

Store some pointers for cur Minimum dp value and cur minmum index, also the second minimum. So that for next house, if j == prev min Ind, then paint with second min, if not then paint with min ind. O(m * n)

```java
    public int minCostII(int[][] costs) {
        if (costs == null || costs.length == 0 || costs[0].length == 0) {
            return 0;
        }
        int m = costs.length;
        int n = costs[0].length;
        int[][] dp = new int[m][n];
        int prevMinInd = 0;
        int prevSecInd = 0;
        for (int i = 0; i < m; i++) {
            int curMin = Integer.MAX_VALUE;
            int curSecMin = Integer.MAX_VALUE;
            int curMinInd = 0;
            int curSecInd = 0;
            for (int j = 0; j < n; j++) {
                if (i == 0) {
                    dp[i][j] = costs[i][j];
                }
                else {
                    if (j == prevMinInd) {
                        dp[i][j] = dp[i - 1][prevSecInd] + costs[i][j];
                    }
                    else {
                        dp[i][j] = dp[i - 1][prevMinInd] + costs[i][j];
                    }
                }
                if (dp[i][j] < curMin) {
                    curSecInd = curMinInd;
                    curSecMin = curMin;
                    curMin = dp[i][j];
                    curMinInd = j;
                }
                else if (dp[i][j] < curSecMin) {
                    curSecInd = j;
                    curSecMin = dp[i][j];
                }
            }
            prevMinInd = curMinInd;
            prevSecInd = curSecInd;
        }
        return dp[m - 1][prevMinInd];
    }
```

63. Unique Paths II

Check whether the start point and the end point are obstacle.

Also check whether first column contains obstacle.

```java
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        if (obstacleGrid[m - 1][n - 1] == 1 || obstacleGrid[0][0] == 1) {
            return 0;
        }
        int[] dp = new int[m];
        for (int i = 0; i < m; i++) {
            if (obstacleGrid[i][0] == 1) {
                break;
            }
            dp[i] = 1;
        }
        for (int j = 1; j < n; j++) {
            for (int i = 0; i < m; i++) {
                if (obstacleGrid[i][j] == 1) {
                    dp[i] = 0;
                }
                else {
                    dp[i] = dp[i] + (i > 0 ? dp[i - 1] : 0); 
                } 
            }                           
        }
        return dp[m - 1];
    }
```

64. Minimum Path Sum

Modify on original grid. O(1).

```java
    public int minPathSum(int[][] grid) {
        for(int i = 0; i < grid.length; i++){
            for(int j = 0; j < grid[0].length; j++){
                if(i == 0 && j == 0){
                    continue;
                }
                if(i == 0){
                    grid[i][j] += grid[i][j - 1];
                }
                else if(j == 0){
                    grid[i][j] += grid[i - 1][j];
                }
                else{
                    grid[i][j] += Math.min(grid[i][j - 1], grid[i - 1][j]);
                }
                
            }
        }
        return grid[grid.length - 1][grid[0].length - 1];
    }
```
221. Maximal Square

check the left, upper, and left-upper grid of dp array.
When using circular array, remember to reset it's value to be zero, when current grid is zero.

```java
    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0){
            return 0;
        }   
        int m = matrix.length;
        int n = matrix[0].length;
        int[][] dp = new int[2][n];
        int max = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    dp[i % 2][j] = 1;
                    if (i > 0 && j > 0) {
                        int min = Math.min(dp[(i - 1) % 2][j - 1], Math.min(dp[(i - 1) % 2][j], dp[i % 2][j - 1]));
                        dp[i % 2][j] = min + 1;
                    }
                    max = Math.max(max, dp[i % 2][j]);
                }
                else {
                    dp[i % 2][j] = 0;
                }
            }
        }
        return max * max;
    }
```
Follow up: longest diagnal one with other grid to be zero.







## Memorization Search


Warm up :

674. Longest Continuous Increasing Subsequence

```java
    public int findLengthOfLCIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int cur = 1;
        int max = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                cur++;
                max = Math.max(max, cur);
            }
            else {
                cur = 1;
            }
        }
        return max;
    }
```
329. Longest Increasing Path in a Matrix

Use a map / int[][] to keep track of point we already calculated. 

This is a direct graph so we don't need to care about revisiting same point.

If that's difference of three path, then we cannot memo and we need to use a visited array. Since we may have path on both sides.

Time O(mn) space O(mn) 

```java
    int[] dx = {-1, 0, 1, 0};
    int[] dy = {0, -1, 0, 1};
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }
        int[][] map = new int[matrix.length][matrix[0].length];
        int max = 0;
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                max = Math.max(max, helper(matrix, map, i, j));
            }
        }
        return max;
    }
    
    public int helper(int[][] matrix, int[][] map, int x, int y) {
        if (map[x][y] != 0) {
            return map[x][y];
        }
        int res = 1;
        int cur = matrix[x][y];
        for (int k = 0; k < 4; k++) {
            int nx = x + dx[k];
            int ny = y + dy[k];
            if (nx < 0 || ny < 0 || nx >= matrix.length || ny >= matrix[0].length || matrix[nx][ny] <= cur) {
                continue; 
            }
            res = Math.max(res, helper(matrix, map, nx, ny) + 1);
        }
        map[x][y] = res;
        return res;
    }
```


## Optimal Game Strategy Problem

[394. Coins in a Line](https://www.lintcode.com/problem/coins-in-a-line/description)

win[i] wins if (win[i - 1] cannot win or win[i - 2] cannot win). Take turns.

```java
    public boolean firstWillWin(int n) {
        if (n == 0) {
            return false;
        }
        if (n <= 2) {
            return true;
        }
        boolean[] res = new boolean[n + 1];
        res[1] = true;
        res[2] = true;
        for (int i = 3; i <= n; i++) {
            res[i] = !res[i - 1] || !res[i - 2];
        }
        return res[n];
    }
```

[395. Coins in a Line II](https://www.lintcode.com/problem/coins-in-a-line-ii/description)

dp[i] means start from i, the max value the player can take.
sum[i] means the total value start from i.

```java
    public boolean firstWillWin(int[] values) {
        if (values == null || values.length == 0) {
            return false;
        }
        if (values.length <= 2) {
            return true;
        }
        int len = values.length;
        int[] sum = new int[len];
        for (int i = len - 1; i >= 0; i--) {
            sum[i] = (i < len - 1 ? sum[i + 1] : 0) + values[i];
        }
        int[] dp = new int[values.length + 1];
        
        dp[len - 1] = values[len - 1];
        dp[len - 2] = values[len - 2] + values[len - 1];
        for (int i = len - 3; i >= 0; i--) {
            dp[i] = Math.max(sum[i] - dp[i + 1], sum[i] - dp[i + 2]);
        }
        return dp[0] > sum[0] / 2;
    }
```

[396. Coins in a Line III](https://www.lintcode.com/problem/coins-in-a-line-iii/description)

MinMAX

Use takeLeft or takeRight, the min is the next move of current player since the opponent will make the optimal decision. If start > end it means current player can only get 0 point.

O(n ^ 2)

```java
    public boolean firstWillWin(int[] values) {
        int[][] dp = new int[values.length][values.length];
        int res = memorizeSearch(values, dp, 0, values.length - 1);
        int sum = 0;
        for (int i : values) {
            sum += i;
        }
        return res * 2 > sum;
    }
    
    public int memorizeSearch(int[] values, int[][] dp, int start, int end) {
        if (start > end) {
            return 0;
        }
        if (dp[start][end] != 0) {
            return dp[start][end];
        }
        int res = 0;
        int takeLeft = values[start] + Math.min(memorizeSearch(values, dp, start + 2, end), memorizeSearch(values, dp, start + 1, end - 1));
        int takeRight = values[end] + Math.min(memorizeSearch(values, dp, start + 1, end - 1), memorizeSearch(values, dp, start, end - 2));
        dp[start][end] = Math.max(takeLeft, takeRight);
        
        return dp[start][end];
    }
```

[375. Guess Number Higher or Lower II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

dp[][], search for the minimum cost if guess i. The minimum cost is built by the maximum cost whether the pick number is larger or smaller than i. O(n ^ 2).

```java
    public int getMoneyAmount(int n) {
        int[][] dp = new int[n + 1][n + 1];
        return memorySearch(dp, 1, n);
    }
    
    public int memorySearch(int[][] dp, int start, int end) {
        if (start >= end) {
            return 0;
        }
        if (dp[start][end] != 0) {
            return dp[start][end];
        }
        int res = Integer.MAX_VALUE;
        for (int i = start; i <= end; i++) {
            res = Math.min(res, i + Math.max(memorySearch(dp, start, i - 1), memorySearch(dp, i + 1, end)));
        }
        dp[start][end] = res;
        return dp[start][end];
    }
```

[293. Flip Game](https://leetcode.com/problems/flip-game/description/)

```java
    public List<String> generatePossibleNextMoves(String s) {
        char[] array = s.toCharArray();
        List<String> res = new ArrayList<>();
        for (int i = 0; i < s.length() - 1; i++) {
            if (array[i] == '+' && array[i + 1] == '+'){
                array[i] = '-';
                array[i + 1] = '-';
                res.add(new String(array));
                array[i] = '+';
                array[i + 1] = '+';
            }
        }
        return res;
    }
```

[294. Flip Game II](https://leetcode.com/problems/flip-game-ii/)

Next level is whether the opponent lose.

At most n - 1 to make "++" become "--"
Better than O(n!!) double factorial (n - 1) * (n - 3) * (n - 5) ...

```java
    public boolean canWin(String s) {
        Map<String, Boolean> map = new HashMap<>();
        return helper(map, s);
    }
    
    public boolean helper(Map<String, Boolean> map, String s) {
        if (map.containsKey(s)) {
            return map.get(s);
        }
        char[] cs = s.toCharArray();
        boolean res = false;
        for (int i = 0; i < cs.length - 1; i++) {
            if (cs[i] == '+' && cs[i + 1] == '+') {
                cs[i] = '-';
                cs[i + 1] = '-';
                if (!helper(map, new String(cs))) {
                    res = true;
                    break;
                }
                cs[i] = '+';
                cs[i + 1] = '+';
            }
        }
        map.put(s, res);
        return res;
    }
```

[476. Stone Game](https://www.lintcode.com/problem/stone-game/description)

Split the array in to two part, use precalculated sum array to get the score, see the comment. 

We can use prefix sum array to save some space.

```java
    public int stoneGame(int[] A) {
        int[][] sum = new int[A.length][A.length];
        int[][] dp = new int[A.length][A.length];
        for (int i = 0; i < A.length; i++) {
            sum[i][i] = A[i];
            for (int j = i + 1; j < A.length; j++) {
                sum[i][j] = sum[i][j - 1] + A[j];
            }
        }
        return memorySearch(sum, dp, 0, A.length - 1);
    }
    
    public int memorySearch(int[][] sum, int[][] dp, int start, int end) {
        if (start >= end) {
            return 0;
        }
        // if two index is adjacent, we have cost their sum
        if (start + 1 == end) {
            return sum[start][end];
        }
        if (dp[start][end] != 0){
            return dp[start][end];
        }
        int min = Integer.MAX_VALUE;
        // split this array 
        // [3, 4, 3] - > [3], [4, 3] or [3, 4], [3]
        //                3       7       7      3
        // [4, 3]  -> 7
        // res = 17
        // cost means the cost to split this array from start to i(inclusive)
        for (int i = start; i <= end - 1; i++) {
            int cost = sum[start][i] + sum[i + 1][end] + memorySearch(sum, dp, start, i) + memorySearch(sum, dp, i + 1, end);
            min = Math.min(min, cost);
            
        }
        dp[start][end] = min;
        return min;
    }
```

[312. Burst Balloons](https://leetcode.com/problems/burst-balloons/description/)

Padding 1. Merge the interval by getting score with the outer adjacent balloons.

1 3 1 5 8 1

1 8 1 + 1 3 8 + 3 5 8 + 3 1 5

the left and right bollon score is not included in [start, end]

```java
    public int maxCoins(int[] nums) {
        int len = nums.length;
        int[] coins = new int[len + 2];
        coins[0] = 1;
        coins[len + 1] = 1;
        for (int i = 1; i <= len; i++) {
            coins[i] = nums[i - 1];
        }
        int[][] dp = new int[len + 2][len + 2];
        return memorySearch(dp, coins, 1, len);
    }
    
    public int memorySearch(int[][] dp, int[] coins, int start, int end) {
        if (start > end) {
            return 0;
        }
        if (dp[start][end] != 0) {
            return dp[start][end];
        }
        int res = 0;
        for (int i = start; i <= end; i++) {
            int score = coins[start - 1] * coins[i] * coins[end + 1];
            score += memorySearch(dp, coins, start, i - 1);
            score += memorySearch(dp, coins, i + 1, end);
            res = Math.max(res, score);
        }
        dp[start][end] = res;
        return res;
    }
```

[87. Scramble String](https://leetcode.com/problems/scramble-string/description/)


## Devide Array

### Kadane's algorithm


```java
	int[] leftMax = new int[n];
	int prefixSum = 0;
	// 代表从起始位置开始，值最小的连续和数组
	int localMin = 0;
	int globalMax = Integer.MIN_VALUE;
	for(int i = 0; i < n; i++){
		prefixSum += nums[i];
		globalMax = Math.max(globalMax, prefixSum - localMin);
		localMin = Math.min(localMin, prefixSum);
		leftMax[i] = globalMax;
	}
```

Or we might init with nums[0] and update min from index 1.


[44. Minimum Subarray](https://www.lintcode.com/problem/minimum-subarray/description)

```java
    public int minSubArray(List<Integer> nums) {
        if (nums == null || nums.size() == 0) {
            return 0;
        }
        int minPrev = nums.get(0);
        int min = nums.get(0);
        for (int i = 1; i < nums.size(); i++) {
        	// local minimum
            minPrev = Math.min(nums.get(i), minPrev + nums.get(i));
            min = Math.min(minPrev, min);
        }
        return min;
    }
```

Another way by keep prefix and previous max prefix sum.
Interval sum. Min subarray = prefix - max prefix

```java
    public int minSubArray(List<Integer> nums) {
        if (nums == null || nums.size() == 0) {
            return 0;
        }
        int maxPre = 0;
        int min = Integer.MAX_VALUE;
        int prefixSum = 0;
        for (int i = 0; i < nums.size(); i++) {
            prefixSum += nums.get(i);
            min = Math.min(min, prefixSum - maxPre);
            maxPre = Math.max(maxPre, prefixSum);
        }
        return min;
    }
```

[53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/description/)

Interval sum. Max subarray = prefix - min prefix


```java
    public int maxSubArray(int[] nums) {
        int prefix = 0;
        int minPre = 0;
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            prefix += nums[i];
            max = Math.max(max, prefix - minPre);
            minPre = Math.min(prefix, minPre);            
        }
        return max;
    }
```

Kadane's Algorithm

```java
   public int maxSubArray(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int max_ending_here = nums[0];
        int max_so_far = nums[0];
        for (int i = 1; i < nums.length; i++) {
            max_ending_here = Math.max(max_ending_here + nums[i], nums[i]);
            max_so_far = Math.max(max_so_far, max_ending_here);
        }
        return max_so_far;
    }
```

[152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/description/)


Kadane's Algorithm

When we meet negative number, the max prev and min prev will flip. Use a temporary val so that the max will not be covered.

The same with previous problem, calculate the local min/max to index i.

```java
    public int maxProduct(int[] nums) {
        int min = nums[0];
        int max = nums[0];
        int res = nums[0];
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > 0) {
                max = Math.max(nums[i], max * nums[i]);
                min = Math.min(nums[i], min * nums[i]);
            }
            else {
                int oldMax = max;
                max = Math.max(nums[i], min * nums[i]);
                min = Math.min(nums[i], oldMax * nums[i]);
            }
            res = Math.max(max, res);
        }
        return res;
    }
```

[42. Maximum Subarray II](https://www.lintcode.com/problem/maximum-subarray-ii/description)

Calculate max array sum on left of i, on right of i. 
Then concatinate left[i] and right[i + 1] to get the max result.


Use prefix sum

```java
    public int maxTwoSubArrays(List<Integer> nums) {
        int[] leftMax = new int[nums.size()];
        int prefixSum = 0;
        int prefixMin = 0;
        for (int i = 0; i < nums.size(); i++) {
            prefixSum += nums.get(i);
            leftMax[i] = i == 0 ? prefixSum : Math.max(leftMax[i - 1], prefixSum - prefixMin);
            prefixMin = Math.min(prefixMin, prefixSum);
        }
        
        int[] rightMax = new int[nums.size()];
        int suffixSum = 0;
        int suffixMin = 0;
        for (int i = nums.size() - 1; i >= 0; i--) {
            suffixSum += nums.get(i);
            rightMax[i] = i == nums.size() - 1 ? suffixSum : Math.max(rightMax[i + 1], suffixSum - suffixMin);
            suffixMin = Math.min(suffixMin, suffixSum);
        }
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < nums.size() - 1; i++) {
            res = Math.max(leftMax[i] + rightMax[i + 1], res);
        }
        return res;
    }
```
Kadane's Algorithm

Mind the boundary.

```java
    public int maxTwoSubArrays(List<Integer> nums) {
        int[] leftMax = new int[nums.size()];
        int maxEndHere = nums.get(0);
        int maxSoFar = nums.get(0);
        leftMax[0] = maxSoFar;
        for (int i = 1; i < nums.size(); i++) {
            maxEndHere = Math.max(maxEndHere + nums.get(i), nums.get(i));
            maxSoFar = Math.max(maxSoFar, maxEndHere);
            leftMax[i] = maxSoFar;
        }
        
        int[] rightMax = new int[nums.size()];
        maxEndHere = nums.get(nums.size() - 1);
        maxSoFar = nums.get(nums.size() - 1);
        rightMax[nums.size() - 1] = maxSoFar;
        for (int i = nums.size() - 2; i >= 0; i--) {
            maxEndHere = Math.max(maxEndHere + nums.get(i), nums.get(i));
            maxSoFar = Math.max(maxSoFar, maxEndHere);
            rightMax[i] = maxSoFar;
        }
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < nums.size() - 1; i++) {
            res = Math.max(leftMax[i] + rightMax[i + 1], res);
        }
        return res;
    }
```

[43. Maximum Subarray III](https://www.lintcode.com/problem/maximum-subarray-iii/description)

[45. Maximum Subarray Difference](https://www.lintcode.com/problem/maximum-subarray-difference/description)

The same as Maximum subarray II.

4 arrays.



## Stock

[121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

Buy one time.

```java
	public int maxProfit(int[] prices) {
		if(prices == null || prices.length == 0) return 0;
		int min = prices[0];
		int maxProfit = 0;
		for(int i = 1; i < prices.length; i++){
			maxProfit = Math.max(maxProfit, prices[i] - min);
			min = Math.min(min, prices[i]);
		}
		return maxProfit;
	}
```

[122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

Buy infinity times, so just add difference if larger than previous one.

```java
    public int maxProfit(int[] prices) {
        int count = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1]) {
                count += prices[i] - prices[i - 1];
            }
        }
        return count;
    }
```

[123. Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

1. Use the method like maximum subarray. First find the left array, left[i] means with one trade, the maximum profit we can get from 0 - i days. Right[i] means with one trade, the maximum profit we can get from i - len days. Remember to max the result with left[len - 1] since we may only trade once to get the maximum profit. Use prevmin to calculate left array, use nextmax to calculate right array.

```java
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) {
            return 0;
        }
        int len = prices.length;
        int[] left = new int[len];
        int[] right = new int[len];
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < len; i++) {
            if (prices[i] < min) {
                min = prices[i];
            }
            int profit = prices[i] - min;
            left[i] = i > 0 ? Math.max(profit, left[i - 1]) : 0;
        }
        
        int max = Integer.MIN_VALUE;
        for (int i = len - 1; i >= 0; i--) {
            if (prices[i] > max) {
                max = prices[i];
            }
            int profit = max - prices[i];
            right[i] = (i < len - 1) ? Math.max(profit, right[i + 1]) : 0;
        }
        int res = 0;
        for (int i = 0; i < len - 1; i++) {
            res = Math.max(res, left[i] + right[i + 1]);
        }
        res = Math.max(res, left[len - 1]);
        return res;
    }
```

2. State Machine. We can initialize all value with MINVALUE, since they will be overwritten in the for loop.

```java
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) {
            return 0;
        }
        int buy1 = Integer.MIN_VALUE;
        int buy2 = Integer.MIN_VALUE;
        int sell1 = Integer.MIN_VALUE;
        int sell2 = Integer.MIN_VALUE;
        for (int price : prices) {
            buy1 = Math.max(buy1, -price);
            sell1 = Math.max(sell1, buy1 + price);
            buy2 = Math.max(buy2, sell1 - price);
            sell2 = Math.max(sell2, buy2 + price);
        }
        return sell2;
    }
```

[188. Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

```java
    public int maxProfit(int k, int[] prices) {
        if (prices == null || prices.length == 0 || k == 0) {
            return 0;
        }
        if (k * 2 >= prices.length) {
            int res = 0;
            for (int i = 1; i < prices.length; i++) {
                if (prices[i] > prices[i - 1]) {
                    res += prices[i] - prices[i - 1];
                }
            }
            return res;
        }
        int len = prices.length;
        int[][] dp = new int[k + 1][len];
        for (int i = 1; i <= k; i++) {
            int buy = -prices[0];
            for (int j = 1; j < len; j++) {
                //                  hold(not sell)     sell
                dp[i][j] = Math.max(dp[i][j - 1], buy + prices[j]);
                //           hold(not buy)     buy
                buy = Math.max(buy, dp[i - 1][j - 1] - prices[j]);
            }
        }
        return dp[k][len - 1];
    }
```

A solution which is better understood. But use more space.

hold[i][j] means using i transactions from 0 - j, the max profit we can get with stock holding in hand.

hold[i][j] means using i transactions from 0 - j, the max profit we can get with no stock holding.

Initial all hold[0][days] with the maximum value, (cheapest buying price so far).

Initial all hold[times][0] with the first day buying cost.

```java
    public int maxProfit(int k, int[] prices) {
        if (prices == null || prices.length == 0 || k == 0) {
            return 0;
        }
        if (k * 2 >= prices.length) {
            int res = 0;
            for (int i = 1; i < prices.length; i++) {
                if (prices[i] > prices[i - 1]) {
                    res += prices[i] - prices[i - 1];
                }
            }
            return res;
        }
        int len = prices.length;
        int[][] hold = new int[k + 1][len];
        int[][] unhold = new int[k + 1][len];
        hold[0][0] = -prices[0];
        for (int j = 1; j < len; j++) {
            hold[0][j] = Math.max(hold[0][j - 1], -prices[j]);
        }
        for (int i = 1; i <= k; i++) {
            hold[i][0] = -prices[0];
        }
        for (int i = 1; i <= k; i++) {
            for (int j = 1; j < len; j++) {
                unhold[i][j] = Math.max(hold[i - 1][j - 1] + prices[j], unhold[i][j - 1]);
                hold[i][j] = Math.max(unhold[i][j - 1] - prices[j], hold[i][j - 1]);
            }
        }
        return Math.max(hold[k][len - 1], unhold[k][len - 1]);
    }
 ```
[309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

State machine.

```java
     public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0){
            return 0;
        }
        int[][] dp = new int [prices.length][3];
        // hold, with no stock in hand
        dp[0][0] = 0;
        // with stock in hand
        dp[0][1] = -prices[0];
        // cool down day, cannot buy.
        dp[0][2] = 0;
        for (int i = 1; i < prices.length; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][2]);
            dp[i][1] = Math.max(dp[i - 1][0] - prices[i], dp[i - 1][1]);
            dp[i][2] = dp[i - 1][1] + prices[i];
        }
        return Math.max(dp[prices.length - 1][0], dp[prices.length - 1][2]);
    }
```
[714. Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)

Same with the previous one, but only two states.

```java
    public int maxProfit(int[] prices, int fee) {
        if (prices == null || prices.length == 0) {
            return 0;
        }
        int[] hold = new int[prices.length];
        int[] unhold = new int[prices.length];
        hold[0] = -prices[0];
        for (int i = 1; i < prices.length; i++) {
            unhold[i] = Math.max(hold[i - 1] + prices[i] - fee, unhold[i - 1]);
            hold[i] = Math.max(unhold[i - 1] - prices[i], hold[i - 1]);
        }
        return unhold[prices.length - 1];
    }
```


## String 

[5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/description/)

i goes from right to left, j goes from i to right, by this we can utilize the previous result.

```java
    int maxLen = 0;
    int start = 0;
    int end = 0;
    public String longestPalindrome(String s) {
        boolean[][] dp = new boolean[s.length()][s.length()];
	    int len = s.length();
	    for(int i = len - 1; i >= 0; i--) {
		    for (int j = i; j < len; j++) {
			    if (s.charAt(i) == s.charAt(j)) {
				    if (i > j - 2 ||  dp[i + 1][j - 1]){
					    dp[i][j] = true;
					    if (j - i + 1 > maxLen) {                            
						    maxLen = j - i + 1;
						    start = i;
						    end = j;
					    }
				    }
			    }
		    }
	    }
	    return s.substring(start, end + 1);
    }
```

[77. Longest Common Subsequence](https://www.lintcode.com/problem/longest-common-subsequence/description)

For String dp, iterate from right to the left is better since we don't need to modify the index + 1. Since dp[len][len] serve as the base case.

```java
    public int longestCommonSubsequence(String A, String B) {
        int lenA = A.length();
        int lenB = B.length();
        int[][] dp = new int[lenA + 1][lenB + 1];
        for (int i = lenA - 1; i >= 0; i--) {
            for (int j = lenB - 1; j >= 0; j--) {
                if (A.charAt(i) == B.charAt(j)) {
                    dp[i][j] = dp[i + 1][j + 1] + 1;
                }
                else {
                    dp[i][j] = Math.max(dp[i + 1][j], dp[i][j + 1]);
                }
            }
        }
        return dp[0][0];
    }
```

Circular array, just keep two rows of the whole table.

```java
    public int longestCommonSubsequence(String A, String B) {
        int lenA = A.length();
        int lenB = B.length();
        int[][] dp = new int[2][lenB + 1];
        for (int i = lenA - 1; i >= 0; i--) {
            for (int j = lenB - 1; j >= 0; j--) {
                if (A.charAt(i) == B.charAt(j)) {
                    dp[i % 2][j] = dp[(i + 1) % 2][j + 1] + 1;
                }
                else {
                    dp[i % 2][j] = Math.max(dp[(i + 1) % 2][j], dp[i % 2][j + 1]);
                }
            }
        }
        return dp[0][0];
    }
```

[161. One Edit Distance](https://leetcode.com/problems/one-edit-distance/description/)

When chars do not match, check three situation:

s remove cur char || t remove cur char || both remove cur char(replace char)

At last, since i might not come to end of both string, so check whether length difference is 1.  


```java
    public boolean isOneEditDistance(String s, String t) {
        if (Math.abs(s.length() - t.length()) > 1) {
            return false;
        } 
        for (int i = 0; i < t.length() && i < s.length(); i++) {
            if (s.charAt(i) != t.charAt(i)) {
                return s.substring(i + 1).equals(t.substring(i + 1)) || s.substring(i).equals(t.substring(i + 1)) || s.substring(i + 1).equals(t.substring(i));
            }
        }
        return Math.abs(s.length() - t.length()) == 1;
    }
```

[72. Edit Distance](https://leetcode.com/problems/edit-distance/description/)

Go from left to right. Set the init state correctly!!!

dp[i][j] means how many manipulation to change first i (inclusive) char of word1 to first j of word2.

```java
    public int minDistance(String word1, String word2) {
        int len1 = word1.length();
        int len2 = word2.length();
        int[][] dp = new int[len1 + 1][len2 + 1];
        dp[0][0] = 0;
        // i can be equals to len1 !!!!
        for (int i = 0; i <= len1; i++) {
            dp[i][0] = i;
        }
        for (int j = 0; j <= len2; j++) {
            dp[0][j] = j;
        }
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
                else {
                    //                       replace       delete char from word1, delete char from word2
                    dp[i][j] = Math.min(dp[i - 1][j - 1], Math.min(dp[i - 1][j], dp[i][j - 1])) + 1;
                }
            }
        }
        return dp[len1][len2];
    }
```
[44. Wildcard Matching](https://leetcode.com/problems/wildcard-matching/description/)

Be careful at the initialization. 

Star match previous i, empty, or (current char) (not necessary)

```java
    public boolean isMatch(String s, String p) {
        if (s.length() == 0 && p.length() == 0) {
            return true;
        }
        int len1 = s.length();
        int len2 = p.length();
        boolean[][] dp = new boolean[len1 + 1][len2 + 1];
        dp[0][0] = true;
        for (int j = 1; j <= len2; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = true;
            }
            else {
                break;
            }
        }
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '?') {
                    dp[i][j] = dp[i - 1][j - 1];
                }
                else if (p.charAt(j - 1) == '*') {
                    //   match    multiple prev   empty     current char(not neccessary)
                    dp[i][j] = dp[i - 1][j] || dp[i][j - 1] || (dp[i - 1][j - 1]);
                }
            }
        }
        return dp[len1][len2];
    }
```

[10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/description/)

```java
    public boolean isMatch(String s, String p) {
        int len1 = s.length();
        int len2 = p.length();
        boolean[][] dp = new boolean[len1 + 1][len2 + 1];
        dp[0][0] = true;
        for (int i = 1; i <= len2; i++) {
            // init * can act as empty
            if (p.charAt(i - 1) == '*') {
                dp[0][i] = dp[0][i - 2];
            } 
        }
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '.') {
                    dp[i][j] = dp[i - 1][j - 1];
                }
                else if (p.charAt(j - 1) == '*') {
                    // Whether previous char is the same as current s
                    if (p.charAt(j - 2) == s.charAt(i - 1) || p.charAt(j - 2) == '.'){
                        //match   multiple prev char,  one char,     empty
                        dp[i][j] = dp[i - 1][j] || dp[i][j - 1] || dp[i][j - 2];
                    } 
                    else {
                        // * as empty
                        dp[i][j] = dp[i][j - 2];
                    }                    
                }
            }
        }
        return dp[len1][len2];
    }
```

[139. Word Break](https://leetcode.com/problems/word-break/description/)

In this problem, using list.contains is much faster than converting it into set then using contains. The overhead for Hashset is quite high and I don't know why.

```java
    public boolean wordBreak(String s, List<String> wordDict) {
        int len = s.length();
        boolean[] dp = new boolean[len + 1];
        dp[0] = true;
        for (int i = 0; i < len; i++) {
            if (!dp[i]) {
                continue;
            }
            for (int j = i + 1; j < len + 1; j++) {
                if (wordDict.contains(s.substring(i, j))) {
                    dp[j] = true;
                }
            }
        }
        return dp[len];
    }
```

[140. Word Break II](https://leetcode.com/problems/word-break-ii/description/)

Memorization Search, use startsWith to find the word in list.

The helper return a list, so we can concatenate the previous string with the result we already found for the latter part of string. If input string is empty, then add "" in the return list.

```java
    public List<String> wordBreak(String s, List<String> wordDict) {
        Map<String, List<String>> map = new HashMap<>();        
        return helper(map, s, wordDict);
    }
    
    public List<String> helper(Map<String, List<String>> map, String s, List<String> wordDict) {
        if (s.length() == 0) {
            List<String> lst = new ArrayList<>();
            lst.add("");
            return lst;
        } 
        if (map.containsKey(s)) {
            return map.get(s);
        }
        List<String> res = new ArrayList<>();
        for (String word : wordDict) {
            if (s.startsWith(word)) {
                List<String> recur = helper(map, s.substring(word.length()), wordDict);
                for (String sub : recur) {
                    String combine = word + (sub.length() == 0 ? "" : " ") + sub;
                    res.add(combine);
                } 
            }
        }
        map.put(s, res);
        return res;        
    }
```

[97. Interleaving String](https://leetcode.com/problems/interleaving-string/description/)

dp[i][j] means first i of s1 and first j of s2 can interleve to create s3.
init with whether single string can match s3. Since the other string has length 0. In initialization remember to use the previou result!!!
Then check whether when we increment i and j the last character can match the last char of s3(if previous result is true).
The dimension of s3 can be expressed by the index of s1 and s2.

```java
    public boolean isInterleave(String s1, String s2, String s3) {
        if (s1.length() + s2.length() != s3.length()) {
            return false;
        }
        int len1 = s1.length();
        int len2 = s2.length();
        boolean[][] dp = new boolean[len1 + 1][len2 + 1];
        dp[0][0] = true;
        for (int i = 1; i <= len1; i++) {
            dp[i][0] = dp[i - 1][0] && s1.charAt(i - 1) == s3.charAt(i - 1);
        }
        for (int j = 1; j <= len2; j++) {
            dp[0][j] = dp[0][j - 1] && s2.charAt(j - 1) == s3.charAt(j - 1);
        }
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                dp[i][j] = (dp[i - 1][j] && s1.charAt(i - 1) == s3.charAt(i + j - 1)) || (dp[i][j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1));
            }
        }
        return dp[len1][len2];
    }
```

[361. Bomb Enemy](https://leetcode.com/problems/bomb-enemy/description/)

Use a rowCount to store how many enemy we can hit in this empty space of row, and a colCount array for this empty space of col. Init them to be zero when i == 0 or prev row has a wall and j == 0 or prev col has a wall. 

```java
    public int maxKilledEnemies(char[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        int m = grid.length;
        int n = grid[0].length;
        int rowCount = 0;
        int[] colCount = new int[n];
        int max = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 'W') {
                    continue;
                }
                if (i == 0 || grid[i - 1][j] == 'W') {
                    colCount[j] = 0;
                    for (int k = i; k < m && grid[k][j] != 'W'; k++) {
                        if (grid[k][j] == 'E') {
                            colCount[j]++;
                        }                        
                    }
                }
                if (j == 0 || grid[i][j - 1] == 'W') {
                    rowCount = 0;
                    for (int k = j; k < n && grid[i][k] != 'W'; k++) {
                        if (grid[i][k] == 'E') {
                            rowCount++;
                        }
                    }
                }
                if (grid[i][j] == '0') {
                    max = Math.max(max, rowCount + colCount[j]);
                }
            }
        }
        return max;
    }
```


## Backpack

[92. Backpack](https://www.lintcode.com/problem/backpack/description)

time O(mn) space O(mn)
dp[i][j] means with size i and first j elements, the max size we can get.

```java
    public int backPack(int m, int[] A) {
        int[][] dp = new int[m + 1][A.length + 1];
        int max = 0;
        for (int j = 1; j <= A.length; j++) {
            for (int i = 0; i <= m; i++) {
                if (i - A[j - 1] >= 0) {
                    dp[i][j] = dp[i - A[j - 1]][j - 1] + A[j - 1];
                }
                dp[i][j] = Math.max(dp[i][j - 1], dp[i][j]);
                max = Math.max(max, dp[i][j]);
            }
        }
        return max;
    }
```

time O(mn) space O(m)
We reuse the previous result, so we loop through from m to 0.

```java
    public int backPack(int m, int[] A) {
        int[] dp = new int[m + 1];
        int max = 0;
        for (int j = 0; j < A.length; j++) {
            for (int i = m; i >= 0; i--) {
                if (i - A[j] >= 0) {
                    dp[i] = Math.max(dp[i - A[j]] + A[j], dp[i]);
                }
                max = Math.max(max, dp[i]);
            }
        }
        return max;
    }
```

[125. Backpack II](https://www.lintcode.com/problem/backpack-ii/description)

```java
    public int backPackII(int m, int[] A, int[] V) {
        int[] dp = new int[m + 1];
        int max = 0;
        for (int j = 0; j < A.length; j++) {
            for (int i = m; i >= 0; i--) {
                if (i - A[j] >= 0) {
                    dp[i] = Math.max(dp[i], dp[i - A[j]] + V[j]);
                }
                max = Math.max(max, dp[i]);
            }
        }
        return max;
    }
```

[279. Perfect Squares](https://leetcode.com/problems/perfect-squares/description/)

Like back pack. For each size, chech we can get a smaller number if we subtrack a square number.

```java
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        for (int j = 1; j <= n; j++) {
            for (int i = 1; i * i <= j; i++) {
                dp[j] = Math.min(dp[j], dp[j - i * i] + 1);
            }
        }
        return dp[n];
    }
```



