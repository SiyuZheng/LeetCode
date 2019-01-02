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

151. Reverse Words in a String

reverse all then reverse each word then remove heading 0, redundant and trailling 0. O(n).

```java
    public String reverseWords(String s) {
        char[] cs = s.toCharArray();
        reverse(cs, 0, cs.length - 1);
        reverseWord(cs);
        return removeZero(cs);
    }
    
    public void reverse(char[] cs, int s, int e) {
        while (s < e) {
            char temp = cs[e];
            cs[e] = cs[s];
            cs[s] = temp;
            s++;
            e--;
        }
    }
    
    public void reverseWord(char[] cs) {
        int l = 0, r = 0;
        while (r < cs.length) {
            while (r < cs.length && cs[r] == ' ') {
                r++;
            }
            l = r;
            while (r < cs.length && cs[r] != ' ') {
                r++;
            }
            reverse(cs, l, r - 1);
        }
    }
    
    public String removeZero(char[] cs) {
        int l = 0, r = 0;
        while (r < cs.length) {
            while (r < cs.length && cs[r] == ' ') {
                r++;
            }
            while (r < cs.length && cs[r] != ' ') {
                cs[l++] = cs[r++];
            }
            while (r < cs.length && cs[r] == ' ') {
                r++;
            }
            // if r < cs.length, it means there still some word afterwards, so append a space.
            if (r < cs.length) {
                cs[l++] = ' ';
            }
        }
        return new String(cs, 0, l);
    }
```

## Palindrome

5. Longest Palindromic Substring

Extend string from single / double character. O(1)

```java
    int len = 0;
    int start = 0;
    int end = 0;
    public String longestPalindrome(String s) {
        for (int i = 0; i < s.length(); i++) {
            helper(s, i, i);
            helper(s, i, i + 1);
        }
        return len == 0 ? "" : s.substring(start, end + 1);
    }
    
    public void helper(String s, int l, int r) {
        while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)) {
            l--;
            r++;
        }
        l++;
        r--;
        if (r - l + 1 > len) {
            len = r - l + 1;
            end = r;
            start = l;
        }
    }
```

125. Valid Palindrome

Use built-in function to check character. 
str.toLowerCase(),  Character.toLowerCase(char)
Character.isLetterOrDigit(char).

```java
    public boolean isPalindrome(String s) {
        int head = 0, tail = s.length() - 1;
        char cHead, cTail; 
        while(head <= tail){
            cHead = s.charAt(head);
            cTail = s.charAt(tail);
            if(!Character.isLetterOrDigit(cHead)){
                head++;
            }
            else if(!Character.isLetterOrDigit(cTail)){
                tail--;
            }
            else if (Character.toLowerCase(cHead) != Character.toLowerCase(cTail)){
                return false;
            }
            else{
                head++;
                tail--;
            }
        }       
        return true;
    }
```

266. Palindrome Permutation

Count occurrences, odd number can only appear once.

two pass O(n).

```java
    public boolean canPermutePalindrome(String s) {
        if (s == null || s.length() <= 1) {
            return true;
        }
        int[] hash = new int[256];
        for (int i = 0; i < s.length(); i++) {
            hash[(int)s.charAt(i)]++;
        } 
        boolean odd = false;
        for (int i = 0; i < hash.length; i++) {
            if (hash[i] % 2 == 1) {
                if (odd) {
                    return false;
                }
                odd = true;
            }
        }
        return true;
    }
```
267. Palindrome Permutation II

Return all palindrome permutation without duplicate. 
First check whether palindrome permutation can be built then use dfs to construct permutation. Use int[] as hashmap and char[] instead of StringBuilder may be faster. 

O(how many kinds of chars ^ (len / 2))

```java
    public List<String> generatePalindromes(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            map.put(s.charAt(i), map.getOrDefault(s.charAt(i), 0) + 1);
        }
        List<String> res = new ArrayList<>();
        char single = ' ';
        boolean hasOdd = false;
        for (Character c : map.keySet()) {
            if (map.get(c) % 2 == 1) {
                if (hasOdd) {
                    return res;
                }
                hasOdd = true;
                single = c;
            }
        }
        StringBuilder sb = new StringBuilder();
        int len = s.length();
        if (hasOdd) {
            helper(res, map, sb.append(single), len);
        }
        else {
            helper(res, map, sb, len);
        }
        return res;
    }
    
    public void helper(List<String> res, Map<Character, Integer> map, StringBuilder sb, int len) {
        if (sb.length() == len) {
            res.add(sb.toString());
            return;
        }
        for (char c : map.keySet()) {
            if (map.get(c) < 2) {
                continue;
            }
            sb.append(c);
            sb.insert(0, c);
            map.put(c, map.get(c) - 2);
            helper(res, map, sb, len);
            sb.deleteCharAt(sb.length() - 1);
            sb.deleteCharAt(0);
            map.put(c, map.get(c) + 2);
        }
    }

```

132. Palindrome Partitioning II

Get the boolean array for isPalindrome.
Then search for previous char, if j to i is palindrome, then we can update dp[i] with 0 (j == 0) or dp[j - 1] + 1.

dp[i] means min cut of s.substring(0, i), init with i.

time O(n^2) space O(n^2)

```java
    public int minCut(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        boolean[][] isPalin = new boolean[s.length()][s.length()];
        int[] dp = new int[s.length()];
        for (int i = 0; i < s.length(); i++) {
            dp[i] = i;
            for (int j = 0; j <= i; j++) {
                if (s.charAt(i) == s.charAt(j) && (i - j < 2 || isPalin[j + 1][i - 1])) {
                    isPalin[i][j] = isPalin[j][i] = true;
                    if (j == 0) {
                        dp[i] = 0;
                    }
                    else {
                        dp[i] = Math.min(dp[i], dp[j - 1] + 1);
                    }
                }
            }
        }
        return dp[dp.length - 1];
    }
```

## KMP

214. Shortest Palindrome

[KMP explain](http://yucoding.blogspot.com/2015/10/leetcode-question-shortest-palindrome.html)

S = QP
P'S = P'QP is palindrome. Q is palindrome.
SS' = QPP'Q' = QPP'Q

Use kmp to find longest prefix and suffix Q.
Q cannot be longer than S. So we can add a "#" between S and S' when we get next array.

```java
public String shortestPalindrome(String s) {
        if (s.length() <= 1) {
            return s;
        }
        int[] next = getNext(s + "#" + new StringBuilder(s).reverse().toString());
        return new StringBuilder(s.substring(next[next.length - 1])).reverse().toString() + s; 
    }
    
    public int[] getNext(String s) {
        int[] next = new int[s.length()];
        int k = 0;
        for (int i = 1; i < s.length(); i++) {
            while (k > 0 && s.charAt(k) != s.charAt(i)) {
                k = next[k - 1];
            }
            if (s.charAt(k) == s.charAt(i)) {
                k++;
            }
            next[i] = k;
        }
        return next;
    }
```

28. Implement strStr()

kmp, needle is the patten, and search for haystack to match for the next array.

```java
    public int strStr(String haystack, String needle) {
        if (haystack.length() < needle.length()) {
            return -1;
        }
        if (needle.length() == 0) {
            return 0;
        }
        int[] next = getNext(needle);
        int k = 0;
        for (int i = 0; i < haystack.length(); i++) {
            while (k > 0 && haystack.charAt(i) != needle.charAt(k)) {
                k = next[k - 1];
            }
            if (haystack.charAt(i) == needle.charAt(k)) {
                k++;
            }
            if (k == needle.length()) {
                return i - needle.length() + 1;
            }
        }
        return -1;
    }
    
    public int[] getNext(String pattern) {
        int[] next = new int[pattern.length()];
        int k = 0;
        for (int i = 1; i < pattern.length(); i++) {
            while (k > 0 && pattern.charAt(k) != pattern.charAt(i)) {
                k = next[k - 1];
            }
            if (pattern.charAt(k) == pattern.charAt(i)) {
                k++;
            }
            next[i] = k;
        }
        return next;
    }
```

43. Multiply Strings

Keep a int array to store all the number. O(m * n).
The max length will be m + n. Current index is i + j + 1.
Remember to remove the heading zero.

```java
    public String multiply(String num1, String num2) {
        int len1 = num1.length();
        int len2 = num2.length();
        int[] num = new int[len1 + len2];
        int i, j;
        for (i = len1 - 1; i >= 0; i--) {
            int carry = 0;
            for (j = len2 - 1; j >= 0; j--) {
                int multi = num[i + j + 1] + (num1.charAt(i) - '0') * (num2.charAt(j) - '0') + carry;     
                num[i + j + 1] = multi % 10;
                carry = multi / 10;
            }
            num[i + j + 1] = carry;
        }
        int ind = 0;
        while (num[ind] == 0) {
            ind++;
            if (ind == num.length) {
                return "0";
            }
        }
        StringBuilder sb = new StringBuilder();
        for (int k = ind; k < num.length; k++) {
            sb.append(num[k]);
        }
        return sb.toString();
    }
```

271. Encode and Decode Strings

Append length then ":"(can be any punctuation) then the string.

Use indexOf to get the index of ":", then get the length.

```java
    public String encode(List<String> strs) {
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            sb.append(s.length()).append(":").append(s);
        }
        return sb.toString();
    }

    // Decodes a single string to a list of strings.
    public List<String> decode(String s) {
        int start = 0;
        List<String> res = new ArrayList<>();
        while (start < s.length()) {
            int index = s.indexOf(":", start);
            int len = Integer.parseInt(s.substring(start, index));
            String cur = s.substring(index + 1, index + len + 1);
            res.add(cur);
            start = index + len + 1;
        }
        return res;
    }
```
288. Unique Word Abbreviation

Use map to check whether abbreviation is stored.
The value is the word, If there are more than one word map to that abbreviation, the value will be "".

```java
class ValidWordAbbr {
    
    Map<String, String> map = new HashMap<>();

    public ValidWordAbbr(String[] dictionary) {
        for(String s : dictionary) {
            String abbr = getAbbr(s);
            if (map.containsKey(abbr) && !map.get(abbr).equals(s)) {
                map.put(abbr, "");
            }
            else {
                map.put(abbr, s);
            }
        }
    }
    
    public boolean isUnique(String word) {
        String abbr = getAbbr(word);
        return (!map.containsKey(abbr)) || map.get(abbr).equals(word);
    }
    
    public String getAbbr(String word) {
        if (word.length() <= 2) {
            return word;
        }
        int len = word.length();
        int middle = len - 2;
        return word.charAt(0) + "" + middle + word.charAt(len - 1);
    }
}
```

320. Generalized Abbreviation

dfs, O(2^(len))

```java
    public List<String> generateAbbreviations(String word) {
        List<String> res = new ArrayList<>();
        helper(word, res, new StringBuilder(), 0, 0);
        return res;
    }
    
    public void helper(String word, List<String> res, StringBuilder sb, int count, int ind) {
        if (ind == word.length()) {
            if (count != 0) {
                sb.append(count);
            }
            res.add(sb.toString());
            return;
        }
        char c = word.charAt(ind);
        int len = sb.length();  
        // option 1: increment count
        helper(word, res, sb, count + 1, ind + 1);
        // in every branch, we have to reset the length of stringbuilder 
        // or we can reset the length in deeper level of search
        sb.setLength(len);
        // option 2: append char, reset count to be 0
        if (count > 0) {
            sb.append(count);
        }    
        helper(word, res, sb.append(c), 0, ind + 1);
        sb.setLength(len);        
    }
```

394. Decode String

Use open to find matching ']' of '['. then do recursive. Be careful of the index manipulation.

```java
    public String decodeString(String s) {
        if (s == null || s.length() == 0) {
            return s;
        }
        StringBuilder sb = new StringBuilder();
        int count = 0;
        int i = 0;
        while (i < s.length()) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                count = count * 10 + c - '0';
                i++;
            }
            else if (c == '[') {
                int j = i + 1;
                int open = 1;
                while (j < s.length() && open > 0) {
                    if (s.charAt(j) == '[') {
                        open++;
                    }
                    else if (s.charAt(j) == ']') {
                        open--;
                    }
                    j++;
                }
                String sub = decodeString(s.substring(i + 1, j - 1));
                while (count > 0) {                    
                    sb.append(sub);
                    count--;
                }
                i = j;
            }
            else {
                sb.append(c);
                i++;
            }
        }
        return sb.toString();
    }
```

Two stack or one queue.

14. Longest Common Prefix

```java
    public String longestCommonPrefix(String[] strs) {
        if(strs.length == 0) {
	        return "";
        }
	    if(strs.length == 1) {
	        return strs[0];
        }
        //maximum length of the prefix
        int maxLen = Integer.MAX_VALUE;
        for(String str : strs) {
	        maxLen = Math.min(maxLen, str.length());
        }
        for(int i = 0; i < maxLen; i++) {
	        char c = strs[0].charAt(i);
	        for(String str : strs) {
		        if(str.charAt(i) != c) {
			        return strs[0].substring(0, i);
                }
	        }
        }
        return strs[0].substring(0, maxLen);
    }
```

