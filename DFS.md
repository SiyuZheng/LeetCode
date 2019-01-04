[77. Combinations](https://leetcode.com/problems/combinations/description/)

In dfs, use index + 1 to remove all we have traverse to remove duplicate.

```java
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        helper(res, 1, n, k, new ArrayList<Integer>());
        return res;
    }
    
    public void helper(List<List<Integer>> res, int start, int n, int k, List<Integer> lst) {
        if (lst.size() == k) {
            res.add(new ArrayList<Integer>(lst));
            return;
        }
        for (int i = start; i <= n; i++) {
            lst.add(i);
            helper(res, i + 1, n, k, lst);
            lst.remove(lst.size() - 1);
        }
    }
```

[39. Combination Sum](https://leetcode.com/problems/combination-sum/description/)

Same as previous problem, use a index to remove all possible choice ahead of it([2, 2, 3] and [3, 2, 2] is the same). In for loop the i can be also start, so we can use an element multiple times.

```java
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        helper(candidates, target, 0, new ArrayList<Integer>(), res);
        return res;
    }
    
    public void helper(int[] candidates, int target, int start, List<Integer> cur, List<List<Integer>> res) {
        if (target < 0) {
            return;
        }
        if (target == 0) {
            res.add(new ArrayList<Integer>(cur));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            cur.add(candidates[i]);
            helper(candidates, target - candidates[i], i, cur, res);
            cur.remove(cur.size() - 1);
        }
    }
```

[216. Combination Sum III](https://leetcode.com/problems/combination-sum-iii/description/)

```java
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> res = new ArrayList<>();
        helper(k, n, new ArrayList<Integer>(), res, 0, 1);
        return res;
        
    }
    
    public void helper(int k, int n, List<Integer> lst, List<List<Integer>> res, int sum, int j){
        if(lst.size() == k && sum == n){
            res.add(new ArrayList<Integer>(lst));
            return;
        }
        if(lst.size() >= k){
            return;
        }
        for(int i = j; i <= 9; i++) {
            lst.add(i);
            helper(k, n, lst, res, sum + i, i + 1);
            lst.remove(lst.size() - 1);
        }
    }
```

[78. Subsets](https://leetcode.com/problems/subsets/description/)

Still need to add list when k equals the length;

```java
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        helper(res, new ArrayList<Integer>(), nums, 0);
        return res;
    }
    
    public void helper(List<List<Integer>> res, List<Integer> current, int[] nums, int k) {
        if (k > nums.length) {
            return;
        }
        List<Integer> addList = new ArrayList<>(current);
        res.add(addList);
        for (int i = k; i < nums.length; i++) {
            current.add(nums[i]);
            helper(res, current, nums, i + 1);
            current.remove(current.size() - 1);
        }
    }
```

[90. Subsets II](https://leetcode.com/problems/subsets-ii/description/)

Some methods to skip duplicate value.
Sort the array first.

1. Use a boolean[] used array, if previous is used and current one is the same as previous one, skip.

2. After use current one, find the next one which is not the duplicate

```java
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        helper(res, nums, 0, new ArrayList<Integer>());
        return res;
    }
    
    public void helper(List<List<Integer>> res, int[] nums, int start, List<Integer> lst) {
        if (start > nums.length) {
            return;
        }
        res.add(new ArrayList<Integer>(lst));
        for (int i = start; i < nums.length; i++) {
            lst.add(nums[i]);
            helper(res, nums, i + 1, lst);
            lst.remove(lst.size() - 1);
            while (i < nums.length - 1 && nums[i + 1] == nums[i]) {
                i++;
            }
        }
    }
```


## Pruning

[22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/description/)

Use open or left > right. 
left means how many left perenthesis we still need to use.


```java
    public List<String> generateParenthesis(int n) {
        List<String> ans = new ArrayList<>();
        helper(ans, new StringBuilder(), n, n);
        return ans;
    }
    
    public void helper(List<String> ans, StringBuilder sb, int left, int right){
        if(left == 0 && right == 0){
            ans.add(sb.toString());
            return;
        }
        if(left > right){
            return;
        }
        int len = sb.length();
        if(left > 0){
            sb.append('(');
            helper(ans, sb, left - 1, right);
            sb.setLength(len);
        }
        if(right > 0){
            sb.append(')');
            helper(ans, sb, left, right - 1);
            sb.setLength(len);
        }
    }
```

[17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/)

```java
    public List<String> letterCombinations(String digits) {
        List<String> ans = new ArrayList<String>();
        if(digits.length() == 0){
            return ans;
        }
        String[] map = new String[]{"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        helper(ans, digits, new StringBuilder(), map);
        return ans;
    }
    
    public void helper(List<String> ans, String digits, StringBuilder sb, String[] map){
        if (sb.length() == digits.length()) {
            ans.add(sb.toString());
            return;
        }
        int len = sb.length();
        String mapstr = map[digits.charAt(len) - '0'];
        
        for (int j = 0; j < mapstr.length(); j++) {
            sb.append(mapstr.charAt(j));
            helper(ans, digits, sb, map);
            sb.setLength(len);
        }
    }
```

[93. Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/description/)

```java
```

[51. N-Queens](https://leetcode.com/problems/n-queens/description/)

Use three visited array. Iterate through row, so we don't need visted on rows. Visited for each column, each diagnal.

45 -> row - col is the same, add a offset to become positive;
135 -> row + col is the same.

```java
    public List<List<String>> solveNQueens(int n) {
        char[][] board = new char[n][n];
        for (int i = 0; i < n; i++) {
            Arrays.fill(board[i], '.');
        }
        boolean[] usedcol = new boolean[n];
        boolean[] used45 = new boolean[2 * n];
        boolean[] used135 = new boolean[2 * n];
        List<List<String>> res = new ArrayList<>();
        helper(res, board, n, 0, usedcol, used45, used135);
        return res;
    }
    
    public void helper(List<List<String>> res, char[][] board, int n, int row, boolean[] usedcol, boolean[] used45, boolean[] used135) {
        if (row == n) {
            List<String> curRes = new ArrayList<>();
            for (int i = 0; i < n; i++) {
                String rowS = new String(board[i]);
                curRes.add(rowS);
            }
            res.add(curRes);
            return;
        }
        for (int col = 0; col < n; col++) {
            if (!usedcol[col] && !used45[row - col + n] && !used135[row + col]) {
                usedcol[col] = true;
                used45[row - col + n] = true;
                used135[row + col] = true;
                board[row][col] = 'Q';
                helper(res, board, n, row + 1, usedcol, used45, used135);
                usedcol[col] = false;
                used45[row - col + n] = false;
                used135[row + col] = false;
                board[row][col] = '.';
            }
        }
    }
```

[52. N-Queens II](https://leetcode.com/problems/n-queens-ii/description/)

Recursive return a value, to calculate how many results we get. Better than using global variable.

```java
    public int totalNQueens(int n) {
        boolean[] col = new boolean[n];
        boolean[] d1 = new boolean[2 * n];
        boolean[] d2 = new boolean[2 * n];
        int res = helper(0, n, col, d1, d2); 
        return res;
    }
    
    public int helper(int r, int n, boolean[] col, boolean[] d1, boolean[] d2) {
        if (r == n) {
            return 1;
        }
        int res = 0;
        for (int i = 0; i < n; i++) {
            if (col[i]) {
                continue;
            }
            int dd1 = r - i + n;
            int dd2 = r + i;
            if (d1[dd1] || d2[dd2]) {
                continue;
            }
            col[i] = true;
            d1[dd1] = true;
            d2[dd2] = true;
            res += helper(r + 1, n, col, d1, d2);
            col[i] = false;
            d1[dd1] = false;
            d2[dd2] = false;
        }
        return res;
    }
```

[36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/description/)

Check the index of sub-boxes. 
int boxRow = i / 3 * 3; int boxCol = (i % 3) * 3; Start index of the sub-boxes.

```java
    public boolean isValidSudoku(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return false;
        }
        for (int i = 0; i < 9; i++) {
            boolean[] rows = new boolean[9];
            boolean[] cols = new boolean[9];
            boolean[] boxes = new boolean[9];
            int boxRow = i / 3 * 3;
            int boxCol = (i % 3) * 3;
            for (int j = 0; j < 9; j++) {
                if (board[i][j] != '.') {
                    int ind = board[i][j] - '1';
                    if (rows[ind]) {
                        return false;
                    }
                    rows[ind] = true;
                }
                if (board[j][i] != '.') {
                    int ind = board[j][i] - '1';
                    if (cols[ind]) {
                        return false;
                    }
                    cols[ind] = true;
                }
                int subRow = boxRow + j / 3;
                int subCol = boxCol + j % 3;
                if (board[subRow][subCol] != '.') {
                    int ind = board[subRow][subCol] - '1';
                    if (boxes[ind]) {
                        return false;
                    }
                    boxes[ind] = true;
                }
            }
        }
        return true;
    }
```

[36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/description/)

Return a boolean to indicate whether this is a valid approach. Early termination.

Use precalculate rows, cols, subboxes boolean array to store previous result, extra space. 

```java
    public void solveSudoku(char[][] board) {
        boolean[][] rows = new boolean[9][9];
        boolean[][] cols = new boolean[9][9];
        boolean[][] subboxes = new boolean[9][9];
        // row
        for (int i = 0; i < 9; i++) {
            // col
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    continue;
                }
                int ind = board[i][j] - '1';
                rows[i][ind] = true;
                cols[j][ind] = true;
                int boxind = i / 3 * 3 + j / 3;
                subboxes[boxind][ind] = true;
            }
        }
        helper(board, 0, rows, cols, subboxes);
    }
    
    public boolean helper(char[][] board, int ind, boolean[][] rows, boolean[][] cols, boolean[][] subboxes) {
        if (ind == 81) {
            return true;
        }
        int row = ind / 9;
        int col = ind % 9;
        int block = row / 3 * 3 + col / 3;
        if (board[row][col] != '.') {
            return helper(board, ind + 1, rows, cols, subboxes);
        }
        for (char c = '1'; c <= '9'; c++) {
            int i = c - '1';
            if (!rows[row][i] && !cols[col][i] && !subboxes[block][i]) {
                board[row][col] = c;
                rows[row][i] = true;
                cols[col][i] = true;
                subboxes[block][i] = true;
                if (helper(board, ind + 1, rows, cols, subboxes)) {
                    return true;
                }
                board[row][col] = '.';
                rows[row][i] = false;
                cols[col][i] = false;
                subboxes[block][i] = false;
            }
        }
        return false;
    }
```


有向图找最短距离用 BFS
有向图返回所有路径用 DFS

[126. Word Ladder II](https://leetcode.com/problems/word-ladder-ii/description/)

Use a neighbors Map and a distance Map. In bfs, update distance map to get the shortest route to this word. So we can utilize it in dfs, so that each time we check the word in dfs, we go with the shortest length.

```java
    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        Set<String> dict = new HashSet<>(wordList);
        List<List<String>> res = new ArrayList<>();
        if (!dict.contains(endWord)) {
            return res;
        }
        dict.add(beginWord);
        Map<String, List<String>> neighbors = buildMap(dict); 
        if (neighbors.get(beginWord).size() == 0) {
            return res;
        }
        Map<String, Integer> distanceMap = new HashMap<>();
        bfs(distanceMap, beginWord, endWord, neighbors);
        if (!distanceMap.containsKey(endWord)) {
            return res;
        }
        dfs(res, beginWord, endWord, neighbors, distanceMap, new ArrayList<String>());
        return res;
    }
    
    public void dfs(List<List<String>> res, String beginWord, String endWord, Map<String, List<String>> neighbors, Map<String, Integer> distanceMap, List<String> cur) {
        if  (beginWord.equals(endWord)) {
            cur.add(endWord);
            res.add(new ArrayList<String>(cur));
            cur.remove(cur.size() - 1);
            return;
        }
        cur.add(beginWord);
        for (String n : neighbors.get(beginWord)) {
            if (distanceMap.get(n) == distanceMap.get(beginWord) + 1) {
                dfs(res, n, endWord, neighbors, distanceMap, cur);
            }
        }
        cur.remove(cur.size() - 1);
    }
    
    public void bfs(Map<String, Integer> distanceMap, String beginWord, String endWord, Map<String, List<String>> neighbors) {
        Queue<String> q = new LinkedList<>();
        q.offer(beginWord);
        distanceMap.put(beginWord, 0);
        int step = 1;
        while(!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                String cur = q.poll();
                List<String> neighbor = neighbors.get(cur);
                for (String n : neighbor) {
                    if (!distanceMap.containsKey(n)) {
                        distanceMap.put(n, step);
                        q.offer(n);
                    }
                }
            }
            step++;
        }
        return;
    }
    
    public Map<String, List<String>> buildMap(Set<String> dict) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : dict) {
            List<String> lst = new ArrayList<>();
            char[] cs = s.toCharArray();
            for (int i = 0; i < cs.length; i++) {
                char old = cs[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    if (c == old) {
                        continue;
                    }
                    cs[i] = c;
                    String news = new String(cs);
                    if (dict.contains(news)) {  
                        lst.add(news);
                    }
                }
                cs[i] = old;
            }
            map.put(s, lst);
            
        }
        return map;
    }
```

[351. Android Unlock Patterns](https://leetcode.com/problems/android-unlock-patterns/description/)

Use a boolean for visited. And keep a map of pass through so that we cannot pass no-visited number. No pass through then it's set as zero.

```java
 public int numberOfPatterns(int m, int n) {
        boolean[] visited = new boolean[10];
        int[][] passThrough = new int[10][10];
        passThrough[1][3] = 2;
        passThrough[3][1] = 2;
        passThrough[1][7] = 4;
        passThrough[7][1] = 4;
        passThrough[1][9] = 5;
        passThrough[9][1] = 5;
        passThrough[2][8] = 5;
        passThrough[8][2] = 5;
        passThrough[4][6] = 5;
        passThrough[6][4] = 5;
        passThrough[3][9] = 6;
        passThrough[9][3] = 6;
        passThrough[7][9] = 8;
        passThrough[9][7] = 8;
        passThrough[3][7] = 5;
        passThrough[7][3] = 5;
        visited[0] = true;
        int res = 0;
        res += helper(1, 1, visited, passThrough, m, n) * 4 + helper(1, 2, visited, passThrough, m, n) * 4 + helper(1, 5, visited, passThrough, m, n);
        return res;
    }
    
    public int helper(int step, int cur, boolean[] visited, int[][] passThrough, int m, int n) {
        int res = 0;        
        // Be careful at the step trick so that we don't need to use the total step to calculate multiple times.
        if (step >= m) {
            // cur length is valid, we can keep going
            res++;
        }
        if (step >= n) {
            // if step == n, add one, and we cannot move anymore
            return res;
        }
        visited[cur] = true;        
        for (int i = 1; i <= 9; i++) {
            if (!visited[i] && visited[passThrough[cur][i]]) {                
                res += helper(step + 1, i, visited, passThrough, m, n);
            }
        }
        visited[cur] = false;
        return res;
    }
```


## DFS flooding

[79. Word Search](https://leetcode.com/problems/word-search/description/)

Return boolean in dfs for early termination.

Modify the current visiting grid to be '#', Or use a visited [][] if we cannot modify original input. 

O(mn * length(word)).

```java
    int[] di = {1, 0, -1, 0};
    int[] dj = {0, 1, 0, -1};
    
    public boolean exist(char[][] board, String word) {
        if (board == null || word == null || word.length() == 0 || board.length == 0 || board[0].length == 0) {
            return false;
        }
        char[] cs = word.toCharArray();
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (searchForWord(i, j, 0, cs, board)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    public boolean searchForWord(int i, int j, int ind, char[] cs, char[][] board) {
        if (i < 0 || j < 0 || i >= board.length || j >= board[0].length || board[i][j] != cs[ind] || ind >= cs.length) {
            return false;
        }
        if (ind == cs.length - 1) {
            return true;
        }
        char c = board[i][j];
        board[i][j] = '#';
        for (int k = 0; k < 4; k++) {
            int ni = i + di[k];
            int nj = j + dj[k];
            if (searchForWord(ni, nj, ind + 1, cs, board)) {
                return true;
            }
        }
        board[i][j] = c;
        return false;
    }
```

[200. Number of Islands](https://leetcode.com/problems/number-of-islands/description/)

Set visited grid to be 0. Count the islands in outer for loop
O(mn) since we modify the input, only check one grid once.

```java
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        int count = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == '1') {
                    clearIsland(grid, i, j);
                    count++;
                }
            }
        }
        return count;
    }
    
    public void clearIsland(char[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == '0') {
            return;
        }
        grid[i][j] = '0';
        clearIsland(grid, i - 1, j);
        clearIsland(grid, i + 1, j);
        clearIsland(grid, i, j - 1);
        clearIsland(grid, i, j + 1);
    }
```

[694. Number of Distinct Islands](https://leetcode.com/problems/number-of-distinct-islands/description/)

Build the string by character meaning the direction, remember to add 'b' after backtracking. Remove last char if not valid. Or if valid, go that direction, add the char, so we don't need to remove char.

O(mn) since we modify the input, only check one grid once.

```java
        int[] dx = {-1, 0, 1, 0};
    int[] dy = {0, -1, 0, 1};
    char[] cs = {'l', 'u', 'r', 'd'};
    
    public int numDistinctIslands(int[][] grid) {
        Set<String> set = new HashSet<>();
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == 1) {
                    StringBuilder sb = new StringBuilder();
                    sb.append('s');
                    search(grid, i, j, sb);
                    set.add(sb.toString());
                }
            }
        }
        return set.size();
    }
    
    public void search(int[][] grid, int i, int j, StringBuilder sb) {
        grid[i][j] = 0;
        for (int k = 0; k < 4; k++) {
            int ni = i + dy[k];
            int nj = j + dx[k];
            char nc = cs[k];
            if (ni >= 0 && nj >= 0 && ni < grid.length && nj < grid[0].length && grid[ni][nj] != 0) {
                search(grid, ni, nj, sb.append(nc));
            }        
        }
        sb.append('b');
    }
```

[248. Strobogrammatic Number III](https://leetcode.com/problems/strobogrammatic-number-iii/description/)

Build the string by append char in left or in right. Remember we cannot add 0 in head. Split them into two situation when len is odd and len is even.

We can write a new compare function to find those value between low and high, remember to compare the length before using string compareTo function.

O(5 ^ m * (m - n))

```java
    public int strobogrammaticInRange(String low, String high) {
        int len1 = low.length();
        int len2 = high.length();
        List<String> res = new ArrayList<>();
        for (int i = len1; i <= len2; i++) {
            buildString(res, i, low, high);
        }
        return res.size();
    }
    
    public void buildString(List<String> res, int len, String low, String high) {
        if (len % 2 == 1) {
            helper(res, len - 1, "0", low, high);
            helper(res, len - 1, "1", low, high);
            helper(res, len - 1, "8", low, high);
        }
        else {
            helper(res, len, "", low, high);
        }
    }
    
    public void helper(List<String> res, int len, String cur, String low, String high) {       
        if (len == 0) {
            if (compare(cur, low) >= 0 && compare(cur, high) <= 0) {
                res.add(cur);
            }
            return;
        }
        if (len > 2) {
            helper(res, len - 2, "0" + cur + "0", low, high);
        }
        helper(res, len - 2, "1" + cur + "1", low, high);
        helper(res, len - 2, "8" + cur + "8", low, high);
        helper(res, len - 2, "6" + cur + "9", low, high);
        helper(res, len - 2, "9" + cur + "6", low, high);
    }
    
    public int compare(String s1, String s2) {
        if (s1.length() != s2.length()) {
            return s1.length() - s2.length();
        }
        else {
            return s1.compareTo(s2);
        }
    }
```

[301. Remove Invalid Parentheses](https://leetcode.com/problems/remove-invalid-parentheses/description/)

First, find how many left and right parentheses we need to remove, then use dfs to remove then. 

if removeLeft, removeRight or open is < 0, return to terminate.

Remember to reset the length of stringbuilder if we modify it.


```java
    public List<String> removeInvalidParentheses(String s) {
        char[] cs = s.toCharArray();
        int removeRight = 0;
        int removeLeft = 0;
        int open = 0;
        for (int i = 0; i < cs.length; i++) {
            if (cs[i] == '(') {
                open++;
            }
            else if (cs[i] == ')') {
                if (open == 0) {
                    removeRight++;
                }
                else {
                    open--;
                }
            }
        }
        removeLeft = open;
        Set<String> res = new HashSet<>();
        helper(res, new StringBuilder(), cs, removeLeft, removeRight, 0, 0);
        return new ArrayList<String>(res);
    }
    
    public void helper(Set<String> res, StringBuilder sb, char[] cs, int removeLeft, int removeRight, int open, int ind) {
        if (removeLeft < 0 || removeRight < 0 || open < 0) {
            return;
        }
        if (ind == cs.length) {
            if (removeLeft == 0 && removeRight == 0 && open == 0){
                res.add(sb.toString());
            }
            return;
        }
        int len = sb.length();
        if (cs[ind] == '(') {            
            helper(res, sb, cs, removeLeft - 1, removeRight, open, ind + 1);
            helper(res, sb.append(cs[ind]), cs, removeLeft, removeRight, open + 1, ind + 1);
        }
        else if (cs[ind] == ')') {            
            helper(res, sb, cs, removeLeft, removeRight - 1, open, ind + 1);
            helper(res, sb.append(cs[ind]), cs, removeLeft, removeRight, open - 1, ind + 1);
        }
        else {
            helper(res, sb.append(cs[ind]), cs, removeLeft, removeRight, open, ind + 1);
        }
        sb.setLength(len);
    }
```

[282. Expression Add Operators](https://leetcode.com/problems/expression-add-operators/description/)

Use long for large value. Use a current value and a last value. So that we can track back the result before we do multiply. 

The value we append can be '0' but can not be a longer digit starts with 0.

O(4^(n - 1)). or O(n * 4 ^(n - 1)) ?

```java
    public List<String> addOperators(String num, int target) {
        List<String> res = new ArrayList<>();
        helper(res, num, 0, target, 0, 0, new StringBuilder());
        return res;
    }
    
    public void helper(List<String> res, String num, int ind, int target, long cur, long last, StringBuilder sb) {
        if (ind == num.length()) {
            if (target == cur) {
                res.add(sb.toString());
            }
            return;
        }
        int len = sb.length();
        for (int i = ind + 1; i <= num.length(); i++) {
            if (num.charAt(ind) == '0' && i != ind + 1) {
                return;
            }
            String subNum = num.substring(ind, i);
            long val = Long.parseLong(subNum);
            if (ind == 0) {
                helper(res, num, i, target, val, val, sb.append(subNum));
                sb.setLength(len);
            }
            else {
                helper(res, num, i, target, cur + val, val, sb.append('+').append(subNum));
                sb.setLength(len);
                helper(res, num, i, target, cur - val, -val, sb.append('-').append(subNum));
                sb.setLength(len);
                helper(res, num, i, target, cur - last + last * val, last * val, sb.append('*').append(subNum));
                sb.setLength(len);
            }
        }
    }
```