111. Minimum Depth of Binary Tree

```java
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        if (root.left == null) {
            return minDepth(root.right) + 1;
        }
        if (root.right == null) {
            return minDepth(root.left) + 1;
        }
        return Math.min(minDepth(root.left), minDepth(root.right)) + 1;
    }
```

110. Balanced Binary Tree

Use a global variable to indicate whether it's balanced.
Another solution is to return -1 in depth to indicate the subtree is not balance, so we don't need to call depth on each node.

```java
    boolean res = true;
    public boolean isBalanced(TreeNode root) {
        if (root == null) {
            return true;
        }
        depth(root);
        return res;
    }
    
    public int depth(TreeNode node) {
        if (node == null) {
            return 0;
        }
        int left = depth(node.left);
        int right = depth(node.right);
        if (Math.abs(left - right) > 1) {
            res = false;
        }
        return Math.max(left, right) + 1;
    }
```


## LCA

235. Lowest Common Ancestor of a Binary Search Tree

Use binary search tree to compare value. Traverse half of the tree to find the LCA.

Recursive

```java
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return root;
        }
        if (root == p || root == q) {
            return root;
        }
        if (p.val < root.val && q.val < root.val) {
            return lowestCommonAncestor(root.left, p, q);
        }
        else if (p.val > root.val && q.val > root.val) {
            return lowestCommonAncestor(root.right, p, q);
        }
        return root;
    }
```

Iterative, tail recursive can be modified into iterative.
```java
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while (root != null) {
            if (root.val < p.val && root.val < q.val) {
                root = root.right;
            }
            else if (root.val > p.val && root.val > q.val) {
                root = root.left;
            }
            else {
                return root;
            }
        }
        return root;
    }
```

236. Lowest Common Ancestor of a Binary Tree

Post order traversal, O(n).

Recursive
```java
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return root;
        }
        if (root == p || root == q) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left == null && right == null) {
            return null;
        }
        if (left == null) {
            return right;
        }
        if (right == null) {
            return left;
        }
        return root;
    }
```

Iterative, traverse the tree to find the parent of nodes, then store them in a map. Use a set to store all ancestor of p, then check which ancentor of q is inside the set. Not pretty fast than recursive.

```java
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return root;
        }
        Stack<TreeNode> st = new Stack<>();
        Map<TreeNode, TreeNode> parent = new HashMap<>();
        st.push(root);
        parent.put(root, null);
        while (!st.isEmpty()) {
            TreeNode cur = st.pop();
            if (cur.left != null) {
                parent.put(cur.left, cur);
                st.push(cur.left);
            }
            if (cur.right != null) {
                parent.put(cur.right, cur);
                st.push(cur.right);
            }
        }
        Set<TreeNode> set = new HashSet<>();
        TreeNode par = p;
        while (par != null) {
            set.add(par);
            par = parent.get(par);
        }
        par = q;
        while (par != null) {
            if (set.contains(par)) {
                return par;
            }
            par = parent.get(par);
        }
        return root;
    }
```

input is a list of nodes

```java
public TreeNode lowestCommonAncestor(TreeNode root, List<TreeNode> nodes) {
	Map<TreeNode, TreeNode> parent = new HashMap<>();
	Stack<TreeNode> st = new Stack<>();
	st.push(root);
	while(!st.isEmpty()) {
		TreeNode cur = st.pop();
		if(cur.left != null) {
			map.put(cur.left, cur);
			st.push(cur.left);
		}
		if(cur.right != null) {
			map.put(cur.right, cur);
			st.push(cur.right);
		}
	}
	
}

```

LCA of Deepest Node


## Order traversal

Recursive:

```java
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        helper(root, res);
        return res;
    }
    
    public void helper(TreeNode root, List<Integer> res) {
        if (root == null) {
            return;
        }
        res.add(root.val);
        helper(root.left, res);
        helper(root.right, res);
    }
```

PreOrder  Stack
InOrder Stack + Cur
PostOrder Stack + Cur + Prev 

144. Binary Tree Preorder Traversal

Stack. Add right child first then add left child.

```java
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Stack<TreeNode> st = new Stack<>();
        st.push(root);
        while (!st.isEmpty()) {
            TreeNode cur = st.pop();
            res.add(cur.val);
            if (cur.right != null) {
                st.push(cur.right);
            }
            if (cur.left != null)  {
                st.push(cur.left);
            }
        }
        return res;
    }
```
94. Binary Tree Inorder Traversal

Stack + cur
Add leftmost to the stack, then pop to add top of stack, then turn right.

```java
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        TreeNode cur = root;
        Stack<TreeNode> st = new Stack<>();
        while (cur != null || !st.isEmpty()) {
            while (cur != null) {
                st.push(cur);
                cur = cur.left;
            }
            cur = st.pop();
            res.add(cur.val);
            cur = cur.right;
        }
        return res;
    }
```

145. Binary Tree Postorder Traversal

Stack + cur + prev

```java
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        TreeNode cur = root;
        TreeNode prev = null;
        Stack<TreeNode> st = new Stack<>();
        st.push(root);
        while (!st.isEmpty()) {
            cur = st.peek();
            // search for deeper node
            if (prev == null || prev.left == cur || prev.right == cur) {
                if (cur.left != null) {
                    st.push(cur.left);
                }
                // use else if to add just one node each time 
                else if (cur.right != null) {
                    st.push(cur.right);
                }
            }
            // means we only go to left in the previous step, then we try right child
            else if (cur.left == prev) {
                if (cur.right != null) {
                    st.push(cur.right);
                }
            }
            // here prev equals cur, so that we can add it to the res
            else {
                res.add(st.pop().val);
            }
            prev = cur;
        }
        return res;  
    }
```
285. Inorder Successor in BST

Recursive

```java
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        if (root == null || p == null) {
            return null;
        }
        if (p.val < root.val) {
            TreeNode res = inorderSuccessor(root.left, p);
            if (res == null) {
                return root;
            }
            return res;
        }
        else {
            return inorderSuccessor(root.right, p);
        }
    }
```

Iterative

Store node which has value greater than p, then go to the left child to find a smaller value which is greater than p.

```java
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        TreeNode res = null;
        while (root != null) {
            if (root.val <= p.val) {
                root = root.right;
            }
            else {
                res = root;
                root = root.left;
            }
        }
        return res;
    }
```
314. Binary Tree Vertical Order Traversal

Two queue and one map. O(n)

```java
    public List<List<Integer>> verticalOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<List<Integer>>();
        }
        Queue<TreeNode> nq = new LinkedList<>();
        Queue<Integer> iq = new LinkedList<>();
        Map<Integer, List<Integer>> map = new HashMap<>();
        int min = 0;
        int max = 0;
        nq.offer(root);
        iq.offer(0);
        while (!nq.isEmpty()) {
            TreeNode cur = nq.poll();
            int ind = iq.poll();
            if (!map.containsKey(ind)) {
                map.put(ind, new ArrayList<Integer>());
            }
            map.get(ind).add(cur.val);
            if (cur.left != null) {
                nq.offer(cur.left);
                iq.offer(ind - 1);
                min = Math.min(min, ind - 1);
            }
            if (cur.right != null) {
                nq.offer(cur.right);
                iq.offer(ind + 1);
                max = Math.max(max, ind + 1);
            }
        }
        List<List<Integer>> res = new ArrayList<>();
        for (int i = min; i <= max; i++) {
            res.add(map.get(i));
        }
        return res;
    }
```

199. Binary Tree Right Side View

level order traversal

```java
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        if(root == null){
            return res;
        }
        q.offer(root);
        while(!q.isEmpty()){
            int size = q.size();
            for (int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                if (i == size - 1) {
                    res.add(cur.val);
                }
                if (cur.left != null) {
                    q.offer(cur.left);
                }
                if (cur.right != null) {
                    q.offer(cur.right);
                }
            }
        }
        return res;
    }
```


331. Verify Preorder Serialization of a Binary Tree

Using queue to check whether the serialization is complete.
For root, check whether after inorder traversal, the queue is empty. 
For each node not root, check whether queue still has something.
O(n)

```java
    public boolean isValidSerialization(String preorder) {
        String[] sp = preorder.split(",");
        Queue<String> q = new LinkedList<>(Arrays.asList(sp));
        if (q.poll().equals("#")) {
            return q.isEmpty();
        }
        boolean left = isValid(q);
        boolean right = isValid(q);
        return left && right && q.isEmpty();
    }
    
    public boolean isValid(Queue<String> q) {
        if (q.isEmpty()) {
            return false;
        }
        String s = q.poll();
        if (s.equals("#")) {
            return true;
        }
        boolean left = isValid(q);
        boolean right = isValid(q);
        return left && right;
    }
```

Stack solution:



99. Recover Binary Search Tree

Recursive global pointer O(n)

```java
    TreeNode first = null;
    TreeNode second = null;
    TreeNode prev = null;
    
    public void recoverTree(TreeNode root) {
        helper(root);
        int temp = first.val;
        first.val = second.val;
        second.val = temp;
    }
    
    public void helper(TreeNode cur) {
        if (cur == null) {
            return;
        }
        helper(cur.left);
        if (prev != null) {
            if (first == null && cur.val < prev.val) {
                first = prev;
            }
            if (first != null && cur.val < prev.val) {
                second = cur;
            }
        }
        prev = cur;
        helper(cur.right);
    }
```

Iterative, inorder traversal, O(n)

```java
    public void recoverTree(TreeNode root) {
        TreeNode first = null;
        TreeNode second = null;
        Stack<TreeNode> st = new Stack<>();
        TreeNode cur = root;
        TreeNode prev = null;
        while (cur != null || !st.isEmpty()) {
            while (cur != null) {
                st.push(cur);
                cur = cur.left;
            }
            cur = st.pop();
            if (prev != null && prev.val > cur.val) {
                if (first == null) {
                    first = prev;
                    second = cur;
                }
                else {
                    second = cur;
                }
            }
            prev = cur;
            cur = cur.right;
        }
        int temp = first.val;
        first.val = second.val;
        second.val = temp;
        return;
    }
```

272. Closest Binary Search Tree Value II

Inorder traversal, using deque to maintain all value. O(n), but the order in result is inorder, not sorted by difference to the target.

```java
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        Deque<Integer> dq = new LinkedList<>();
        helper(root, target, dq, k);
        return new ArrayList<Integer>(dq);
    }
    
    public void helper(TreeNode root, double target, Deque<Integer> dq, int k) {
        if (root == null) {
            return;
        }
        helper(root.left, target, dq, k);
        if (dq.size() < k) {
            dq.offerLast(root.val);
        }
        else {
            if (Math.abs(dq.peekFirst() - target) > Math.abs(root.val - target)) {
                dq.pollFirst();
                dq.offerLast(root.val);
            }
            else {
                return;
            }
        }
        helper(root.right, target, dq, k);
    }
```
230. Kth Smallest Element in a BST

Count the elements in the left subtree then compare with k. O(n).

```java
    public int kthSmallest(TreeNode root, int k) {
        int leftCount = count(root.left);
        if (k <= leftCount) {
            return kthSmallest(root.left, k);
        }
        else if (k == leftCount + 1) {
            return root.val;
        }
        else {
            return kthSmallest(root.right, k - leftCount - 1);
        }
    }
    
    public int count(TreeNode root) {
        if (root == null) {
            return 0;
        } 
        return count(root.left) + count(root.right) + 1;
    }
```

Iterative, stack + cur, inorder traversal, O(n).

```java
    public int kthSmallest(TreeNode root, int k) {
        Stack<TreeNode> st = new Stack<>();
        TreeNode cur = root;
        while (cur != null || !st.isEmpty()) {
            while (cur != null) {
                st.push(cur);
                cur = cur.left;
            }
            cur = st.pop();
            if (k == 1) {
                return cur.val;
            }
            else {
                k--;
            }
            cur = cur.right;
        }
        return -1;
    }
```
108. Convert Sorted Array to Binary Search Tree

Divide and conquer.

255. Verify Preorder Sequence in Binary Search Tree

Divide and conquer. O(nlogn) time O(1) space


```java
    public boolean verifyPreorder(int[] preorder) {
        return helper(preorder, 0, preorder.length - 1);
    }
    
    public boolean helper(int[] preorder, int start, int end) {
        if(end - start <= 1) {
            return true;
        }
        int root = preorder[start];
        int i = start;
        for (; i <= end; i++) {
            if (preorder[start] < preorder[i]) {
                break;
            }
        }
        // if we can't find a preorder[i] > preorder[start], then i == end + 1
        int split = i;
        for (; i <= end; i++) {
            if (preorder[start] > preorder[i]) {
                return false;
            }
        }
        return helper(preorder, start + 1, split - 1) && helper(preorder, split, end);
    }
```


## Subtree

helper recursive

If using count / size, we can use sign to serve as flag.

Sometimes defind some class for a lot of subtree information.

100. Same Tree
101. Symmetric Tree

These two problems using recursive is trivials. Using two stack for iterative approach.


333. Largest BST Subtree

helper class with min max value and count of this BST. If not BST, set count to be -1. O(n), no duplicate recursive. Not very fast.

```java
    class BST {
        int min;
        int max;
        int count;
        
        public BST(int min, int max, int count) {
            this.min = min;
            this.max = max;
            this.count = count;
        }
    }
    
    int max = 0;
    public int largestBSTSubtree(TreeNode root) {
        helper(root);
        return max;
    }
    
    public BST helper(TreeNode node) {
        if (node == null) {
            return new BST(Integer.MAX_VALUE, Integer.MIN_VALUE, 0);
        }
        BST left = helper(node.left);
        BST right = helper(node.right);
        if (left.count == -1 || right.count == -1 || node.val <= left.max || node.val >= right.min) {
            return new BST(Integer.MAX_VALUE, Integer.MIN_VALUE, -1);
        }
        int size = left.count + right.count + 1;
        int minV = Math.min(left.min, node.val);
        int maxV = Math.max(right.max, node.val);
        max = Math.max(max, size);
        return new BST(minV, maxV, size);
    }
```

124. Binary Tree Maximum Path Sum

Like the previous problem. Calculate left and right, then update with current root, then return.

Remember to clamp path sum with 0 to remove negative.

```java
    int max = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        if (root == null) {
            return 0;
        }
        pathSum(root);
        return max;
    }
    
    public int pathSum(TreeNode node) {
        if (node == null) {
            return 0;
        }
        int leftSum = Math.max(pathSum(node.left), 0);
        int rightSum = Math.max(pathSum(node.right), 0);
        if (leftSum + rightSum + node.val > max) {
            max = leftSum + rightSum + node.val;
        }
        return Math.max(leftSum, rightSum) + node.val;
    }
```
250. Count Univalue Subtrees

boolean helper function, check whether left child and right child is null, then check value.
O(n)

```java
    int count = 0;
    public int countUnivalSubtrees(TreeNode root) {
        if (root == null) {
            return 0;
        }
        helper(root);
        return count;
    }
    
    public boolean helper(TreeNode root) {
        if (root == null) {
            return true;
        }
        boolean left = helper(root.left);
        boolean right = helper(root.right);
        if (left && right) {
            if (root.left != null && root.val != root.left.val) {
                return false;
            }
            if (root.right != null && root.val != root.right.val) {
                return false;
            }
            count++;
            return true;
        }
        return false;
    }
```
222. Count Complete Tree Nodes

Use height to count nodes.
2 ^ 0 + 2 ^ 1 + ... 2 ^ n = 2 ^ (n + 1) - 1
n + 1 is height in the code. 
-1 can be compensated by add root + 1.

```java
    public int countNodes(TreeNode root) {
        if (root == null){
            return 0;
        }
        int left = height(root.left);
        int right = height(root.right);
        if (left > right) {
            return countNodes(root.left) + (1 << right) - 1 + 1;
        }
        else {
            return countNodes(root.right) + (1 << left) - 1 + 1;
        }
    }
    
    public int height(TreeNode node) {
        return node == null ? 0 : height(node.left) + 1;
    }
```

96. Unique Binary Search Trees

Given i nodes, it can be build with left j, and right i - j - 1 nodes. O(n^2).

```java
    public int numTrees(int n) {
        int[] count = new int[n + 1];
        if (n < 3) {
            return n;
        }
        count[0] = 1;
        count[1] = 1;
        count[2] = 2;
        for (int i = 3; i <= n; i++) {
            for (int j = 0; j <= i - 1; j++) {
                count[i] += count[j] * count[i - j - 1];
            }
        }
        return count[n];
    }
```
95. Unique Binary Search Trees II

Create left child list and right child list, and combine with the root nodes.

Collections can store null.

```java
    public List<TreeNode> generateTrees(int n) {
        if (n == 0) {
            return new ArrayList<TreeNode>();
        }
        return helper(1, n);
    }
    
    public List<TreeNode> helper(int start, int end) {
        List<TreeNode> lst = new ArrayList<>();
        if (start > end) {
            lst.add(null);
            return lst;
        }
        for (int i = start; i <= end; i++) {
            List<TreeNode> left = helper(start, i - 1);
            List<TreeNode> right = helper(i + 1, end);            
            for (TreeNode leftChild : left) {
                for (TreeNode rightChild : right) {
                    TreeNode root = new TreeNode(i);
                    root.left = leftChild;
                    root.right = rightChild;
                    lst.add(root);
                }
            }          
        }
        return lst;
    }
```

## Modify tree structure

114. Flatten Binary Tree to Linked List

Recursive, worse case O(n^2).

```java
    public void flatten(TreeNode root) {
        if (root == null) {
            return;
        }
        TreeNode left = root.left;
        TreeNode right = root.right;
        flatten(root.left);
        flatten(root.right);
        root.left = null;
        root.right = left;
        TreeNode cur = root;
        while (cur.right != null) {
            cur = cur.right;
        }
        cur.right = right;s        
    }
```

117. Populating Next Right Pointers in Each Node II

Use a dummy node. It's like build up a linked list for each level.

```java
public void connect(TreeLinkNode root) {
    while (root != null) {
        TreeLinkNode dummy = new TreeLinkNode(0);
        TreeLinkNode cur = dummy;
        while (root != null) {
            if (root.left != null) {
                cur.next = root.left;
                cur = cur.next;
            }
            if (root.right != null) {
                cur.next = root.right;
                cur = cur.next;
            }
            root = root.next;
        }
        root = dummy.next;
    }
}

```

257. Binary Tree Paths

```java
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> path = new ArrayList<String>();
        helper(path, root, new StringBuilder());
        return path;
    }
    
    public void helper(List<String> path, TreeNode root, StringBuilder sb) {
        if (root == null) {
            return;
        }
        int len = sb.length();
        sb.append(root.val);
        if (root.left == null && root.right == null) {            
            path.add(sb.toString());
            sb.setLength(len);
            return;
        }
        sb.append("->");
        helper(path, root.left, sb);
        helper(path, root.right, sb);
        sb.setLength(len);
    }
```
426. Convert Binary Search Tree to Sorted Doubly Linked List

Iterative

```java
    Node prev = null;
    Node head = null;
    
    public Node treeToDoublyList(Node root) {
        if (root == null) {
            return root;
        }
        Stack<Node> st = new Stack<>();
        Node cur = root;
        while (cur != null || !st.isEmpty()) {
            while (cur != null) {
                st.push(cur);
                cur = cur.left;
            }
            cur = st.pop();
            if (head == null) {
                head = cur;
            }
            else {
                prev.right = cur;
                cur.left = prev;
            }
            prev = cur;
            cur = cur.right;
        }
        prev.right = head;
        head.left = prev;
        return head;
    }
```

Recursive



112. Path Sum

Check whether current node is leaf node.

```java
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) {
            return false;
        }
        sum -= root.val;
        if (root.left == null && root.right == null) {
            return sum == 0;
        }
        return hasPathSum(root.left, sum) || hasPathSum(root.right, sum);
    }
```

113. Path Sum II

Use dfs to traverse all possible paths. Check whether current node is leaf node(Don't add current list when current node is null since it will cause duplicate where leaf node has two null child!). Remember to remove last value before backtracking. O(n)!! Each node is processed once.

```java
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> res = new ArrayList<>();
        helper(res, root, sum, new ArrayList<Integer>());
        return res;
    }
    
    public void helper(List<List<Integer>> res, TreeNode root, int sum, List<Integer> cur) {
        if (root == null) {
            return;
        }
        sum -= root.val;
        cur.add(root.val);
        if (sum == 0 && root.left == null && root.right == null) {
            res.add(new ArrayList<Integer>(cur));
            cur.remove(cur.size() - 1);
            return;
        }
        helper(res, root.left, sum, cur);
        helper(res, root.right, sum, cur);
        cur.remove(cur.size() - 1);
        return;
    }
```

437. Path Sum III

pathSumFrom means we start counting value from this node. The solution is like two pointer, the length of this "array" is the height of tree. So worse case O(n^2), best case O(nlogn). Space O(n^2).

```java
    public int pathSum(TreeNode root, int sum) {
        if (root == null) {
            return 0;
        }
        return pathSumFrom(root, sum) + pathSum(root.left, sum) + pathSum(root.right, sum);
    }
    
    public int pathSumFrom(TreeNode node, int sum) {
        if (node == null) {
            return 0;
        }
        return (node.val == sum ? 1 : 0) + pathSumFrom(node.left, sum - node.val) + pathSumFrom(node.right, sum - node.val);
    }
```

297. Serialize and Deserialize Binary Tree

Preorder traversal(the root can get its children directly), then use queue to rebuild. O(n).

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serializeHelper(root, sb);
        return sb.toString();
    }
    
    public void serializeHelper(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("n").append(",");
            return;
        }
        sb.append(node.val).append(",");
        serializeHelper(node.left, sb);
        serializeHelper(node.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        String[] sp = data.split(",");
        Queue<String> q = new LinkedList<String>(Arrays.asList(sp));
        return deserializeHelper(q);
    }
    
    public TreeNode deserializeHelper(Queue<String> q) {
        String cur = q.poll();
        if (cur.equals("n")) {
            return null;
        }
        TreeNode root = new TreeNode(Integer.parseInt(cur));
        root.left = deserializeHelper(q);
        root.right = deserializeHelper(q);
        return root;
    }
}
```


129. Sum Root to Leaf Numbers

Need to check whether current node is leaf node. O(n).

```java
    int count = 0;
    public int sumNumbers(TreeNode root) {
        helper(root, 0);
        return count;
    }
    
    public void helper(TreeNode root, int cur) {
        if (root == null) {
            return;
        }
        cur = cur * 10 + root.val;
        if (root.left == null && root.right == null) {
            count += cur;
        }
        helper(root.left, cur);
        helper(root.right, cur);
    }
```

298. Binary Tree Longest Consecutive Sequence

dfs, O(n).

```java
    int max = 1;
    
    public int longestConsecutive(TreeNode root) {
        if(root == null) {
            return 0;
        }
        helper(root.left, 1, root.val);
        helper(root.right, 1, root.val);
        return max;
    }
    
    public void helper(TreeNode node, int cur, int prev) {
        if (node == null) {
            return;
        }
        if (node.val == prev + 1) {
            cur++;
            max = Math.max(max, cur);
            helper(node.left, cur, node.val);
            helper(node.right, cur, node.val);
        }
        else {
            helper(node.left, 1, node.val);
            helper(node.right, 1, node.val);
        }
    }
```

364. Nested List Weight Sum II

1. DFS, Find maxdepth, then sum += (maxdepth - curdepth) * val

2. BFS, double previous result at the end of each level.

```java

```


[246. Binary Tree Path Sum II](https://www.lintcode.com/problem/binary-tree-path-sum-ii/description)

Keep a list from root to current node, then substract one be one level(lst.size() - 1) to find whether the sum is equal to target. 


```java
    public List<List<Integer>> binaryTreePathSum2(TreeNode root, int target) {
        // write your code here
        List<List<Integer>> res = new ArrayList<>();
        helper(target, new ArrayList<Integer>(), res, root);
        return res;
    }
    
    public void helper(int target, List<Integer> lst, List<List<Integer>> res, TreeNode root) {
        if (root == null) {
            return;
        }
        lst.add(root.val);
        int temp = target;
        for (int j = lst.size() - 1; j >= 0; j--) {
            temp -= lst.get(j);
            if (temp == 0) {
                List<Integer> subres = new ArrayList<>();
                for (int i = j; i < lst.size(); i++) {
                    subres.add(lst.get(i));
                }
                res.add(subres);
            }
        }
        
        helper(target, lst, res, root.left);
        helper(target, lst, res, root.right);
        lst.remove(lst.size() - 1);
    }
```



