---
layout: post
title: Weekly Contest 108 
category: leetcode
tags: leetcode
keywords: leetcode, weekly contest
---
<p>这次练习只做出来两道题目，虽然是1E+3M，还是做得不好，需要整理一下。</p>

**929. Unique Email Addresses**
<p>这道题目的思路还是比较简单的，字符串处理，用unordered_map或者set存储都可以。只是自己set用的太不熟悉，写的比较慢。</p>

```c++
class Solution {
public:
    int numUniqueEmails(vector<string>& emails) {
        // emails = {"aa.a+a.a@usc.edu"};
        int len = emails.size();
        int res = 0;
        if (len == 0) {
            return res;
        }
        //use set
        unordered_map<string, int> mm;
        for(int i = 0; i < len; i++){
            string cur = buildE(emails[i]);
            if (mm.find(cur) == mm.end()) {
                mm[cur] = 1;
                res++;
            }
        }
        return res;
    }
private:
    string buildE(string cur){
        string res = "";
        int len = cur.length();
        int pos = 0;
        int flag = 0;
        int ignal = 0;
        while(pos < len){
            if (flag == 1) {
                res += cur[pos];
                pos++;
                continue;
            }
            if (cur[pos] == '.') {
                pos++;
            } else if (cur[pos] == '@') {
                flag = 1;
                ignal = 0;
                res += cur[pos];
                pos++;
            } else if (cur[pos] == '+') {
                ignal = 1;
                pos++;
            } else {
                if (ignal == 0) {
                    res += cur[pos];
                }
                pos++;
            }
        }
        // cout<<cur<<" "<<res<<endl;
        return res;
    }
};
```


**930. Binary Subarrays With Sum**
<p>这道题目花费的时间最长，思路不明确，并且一直没有搞定。最开始以为是简单的用queue处理，但是后面发现如果前后都存在0存在组合的情况。接着想用左边0和右边0去排列组合求出，但是对于S=0的情况又比较难处理。所以卡了很久，最后在比赛结束后写了一个很尴尬的版本。还要特殊去考虑S=0的情况。这道题即便是没有好的思路，也应该尽快找到一个次优的解决方案，不应该卡这么久的。</p>
<p>最后参考别人的答案，使用map记录一个和的位置。当时我也考虑过这个解法，但是我想的是要二分找位置，感觉比较麻烦就放弃了。正确的思路是，用map保存前缀和的次数，对应的前缀和有多少个，剩余部分就有多少个。看了好几遍才能看懂。。。</p>

```c++
class Solution {
public:
    int numSubarraysWithSum(vector<int>& A, int S) {
        // A = {0,0,1,0,1,0};
        // A = {0,0,1,0,1};
        // A = {0,0,0,0,0};
        // S = 0;
        // A = {0,0,0,1};
        A = {1,0,1,0,1,0,1};
        int len = A.size();
        int res = 0;
        if (len == 0) {
            return res;
        }
        int pos = 0;
        unordered_map<int, int> mm;
        for(int i = 0; i < len; i++){
            mm[pos] += 1;
            pos += A[i];
            res += mm[pos-S];
        }
        return res;
    }
};
```


**931. Minimum Falling Path Sum**
<p>这道题目是简单dp。</p>

```c++
class Solution {
public:
    int minFallingPathSum(vector<vector<int>>& A) {
        int len = A.size();
        vector<vector<int>> mm(len, vector<int>(len, INT_MAX));
        for(int i = 0; i < len; i++){
            mm[len-1][i] = A[len-1][i];
        }
        for(int i = len-2; i >= 0; i--){
            for(int j = 0; j < len; j++){
                if (j == 0) {
                    mm[i][j] = min(mm[i+1][j], mm[i+1][j+1])+A[i][j];
                } else if (j == len-1) {
                    mm[i][j] = min(mm[i+1][j], mm[i+1][j-1])+A[i][j];
                } else {
                    mm[i][j] = min(min(mm[i+1][j], mm[i+1][j-1]), mm[i+1][j+1])+A[i][j];
                }
            }
        }
        int res = INT_MAX;
        for(int i = 0; i < len; i++){
            res = min(res, mm[0][i]);
        }
        return res;
    }
};
```

**932. Beautiful Array**
<p>一开始想要简单dfs求解，事实证明TLE。因为N并不算大，算是个次优解。参考答案，题目要求的数组可以根据性质拼凑出来，不需要暴力模拟。首先要考虑分治，将奇数和偶数分开。两个奇数的和中间不会出现偶数，两个偶数的和中间不会出现2倍的奇数。同时除以2可以看出。所以从最小的集合开始，每次分别搞定奇数和偶数就可以了。</p>
<p>https://leetcode.com/problems/beautiful-array/discuss/186679/C++JavaPython-Odd-+-Even-Pattern-O(N)</p>

```c++
    vector<int> beautifulArray(int N) {
        vector<int> res = {1};
        while (res.size() < N) {
            vector<int> tmp;
            for (int i : res) if (i * 2 - 1 <= N) tmp.push_back(i * 2 - 1);
            for (int i : res) if (i * 2 <= N) tmp.push_back(i * 2);
            res = tmp;
        }
        return res;
    }
```