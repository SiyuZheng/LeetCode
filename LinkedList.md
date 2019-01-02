445. Add Two Numbers II

Using two stack, remember to append new head if carry is one!!!

```java
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if (l1 == null || l2 == null) {
            return null;
        }
        Stack<ListNode> st1 = new Stack<>();
        Stack<ListNode> st2 = new Stack<>();
        ListNode cur1 = l1;
        ListNode cur2 = l2;
        while (l1 != null) {
            st1.push(l1);
            l1 = l1.next;
        }
        while (l2 != null) {
            st2.push(l2);
            l2 = l2.next;
        }
        ListNode tail = null;
        ListNode cur = null;
        int carry = 0;
        while (!st1.isEmpty() || !st2.isEmpty()) {
            int curVal = (st1.isEmpty() ? 0 : st1.pop().val) + (st2.isEmpty() ? 0 : st2.pop().val) + carry;            
            cur = new ListNode(curVal % 10);
            carry = curVal / 10;
            cur.next = tail;
            tail = cur;
        }
        if (carry == 1) {
            cur = new ListNode(1);
            cur.next = tail;
            tail = cur;
        }
        return tail;
    }
```

Or reverse two linkedlist.