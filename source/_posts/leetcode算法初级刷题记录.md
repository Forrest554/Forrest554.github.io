---
title: leetcode算法初级刷题记录
date: 2021-03-20 20:30:00
tags: leetcode
---

# 算法初级（更新ing）

## 数组

### 01删除排序数组中的重复项

>给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。不要使用额外的数组空间，你必须在原地修改输入数组，并在使用 O(1) 额外空间的条件下完成。

#### 个人思路

>cout的数目和索引为count的数组值都是不同的，因此只要遍历一次数组，遇到不同的赋值给nums[count]就行了，同时count增加1

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        int count = 1;
        int temp = nums[0];
        for (int i = 1; i <nums.length ; i++) {
            if(nums[i] == temp) {
                continue;
            } else {
                temp = nums[i];
                nums[count] = temp;
                count ++ ;
            }
        }
        return count;
    }
}
```

#### 官方解答

```java
public int removeDuplicates(int[] nums) {
    if (nums.length == 0) return 0;
    int i = 0;
    for (int j = 1; j < nums.length; j++) {
        if (nums[j] != nums[i]) {
            i++;
            nums[i] = nums[j];
        }
    }
    return i + 1;
}
```



### 02买卖股票的最佳时机 II

>给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
>
>注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

#### 贪心思想

只要第二天的价格比前一天高，就对买入卖出。

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length == 0){
            return  0;
        }
        int profit = 0;
        for (int i = 0; i < prices.length-1; i++) {
            int dirr = prices[i+1] - prices[i];
            if (dirr > 0){
                profit += dirr;
            }
        }
        return profit;
    }
}
```

#### 动态规划

待补充



### 03旋转数组

>给定一个数组，将数组中的元素向右移动 k个位置，其中k 是非负数。

#### 个人思路

>构建一个新数组，原数组第0个元素，赋值给新数组中索引为k%数组长度的位置。接下来按照顺序赋值就行

```java
class Solution {
    public void rotate(int[] nums, int k) {
        int length = nums.length;
        int[] resNums = new int[length];
        int absRemove = k%length;

        resNums[absRemove] = nums[0];
        for (int i = absRemove; i <length ; i++) {
            resNums[i] = nums[i-absRemove];
        }
        for (int i = 0; i < absRemove; i++) {
            resNums[i] = nums[length-absRemove+i];
        }
        for (int i = 0; i < length ; i++) {
            nums[i] = resNums[i];
        }
    }
}

```

#### 官方解答

- 方法一：创建新数组
  同个人思路，但人家更加简便

  ```java
  class Solution {
      public void rotate(int[] nums, int k) {
          int n = nums.length;
          int[] newArr = new int[n];
          for (int i = 0; i < n; ++i) {
              newArr[(i + k) % n] = nums[i];
          }
          System.arraycopy(newArr, 0, nums, 0, n);
      }
  }
  ```

  

- 方法二：

- 方法三：数组翻转

  ```java
  class Solution {
      public void rotate(int[] nums, int k) {
          k %= nums.length;
          reverse(nums, 0, nums.length - 1);
          reverse(nums, 0, k - 1);
          reverse(nums, k, nums.length - 1);
      }
  
      public void reverse(int[] nums, int start, int end) {
          while (start < end) {
              int temp = nums[start];
              nums[start] = nums[end];
              nums[end] = temp;
              start += 1;
              end -= 1;
          }
      }
  }
  ```