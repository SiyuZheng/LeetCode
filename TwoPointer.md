3. Longest Substring Without Repeating Characters

First: pretty slow, using map for occurence. Greater than O(n).

```java
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> map = new HashMap<>();
        int l = 0, r = 0;
        char[] cs = s.toCharArray();
        int max = 0;
        while (r < cs.length) {
            map.put(cs[r], map.getOrDefault(cs[r], 0) + 1);
            while (map.get(cs[r]) > 1) {
                map.put(cs[l], map.get(cs[l]) - 1);
                l++;
            }
            max = Math.max(max, r - l + 1);
            r++;
        }
        return max;
    }
```

Second : keep the last occurence index, if we find a old char in the right, then update left pointer to the max of left or the last occurence of this char + 1. A bit faster. HashMap can be changed to a int[128]

```java
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> map = new HashMap<>();
        int l = 0, r = 0;
        char[] cs = s.toCharArray();
        int max = 0;
        while (r < cs.length) {
            if (map.containsKey(cs[r])) {
                l = Math.max(l, map.get(cs[r]) + 1);
            }
            map.put(cs[r], r);
            max = Math.max(max, r - l + 1);
            r++;
        }
        return max;
    }
```

Third: HashSet

```java
    public int lengthOfLongestSubstring(String s) {
        Set<Character> set = new HashSet<>();
        char[] cs = s.toCharArray();
        int l = 0, r = 0;
        int max = 0;
        while (r < cs.length) {
            if (!set.contains(cs[r])) {
                set.add(cs[r]);
                max = Math.max(max, r - l + 1);
                r++;
            }
            else {
                set.remove(cs[l++]);
            }
        }
        return max;
    }
```