---
title: "动态规划I"
date: 2024-01-30T01:44:57Z
draft: false
author: 李双双
tags:
image:
description:
toc:
---

## 一 状态机DP
#### 1.1 打家劫舍
题目：[198.打家劫舍](https://leetcode.cn/problems/house-robber/description/ "Visit 打家劫舍!")

分析：此题考虑偷某个房屋和相邻的房屋有关系，例如示例1最后一个房屋和前一房屋有关，当前房屋偷与不偷取决于 前一个房屋和前两个房屋是否被偷了。因此，抽象理解当前状态和前面状态会有一种依赖关系，一般考虑动态规划。

    考虑动规五步曲：
    1）dp数组的含义：dp[i]，考虑当前房屋i（包含i之前的房屋）,偷窃的最高金额。
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。当前第i的房屋分为2种状态：选与不选？
        a）选，选i房屋时，最大金额 = dp[i-2] + nums[i]。
        b)不选，不选i房屋时，最大金额 = dp[i-1]。
        这里dp[i-1]表示考虑i-1房屋即i-1之前房屋的最大金额，具体偷不偷不一定。 
       递推公式：dp[i] = max(dp[i-2]+nums[i], dp[i-1]);
    3) dp数组初始化：dp[0] = nums[0], 只考虑第一个房屋，最大值为nums[0], 
    dp[1] = max(nums[0], nums[1]),因为对于第2房屋，并不一定偷，若不偷的可能偷1房屋，因此选最大。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int rob(vector<int>& nums) {
       if (nums.size() == 0) return 0;
       if (nums.size() == 1) return nums[0];
       
       vector<int> dp(nums.size()+1, 0);
       dp[0] = nums[0];
       dp[1] = max(nums[0], nums[1]);

       for (int i = 2; i < nums.size(); i++) {
            dp[i] = max(dp[i-2] + nums[i], dp[i-1]);
       }

       return dp[nums.size()-1];
    }
};
```
</details>

## 二 线性DP
#### 2.1 打家劫舍||
题目：[213.打家劫舍||](https://leetcode.cn/problems/house-robber-ii/description/ "Visit 打家劫舍||!")

分析：此题由于房屋的成环，说明题目所给数组为环状，若要所偷的金额最大，分为3种情况，第一种不考虑第一个房屋和最后一个房屋，此时为线性数组。第二种为考虑第一个房屋，不考虑最后一个房屋，第三种为考虑最后一个房屋，不考虑第一个房屋。（此种考虑是因为环形数组第一个与最后一个相同）

    考虑动规五步曲：
    1）dp数组的含义：dp[i]，考虑当前房屋i（包含i之前的房屋）,偷窃的最高金额。
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。当前第i的房屋分为2种状态：选与不选？
        a）选，选i房屋时，最大金额 = dp[i-2] + nums[i]。
        b)不选，不选i房屋时，最大金额 = dp[i-1]。
        这里dp[i-1]表示考虑i-1房屋即i-1之前房屋的最大金额，具体偷不偷不一定。 
       递推公式：dp[i] = max(dp[i-2]+nums[i], dp[i-1]);
    3) dp数组初始化：dp[0] = nums[0], 只考虑第一个房屋，最大值为nums[0], 
    dp[1] = max(nums[0], nums[1]),因为对于第2房屋，并不一定偷，若不偷的可能偷1房屋，因此选最大。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int rob(vector<int>& nums) {
       if (nums.size() == 0) return 0;
       if (nums.size() == 1) return nums[0];

       int result   = robv1(nums, 0, nums.size() - 2);
       int resultv1 = robv1(nums, 1, nums.size() - 1);
       return max(result, resultv1); 
    }

    int robv1(vector<int>& nums, int start, int end) {
        if (start == end) return nums[start];
        vector<int> dp(nums.size()+1, 0);
        dp[start] = nums[start];
        dp[start+1] = max(nums[start], nums[start+1]);
        for (int i = start + 2; i <= end; i++) {
            dp[i] = max(dp[i-2] + nums[i], dp[i-1]);
        }

        return dp[end];
        
    }
};
```
</details>

## 三 树形DP
#### 3.1 
小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 root 。

除了 root 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 两个直接相连的房子在同一天晚上被打劫 ，房屋将自动报警。

给定二叉树的 root 。返回 在不触动警报的情况下 ，小偷能够盗取的最高金额 。

    示例 1:
        输入: root = [3,2,3,null,3,null,1]
        输出: 7 
        解释: 小偷一晚能够盗取的最高金额 3 + 3 + 1 = 7
    示例 2:
        输入: root = [3,4,5,1,3,null,1]
        输出: 9
        解释: 小偷一晚能够盗取的最高金额 4 + 5 = 9
    提示：
        树的节点数在 [1, 104] 范围内
        0 <= Node.val <= 104

分析：此题由于房屋的成环，说明题目所给数组为环状，若要所偷的金额最大，分为3种情况，第一种不考虑第一个房屋和最后一个房屋，此时为线性数组。第二种为考虑第一个房屋，不考虑最后一个房屋，第三种为考虑最后一个房屋，不考虑第一个房屋。（此种考虑是因为环形数组第一个与最后一个相同）

    考虑动规五步曲：
    1）dp数组的含义：dp[i]，考虑当前房屋i（包含i之前的房屋）,偷窃的最高金额。
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。当前第i的房屋分为2种状态：选与不选？
        a）选，选i房屋时，最大金额 = dp[i-2] + nums[i]。
        b)不选，不选i房屋时，最大金额 = dp[i-1]。
        这里dp[i-1]表示考虑i-1房屋即i-1之前房屋的最大金额，具体偷不偷不一定。 
       递推公式：dp[i] = max(dp[i-2]+nums[i], dp[i-1]);
    3) dp数组初始化：dp[0] = nums[0], 只考虑第一个房屋，最大值为nums[0], 
    dp[1] = max(nums[0], nums[1]),因为对于第2房屋，并不一定偷，若不偷的可能偷1房屋，因此选最大。
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。


## 四 股票问题
#### 4.1 买卖股票的最佳时机
题目：[121.买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/description/ "Visit 买卖股票的最佳时机!")

分析：股票对于某天就两种状态，持有股票和不持有股票，再加上“某天”这个维度，所以状态有两个：某天与是否持有；用一个二维数组就可以表示所有天数的情况。

    考虑动规五步曲：
    1）dp数组的含义：第i天所获得最大利润。
       dp[i][0]-第i天手里持有股票的状态。有两种情况，
       第一种一直没买过股票，第i天买入这个股票，
       第二种是第i天之前已经买入了这个股票，一直持有到第i天。
       dp[i][1]-第i天手里不持有股票的状态。有三种情况，
       第一种一直没有买过这个股票（理解为i-1天不持有），第二种第i天之前买入了，第i天卖出，（理解为i-1天持有）
       第三种第i天之前，已经买入与卖出了这支股票。（理解为i-1天不持有）
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        dp[i][0]-第i天手里持有股票的状态2种状态：
        a）第一种，最大利润 - prices[i]。
        b）第二种，最大利润 dp[i-1][0]。
        最大利润：dp[i][0] = max(- prices[i], dp[i-1][0])。
        dp[i][1]-第i天手里不持有股票的状态3种状态:
        a）第一种，最大利润 dp[i-1][1]。
        b）第二种，最大利润 dp[i-1][0] + prices[i]。
        c）第三种，最大利润 dp[i-1][1]。
       递推公式：dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i]);
    3) dp数组初始化：dp[0][0] = -prices[0](初始最大利润为0,第一天持有，说明为第一天买入，因此为负数), 
       dp[0][1] = 0;
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {

       if (prices.empty()) return 0; 

       vector<vector<int>>dp(prices.size()+1, vector<int>(2));

       dp[0][0] = -prices[0];
       dp[0][1] = 0;

       for (int i = 1; i < prices.size(); i++) {
            dp[i][0] = max(-prices[i], dp[i-1][0]);
            dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i]);
       }

       return max(dp[prices.size()-1][0], dp[prices.size()-1][1]);
    }
};
```
</details>


#### 4.2 买卖股票的最佳时机II
题目: [122.买卖股票的最佳时机II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/description/ "Visit 买卖股票的最佳时机II!")

分析：股票对于某天就两种状态，持有股票和不持有股票，再加上“某天”这个维度，所以状态有两个：某天与是否持有；用一个二维数组就可以表示所有天数的情况。

    考虑动规五步曲：
    1）dp数组的含义：第i天所获得最大利润。dp[i][0]-第i天手里持有股票的状态。有两种情况，
       第一种第i天买入这个股票，i-1之前可能交易过，（理解为i-1天不持有）
       第二种是第i天之前已经买入了这个股票，一直持有到第i天。（理解为i-1天持有）
       dp[i][1]-第i天手里不持有股票的状态。有三种情况，
       第一种一直没有买过这个股票（理解为i-1天不持有），第二种第i天之前买入了，第i天卖出，（理解为i-1天持有）
       第三种第i天之前，已经买入与卖出了这支股票。（理解为i-1天不持有）
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        dp[i][0]-第i天手里持有股票的状态2种状态：
        a）第一种，最大利润 dp[i-1][1] - prices[i]。
        b）第二种，最大利润 dp[i-1][0]。
        最大利润：dp[i][0] = max(dp[i-1][1] - prices[i], dp[i-1][0])。
        dp[i][1]-第i天手里不持有股票的状态3种状态:
        a）第一种，最大利润 dp[i-1][1]。
        b）第二种，最大利润 dp[i-1][0] + prices[i]。
        c）第三种，最大利润 dp[i-1][1]。
       递推公式：dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i]);
    3) dp数组初始化：dp[0][0] = -prices[0](初始最大利润为0,第一天持有，说明为第一天买入，因此为负数), 
       dp[0][1] = 0;
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.size() == 0) return 0;

        vector<vector<int>> dp(prices.size() + 1, vector<int>(2));
        dp[0][0] = -prices[0];
        dp[0][1] = 0;

        for (int i = 1; i < prices.size(); i++) {
            dp[i][0] = max(dp[i-1][1] - prices[i], dp[i-1][0]);
            dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i]);
        }
        return max(dp[prices.size()-1][0], dp[prices.size()-1][1]);
    }
};
```
</details>

#### 4.3 买卖股票的最佳时机III
题目： [123.买卖股票的最佳时机III](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/description/ "Visit 买卖股票的最佳时机III!")

分析：股票对于某天就两种状态，持有股票和不持有股票，再加上“某天”这个维度，所以状态有两个：某天与是否持有；用一个二维数组就可以表示所有天数的情况。
但是题目要求**至多买卖两次**，这意味着可以买卖一次，可以买卖两次，也可以不买卖, 因此对于某天持有股票分为5种状态。

    考虑动规五步曲：
    1）dp数组的含义：第i天所获得最大利润。
        dp[i][0]-第i天一直没有买卖股票的状态。
        dp[i][1]-第i天第一次持有股票的状态。有两种情况，
        第一种第i天第一次买入这个股票，i-1之前没有交易过，（理解为i-1天不持有）
        第二种是第i天之前已经买入了这个股票，一直持有到第i天。（理解为i-1天持有）
        dp[i][2]-第i天第一次不持有股票的状态。有两种情况，
        第一种第i天之前第一次买入这个股票，第i天卖出，（理解为i-1天持有）
        第二种第i天之前已经卖出了这个股票。（理解为i-1天不持有）
        dp[i][3]-第i天第二次持有股票的状态。有两种情况，
        第一种第i天第二次买入这个股票，i-1之前已经交易过，（理解为i-1天不持有）
        第二种第i天之前已经第二次买入了这个股票，一直持有到第i天。（理解为i-1天持有）
        dp[i][4]-第i天第二次不持有股票的状态。有两种情况，
        第一种第i天之前第二次买入这个股票，第i天卖出股票，（理解为i-1天持有）
        第二种第i天之前已经第二次卖出这个股。（理解为i-1天不持有）
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        dp[i][0]-第i天一直没有买卖股票的状态： 最大利润 0
        dp[i][1]-第i天第一次持有股票的状态:
        a）第一种，最大利润 dp[i-1][0] - prices[i]。
        b）第二种，最大利润 dp[i-1][1]。
        最大利润：dp[i][1] = max(dp[i-1][0] - prices[i], dp[i-1][1])。
        dp[i][2]-第i天第一次不持有股票的状态:
        a）第一种，最大利润 dp[i-1][1] + prices[i]。
        b）第二种，最大利润 dp[i-1][2]。
        最大利润：dp[i][2] = max(dp[i-1][1] + prices[i], dp[i-1][2])。
        dp[i][3]-第i天第二次持有股票的状态:
        a）第一种，最大利润 dp[i-1][2] - prices[i]。
        b）第二种，最大利润 dp[i-1][3]。
        最大利润：dp[i][3] = max(dp[i-1][2] - prices[i], dp[i-1][3])。
        dp[i][4]-第i天第二次不持有股票的状态:
        a）第一种，最大利润 dp[i-1][3] + prices[i]。
        b）第二种，最大利润 dp[i-1][4]。
        最大利润：dp[i][3] = max(dp[i-1][3] + prices[i], dp[i-1][4])。
       递推公式：最大利润 = max(dp[i][0], dp[i][1], dp[i][2], dp[i][3], dp[i][4]);
    3) dp数组初始化：dp[0][0] = 0;
       dp[0][1] = -prices[0](初始最大利润为0,第一天持有，说明为第一天买入，因此为负数), 
       dp[0][2] = 0;   dp[0][3] = -prices[0](第一天完成了第一次买卖股票，又再次买入)   
       dp[0][4] = 0（第一天完成了第一次买卖股票，又再次买入并卖出）
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {

        if (prices.empty()) return 0;

        vector<vector<int>> dp(prices.size()+1, vector<int>(5, 0));

        dp[0][0] = 0;
        dp[0][1] = -prices[0];
        dp[0][2] = 0;
        dp[0][3] = -prices[0];
        dp[0][4] = 0;

        for (int i = 1; i < prices.size(); i++) {
            dp[i][1] = max(dp[i-1][0] - prices[i], dp[i-1][1]);
            dp[i][2] = max(dp[i-1][1] + prices[i], dp[i-1][2]);
            dp[i][3] = max(dp[i-1][2] - prices[i], dp[i-1][3]);
            dp[i][4] = max(dp[i-1][3] + prices[i], dp[i-1][4]);
        }

        return dp[prices.size()-1][4];
    }
};
```
</details>

#### 4.4 买卖股票的最佳时机IV
题目：[188.买卖股票的最佳时机IV](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/description/ "Visit 买卖股票的最佳时机IV!")

分析： 股票对于某天就两种状态，持有股票和不持有股票，再加上“某天”这个维度，所以状态有两个：某天与是否持有；用一个二维数组就可以表示所有天数的情况。
但是题目要求**至多买卖K次**，对比买卖股票最佳时机III，可知，某天的股票状态除了0以外，偶数就是卖出，奇数就是买入。

    考虑动规五步曲：
    1）dp数组的含义：第i天所获得最大利润。
        dp[i][0]-第i天一直没有买卖股票的状态。
        dp[i][1]-第i天第一次持有股票的状态。有两种情况，
        第一种第i天第一次买入这个股票，i-1之前没有交易过，（理解为i-1天不持有）
        第二种是第i天之前已经买入了这个股票，一直持有到第i天。（理解为i-1天持有）
        dp[i][2]-第i天第一次不持有股票的状态。有两种情况，
        第一种第i天之前第一次买入这个股票，第i天卖出，（理解为i-1天持有）
        第二种第i天之前已经卖出了这个股票。（理解为i-1天不持有）
        dp[i][3]-第i天第二次持有股票的状态。有两种情况，
        第一种第i天第二次买入这个股票，i-1之前已经交易过，（理解为i-1天不持有）
        第二种第i天之前已经第二次买入了这个股票，一直持有到第i天。（理解为i-1天持有）
        dp[i][4]-第i天第二次不持有股票的状态。有两种情况，
        第一种第i天之前第二次买入这个股票，第i天卖出股票，（理解为i-1天持有）
        第二种第i天之前已经第二次卖出这个股。（理解为i-1天不持有）

        规律：偶数就是卖出，奇数就是买入； j的取值范围 0<= j <= 2j+1；
        奇数：dp[i][j+1]   偶数： dp[i][j+2]
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        dp[i][0]-第i天一直没有买卖股票的状态： 最大利润 0
        dp[i][1]-第i天第一次持有股票的状态:
        a）第一种，最大利润 dp[i-1][0] - prices[i]。
        b）第二种，最大利润 dp[i-1][1]。
        最大利润：dp[i][1] = max(dp[i-1][0] - prices[i], dp[i-1][1])。
        dp[i][2]-第i天第一次不持有股票的状态:
        a）第一种，最大利润 dp[i-1][1] + prices[i]。
        b）第二种，最大利润 dp[i-1][2]。
        最大利润：dp[i][2] = max(dp[i-1][1] + prices[i], dp[i-1][2])。
        dp[i][3]-第i天第二次持有股票的状态:
        a）第一种，最大利润 dp[i-1][2] - prices[i]。
        b）第二种，最大利润 dp[i-1][3]。
        最大利润：dp[i][3] = max(dp[i-1][2] - prices[i], dp[i-1][3])。
        dp[i][4]-第i天第二次不持有股票的状态:
        a）第一种，最大利润 dp[i-1][3] + prices[i]。
        b）第二种，最大利润 dp[i-1][4]。

        奇数：dp[i][j+1] = max(dp[i-1][j] - prices[i], dp[i-1][j+1])
        偶数：dp[i][j+2] = max(dp[i-1][j+1] + prices[i], dp[i-1][j+2])
       递推公式：最大利润 = max(dp[i][j+1], dp[i][j+2])。
    3) dp数组初始化：dp[0][0] = 0;
       dp[0][1] = -prices[0](初始最大利润为0,第一天持有，说明为第一天买入，因此为负数), 
       dp[0][2] = 0;   dp[0][3] = -prices[0](第一天完成了第一次买卖股票，又再次买入)   
       dp[0][4] = 0（第一天完成了第一次买卖股票，又再次买入并卖出）
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {

        if (prices.size() == 0) return 0;
        vector<vector<int>> dp(prices.size(), vector<int>(2 * k + 1, 0));
        for (int j = 1; j < 2 * k; j += 2) {
            dp[0][j] = -prices[0];
        }
        for (int i = 1;i < prices.size(); i++) {
            for (int j = 0; j < 2 * k - 1; j += 2) {
                dp[i][j + 1] = max(dp[i - 1][j + 1], dp[i - 1][j] - prices[i]);
                dp[i][j + 2] = max(dp[i - 1][j + 2], dp[i - 1][j + 1] + prices[i]);
            }
        }
        return dp[prices.size() - 1][2 * k];
    }
};
```
</details>

#### 4.5 买卖股票的最佳时机含冷冻期
题目： [309.买卖股票的最佳时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/ "Visit 买卖股票的最佳时机含冷冻期!")

分析： 股票对于某天就两种状态，持有股票和不持有股票，再加上“某天”这个维度，所以状态有两个：某天与是否持有；用一个二维数组就可以表示所有天数的情况。
但是题目要求**卖出股票后有一天冷冻期**，考虑因为本题我们有冷冻期，而冷冻期的前一天，只能是 「今天卖出股票」状态，如果是 「不持有股票状态」那么就很模糊，因为不一定是 卖出股票的操作，本题目将第i天不持有股票状态拆分出来，状态-第i天卖出股票，状态-第i天之前已经卖出股票。总分为4个状态。

    考虑动规五步曲：
    1）dp数组的含义：第i天所获得最大利润。
       dp[i][0]-第i天手里持有股票的状态。有三种情况，
       第一种第i天买入这个股票，i-1处于冷冻期，（理解为i-1天不持有）
       第二种第i天买入这个股票，i-1不持有，（理解为i-1天不持有）
       第二种是第i天之前已经买入了这个股票，一直持有到第i天。（理解为i-1天持有）
       dp[i][1]-第i天保持卖出股票的状态。有二种情况，
       第一种第i天之前卖出，（理解为i-1天不持有）
       第二种第i天之前卖出，i-1天处于冷冻期。（理解为i-1天不持有）
       dp[i][2]-第i天卖出股票的状态。有一种情况，
       第i天之前买入了，第i天卖出，（理解为i-1天持有）
       dp[i][3]-第i天为冷冻期的状态。有一种情况，
       第一种第i-1天卖出股票，第i天为冷冻期。
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        dp[i][0]-第i天手里持有股票的状态3种状态：
        a）第一种，最大利润 dp[i-1][3] - prices[i]。
        b）第二种，最大利润 dp[i-1][1] - prices[i]。
        b）第二种，最大利润 dp[i-1][0]。
        最大利润：dp[i][0] = max(dp[i-1][3] - prices[i], max(dp[i-1][1] - prices[i], dp[i-1][0]))。
        dp[i][1]-第i天保持卖出股票的状态2种状态:
        a）第一种，最大利润 dp[i-1][1]。
        b）第二种，最大利润 dp[i-1][3]。
        最大利润：dp[i][0] = max(dp[i-1][3], dp[i-1][1])。
        dp[i][2]-第i天卖出股票的状态
        a）第一种，最大利润 dp[i-1][0] + prices[i]。
        dp[i][3]-第i天为冷冻期的状态
        a）第一种，最大利润 dp[i-1][2]。
       递推公式：最大利润 = max(dp[i][0], max(dp[i][1], max(dp[i][2], dp[i][3])));
    3) dp数组初始化：dp[0][0] = -prices[0](初始最大利润为0,第一天持有，说明为第一天买入，因此为负数), 
       dp[0][1] = 0;
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {

       if (prices.empty())  return 0;

       vector<vector<int>>dp(prices.size()+1, vector<int>(4, 0));
       dp[0][0] = -prices[0];
       dp[0][1] = 0;
       dp[0][2] = 0;
       dp[0][3] = 0;

       for (int i = 1; i < prices.size(); i++) {
            dp[i][0] = max(dp[i-1][3] - prices[i], max(dp[i-1][1] - prices[i], dp[i-1][0]));
            dp[i][1] = max(dp[i-1][3], dp[i-1][1]);
            dp[i][2] = dp[i-1][0] + prices[i];
            dp[i][3] = dp[i-1][2];

       }


       return max(dp[prices.size()-1][0],max(dp[prices.size()-1][1], max(dp[prices.size()-1][2], dp[prices.size()-1][3])));
    }
};
```
</details>

#### 4.5 买卖股票的最佳时机含手续费
题目： [714.买卖股票的最佳时机含手续费](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/ "Visit 买卖股票的最佳时机含手续费!")

分析：股票对于某天就两种状态，持有股票和不持有股票，再加上“某天”这个维度，所以状态有两个：某天与是否持有；用一个二维数组就可以表示所有天数的情况。但是本题目考虑卖出股票时扣除手续费。

    考虑动规五步曲：
    1）dp数组的含义：第i天所获得最大利润。dp[i][0]-第i天手里持有股票的状态。有两种情况，
       第一种第i天买入这个股票，i-1之前可能交易过，（理解为i-1天不持有）
       第二种是第i天之前已经买入了这个股票，一直持有到第i天。（理解为i-1天持有）
       dp[i][1]-第i天手里不持有股票的状态。有三种情况，
       第一种一直没有买过这个股票（理解为i-1天不持有），第二种第i天之前买入了，第i天卖出，（理解为i-1天持有）
       第三种第i天之前，已经买入与卖出了这支股票。（理解为i-1天不持有）
    2）递推公式：如何通过历史状态递推出当前状态dp[i]。
        dp[i][0]-第i天手里持有股票的状态2种状态：
        a）第一种，最大利润 dp[i-1][1] - prices[i]。
        b）第二种，最大利润 dp[i-1][0]。
        最大利润：dp[i][0] = max(dp[i-1][1] - prices[i], dp[i-1][0])。
        dp[i][1]-第i天手里不持有股票的状态3种状态:
        a）第一种，最大利润 dp[i-1][1]。
        b）第二种，最大利润 dp[i-1][0] + prices[i] - fee。
        c）第三种，最大利润 dp[i-1][1]。
       递推公式：dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i] - fee);
    3) dp数组初始化：dp[0][0] = -prices[0](初始最大利润为0,第一天持有，说明为第一天买入，因此为负数), 
       dp[0][1] = 0;
    4) 确定遍历顺序: 由于当前状态与历史状态有关，因此需要i从小到大开始遍历。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {

       vector<vector<int>> dp(prices.size()+1, vector<int>(2, 0));
       dp[0][0] = -prices[0];
       dp[0][1] = 0;

       for (int i = 1; i < prices.size(); i++) {
            dp[i][0] = max(dp[i-1][1] - prices[i], dp[i-1][0]);
            dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i] - fee);
       }

       return max(dp[prices.size()-1][0], dp[prices.size()-1][1]);
    }
};
```
</details>