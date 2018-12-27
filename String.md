139. Word Break

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