359. Logger Rate Limiter

HashMap check timestamp. O(1)

```java
class Logger {
    
    Map<String, Integer> map = new HashMap<>();

    /** Initialize your data structure here. */
    public Logger() {
        
    }
    
    /** Returns true if the message should be printed in the given timestamp, otherwise returns false.
        If this method returns false, the message will not be printed.
        The timestamp is in seconds granularity. */
    public boolean shouldPrintMessage(int timestamp, String message) {
        if (!map.containsKey(message)) {
            map.put(message, timestamp);
            return true;
        }
        else {
            if (map.get(message) <= timestamp - 10) {
                map.put(message, timestamp);
                return true;
            }
            else {
                return false;
            }
        }
    }
}
```

362. Design Hit Counter

Deque solution, O(1).

```java
class HitCounter {
    
    Deque<Integer> dq;

    /** Initialize your data structure here. */
    public HitCounter() {
        dq = new LinkedList<>();
    }
    
    /** Record a hit.
        @param timestamp - The current timestamp (in seconds granularity). */
    public void hit(int timestamp) {
        while (dq.size() > 0 && dq.peekFirst() <= timestamp - 300) {
            dq.pollFirst();
        }
        dq.offerLast(timestamp);
    }
    
    /** Return the number of hits in the past 5 minutes.
        @param timestamp - The current timestamp (in seconds granularity). */
    public int getHits(int timestamp) {
        while (dq.size() > 0 && dq.peekFirst() <= timestamp - 300) {
            dq.pollFirst();
        }
        return dq.size();
    }
}
```
348. Design Tic-Tac-Toe

Row array and col array to save space. Two diagnal lines.
1 and -1 to indicate two players to save space.

```java
class TicTacToe {
    
    int leftDiag;
    int rightDiag;
    int[] rows;
    int[] cols;
    int n;

    /** Initialize your data structure here. */
    public TicTacToe(int n) {
        leftDiag = 0;
        rightDiag = 0;
        rows = new int[n];
        cols = new int[n];
        this.n = n;
    }
    
    /** Player {player} makes a move at ({row}, {col}).
        @param row The row of the board.
        @param col The column of the board.
        @param player The player, can be either 1 or 2.
        @return The current winning condition, can be either:
                0: No one wins.
                1: Player 1 wins.
                2: Player 2 wins. */
    public int move(int row, int col, int player) {
        int m = player == 1 ? 1 : -1;
        rows[row] += m;
        cols[col] += m;
        if (row == col) {
            leftDiag += m;
        }
        if (row + col == n - 1) {
            rightDiag += m;
        }
        if (Math.abs(rows[row]) == n || Math.abs(cols[col]) == n || Math.abs(leftDiag) == n || Math.abs(rightDiag) == n) {
            return player;
        }
        return 0;
    }
}
```

array solution :

355. Design Twitter

User class, Tweet class. Use a minimum heap to keep most recent 10 tweets.
Or we can use tweet as node to keep a next instead of keeping a list in user.

```java
class Twitter {
    public static int timestamp = 0;
    class Tweet {
        int data;
        int time;
        public Tweet(int data) {
            this.data = data;
            time = timestamp++;
        }
    }
    
    class User {
        List<Tweet> tweets;
        Set<User> follows;
        int uid;
        public User(int id) {
            uid = id;
            tweets = new ArrayList<>();
            follows = new HashSet<>();
            follows.add(this);
        }
    }
    
    Map<Integer, User> userMap;

    /** Initialize your data structure here. */
    public Twitter() {
        userMap = new HashMap<>();
    }
    
    /** Compose a new tweet. */
    public void postTweet(int userId, int tweetId) {
        if (!userMap.containsKey(userId)) {
            userMap.put(userId, new User(userId));
        }
        User user = userMap.get(userId);
        user.tweets.add(new Tweet(tweetId));
    }
    
    /** Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent. */
    public List<Integer> getNewsFeed(int userId) {
        if (!userMap.containsKey(userId)) {
            return new ArrayList<Integer>();
        }
        PriorityQueue<Tweet> pq = new PriorityQueue<>(1, new Comparator<Tweet>(){
            @Override
            public int compare(Tweet t1, Tweet t2) {
                return t1.time - t2.time;
            }
        });
        for (User u : userMap.get(userId).follows) {
            for (int i = 0; i < 10; i++) {
                int ind = u.tweets.size() - 1 - i;
                if (ind < 0) {
                    break;
                }
                Tweet t = u.tweets.get(ind);
                if (pq.size() < 10) {
                    pq.offer(t);
                }
                else {
                    if (t.time < pq.peek().time) {
                        continue;
                    }
                    else {
                        pq.poll();
                        pq.offer(t);
                    }
                }
            } 
        }
        LinkedList<Integer> ll = new LinkedList<>();
        while (pq.size() > 0) {
            ll.addFirst(pq.poll().data);
        }
        return ll;
    }
    
    /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
    public void follow(int followerId, int followeeId) {
        if (!userMap.containsKey(followerId)) {
            userMap.put(followerId, new User(followerId));
        }
        if (!userMap.containsKey(followeeId)) {
            userMap.put(followeeId, new User(followeeId));
        }
        userMap.get(followerId).follows.add(userMap.get(followeeId));
    }
    
    /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
    public void unfollow(int followerId, int followeeId) {
        if (!userMap.containsKey(followerId) || followerId == followeeId) {
            return;
        }
        userMap.get(followerId).follows.remove(userMap.get(followeeId));
    }
}
```

353. Design Snake Game

Check eat food first to save time. Use integer in set.

```java
class SnakeGame {
    
    Deque<int[]> dq = new LinkedList<>();
    Set<Integer> body = new HashSet<>();
    int width;
    int height;
    int[][] food;
    int foodIdx;

    /** Initialize your data structure here.
        @param width - screen width
        @param height - screen height 
        @param food - A list of food positions
        E.g food = [[1,1], [1,0]] means the first food is positioned at [1,1], the second is at [1,0]. */
    public SnakeGame(int width, int height, int[][] food) {
        this.food = food;
        this.width = width;
        this.height = height;
        int[] start = new int[]{0, 0};
        body.add(start[0] * width + start[1]);
        dq.offerLast(start);
        foodIdx = 0;
    }
    
    /** Moves the snake.
        @param direction - 'U' = Up, 'L' = Left, 'R' = Right, 'D' = Down 
        @return The game's score after the move. Return -1 if game over. 
        Game over when snake crosses the screen boundary or bites its body. */
    public int move(String direction) {
        int[] cur = dq.peekFirst();
        int[] next = new int[2];
        next[0] = cur[0];
        next[1] = cur[1];
        if (direction.equals("U")) {
            next[0]--;
        }
        else if (direction.equals("L")) {
            next[1]--;
        }
        else if (direction.equals("R")) {
            next[1]++;
        }
        else if (direction.equals("D")) {
            next[0]++;
        }
        if (foodIdx < food.length && next[0] == food[foodIdx][0] && next[1] == food[foodIdx][1]) {
            foodIdx++;
            dq.offerFirst(next); 
            body.add(next[0] * width + next[1]);
            return foodIdx;
        }
        if (!inBound(next)) {
            return -1;
        }        
        int[] tail = dq.peekLast();        
        if (tail[0] == next[0] && tail[1] == next[1]) {

        }
        else if (body.contains(next[0] * width + next[1])) {
            return -1;
        }        
        dq.offerFirst(next);        
        dq.pollLast();
        body.remove(tail[0] * width + tail[1]);
        body.add(next[0] * width + next[1]); 
        return foodIdx;
    }
    
    public boolean inBound(int[] pos) {
        return pos[0] >= 0 && pos[0] < height && pos[1] >= 0 && pos[1] < width;
    }
}
```

379. Design Phone Directory

Queue + Set

```java
class PhoneDirectory {
    
    Deque<Integer> dq = new LinkedList<>();
    Set<Integer> set = new HashSet<>();

    /** Initialize your data structure here
        @param maxNumbers - The maximum numbers that can be stored in the phone directory. */
    public PhoneDirectory(int maxNumbers) {
        for (int i = 0; i < maxNumbers; i++) {
            dq.offerLast(i);
        }
    }
    
    /** Provide a number which is not assigned to anyone.
        @return - Return an available number. Return -1 if none is available. */
    public int get() {
        if (dq.size() == 0) {
            return -1;
        }
        int res = dq.pollFirst();
        set.add(res);
        return res;
    }
    
    /** Check if a number is available or not. */
    public boolean check(int number) {
        return !set.contains(number);
    }
    
    /** Recycle or release a number. */
    public void release(int number) {
        if (!set.contains(number)) {
            return;
        }
        set.remove(number);
        dq.offerLast(number);
    }
}
```