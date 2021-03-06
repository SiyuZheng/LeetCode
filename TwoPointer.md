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

[438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/description/)

```java
    public List<Integer> findAnagrams(String s, String p) {
        int len = p.length();
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < p.length(); i++) {
            char c = p.charAt(i);
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        int left = 0;
        int right = 0;
        int counter = map.size();
        List<Integer> res = new ArrayList<>();
        while(right < s.length()) {
            char cr = s.charAt(right);
            if (map.containsKey(cr)) {
                map.put(cr, map.get(cr) - 1);
                if (map.get(cr) == 0) {
                    counter--;
                }   
            }
            
            if (right >= len - 1) {
                if (counter == 0) {
                    res.add(left);
                }
                char cl = s.charAt(left);
                if (map.containsKey(cl)) {
                    map.put(cl, map.get(cl) + 1);
                    if (map.get(cl) == 1) {
                        counter++;
                    }
                }               
                left++; 
            }
            right++;
        }
        return res;
    }
```

[763. Partition Labels](https://leetcode.com/problems/partition-labels/description/)

```java
    public List<Integer> partitionLabels(String S) {
        List<Integer> lst = new ArrayList<>();
        int[] hash = new int[26];
        for (int i = 0; i < S.length(); i++) {
            hash[S.charAt(i) - 'a'] = i;
        }
        int start = 0;
        int end = 0;
        for (int i = 0; i < S.length(); i++) {
            end = Math.max(end, hash[S.charAt(i) - 'a']);
            if (end == i) {
                lst.add(end - start + 1);
                start = end + 1;
            }
        }
        return lst;
    }
```

