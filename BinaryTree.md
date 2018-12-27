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