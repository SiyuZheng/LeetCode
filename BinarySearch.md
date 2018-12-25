74. Search a 2D Matrix

Not very good solution, use template to avoid endless loop. O(log(m * n))

```java
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        int m = matrix.length, n = matrix[0].length;
        int s = 0, e = m * n - 1;
        while (s + 1 < e) {
            int mid = s + (e - s) / 2;
            int row = mid / n;
            int col = mid % n;
            if (matrix[row][col] < target) {
                s = mid;
            }
            else {
                e = mid;
            }
        }
        int row = s / n;
        int col = s % n;
        if (matrix[row][col] == target) {
            return true;
        }
        row = e / n;
        col = e % n;
        if (matrix[row][col] == target) {
            return true;
        }
        return false;
    }
```
240. Search a 2D Matrix II

Start from right top corner, then traverse with O(m + n).

```java
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        int m = matrix.length;
        int n = matrix[0].length;
        int x = n - 1, y = 0;
        while (x >= 0 && y < m) {
            if (matrix[y][x] == target){
                return true;
            }
            else if (matrix[y][x] < target){
                y++;
            }
            else {
                x--;
            }
        }
        return false;
    }
```