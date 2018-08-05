# Weekly Contest 96
---
layout: post
title: Weekly Contest 96
category: leetcode
tags: leetcoe, weekly contest
keywords: leetcode, weekly contest
---

这次比赛在结束前只A掉了两道题，主要是在第三题花了比较多的时间。<br>
**887. Projection Area of 3D Shapes**
<p>给出一个2d的数组，表示某个坐标(i,j)的高度为多少，然后根据高度求出三视图的面积。题目比较直接，应该能直接想出解法。</p>

```c++
class Solution {
public:
    int projectionArea(vector<vector<int>>& grid) {
        int res = 0;
        int M = grid.size();
        if (M == 0) {
            return res;
        }
        int N = grid[0].size();
        if (N == 0) {
            return res;
        }
        for(int i = 0; i < M; i++){
            int tmp = 0;
            for(int j = 0; j < N; j++){
                tmp = max(tmp, grid[i][j]);
                if (grid[i][j] != 0) {
                    res++;
                }
            }
            res += tmp;
        }
        for(int j = 0; j < N; j++){
            int tmp = 0;
            for(int i = 0; i < M; i++){
                tmp = max(tmp, grid[i][j]);
            }
            res += tmp;
        }
        return res;
    }
};
```
因为错误提交有罚时，所以对给出的case都最好验证一下。

**885. Boats to Save People**
<p>有一列人，每个人有不同的体重。每条船可以最多装两个人，并且每条船的限重都是一样的。这道题的思路也比较简单，首先排序是必要的，根据有序，可以直接从两端开始处理。如果每条船在装一个右边肥佬的同时，尽可能捎带一个瘦子。如果不行，就留给下一条船。</p>

```c++
class Solution {
public:
    int numRescueBoats(vector<int>& people, int limit) {
        int res = 0;
        int len = people.size();
        sort(people.begin(), people.end());
        int left = 0;
        int right = len-1;
        while(left < right){
            int sum = people[left]+people[right];
            if (sum > limit) {
                res++;
                right--;
            } else {
                res++;
                right--;
                left++;
            }
        }
        if (left == right) {
            res++;
        }
        return res;
    }
};
```
**884. Decoded String at Index**
<p>这道题目花费了比较久的时间，并且第一个思路会遇到MLE，换了思路后等A掉比赛已经结束。
首先第一个思路比较直接，模拟整个过程，找到第K位字母。选择这个算法的想法是我认为既然单循环字符串可能会不断更新，除了把完整的字符串解码出来，好像没有更好的方法去找到第K位。这种简单暴力的算法在小数据的情况是保证正确的。但是既然MLE说明还需要进一步优化。</p>
<p>我在思考的是为什么会花时间implement了一个次优的算法，有没有地方可以从一开始看出暴力法是不可行的？</p>
题目给出的变量取值范围：<br>

```c++
1. 2 <= S.length <= 100
2. S will only contain lowercase letters and digits 2 through 9.
3. S starts with a letter.
4. 1 <= K <= 10^9
5. The decoded string is guaranteed to have less than 2^63 letters.
```
<p>2^63, 最多有这个多个字符。如果完整存下来的话，这个数字是很恐怖的。并且也要注意到这个长度会超过int的取值范围。也就是说简单模拟从一开始就应该判断出是不可行的，这次比赛花时间在这个上面是不应该的。Think one more step.</P>
<p>既然暴力模拟的方法不可取，那么怎么将前面循环字符串保存下来呢？</p>
<p>首先注意到连续的数字，可以转化为他们的乘积。比如：ad23 = ad6. 那么初始的密文就可以转化为一个单字符串和一个循环次数了。（注意之后的字符串都是加在前面复制结果之后的）很自然的想到我们可以将这个分别存储下来。终止条件是什么呢？找到第K位，也就是复制之后的长度大于等于K。而这个总长度是很好记录的。</p>
<p>那么现在的问题就是根据存储下来的piece和total length判断K的位置。可以感觉到这是一个递归的过程。比如前一个字符串长度为a,循环3次。下一个piece长度为b.假如K在3*a~3*a+b的位置，那么我们要找的第K位就在下一个piece中。假如K < 3*a,那么令K%a,从前面找K落在第几个区间。算法的关键在于模拟这个backtracking的过程。</p>

```c++
class Solution {
public:
    string decodeAtIndex(string S, int K) {
        // S = "a2345678999999999999999", K = 10000000;
        // S = "ha22", K = 5;
        // S= "abc", K = 1;
        // S = "yyuele72uthzyoeut7oyku2yqmghy5luy9qguc28ukav7an6a2bvizhph35t86qicv4gyeo6av7gerovv5lnw47954bsv2xruaej";
        // K = 123365626;
        // S = "a2345678999999999999999", K = 1;
        // S = "ixm5xmgo78";
        // K = 711;
        vector<string> peace;
        vector<long long> sum;
        peace.push_back("");
        sum.push_back(0);
        long long total = 0;
        for(int i = 0; i < S.size(); i++){
            char s = S[i];
            // cout<<s<<endl;
            if (s>='0' && s<='9') {
                // cout<<"in count"<<endl;
                long long base = 1;
                while (i<S.size() && s>='0' && s<='9') {
                    base *= s-'0';
                    i++;
                    s = S[i];
                }
                i--;
                // cout<<"base:"<<base<<endl;
                total *= base;
                sum.push_back(total);
                if (total >= K) {
                    break;
                }
            } else {
                // cout<<"in peace"<<endl;
                string plus = "";
                while (i<S.size() && s>='a' && s<='z') {
                    plus += s;
                    i++;
                    s = S[i];
                }
                // cout<<plus<<endl;
                peace.push_back(plus);
                i--;
                // cout<<"i: "<<i<<endl;
                total += plus.length();
            }
        }
        int len = peace.size()-1;
        // for(int i = 0; i <= len; i++){
        //     cout<<"peace "<<peace[i]<<endl;
        //     cout<<"count "<<count[i]<<endl;
        //     cout<<"sum "<<sum[i]<<endl;
        // }
        K -= 1;
        while(len > 0){
            long long base = sum[len-1]+peace[len].size();
            K = K%base;
            // cout<<K<<endl;
            if (K >= sum[len-1]) {
                K -= sum[len-1];
                return string(1, peace[len][K]);
            } else {
                len--;
            }
        }
        return "";
    }
};
```
<p>这个backtracking还是不好想的，也可能是自己用的方法太复杂，导致用的时间比较多。看到一个递归的方法，实现起来简单很多，但是效率可能会差一些。</p>

```c++
class Solution {
public:
    string decodeAtIndex(string S, int K) {
        long long now = 0;
        for(int i = 0;i < S.length();i++){
            if(isdigit(S[i])){
                int d = S[i] - '0';
                if(d * now >= K){
                    string S1 = S.substr(0, i);
                    return decodeAtIndex(S1, (K - 1) % now + 1);
                }else{
                    now = now * d;
                }
            }else{
                now++;
                if(now == K) {
                    string temp = "";
                    temp += S[i];
                    return temp;
                }
            }
        }
        return 0;
    }
};
```

<p>另外贴一个也是backtracking，但是在空间和时间上都比较好的解法。</p>

```c++
class Solution {
public:
    string decodeAtIndex(string S, int K) {
        int len=S.size();
        long long tot=0;
        for(int i=0;i<len;i++)
        {
            if(S[i]>='a'&&S[i]<='z')tot++;
            else
            {
                int p=(S[i]-'0');
                tot=tot*p;
            }
        }
        S=S+"s";
        tot++;
        for(int i=len;i>=0;i--)
        {
            if(S[i]>='a'&&S[i]<='z')tot--;
            else
            {
                int p=(S[i]-'0');
                tot=tot/p;
                K=(K-1)%tot+1;
            }
            if(K==tot)
            {
                int l=i-1;
                while(l>=0&&S[l]>='2'&&S[l]<='9')l--;
                string res="";
                res=res+S[l];
                return res;
            }
        }
      }
  };
```
