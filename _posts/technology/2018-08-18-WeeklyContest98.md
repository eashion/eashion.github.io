# Weekly Contest 98

---
layout: post
title: Weekly Contest 96
category: leetcode
tags: leetcoe, weekly contest
keywords: leetcode, weekly contest
---

中间去休斯顿玩错过了一次，重新接上。这次比赛A了三道题目，前三道还算比较顺利，没遇到太大的问题，第四题不会做。<br>
**888. Fair Candy Swap**
<p>找到两个数组中的相差固定的两个数，时间复杂度应该是O(nlgn)。可以排序，然后二分查找。也可以用map。</p>

```c++
class Solution {
public:
    vector<int> fairCandySwap(vector<int>& A, vector<int>& B) {
        // A = {1,5,2}, B = {2,4};
        sort(A.begin(), A.end());
        sort(B.begin(), B.end());
        vector<int> res;
        int s1 = 0;
        int s2 = 0;
        for(int i = 0; i < A.size(); i++){
            s1 += A[i];
        }
        for(int i = 0; i < B.size(); i++){
            s2 += B[i];
        }
        int add = (s2-s1)/2;
        // cout<<add<<endl;
        for(int i = 0; i < A.size(); i++){
            int tmp = findB(A[i]+add, B);
            if (tmp != -1) {
                res.push_back(A[i]);
                res.push_back(B[tmp]);
                return res;
            }
        }
        return res;
    }
private:
    int findB(int num, vector<int> B){
        int left = 0;
        int right = B.size()-1;
        while(left < right){
            int mid = left + (right-left)/2;
            if (B[mid] == num) {
                return mid;
            }
            if (B[mid] > num) {
                right = mid-1;
            } else {
                left = mid+1;
            }
        }
        return B[left]==num ? left:-1;
    }
};
```

**890. Find and Replace Pattern**
<p>判断两个字符串的pattern，注意每次的匹配都可以变换，但是要保证不会存在两个字母对应一个的情况。</p>

```c++
class Solution {
public:
    vector<string> findAndReplacePattern(vector<string>& words, string pattern) {
        vector<string> res;
        int len = words.size();
        for(int i = 0; i < len; i++){
            if (checkPattern(words[i], pattern) == true) {
                res.push_back(words[i]);
            }
        }
        return res;
    }
private:
    bool checkPattern(string base, string pattern){
        if (base.length() != pattern.length()) {
            return false;
        }
        unordered_map<char, char> mm;
        unordered_map<char, int> tag;
        for(int i = 0; i < base.size(); i++){
            if (mm.find(base[i]) == mm.end()) {
                if (tag.find(pattern[i]) == tag.end()) {
                    mm[base[i]] = pattern[i];
                    tag[pattern[i]] = 1;
                } else {
                    return false;
                }
                
            } else if (mm[base[i]] != pattern[i]){
                return false;
            }
        }
        return true;
    }
};
```

**889. Construct Binary Tree from Preorder and Postorder Traversal**
<p>根据后序和前序构造二叉树，这个之前似乎没有遇到过，但是也比较好想。就按照自己的构造逻辑去把代码写出来就可以了。注意终止条件。</p>

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* constructFromPrePost(vector<int>& pre, vector<int>& post) {
        int len = pre.size();
        return buildTree(0, len-1, 0, len-1, pre, post);
    }
private:
    TreeNode* buildTree(int l1, int r1, int l2, int r2, vector<int> pre, vector<int> post){
        // cout<<"enter"<<l1<<" "<<r1<<" "<<l2<<" "<<r2<<endl;
        if (l1 > r1) {
            return NULL;
        }
        if (l1 == r1) {
            TreeNode* node = new TreeNode(pre[l1]);
            return node;
        }
        TreeNode* node = new TreeNode(pre[l1]);
        l1++;
        r2--;
        int pos = findPos(post, l2, r2, pre[l1]);
        // cout<<"pos:"<<pos<<endl;
        int nxt = l1+pos-l2;
        node->left = buildTree(l1, nxt, l2, pos, pre, post);
        node->right = buildTree(nxt+1, r1, pos+1, r2, pre, post);
        return node;
    }
    int findPos(vector<int> post, int left, int right, int num){
        for(int i = left; i <= right; i++){
            if (post[i] == num) {
                return i;
            }
        }
        return -1;
    }
};
```
**891. Sum of Subsequence Widths**

<p>这道题目最开始注意到的就是数据量有10^4,最大容忍时间复杂度是O(nlgn)。首先把数组sort，构造有序数组。我的思路是dp，从上一个状态，所有的subsequence，加一个数，再计算下一个sequence。每次计算A[i-1]到A[i]的增量。同时要知道前一个状态一共计算了多少个subsequence。可以这么理解，以前的所有sequence把最大的数替换成A[i]，然后还要计算一次所有sequence再加一个数A[i]。这种思路应该是能搞定的。转换方程没搞定，没写出来。</p>
<p>比赛结束后，参考大牛的思路。针对每个数，加上作为最大值的次数，减去作为最小值的次数。太巧妙地方法了，根本没想到从这个大的角度思考问题。</p>

```c++
class Solution {
private:
    int MOD = 1e9 + 7;
public:
    int sumSubseqWidths(vector<int>& A) {
        // A = {1,2,3,4,5};
        // A = {5,69,89,92,31,16,25,45,63,40,16,56,24,40,75,82,40,12,50,62,92,44,67,38,92,22,91,24,26,21,100,42,23,56,64,43,95,76,84,79,89,4,16,94,16,77,92,9,30,13};
        sort(A.begin(), A.end());
        long res = 0;
        int len = A.size();
        vector<long> cnt(len, 0);
        cnt[0] = 1;
        for(int i = 1; i < len; i++){
            cnt[i] = cnt[i-1]*2 % MOD;
            // cout<<cnt[i]<<endl;
        }
        for(int i = 0; i < len; i++){
            res = (res + A[i]*cnt[i] - A[i]*cnt[len-1-i]) % MOD;
        }
        return (res+MOD) % MOD;
    }
};
```

