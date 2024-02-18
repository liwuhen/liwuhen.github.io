---
title: "单调栈"
date: 2024-02-04T03:24:30Z
draft: false
author: 李双双
tags:
image: /images/img007.png
description:
toc:
---

## 一、单调栈
#### 1.1 每日温度
题目： [每日温度](https://leetcode.cn/problems/daily-temperatures/description/, "每日温度")

分析：由题目含义，可知寻找数组中每个元素右边第一个比它大的元素索引。可知此题维护一个单调递增栈即可。
每一个索引下标i与栈顶元素比较，当前遍历的元素T[i]小于栈顶元素T[st.top()]的情况，元素入栈；
当前遍历的元素T[i]等于栈顶元素T[st.top()]的情况，元素入栈；
当前遍历的元素T[i]大于栈顶元素T[st.top()]的情况，栈内元素出栈，元素T[i]入栈。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {

        // 递增栈
        stack<int> st;
        vector<int> result(temperatures.size(), 0);
        st.push(0);
        for (int i = 1; i < temperatures.size(); i++) {
            if (temperatures[i] < temperatures[st.top()]) {
                st.push(i);
            } else if (temperatures[i] == temperatures[st.top()]) {
                st.push(i);
            } else {
                while(!st.empty() && temperatures[i] > temperatures[st.top()]) {
                    result[st.top()] = i - st.top();
                    st.pop();
                }
                st.push(i);
            }
        }
        return result;
    }
};
```
</details>

#### 1.2 下一个更大元素 I
题目：[下一个更大元素 I](https://leetcode.cn/problems/next-greater-element-i/description/, "下一个更大元素 I")

分析：本题目遍历nums2对下标i的元素与nums1进行比对，若存在，则索取nums1对应元素的索引，放置nums2对应的元素值。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        /*分析：本题目遍历nums2对下标i的元素与nums1进行比对，
        若存在，则索取nums1对应元素的索引，放置nums2对应的元素值。*/

        stack<int> st;
        vector<int> result(nums1.size(), -1);
        unordered_map<int, int> map;
        for (int i = 0; i < nums1.size(); i++) {
            map[nums1[i]] = i; 
        }

        st.push(nums2[0]);
        for (int j = 1; j < nums2.size(); j++) {
            if (nums2[j] < st.top()) {
                st.push(nums2[j]);
            } else if (nums2[j] == st.top()) {
                st.push(nums2[j]);
            } else {
                while (!st.empty() && nums2[j] > st.top()) {
                    if (map.count(st.top()) > 0) {
                        result[map[st.top()]] = nums2[j];
                    }
                    st.pop();
                }
                st.push(nums2[j]);
            }
        }
        return result;
    }
};
```
</details>

#### 1.3 下一个更大元素II
题目：[503.下一个更大元素II](https://leetcode.cn/problems/next-greater-element-ii/description/,  "下一个更大元素II")

分析：拼接数组，再将result结果截断。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        /*分析：拼接数组，再将result结果截断。*/
        nums.insert(nums.end(), nums.begin(), nums.end());

        stack<int> st;
        vector<int> result(nums.size(), -1);
        st.push(0);

        for (int i = 1; i < nums.size(); i++) {
            if (nums[i] < nums[st.top()]) {
                st.push(i);
            } else if (nums[i] == nums[st.top()]) {
                st.push(i);
            } else {
                while (!st.empty() && nums[i] > nums[st.top()]) {
                    result[st.top()] = nums[i];
                    st.pop();
                }
                st.push(i);
            }
        }

        // 最后再把结果集即result数组resize到原数组大小
        result.resize(nums.size() / 2);
        return result;
    }
};
```
</details>


#### 1.4 接雨水
题目：[接雨水](https://leetcode.cn/problems/trapping-rain-water/description/, "接雨水")


<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
class Solution {
public:
    int trap(vector<int>& height) {
        if (height.size() <= 2) return 0; // 可以不加
        stack<int> st; // 存着下标，计算的时候用下标对应的柱子高度
        st.push(0);
        int sum = 0;
        for (int i = 1; i < height.size(); i++) {
            if (height[i] < height[st.top()]) {     // 情况一
                st.push(i);
            } if (height[i] == height[st.top()]) {  // 情况二
                st.pop(); // 其实这一句可以不加，效果是一样的，但处理相同的情况的思路却变了。
                st.push(i);
            } else {                                // 情况三
                while (!st.empty() && height[i] > height[st.top()]) { // 注意这里是while
                    int mid = st.top();
                    st.pop();
                    if (!st.empty()) {
                        int h = min(height[st.top()], height[i]) - height[mid];
                        int w = i - st.top() - 1; // 注意减一，只求中间宽度
                        sum += h * w;
                    }
                }
                st.push(i);
            }
        }
        return sum;
    }
};
```
</details>


#### 1.5 
题目: [84.柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/description/, "柱状图中最大的矩形")

分析： 


<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++


```
</details>