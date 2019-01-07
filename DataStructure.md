[146. LRU Cache](https://leetcode.com/problems/lru-cache/description/)

Don't forget to update size!!!

```java
class LRUCache {
    
    class Node {
        int key;
        int val;
        Node next;
        Node prev;
        public Node() {
            
        }
        
        public Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }
    
    Node head;
    Node tail;
    Map<Integer, Node> map = new HashMap<>();
    int size = 0;
    int capacity;

    public LRUCache(int capacity) {
        head = new Node();
        tail = new Node();
        head.next = tail;
        tail.prev = head;
        this.capacity = capacity;
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        update(key);
        return map.get(key).val;
    }
    
    public void put(int key, int value) {
        if (capacity <= 0) {
            return;
        }
        if (map.containsKey(key)) {
            map.get(key).val = value;
            update(key);
            return;
        }
        if (size == capacity) {
            Node rem = removeFromTail();
            map.remove(rem.key);
            size--;
        }
        Node node = new Node(key, value);
        appendHead(node);
        map.put(key, node);
        size++;
        return;
    }
    
    public void update(int key) {
        Node node = map.get(key);
        removeNode(node);
        appendHead(node);
    }
    
    public void appendHead(Node node) {
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
        node.prev = head;
    }
    
    public void removeNode(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    public Node removeFromTail() {
        Node res = tail.prev;
        tail.prev.prev.next = tail;
        tail.prev = tail.prev.prev;
        return res;
    }
}
```

[460. LFU Cache](https://leetcode.com/problems/lfu-cache/description/)

Map for key and node, and freqMap for freq and double linkedlist. Keep a min to indicate the minimum freq node in the cache. When adding a new node, reset minimum to be 1. In update function, if previous freq has no nodes in DLL, increment min.

Be careful at modifying the size !!! modify for each double linkedlist and the cache.

```java
class LFUCache {
    
    class Node {
        int key;
        int val;
        int freq = 1;
        Node next;
        Node prev;
        public Node() {
            
        }
        
        public Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }
    
    class DLL {
        Node head;
        Node tail;
        int size = 0;
        
        public DLL() {
            head = new Node();
            tail = new Node();
            head.next = tail;
            tail.prev = head;
        }
        
        public Node removeFromTail() {
            Node rem = tail.prev;
            removeNode(rem);
            return rem;
        }
        
        public void removeNode(Node node) {
            node.prev.next = node.next;
            node.next.prev = node.prev;
            this.size--;
        }
        
        public void appendToHead(Node node) {
            node.next = head.next;
            head.next.prev = node;
            node.prev = head;
            head.next = node;
            this.size++;
        }
    }
    
    Map<Integer, DLL> freqMap;
    Map<Integer, Node> map;
    int capacity;
    int size = 0;
    int min;

    public LFUCache(int capacity) {
        freqMap = new HashMap<>();
        map = new HashMap<>();
        min = 0;
        this.capacity = capacity;
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        int res = map.get(key).val;
        update(map.get(key));
        return res;
    }
    
    public void put(int key, int value) {
        if (capacity <= 0) {
            return;
        }
        if (map.containsKey(key)) {
            Node node = map.get(key);
            update(node);
            node.val = value;
            return;
        }
        if (size == capacity) {
            Node rem = freqMap.get(min).removeFromTail();
            map.remove(rem.key);
            size--;
        }
        Node addNode = new Node(key, value);
        min = 1;
        map.put(key, addNode);
        freqMap.putIfAbsent(addNode.freq, new DLL());
        freqMap.get(addNode.freq).appendToHead(addNode);
        size++;    
        
    }
    
    public void update(Node node) {
        freqMap.get(node.freq).removeNode(node);
        if (min == node.freq && freqMap.get(min).size == 0) {
            min++;
        }
        node.freq++;
        freqMap.putIfAbsent(node.freq, new DLL());
        freqMap.get(node.freq).appendToHead(node);
    }
}

```

[295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/description/)

Max heap and min heap, when size is odd add to one, and when size is even add to the other. Don't forget to deal with the size is 0.

Add current number first then do poll !!!!

Collections.reverseOrder(), if write my compare will cause overflow.


```java
class MedianFinder {
    
    int size = 0;
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>(1, Collections.reverseOrder());
    
    /** initialize your data structure here. */
    public MedianFinder() {
        
    }
    
    public void addNum(int num) {
        if (size == 0) {
            maxHeap.offer(num);
            size++;
            return;
        }
        if (size % 2 == 0) {
            if (num <= maxHeap.peek()) {
                maxHeap.offer(num);
            }
            else {
                minHeap.offer(num);
                maxHeap.offer(minHeap.poll());
            }
        }
        else {
            if (num <= maxHeap.peek()) {
                maxHeap.offer(num);
                minHeap.offer(maxHeap.poll());
            }
            else {
                minHeap.offer(num);
            }
        }
        size++;
    }
    
    public double findMedian() {
        if (size == 0) {
            return 0.0;
        }
        if (size % 2 == 1) {
            return (double)maxHeap.peek();
        }
        else {
            return (maxHeap.peek() + minHeap.peek()) / 2.0;
        }
    }
}
```

[239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/description/)

Deque, push all value >= peekLast into the queue, if first of deque is the same as i - k, remove it. 

```java
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return new int[0];
        }
        int[] res = new int[nums.length - k + 1];
        Deque<Integer> dq = new LinkedList<>();
        int i = 0;
        while (i < nums.length) {
            while (!dq.isEmpty() && nums[i] > dq.peekLast()) {
                dq.pollLast();
            }
            dq.offer(nums[i]);
            if (i - k >= 0 && nums[i - k] == dq.peekFirst()) {
                dq.pollFirst();
            }
            if (i >= k - 1) {
                res[i - k + 1] = dq.peekFirst();
            }
            i++;
        }
        return res;
    }
```

## Random

[380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/description/)

Use list.set(index, val). O(1) operation
Be careful when the remove value is at the last of list.
In update, reset the last value's new index.

```java
class RandomizedSet {
    
    Map<Integer, Integer> map = new HashMap<>();
    ArrayList<Integer> lst = new ArrayList<>();
    int size = 0;
    Random rand = new Random();

    /** Initialize your data structure here. */
    public RandomizedSet() {
        
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if (map.containsKey(val)) {
            return false;
        }
        map.put(val, size);
        lst.add(val);
        size++;
        return true;
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if (!map.containsKey(val)) {
            return false;
        }
        size--;
        int ind = map.get(val);
        int lastVal = lst.get(size);
        lst.remove(size);
        if (ind != size) {
            lst.set(ind, lastVal);
            map.put(lastVal, ind);
        }       
        map.remove(val);
        return true;
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        int r = rand.nextInt(size);
        return lst.get(r);
    }
}
```


[381. Insert Delete GetRandom O(1) - Duplicates allowed](https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed/description/)

Use a map of set, use iterator to get next remove index. Be careful at swapping two element in arraylist.

```java
class RandomizedCollection {
    
    Map<Integer, Set<Integer>> map = new HashMap<>();
    List<Integer> lst = new ArrayList<>();
    Random rand = new Random();

    /** Initialize your data structure here. */
    public RandomizedCollection() {
        
    }
    
    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
    public boolean insert(int val) {
        boolean res = false;
        if (map.containsKey(val)) {
            res = false;
        }
        else {
            res = true;
            map.put(val, new HashSet<Integer>());
        }
        map.get(val).add(lst.size());
        lst.add(val);
        return res;
    }
    
    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
    public boolean remove(int val) {   
        if (!map.containsKey(val) || map.get(val).size() == 0) {
            return false;
        }
        int remInd = map.get(val).iterator().next();
        // REMOVE FIRST!!!!!!
        map.get(val).remove(remInd);
        if (remInd != lst.size() - 1) {
            int lastVal = lst.get(lst.size() - 1);
            map.get(lastVal).remove(lst.size() - 1);
            lst.set(remInd, lastVal);
            map.get(lastVal).add(remInd);
        }
        lst.remove(lst.size() - 1);
        return true;
    }
    
    /** Get a random element from the collection. */
    public int getRandom() {
        return lst.get(rand.nextInt(lst.size()));
    }
}
```

[382. Linked List Random Node](https://leetcode.com/problems/linked-list-random-node/description/)

Reservoir Sampling.

```java
    ListNode head;
    Random rand;

    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    public Solution(ListNode head) {
        this.head = head;
        this.rand = new Random();
    }
    
    /** Returns a random node's value. */
    public int getRandom() {
        ListNode cur = head;
        int res = head.val;
        int count = 1;
        while (cur != null) {
            int r = rand.nextInt(count);
            if (r == 1) {
                res = cur.val;
            }
            count++;
            cur = cur.next;
        }
        return res;
    }
```