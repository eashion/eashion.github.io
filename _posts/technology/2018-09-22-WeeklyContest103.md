---
layout: post
title: Weekly Contest 103
category: leetcode
tags: leetcode
keywords: leetcode, weekly contest
---
<p>结束前做出来三道题,竟然还进到了前两百。</p>

**908. Smallest Range I**<br>
<p>1很简单，只需要找到最大最小值，尽可能靠近，中间的其他都可以调整到中间。六分钟A掉，排名第一的花一分半A掉手速是真快。。</p>

```c++
class Solution {
public:
    int smallestRangeI(vector<int>& A, int K) {
        // A = {1,3,6};
        // K = 3;
        int res = 0;
        int len = A.size();
        if (len < 2) {
            return res;
        }
        int Min = INT_MAX;
        int Max = INT_MIN;
        for(int i = 0; i < len; i++){
            Min = min(A[i], Min);
            Max = max(A[i], Max);
        }
        res = max(0, Max-Min-2*K);
        return res;
    }
};
```
**910. Smallest Range II**<br>
<p>第二题先选择了II来做，但是看了一会儿觉得没思路，想去看第三题，结果发现第三题题目很复杂，又拐回来做这道题。</p>
<p>首先观察题目输入达到十的四次方，并且觉得O(N)遍历不能解决，选择先进行排序。有想过是不是需要用一个数据结构去维护当前的最大最小值，后面的选择似乎对前面的选择还会产生影响，并不能维护下来。通过画图，发现其实排序过后队列中只可能有一个转折点，即从+K到-K的转折点，而这个转折点是关键。有想过能不能贪心，但是这个拐点并没有贪心的特点。后面想到其实遍历找就可以，这个只需要O(n)。因为枚举转折点，此刻的最大最小值都可以根据性质O(1)算出来。</p>

```c++
class Solution {
public:
    int smallestRangeII(vector<int>& A, int K) {
        // A = {1,3,6}, K = 3;
        // A = {0, 10};
        // K = 2;
        // A = {2,2,7};
        // K = 1;
        int res = 0;
        int len = A.size();
        if (len < 2) {
            return res;
        }
        sort(A.begin(), A.end());
        res = A[len-1]-A[0];
        // cout<<"res"<<res<<endl;
        if (K == 0) {
            return res;
        }
        for(int i = 0; i < len-1; i++){
            int cur = max(A[i]+K, A[len-1]-K) - min(A[0]+K, A[i+1]-K);
            // cout<<cur<<endl;
            res = min(res, cur);
        }
        return res;
    }
};
```
**909. Snakes and Ladders**<br>
<p>这道题目刚开始愣是没看懂，题目绕来绕去的讲。第二遍跟着测试样例才搞懂了，发现就是一个简单的状态变换，BFS加记忆化解决。中间遇到一个问题，我把结构体放到map第二个位置一直报错，搞不懂为什么。是因为本身结构体没必要？换成了pair就没问题了。一遍A掉。</p>

```c++
class Solution {
struct Node{
    int x;
    int y;
    int c;
    Node(int x, int y){
        this->x = x;
        this->y = y;
    }
};
struct state{
    int val;
    int step;
    state(int val, int step){
        this->val = val;
        this->step = step;
    }
};
public:
    int snakesAndLadders(vector<vector<int>>& board) {
        int N = board.size();
        // cout<<N<<endl;
        unordered_map<int, pair<int,int>> mm;
        int px = N-1;
        int py = 0;
        int num = 1;
        int dir = 0;
        // cout<<"OK"<<endl;
        while(num <= N*N){
            // cout<<pos.x<<pos.y<<endl;
            mm[num] = make_pair(px,py);
            // cout<<num<<":"<<px<<"-"<<py<<endl;
            if (dir == 0) {
                py++;
                if (py == N) {
                    py--;
                    dir = 1;
                    px--;
                }
            } else {
                py--;
                if (py == -1) {
                    py++;
                    dir = 0;
                    px--;
                }
            }
            num++;
        }
        queue<state> que;
        que.push(state(1, 0));
        vector<vector<int>> ss(N, vector<int>(N, -1));
        while(!que.empty()){
            state cur = que.front();
            que.pop();
            if (cur.val == N*N) {
                return cur.step;
            }
            pair<int,int> fuck = mm[cur.val];
            if (ss[fuck.first][fuck.second] == -1) {
                ss[fuck.first][fuck.second] = cur.step;
            } else if (ss[fuck.first][fuck.second] <= cur.step) {
                continue;
            }
            for(int i = 1; i <= 6; i++){
                if (cur.val+i > N*N) {
                    break;
                }
                int nx = mm[cur.val+i].first;
                int ny = mm[cur.val+i].second;
                if (board[nx][ny] != -1) {
                    que.push(state(board[nx][ny], cur.step+1));
                } else {
                    que.push(state(cur.val+i, cur.step+1));
                }
            }
        }
        return -1;
    }
};
```
**911. Online Election**<br>
<p>这道题目看的时候只剩下十几分钟了，本来以为会是个硬题，没想到十几分钟还是把框架搭好了。思路也比较简单，q函数就是吓唬人的，其实根据给出的信息，已经能把根据时间线获胜的人找到。q存在的意义就是多一步二分查找。但是也是因为这个二分查找wa了一次。。。边界条件没判断好。总体来说还是比较简单的，很好想。</p>

```c++
class TopVotedCandidate {
private:
    int len;
    unordered_map<int, int> mm;
    vector<int> win;
    vector<int> timeline;
public:
    TopVotedCandidate(vector<int> persons, vector<int> times) {
        len = persons.size();
        timeline = times;
        int curwin = -1;
        for(int i = 0; i < len; i++){
            mm[persons[i]]++;
            if (curwin <= mm[persons[i]]) {
                curwin = mm[persons[i]];
                win.push_back(persons[i]);
            } else {
                win.push_back(win[i-1]);
            }
            // cout<<win[i]<<" ";
        }
        // cout<<endl;
    }
    
    int q(int t) {
        int pos = lower_bound(timeline.begin(), timeline.end(), t)-timeline.begin();
        if (pos==len || timeline[pos]!=t) {
            pos--;
        }
        // cout<<pos<<endl;
        return win[pos];
    }
};

/**
 * Your TopVotedCandidate object will be instantiated and called as such:
 * TopVotedCandidate obj = new TopVotedCandidate(persons, times);
 * int param_1 = obj.q(t);
 */
```
Fight On!