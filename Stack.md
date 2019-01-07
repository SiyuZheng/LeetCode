[224. Basic Calculator](https://leetcode.com/problems/basic-calculator/description/)


```java
    public int calculate(String s) {
        char[] cs = s.toCharArray();
        Stack<Integer> st = new Stack<>();
        int sign = 1;
        int count = 0;
        int res = 0;
        for (int i = 0; i < cs.length; i++) {
            char c = cs[i];
            if (Character.isDigit(c)) {
                count = count * 10 + c - '0';
            }
            else if (c == '+') {
                res += count * sign;
                sign = 1;
                count = 0;
            }
            else if (c == '-') {              
                res += count * sign;
                sign = -1;
                count = 0;
            }
            else if (c == '(') {
                st.push(res);
                st.push(sign);
                sign = 1;
                res = 0;
            }
            else if (c == ')') {
                res += count * sign;
                count = 0;
                sign = 1;
                res = res * st.pop() + st.pop();
            }   
        }
        if (count != 0) {
            res += count * sign;
        }
        return res;
    }
```
