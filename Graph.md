138. Copy List with Random Pointer

Using hashmap to store all the random nodes and use second pass to assign all next and random pointers.

```java
    public RandomListNode copyRandomList(RandomListNode head) {
        if (head == null) {
            return null;
        }
        Map<RandomListNode, RandomListNode> map = new HashMap<>();
        RandomListNode cur = head;
        while (cur != null) {
            map.put(cur, new RandomListNode(cur.label));
            cur = cur.next;          
        }
        cur = head;
        while (cur != null) {
            map.get(cur).random = map.get(cur.random);
            map.get(cur).next = map.get(cur.next);
            cur = cur.next;
        }
        return map.get(head);
    }

```

The idea is to associate the original node with its copy node in a single linked list. In this way, we don't need extra space to keep track of the new nodes. O(1).

The copy one is the next of original node. We have to rebuild the old list and new list afterwards.

Three step:
1. Create new copynode
2. Assign each random node
3. Rebuild new list and old list

```java
    public RandomListNode copyRandomList(RandomListNode head) {
        if (head == null) {
            return null;
        }
        RandomListNode cur = head;
        while (cur != null) {
            RandomListNode temp = cur.next;
            cur.next = new RandomListNode(cur.label);
            cur.next.next = temp;
            cur = temp;
        }
        
        cur = head;
        while (cur != null) {
            if (cur.random != null) {
                cur.next.random = cur.random.next;
            }          
            cur = cur.next.next;
        }
        
        RandomListNode copyHead = new RandomListNode(0);
        RandomListNode copyCur = copyHead;
        cur = head;
        while (cur != null) {
            RandomListNode temp = cur.next.next;
            copyCur.next = cur.next;
            copyCur = copyCur.next;
            cur.next = temp;
            cur = temp;
        }
        return copyHead.next;
    }
```