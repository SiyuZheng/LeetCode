## Topological Sort

[207. Course Schedule](https://leetcode.com/problems/course-schedule/description/)

First find indegree, then put all those indegree 0 into q.

BFS Core: Indegree

```java
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        if (prerequisites == null || prerequisites.length == 0 || numCourses == 0) {
            return true;
        }
        Map<Integer, List<Integer>> map = new HashMap<>();
        int[] indegree = new int[numCourses];
        for (int[] pre : prerequisites) {
            indegree[pre[0]]++;
            map.putIfAbsent(pre[1], new ArrayList<Integer>());
            map.get(pre[1]).add(pre[0]);
        }
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                q.offer(i);
            }
        }
        int num = 0;
        while (!q.isEmpty()) {
            int cur = q.poll();
            num++;
            if (map.get(cur) == null) {
                continue;
            }
            for (int next : map.get(cur)) {
                indegree[next]--;
                if (indegree[next] == 0) {
                    q.offer(next);
                }
            }
        }
        return num == numCourses;
    }
```

DFS, detect cycle.

visited[i] = 0 : never visit this node
visited[i] = 1 : current visiting
visited[i] = 2 : finish all dfs on this node

if we visited a node we are current visiting, it means there is a cycle.
 
```java
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        if (prerequisites == null || prerequisites.length == 0 || numCourses == 0) {
            return true;
        }
        Map<Integer, List<Integer>> map = new HashMap<>();
        int[] indegree = new int[numCourses];
        for (int[] pre : prerequisites) {
            indegree[pre[0]]++;
            map.putIfAbsent(pre[1], new ArrayList<Integer>());
            map.get(pre[1]).add(pre[0]);
        }
        int[] visited = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            if (visited[i] == 0 && hasCycle(visited, map, i)) {
                return false;
            }
        }
        return true;
    }
    
    public boolean hasCycle(int[] visited, Map<Integer, List<Integer>> map, int i) {
        visited[i] = 1;
        if (map.get(i) != null) {
            for (int next : map.get(i)) {
                if (visited[next] == 1 || hasCycle(visited, map, next)) {
                    return true;
                }
            }
        }
        visited[i] = 2;
        return false;
    }
```

[210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/description/)

```java
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] order = new int[numCourses];
        int[] indegree = new int[numCourses];
        for (int[] pre : prerequisites) {
            indegree[pre[0]]++;
        }
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < indegree.length; i++) {
            if (indegree[i] == 0) {
                q.offer(i);
            }
        }
        int ind = 0;
        while (!q.isEmpty()) {
            int cur = q.poll();
            for (int[] pre : prerequisites) {
                if (pre[1] == cur) {
                    indegree[pre[0]]--;
                    if (indegree[pre[0]] == 0) {
                        q.offer(pre[0]);
                    }
                }

            }
            order[ind++] = cur;
        }
        return ind == numCourses ? order : new int[0];
    }
```

[310. Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/description/)

Go from outer nodes to insider nodes. If indegree is 1, then add them into queue. O(V + E).

```java
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        ArrayList[] graph = new ArrayList[n];
        int[] indegree = new int[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<>();
        }
        for (int[] edge : edges) {
            graph[edge[0]].add(edge[1]);
            graph[edge[1]].add(edge[0]);
            indegree[edge[0]]++;
            indegree[edge[1]]++;
        }
        Queue<Integer> q = new LinkedList<>();
        List<Integer> lst = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (indegree[i] <= 1) {
                q.offer(i);
            }
        }
        while(!q.isEmpty()) {
            lst = new ArrayList<>();
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int cur = q.poll();
                lst.add(cur);
                for (int j = 0; j < graph[cur].size(); j++) {
                    int next = (int)graph[cur].get(j);
                    indegree[next]--;
                    if (indegree[next] == 1) {
                        q.offer(next);
                    }
                }
            }
        }
        return lst;
    }
```

[286. Walls and Gates](https://leetcode.com/problems/walls-and-gates/description/)

dfs, faster than bfs, when encounter a 0, start searching, if we found a grid with less distance stop.

O(kmn) k is the number of gates

```java
    public void wallsAndGates(int[][] rooms) {
        if (rooms == null || rooms.length == 0 || rooms[0].length == 0) {
            return;
        }
        for (int i = 0; i < rooms.length; i++) {
            for (int j = 0; j < rooms[0].length; j++) {
                if (rooms[i][j] == 0) {
                	// bfs(rooms, i, j);
                    dfs(rooms, i, j, 0);
                }                
            }
        }
    }
    
    public void dfs(int[][] rooms, int i, int j, int step) {
        if (i < 0 || j < 0 || i >= rooms.length || j >= rooms[0].length || rooms[i][j] < step) {
            return;
        }
        if (step < rooms[i][j]) {
            rooms[i][j] = step;
        }
        dfs(rooms, i + 1, j, step + 1);
        dfs(rooms, i, j + 1, step + 1);
        dfs(rooms, i - 1, j, step + 1);
        dfs(rooms, i, j - 1, step + 1);
    }

    public void bfs(int[][] rooms, int i, int j) {
        Queue<Integer> rows = new LinkedList<>();
        Queue<Integer> cols = new LinkedList<>();
        rows.offer(i);
        cols.offer(j);
        while (!rows.isEmpty()) {
            int size = rows.size();
            for (int k = 0; k < size; k++) {
                int curRow = rows.poll();
                int curCol = cols.poll();
                for (int m = 0; m < 4; m++) {
                    int newRow = curRow + dy[m];
                    int newCol = curCol + dx[m];
                    if (newRow >= 0 && newCol >= 0 && newRow < rooms.length && newCol < rooms[0].length && rooms[newRow][newCol] > rooms[curRow][curCol] + 1) {
                        rooms[newRow][newCol] = rooms[curRow][curCol] + 1;
                        rows.offer(newRow);
                        cols.offer(newCol);
                    }
                }
            }
        }
    }
```

[332. Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/description/)

DFS with priorityqueue

```java
    public List<String> findItinerary(String[][] tickets) {
        Map<String, PriorityQueue<String>> map = new HashMap<>();
        for (String[] ticket : tickets) {
            if (!map.containsKey(ticket[0])) {
                map.put(ticket[0], new PriorityQueue<String>());
            }
            map.get(ticket[0]).offer(ticket[1]);
        } 
        LinkedList<String> res = new LinkedList<>();
        dfs(res, map, "JFK");
        return res;
    }
    
    public void dfs(LinkedList<String> res, Map<String, PriorityQueue<String>> map, String cur) {
        while(map.containsKey(cur) && !map.get(cur).isEmpty()) {
            dfs(res, map, map.get(cur).poll());
        }
        res.offerFirst(cur);
    }
```

[133. Clone Graph](https://leetcode.com/problems/clone-graph/description/)

BFS with hashmap and queue.

```java
    public UndirectedGraphNode cloneGraph(UndirectedGraphNode node) {
        if (node == null) {
            return null;
        }
        Map<UndirectedGraphNode, UndirectedGraphNode> map = new HashMap<>();
        Queue<UndirectedGraphNode> q = new LinkedList<>();
        q.offer(node);
        map.put(node, new UndirectedGraphNode(node.label));
        while (!q.isEmpty()) {
            UndirectedGraphNode cur = q.poll();            
            for (UndirectedGraphNode next : cur.neighbors) {
                if (!map.containsKey(next)) {
                    q.offer(next);
                    map.put(next, new UndirectedGraphNode(next.label));
                }
                map.get(cur).neighbors.add(map.get(next));
            }
        }
        return map.get(node);
    }
```

DFS with hashmap

```java
    public UndirectedGraphNode cloneGraph(UndirectedGraphNode node) {
        if (node == null) {
            return null;
        }
        Map<UndirectedGraphNode, UndirectedGraphNode> map = new HashMap<>();
        return clone(map, node);
    }
    
    public UndirectedGraphNode clone(Map<UndirectedGraphNode, UndirectedGraphNode> map, UndirectedGraphNode node) {
        if (map.containsKey(node)) {
            return map.get(node);
        }
        map.put(node, new UndirectedGraphNode(node.label));
        for (UndirectedGraphNode next : node.neighbors) {
            map.get(node).neighbors.add(clone(map, next));
        }
        return map.get(node);
    }
```

[785. Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/description/)

dfs or bfs, color, 1, 0, -1. Check whether next neighbor has the same color with current one.

```java
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] color = new int[n];
        for (int i = 0; i < n; i++) {
            if (color[i] == 0 && !setColor(color, graph, i, 1)) {
                return false;
            }
        }
        return true;
    }
    
    // dfs
    public boolean setColor(int[] color, int[][] graph, int node, int col) {
        color[node] = col;
        for (int next : graph[node]) {
            if (color[next] == 0) {
                if (!setColor(color, graph, next, -col)) {
                    return false;
                }
            }
            else if (color[next] == col) {
                return false;
            }
        }
        return true;
    }

    // bfs
    public boolean setColor(int[] color, int[][] graph, int node, int col) {
        Queue<Integer> q = new LinkedList<>();
        q.offer(node);
        color[node] = col;
        col = -col;
        while(!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int cur = q.poll();
                for (int next : graph[cur]) {
                    if (color[next] != 0 && color[next] != col) {
                        return false;
                    }
                    else if (color[next] == 0) {
                        color[next] = col;
                        q.offer(next);
                    }
                }                
            }
            col = -col;
        }
        return true;
    }
```

[743. Network Delay Time](https://leetcode.com/problems/network-delay-time/description/)

Dijkstra with Priority Queue. O(E + VlogV).

```java
    public int networkDelayTime(int[][] times, int N, int K) {
        if(times == null || times.length == 0){
            return -1;
        }
        Map<Integer, Integer> map = new HashMap<>();
        Map<Integer, Map<Integer, Integer>> edges = new HashMap<>();
        for (int[] time : times) {
            edges.putIfAbsent(time[0], new HashMap<Integer, Integer>());
            Integer t = edges.get(time[0]).get(time[1]);
            if (t == null || t > time[2]) {
                edges.get(time[0]).put(time[1], time[2]);
            }
        }
        PriorityQueue<int[]> pq = new PriorityQueue<>((i1, i2) -> {return i1[1] - i2[1];});
        pq.offer(new int[]{K, 0});
        map.put(K, 0);
        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            if (map.containsKey(cur[0]) && map.get(cur[0]) < cur[1]) {
                continue;
            }
            if (edges.get(cur[0]) == null) {
                continue;
            }
            Map<Integer, Integer> edge = edges.get(cur[0]);
            for (int next : edge.keySet()) {
                int dist = cur[1] + edge.get(next);
                if (map.containsKey(next) && map.get(next) <= dist) {
                    continue;
                }
                map.put(next, dist);
                pq.offer(new int[]{next, dist});
            }
        }
        if (map.size() < N) {
            return -1;
        }
        int max = 0;
        for (int i : map.keySet()) {
            max = Math.max(map.get(i), max);
        }
        return max;
    }
```

Bellman-Ford

O(VE)

```java
    public int networkDelayTime(int[][] times, int N, int K) {
        int[] dist = new int[N + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[K] = 0;
        for (int i = 0; i < N; i++) {
            for (int[] time : times) {
                int s = time[0], d = time[1], t = time[2];
                if (dist[s] != Integer.MAX_VALUE) {
                    dist[d] = Math.min(dist[d], dist[s] + t);
                }               
            }
        }
        int max = 0;
        for (int i = 1; i <= N; i++) {
            if (dist[i] == Integer.MAX_VALUE) {
                return -1;
            }
            max = Math.max(dist[i], max);
        }
        return max;
    }
```