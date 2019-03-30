---
layout: post
title: 1022. Smallest Integer Divisible by K
category: leetcode
tags: leetcode
keywords: leetcode
---

**1022. Smallest Integer Divisible by K**
<p>There are some math proof behind this question.<br>
If N exist, N <= K, otherwise K has a factor of 2 or 5.<br>
Prove by contradiction. N exist, and N is greater than K. For N=1 to N=K, we should only have (1 to K-1) K-1 different reminders. So there must have two number x and y, which have same reminders.<br>
Assume we have the following funtion f(N) = 111...111(length = N).<br>
According to above, we have f(x)%K = f(y)%K. (x > y).<br> Then we have f(x-y)*10^M%K = 0. Since f(x-y)%K != 0, we have 10^M%K = 0.
<br>
Then we can use brute force to solve this problem.
</p>

```c++
class Solution {
public:
    int smallestRepunitDivByK(int K) {
        if (K%2 == 0 || K%5==0) {
            return -1;
        }
        int r = 0;
        for(int n = 1; n <= K; n++){
            r = (r*10+1)%K;
            if (r == 0) {
                return n;
            }
        }
        return -1;
    }
};
```