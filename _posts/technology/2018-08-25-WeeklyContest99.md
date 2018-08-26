---
layout: post
title: Weekly Contest 99
category: leetcode
tags: leetcode
keywords: leetcode, weekly contest
---
<p>这次周练结束前做出来三道题，排名有点倒退，主要是第二题简单题想复杂了，TLE了两次。</p>

**892. Surface Area of 3D Shapes**
<p>这道题以前见过类似的，三视图。这次要复杂一点，第一遍按照三视图去做，忽视了中间空心的情况。还是至少要把样例看完的哇。之后按照每个格子去判断能露出来的面积，和相加。</p>

```c++
class Solution {
public:
    int surfaceArea(vector<vector<int>>& grid) {
        // grid = {{1,2},{3,4}};
        int res = 0;
        int N = grid.size();
        if (N == 0) {
            return res;
        }
        int M = grid[0].size();
        for(int i = 0; i < N; i++){
            for(int j = 0; j < M; j++){
                res += check(i, j, grid, N, M);
            }
        }
        return res;
    }
private:
    int check(int x, int y, vector<vector<int>> grid, int N, int M){
        if (grid[x][y] == 0) {
            return 0;
        }
        int tmp = 2;
        int ux = x-1;
        int dx = x+1;
        int ly = y-1;
        int ry = y+1;
        if (ux>=0 && grid[ux][y]<grid[x][y]) {
            tmp += grid[x][y] - grid[ux][y];
        } else if (ux == -1) {
            tmp += grid[x][y];
        }
        if (dx<N && grid[dx][y]<grid[x][y]) {
            tmp += grid[x][y] - grid[dx][y];
        } else if (dx == N) {
            tmp += grid[x][y];
        }
        if (ly>=0 && grid[x][ly]<grid[x][y]) {
            tmp += grid[x][y] - grid[x][ly];
        } else if (ly == -1) {
            tmp += grid[x][y];
        }
        if (ry<M && grid[x][ry]<grid[x][y]) {
            tmp += grid[x][y] - grid[x][ry];
        } else if (ry == M) {
            tmp += grid[x][y];
        }
        return tmp;
    }
};
```

**893. Groups of Special-Equivalent Strings**
<p>题目读起来烦人，看样例能很清楚。类似于并查集，但是似乎并不需要想这么复杂。直接按照奇偶挑选出来然后排序，对比字符串就可以。不过手写了一个并查集，就当做练手</p>

```c++
class Solution {
public:
    int numSpecialEquivGroups(vector<string>& A) {
        // A = {"abcd","cdab","adcb","cbad"};
        int N = A.size();
        int res = 0;
        if (N == 0) {
            return res;
        }
        vector<int> father(N, 0);
        for(int i = 0; i < N; i++){
            father[i] = i;
        }
        for(int i = 0; i < N; i++){
            for(int j = 0; j < i; j++){
                if (findF(i,father) == findF(j,father)) {
                    continue;
                }
                if (checkMatch(A[i], A[j])) {
                    Union(i, j, father);
                    break;
                }
            }
        }
        for(int i = 0; i < N; i++){
            father[i] = findF(i, father);
        }
        sort(father.begin(), father.end());
        int cnt = 0;
        int flag = -1;
        for(auto f : father){
            if (f != flag) {
                cnt++;
                flag = f;
            }
        }
        return cnt;
    }
private:
    bool checkMatch(string left, string right){
        if (left == right) {
            return true;
        }
        if (left.length() != right.length()) {
            return false;
        }
        vector<int> lmm(26, 0);
        vector<int> rmm(26, 0);
        vector<int> lbb(26, 0);
        vector<int> rbb(26, 0);
        int len = left.length();
        for(int i = 0; i < len; i++){
            if (i%2 == 0) {
                lmm[left[i]-'a']++;
                rmm[right[i]-'a']++;
            } else {
                lbb[left[i]-'a']++;
                rbb[right[i]-'a']++;
            }
        }
        for(int i = 0; i < 26; i++){
            if (lmm[i] != rmm[i]) {
                return false;
            }
            if (lbb[i] != rbb[i]) {
                return false;
            }
        }
        return true;
    }
    void Union(int x, int y, vector<int>& father){
        int fx = findF(x, father);
        int fy = findF(y, father);
        if (fx != fy) {
            father[fy] = fx;
        }
        return ;
    }
    int findF(int x, vector<int>& father){
        if (x == father[x]) {
            return x;
        }
        return father[x] = findF(father[x], father);
    }
};
```

**894. All Possible Full Binary Trees**
<p>这道题目的思路也比较直接，左右都必须是奇数，才能满足条件。是一个递归的过程。要理清楚构造不同树的逻辑，不能公用的空间要明确。</p>

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
private:
    map<int, vector<TreeNode*>> mm;
public:
    vector<TreeNode*> allPossibleFBT(int N) {
        vector<TreeNode*> lis;
        if (N%2 == 0) {
            return lis;
        };
        lis = buildTree(N);
        // cout<<lis.size()<<endl;
        return lis;
    }
private:
    vector<TreeNode*> buildTree(int left){
        // cout<<"build:"<<left<<endl;
        if (mm.find(left) != mm.end()) {
            return mm[left];
        }
        if (left == 1) {
            vector<TreeNode*> lis;
            lis.push_back(new TreeNode(0));
            return lis;
        }
        left--;
        vector<TreeNode*> lis;
        for(int i = 1; i <= left-1; i++){
            vector<TreeNode*> l = buildTree(i);
            vector<TreeNode*> r = buildTree(left-i);
            buildAll(lis, l, r);
        }
        mm[left+1] = lis;
        return lis;
    }
    void buildAll(vector<TreeNode*>& lis, vector<TreeNode*> l, vector<TreeNode*> r){
        int lsize = l.size();
        int rsize = r.size();
        for(int i = 0; i < lsize; i++){
            for(int j = 0; j < rsize; j++){
                TreeNode* head = new TreeNode(0);
                head->left = l[i];
                head->right = r[j];
                lis.push_back(head);
            }
        }
        return ;
    }
};
```

**895. Maximum Frequency Stack**
<p>这道题目想了一会儿，做了三道以后就有些懈怠了。构造数据结构，满足每次都删除最常出现的一个。应该有比较多的实现方法，使用双链表是为了能比较高效的删除。在数据结构这块还要再看看。</p>

```c++
class FreqStack {
struct Node{
    int x;
    Node* pre;
    Node* nxt;
    Node(){}
    Node(int x){
        this->x = x;
        this->pre = NULL;
        this->nxt = NULL;
    }
};
private:
    map<int, int> freq;
    map<int, int> most;
    Node* topHead;
    Node* tail;
    int large;
public:
    FreqStack() {
        topHead = new Node(-1);
        tail = topHead;
        large = 0;
    }
    
    void push(int x) {
        Node* cur = new Node(x);
        tail->nxt = cur;
        cur->pre = tail;
        tail = cur;
        if (freq[x] != 0) {
            most[freq[x]]--;
        }
        freq[x]++;
        most[freq[x]]++;
        large = max(large, freq[x]);
    }
    
    int pop() {
        Node* p = tail;
        Node* nxt = NULL;
        while(freq[p->x] != large){
            nxt = p;
            p = p->pre;
        }
        int x = p->x;
        most[large]--;
        freq[x]--;
        most[freq[x]]++;
        if (most[large] == 0) {
            large = findLarge();
        }
        p = p->pre;
        p->nxt = nxt;
        if (nxt == NULL) {
            tail = p;
        } else {
            nxt->pre = p;
        }
        // cout<<"pop: "<<x<<"large:"<<large<<endl;
        // cout<<"tail: "<<tail->x<<endl;
        // printSta();
        return x;
    }
private:
    int findLarge(){
        int tmp = 0;
        for(auto iter : most){
            if (iter.second != 0) {
                tmp = max(tmp, iter.first);
            }
        }
        return tmp;
    }
};

/**
 * Your FreqStack object will be instantiated and called as such:
 * FreqStack obj = new FreqStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 */
```
