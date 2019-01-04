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

[23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/description/)

Priority Queue solution. 

```java
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) {
            return null;
        }
        PriorityQueue<ListNode> pq = new PriorityQueue<>(1, new Comparator<ListNode>(){
            @Override
            public int compare(ListNode n1, ListNode n2) {
                if (n1.val < n2.val) {
                    return -1;
                }
                else {
                    return 1;
                }
            }
        });
        for (ListNode node : lists) {
        	if (node == null) {
        		continue;
        	}
            pq.offer(node);
        }
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        while (!pq.isEmpty()) {
            ListNode next = pq.poll();
            if (next.next != null) {
                pq.offer(next.next);
            }
            cur.next = next;
            cur = next;
        }
        cur.next = null;
        return dummy.next;
    }
```

Merge sort, split list into two half. O(log(k) * k* n)
n is the average length of list.Space complexity can be O(1) if we don't create that left and right array.

```java
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) {
            return null;
        }
        if (lists.length == 1) {
            return lists[0];
        }
        int len = lists.length;
        int leftSize = len / 2;
        ListNode[] leftLists = new ListNode[leftSize];
        int rightSize = len - leftSize;
        ListNode[] rightLists = new ListNode[rightSize];
        for (int i = 0; i < leftSize; i++) {
            leftLists[i] = lists[i];
        }
        for (int i = leftSize; i < len; i++) {
            rightLists[i - leftSize] = lists[i];
        }
        ListNode leftNode = mergeKLists(leftLists);
        ListNode rightNode = mergeKLists(rightLists);
        return mergeHelper(leftNode, rightNode);
    }
    
    public ListNode mergeHelper(ListNode left, ListNode right) {
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        while (left != null && right != null) {
            if (left.val < right.val) {
                cur.next = left;
                left = left.next;
            }
            else {
                cur.next = right;
                right = right.next;
            }
            cur = cur.next;
        }
        if (left != null) {
            cur.next = left;
        }
        if (right != null) {
            cur.next = right;
        }
        return dummy.next;
    }
```