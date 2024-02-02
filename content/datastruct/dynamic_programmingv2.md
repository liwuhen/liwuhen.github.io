---
title: "动态规划II"
date: 2024-02-02T03:25:45Z
draft: false
author: 李双双
tags:
image: /images/img007.png
description:
toc:
---

## 一 子序列问题
#### 1.1 最长递增子序列
题目： [300.最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/description/ "Visit 最长递增子序列!")

分析：一般动态规划问题，考虑拆分子问题与历史状态。当前下标i的递增子序列长度，其实和i之前的下标j的子序列长度有关系。
    
    考虑动规五步曲：
    1）dp数组的含义：dp[i]，表示i之前包括i的以nums[i]结尾的最长递增子序列的长度。
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        位置i的最长升序子序列等于j从0到i-1各个位置的最长升序子序列 + 1 的最大值(为什么单独变量j遍历？
        因为子序列不连续，可能存在nums[i]<nums[i-1]情况，此时若采用dp[i] = dp[i-1] + 1思路，此时dp[i]等于初始化的值,
        导致后续遍历无法联系i之前的状态,相当于冲i处视作子序列的开头位置)。
    递推公式：dp[i] = max(dp[i], dp[j]+1);
    3) dp数组初始化：dp[0] = 1, 只考虑数组的第一个元素。dp数组应该初始化为1,因为无论dp[i]
        中i取值如何，至少长度为1,就是nums[i]本身。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
       if (nums.size() <= 1) return nums.size();

       vector<int> dp(nums.size(), 1);
       dp[0] = 1;
       int result = 0;
       for (int i = 1; i < nums.size(); i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = max(dp[i], dp[j]+1);
                }
            }
            result = max(result, dp[i]);
       }

       return result;
    }
};
```
</details>

#### 1.2 最长连续递增序列
题目： [674.最长连续递增子序列](https://leetcode.cn/problems/longest-continuous-increasing-subsequence/description/ "Visit 最长连续递增子序列!") 

分析：一般动态规划问题，考虑拆分子问题与历史状态。当前下标i的递增子序列长度，其实和i之前的下标j的子序列长度有关系。考虑连续性，只需要考虑当前状态与
历史前一个状态即可。

    考虑动规五步曲：
    1）dp数组的含义：dp[i]，表示下标为i的以nums[i]结尾的最长连续递增子序列的长度。
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        如果 nums[i] > nums[i - 1]，那么以 i 为结尾的连续递增的子序列长度
        一定等于 以i - 1为结尾的连续递增的子序列长度 + 1 。
        dp[i] = dp[i-1] + 1;
    递推公式：dp[i] = dp[i-1] + 1;
    3) dp数组初始化：dp[0] = 1, 只考虑数组的第一个元素。dp数组应该初始化为1,因为无论dp[i]
        中i取值如何，至少长度为1,就是nums[i]本身。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {

       if (nums.size() <= 1) return nums.size();

       vector<int> dp(nums.size()+1, 1);
       dp[0] = 1;
       int result = 0;
       for(int i = 1; i < nums.size(); i++){
            if (nums[i] > nums[i-1]){
                dp[i] = dp[i-1] + 1;
            }
            result = max(result, dp[i]);
       }

       return result;
    }
};
```
</details>

#### 1.3 最长重复子数组
题目： [718.最长重复子数组](https://leetcode.cn/problems/maximum-length-of-repeated-subarray/description/, "Visit 最长重复子数组!")

分析：一般动态规划问题，考虑拆分子问题与历史状态。当前为两个数组比较，考虑用二维数组表示状态。

    考虑动规五步曲：
    1）dp数组的含义：dp[i][j]，表示以下标为i-1的nums[i-1]结尾，以下标为j-1的nums[j-1]结尾的最长重复子数组的长度。
    2）递推公式：如何通过历史状态递推出当前状态dp[i][j],因为考虑两个数组的重复连续子序列，所以，上一状态必须为
       dp[i-1][j-1],只有这样才能组成连续的子序列。
        如果 nums[i - 1] == nums[j - 1]，当前的状态存在1个重复元素，考虑上一个状态的重复性dp[i-1][j-1] 。
        dp[i][j] = dp[i-1][j-1] + 1;
    递推公式：dp[i][j] = dp[i-1][j-1] + 1;
    3) dp数组初始化：dp[i][j]应该初始化为0。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历, 且遍历1-nums.size()，包含nums.size()
       因为是从1开始遍历，且考虑历史状态。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int findLength(vector<int>& nums1, vector<int>& nums2) {
        vector<vector<int>> dp(nums1.size()+1, vector<int>(nums2.size()+1, 0));

        int result = 0;
        for (int i = 1; i <= nums1.size(); i++) {
            for (int j = 1; j <= nums2.size(); j++) {
                if (nums1[i-1] == nums2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                }

                result = max(dp[i][j], result);
            }
        }
        return result;
    }
};
```
</details>


#### 1.4 最长公共子序列
题目： [1143.最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/description/, "Visit 最长公共子序列!")

分析：一般动态规划问题，考虑拆分子问题与历史状态。当前为两个数组比较，考虑用二维数组表示状态。

    考虑动规五步曲：
    1）dp数组的含义：dp[i][j]，长度为[0, i - 1]的字符串text1与长度为[0, j - 1]的字符串text2的最长公共子序列为dp[i][j]。
    2）递推公式：如何通过历史状态递推出当前状态dp[i][j], 
        a）考虑两个数组的公共连续子序列，如题目718.最长重复子数组。上一状态表示为dp[i-1][j-1], 只有这样才能组成连续的子序列。
           如果 nums[i - 1] == nums[j - 1]，当前的状态存在1个重复元素，考虑上一个状态的重复性dp[i-1][j-1] 。
           dp[i][j] = dp[i-1][j-1] + 1;
        b）两个数组的公共子序列，不考虑连续性。例如序列1:abcde与序列2:abe，在两个序列的c处切割，c与e不相等，可以考虑两个状态，
        状态1,考虑abc与ab，对应dp[i][j] = dp[i][j-1];状态2考虑ab与abe,对应dp[i-1][j]。
        dp[i][j] = max(dp[i][j-1], dp[i-1][j]);
    3) dp数组初始化：二维数组的初始化，主要考虑dp[0][j]与dp[i][0]两种状态，应该初始化为0。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历, 且遍历1-nums.size()，包含nums.size()
       因为是从1开始遍历，且考虑历史状态。


<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {

        vector<vector<int>> dp(text1.size()+1, vector<int>(text2.size()+1, 0));

        int result = 0;

        for (int i = 1; i <= text1.size(); i++) {
            for (int j = 1; j <= text2.size(); j++) {
                if (text1[i-1] == text2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = max(dp[i][j-1], dp[i-1][j]);
                }
                
                result = max(result, dp[i][j]);
            }
        }

        return result;
    }
};
```
</details>

#### 4.5 不相交的线
题目： [1035.不相交的线](https://leetcode.cn/problems/uncrossed-lines/description/, "Visit 不相交的线!")

分析：此题由2个数组，且求不相交的线等价于求两个数组的公共子序列，思路与4.4最长公共子序列一样。

    考虑动规五步曲：
    1）dp数组的含义：dp[i][j]，长度为[0, i - 1]的数组nums1与长度为[0, j - 1]的数组nums2的最大绘制线数（最长公共子序列）为dp[i][j]。
    2）递推公式：如何通过历史状态递推出当前状态dp[i][j], 
        a）考虑两个数组的公共连续子序列，如题目718.最长重复子数组。上一状态表示为dp[i-1][j-1], 只有这样才能组成连续的子序列。
           如果 nums[i - 1] == nums[j - 1]，当前的状态存在1个重复元素，考虑上一个状态的重复性dp[i-1][j-1] 。
           dp[i][j] = dp[i-1][j-1] + 1;
        b）两个数组的公共子序列，不考虑连续性。例如序列1:abcde与序列2:abe，在两个序列的c处切割，c与e不相等，可以考虑两个状态，
        状态1,考虑abc与ab，对应dp[i][j] = dp[i][j-1];状态2考虑ab与abe,对应dp[i-1][j]。
        dp[i][j] = max(dp[i][j-1], dp[i-1][j]);
    3) dp数组初始化：二维数组的初始化，主要考虑dp[0][j]与dp[i][0]两种状态，应该初始化为0。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历, 且遍历1-nums.size()，包含nums.size()
       因为是从1开始遍历，且考虑历史状态。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxUncrossedLines(vector<int>& nums1, vector<int>& nums2) {
        vector<vector<int>> dp(nums1.size() + 1, vector<int>(nums2.size() + 1, 0));

        int result = 0;
        for (int i = 1; i <= nums1.size(); i++) {
            for (int j = 1; j <= nums2.size(); j++) {
                if (nums1[i-1] == nums2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = max(dp[i][j-1], dp[i-1][j]);
                }

                result = max(result, dp[i][j]);
            }
        }
        return result;
    }
};
```
</details>

#### 4.6 最大子数组和
题目： [53.最大子数组和](https://leetcode.cn/problems/maximum-subarray/description/, "Visit 最大子数组和!")

分析：一般动态规划问题，考虑拆分子问题与历史状态，本题的递推公式需要考虑以nums[i]作为结尾元素的连续子数组的和最大的状态有哪些；
一个是dp[i-1]+nums[i]与nums[i]，因为需要求出每个nums[i]结尾的连续数组对应的和。

    考虑动规五步曲：
    1）dp数组的含义：dp[i]，以nums[i]结尾的最大和的连续子数组dp[i]。
    2）递推公式：如何通过历史状态递推出当前状态dp[i],
        考虑数组的dp[i]的最大只能由dp[i-1]+nums[i]或nums[i]两种状态递推得到。
        dp[i] = max(dp[i-1+nums[i], nums[i]]); 
        
    3) dp数组初始化：dp[0] = nums[0];
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。


<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        
        if (nums.size() == 1) return nums[0];

        vector<int> dp(nums.size()+1, 0);
        dp[0] = nums[0];
        int result = dp[0];
        for (int i = 1; i < nums.size(); i++) {
            dp[i] = max(dp[i-1] + nums[i], nums[i]);

            result = max(result, dp[i]);
        }

        return result;
    }
};
```
</details>

#### 4.7 判断子序列
题目 [392.判断子序列](https://leetcode.cn/problems/is-subsequence/description/, "判断子序列")

分析：题目中判断s是否为t的子序列，考虑转化为求s和t的公共子序列问题，并将公共子序列的长度等于s的长度就可以返回true，确定s就是t的
子序列。

    考虑动规五步曲：
    1）dp数组的含义：dp[i][j]，表示以下标i-1为结尾的字符串s，和以下标j-1为结尾的字符串t，
      相同子序列的长度为dp[i][j]。
    2）递推公式：如何通过历史状态递推出当前状态dp[i][j], 
        a）考虑两个数组的公共连续子序列，如题目718.最长重复子数组。上一状态表示为dp[i-1][j-1], 
           只有这样才能组成连续的子序列。如果 nums[i - 1] == nums[j - 1]，当前的状态存在1个重复元素，
           考虑上一个状态的重复性dp[i-1][j-1] 。
           dp[i][j] = dp[i-1][j-1] + 1;
        b）两个数组的公共子序列，不考虑连续性。例如序列1:abcde与序列2:abe，在两个序列的c处切割，
        c与e不相等，状态考虑ab与abe,对应dp[i-1][j]。
        dp[i][j] = dp[i-1][j];
    3) dp数组初始化：二维数组的初始化，主要考虑dp[0][j]与dp[i][0]两种状态，应该初始化为0。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历, 且遍历1-nums.size()，包含nums.size()
       因为是从1开始遍历，且考虑历史状态。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    bool isSubsequence(string s, string t) {

        if (s.empty()) return true;
       
        vector<vector<int>>dp(t.size()+1, vector<int>(s.size()+1, 0));

        for (int i = 1; i <= t.size(); i++) {
            for (int j = 1; j <= s.size(); j++) {
                if (t[i-1] == s[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = dp[i-1][j];
                }

                if (dp[i][j] == s.size()) {
                    return true;
                }
            }
        }

        return false;   
    }
};

```
</details>

#### 4.8 不同的子序列
题目 [115.不同的子序列](https://leetcode.cn/problems/distinct-subsequences/description/, "不同的子序列")

分析：

