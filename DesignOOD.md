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

array solution :

355. Design Twitter

User class, Tweet class. Use a minimum heap to keep most recent 10 tweets.
Or we can use tweet as node to keep a next instead of keep a list in user.

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