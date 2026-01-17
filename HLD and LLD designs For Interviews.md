# Rate Limiter

```
from collections import deque
import time

'''
Design a Rate Limiter

HLD

Client -> Server
            |
          Redis

1) Sliding Window (Log)
2) Token Bucket
3) Leaky Bucket
'''


# Sliding Window Algorithm (Log)


class SlidingWindowLogRateLimiter():
    def __init__(self,time_frame,requests_allowed):
        if time_frame == 0 or requests_allowed == 0:
            raise Exception("Time Frame or Request Allowed can't be zero.")
        self.window = {}
        self.time_frame = time_frame
        self.requests_allowed = requests_allowed
        return

    def request(self,user_id):
        is_request_allowed = False
        current_time = time.time()
        if user_id in self.window:
            while len(self.window[user_id]) > 0:
                if current_time-self.window[user_id][0] > self.time_frame:
                    self.window[user_id].popleft()
                else:
                    break

            if len(self.window[user_id]) >= self.requests_allowed:
                pass
            else:
                self.window[user_id].append(current_time)
                is_request_allowed = True

        else:
            self.window[user_id] = deque([current_time])
            is_request_allowed = True

        return is_request_allowed


# Token Bucket Algorithm

class TokenBucketRateLimiter:
    def __init__(self, time_window, new_tokens, limit):
        if time_window <= 0 or new_tokens <= 0 or limit <= 0:
            raise ValueError("Invalid parameters")

        self.bucket_cache = {}
        self.time_window = time_window
        self.new_tokens = new_tokens
        self.limit = limit

    def request(self, user_id):
        now = time.time()

        if user_id not in self.bucket_cache:
            self.bucket_cache[user_id] = (now, float(self.limit - 1))
            return True

        last_time, tokens = self.bucket_cache[user_id]

        diff = (now - last_time) / self.time_window
        tokens = min(self.limit, tokens + diff * self.new_tokens)

        if tokens < 1:
            self.bucket_cache[user_id] = (now, tokens)
            return False

        self.bucket_cache[user_id] = (now, tokens - 1)
        return True

# The following algorithm is not user based, it is used for traffic shaping

# Leaky Bucket Algorithm

class LeakyBucketRateLimiter:
    def __init__(self, capacity, leak_rate):
        """
        capacity  : max requests bucket can hold
        leak_rate : requests per second
        """
        self.capacity = capacity
        self.leak_rate = leak_rate
        self.queue = deque()
        self.last_leak_time = time.time()

    def _leak(self):
        now = time.time()
        elapsed = now - self.last_leak_time
        leaked = int(elapsed * self.leak_rate)

        for _ in range(min(leaked, len(self.queue))):
            self.queue.popleft()

        if leaked > 0:
            self.last_leak_time = now

    def request(self):
        self._leak()

        if len(self.queue) >= self.capacity:
            return False  # bucket overflow → reject

        self.queue.append(time.time())
        return True
```
