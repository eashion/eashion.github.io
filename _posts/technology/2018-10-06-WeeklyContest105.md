---
layout: post
title: Weekly Contest 105
category: leetcode
tags: leetcode
keywords: leetcode, weekly contest
---
<p>这次周练结束前做出来三道题，速度还可以，还是第二题卡了一下，第四题没有做出来。</p>

**917. Reverse Only Letters**
<p>第一题还是比较简单的，思路能够直接构建出来。左右各一个指针，每次找到一个letter然后swap就行了。</p>

```c++
class Solution {
public:
    string reverseOnlyLetters(string S) {
        // S = "a-bC-dEf-ghIj";
        // S = "Test1ng-Leet=code-Q!";
        int len = S.length();
        int left = 0;
        int right = len-1;
        while(left < right){
            while(left<right && checkLetter(S[left])==false){
                left++;
            }
            while(left<right && checkLetter(S[right])==false){
                right--;
            }
            swap(S[left], S[right]);
            left++;
            right--;
        }
        return S;
    }
private:
    bool checkLetter(char a){
        int base = a-'a';
        if (base>=0 && base<26) {
            return true;
        }
        base = a-'A';
        if (base>=0 && base<26) {
            return true;
        }
        return false;
    }
};
```

**918. Maximum Sum Circular Subarray**
<p>第二题是循环线段和，可以选择队尾一部分和队头一部分。花了三十三分钟才做出来，而且思路感觉并不是很清晰。最终的思路是将这个题目分两种情况，一种是不考虑队尾和队头的组合，一种是队尾和队头的一个分别贪心。第一种情况是dp，第二种情况需要重新更新一下dp数组，虽然O(N)能做，但是觉得方法不够优美。</p>
<p>参考了别人的解法，分成两种情况是一致的。区别在于第二种情况的处理。在我的算法里，我左右各贪了一次最大值，然后左右相加。看到一个解法，用总数减去求得的最小和，剩下的部分就可以看作是连续的最大值。但是仍然要维护一个最大值变量，因为两种情况要考虑，minsum区间为空或者minsum空间是全部元素，此时需要用最大和去处理。</p>

```c++
class Solution {
public:
    int maxSubarraySumCircular(vector<int>& A) {
        // A = {1,-2,3,-2};
        // A= {3,-2,2,-3};
        // A = {5,-3,5};
        // A = {-2,2,-2,9};
        int len = A.size();
        if (len == 0) {
            return 0;
        }
        vector<int> opt(len, 0);
        vector<int> pre(len, 0);
        for(int i = 0; i < len; i++){
            opt[i] = A[i];
            pre[i] = A[i];
        }
        int res = opt[0];
        for(int i = 1; i < len; i++){
            if (opt[i-1] > 0) {
                opt[i] = A[i]+opt[i-1];
            }
            res = max(res, opt[i]);
        }
        // cout<<"opt:"<<" ";
        // for(int a : opt){
        //     cout<<a<<" ";
        // }
        // cout<<endl;
        int pos = len-1;
        if (opt[pos] <= 0) {
            return res;
        }
        for(int i = 1; i < len; i++){
            opt[i] = A[i]+opt[i-1];
        }
        // cout<<"opt:"<<" ";
        // for(int a : opt){
        //     cout<<a<<" ";
        // }
        // cout<<endl;
        for(int i = len-2; i >= 0; i--){
            pre[i] = A[i]+pre[i+1];
        }
        for(int i = len-2; i >= 0; i--){
            if (pre[i+1] > pre[i]) {
                pre[i] = pre[i+1];
            }
        }
        // cout<<"pre:";
        // for(int a : pre){
        //     cout<<a<<" ";
        // }
        // cout<<endl;
        for(int i = 0; i < len-1; i++){
            res = max(res, opt[i]+pre[i+1]);
        }
        return res;
    }
};
```

```c++
class Solution {
public:
    int maxSubarraySumCircular(vector<int>& A) {
        int total = 0, maxSum = -30000, curMax = 0, minSum = 30000, curMin = 0;
        for (int a : A) {
            curMax = max(curMax + a, a);
            maxSum = max(maxSum, a);
            curMin = min(curMin + a, a);
            minSum = min(minSum, curMin);
            total += a;
        }
        return maxSum > 0 ? max(maxSum, total - minSum) : maxSum;
    }
};
```

**919. Complete Binary Tree Inserter**
<p>这道题目很简单，二叉树的层次遍历。但是还是自己太暴力了，发现其实用数组存就可以，利用完全二叉树的性质去插入，O(1)。只是构建的时候要层次遍历一下，效率会高很多。</p>

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
class CBTInserter {
private:
    vector<TreeNode*> tree;
public:
    CBTInserter(TreeNode* root) {
        tree.push_back(root);
        for(int i = 0; i < tree.size(); i++){
            if (tree[i]->left != NULL) {
                tree.push_back(tree[i]->left);
            }
            if (tree[i]->right != NULL) {
                tree.push_back(tree[i]->right);
            }
        }
    }
    
    int insert(int v) {
        int N = tree.size();
        TreeNode* node = new TreeNode(v);
        tree.push_back(node);
        if(N%2 == 1){
            tree[(N-1)/2]->left = node;
        } else {
            tree[(N-1)/2]->right = node;
        }
        return tree[(N-1)/2]->val;
    }
    
    TreeNode* get_root() {
        return tree[0];
    }
};

/**
 * Your CBTInserter object will be instantiated and called as such:
 * CBTInserter obj = new CBTInserter(root);
 * int param_1 = obj.insert(v);
 * TreeNode* param_2 = obj.get_root();
 */
```

**920. Number of Music Playlists**
<p>这道题目是想了很久没思路的。最开始想的是，究竟按照位置去，还是按照插入去计算。按照位置算，不好保证所有数字都用到，按照插入的话不好控制k的信息。感觉自己思路有问题，首先要确定这个是什么类型的题目，如果确定了是dp，再找到子问题，应该会好像一些。感觉有点像放苹果的问题，苹果放不放某个盘子。在这道题目里就是，歌曲数和播放数目。二维dp，对于dp[N][L],子问题包含dp[N-1][L-1]和dp[N][L-1]。 dp[N][L]=dp[N-1][L-1]*N + dp[N][L-1]*(N-K)。一种是只用了前N-1种，选出这一种有N个选择；一种是只用了前L-1个盘子，最后一个盘子根据K的限制，只有N-K个选择。</p>
<p>这道题的收获就是每次还是要从题目类型开始考虑。</p>

```c++
class Solution {
private:
    vector<long> cnt;
    int Mod = 1e9+7;
public:
    int numMusicPlaylists(int N, int L, int K) {
        // N = 2, L = 3, K = 1;
        // N = 16, L = 16, K = 4;
        int res = 0;
        if (N==0 || L==0) {
            return 0;
        };
        vector<vector<long>> dp(N+1, vector<long>(L+1, 0));
        for(int i = K+1; i <= N; i++){
            for(int j = i; j <= L; j++){
                if (j==i || i==K+1) {
                    dp[i][j] = factorial(i);
                } else {
                    dp[i][j] = (dp[i-1][j-1]*i + dp[i][j-1]*(i-K))%Mod;
                }
            }
        }
        return dp[N][L];
    }
private:
    long factorial(int n){
        return n==1 ? 1 : (factorial(n-1)%Mod*n)%Mod;
    }
};
```
