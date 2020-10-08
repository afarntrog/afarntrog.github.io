---
layout: post
title: Leetcode 198. House Robber- 2 Solutions.
---



### [198. House Robber](https://leetcode.com/problems/house-robber/)


You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

 
Example 1:
```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house
```
---
---

## 1. First thought - Iterative. 
Go through the array backwards. Find the max value for that index and then store the max value in that index. We find the max value by iterating from index `i` until the end of the array. We hop over to i+2 then store this in `max` we then continue to i+3 if this is greater then we store that in `max`. Repeat until i + hop >= nums.length. The max value will be stored in nums[i];
At each iteration we will update the `resultMax` value if the current `nums[i]` value is greater then our running max. Then we can just return the `resultMax`.

```java
class Solution {
    public int rob(int[] nums) {
        int resultMax = 0;      
        for (int i = nums.length - 1; i > -1; i--) {
            int max = nums[i];
            for (int hop = 2; hop + i < nums.length; hop++) {
                if (nums[i] + nums[i + hop] > max)
                    max = nums[i] + nums[i + hop];
            }
            nums[i] = max;
            if (max > resultMax) 
                resultMax = max;
        }
        return resultMax;
    }
}
```

Space complexity is constant space. Whereas the time complexity will run in O(n^2) because for the 0'th index we will be looping over the entire array -1. and for the 0+1th index we will be looping over the entire array -2.

--- 


## Better solution - Iterative + 2 variables [see step 5](https://leetcode.com/problems/house-robber/discuss/156523/From-good-to-great.-How-to-approach-most-of-DP-problems.)


A robber has 2 options:
a) rob current house `i`
b) don't rob current house.
If option "a" is selected it means robber can't rob previous `i-1` house but can safely proceed to the one before previous `i-2` and gets all cumulative loot that follows.
If option "b" is selected the robber gets all the loot from robbery of `i-1` and all the following buildings.

So we go through the array. We store the value of prev1 and prev2. If the house immediately before this one is more valuable then robbing this house + collecting all the loot from the house 2 before this one, then we choose "b" - do not rob this house because the one before this one is more valuable. Else, rob this house and collect the prev2 loot. `Math.max(prev2 + num, prev1);` 
prev1 will always be the most recently robbed house and prev2 will be the house 1 hop over.

We always choose the first house. But we may not stick with it as we progress through the array.
Do i want the house I just robbed or do I want this house + the house before this one and its loot.

Example [1,2,3,1]
we first choose 1 because that is the most valuable house as of current. But then we move on, we can rob house[0] or house[1]. house[0] has value of 1 and house[1] has value of 2. So we un-rob house[0] and then rob house[1]. 
Now, prev1 is the (cumulative) value of the house we just robbed == 2, and prev2 = house[0] == 1
we move on.
we can rob house[1] = 2 or house[2] = 3 + house[0] because that is the current value of prev2 - the house before the one we recently robbed.
we choose latter; house[2] = 3 + house[0]  since that is 4.
next we can rob house[2] + its loot, meaning do not rob this house rather keep the previous robbery or choose to rob this house - house[3] == 1 + prev2 == house we robbed before current rob == 2 this equals 2+1=3. We choose not to rob this house since keeping our previous robbery results in greater loot. We have reached the end of the array so we can just return prev1 which will always be the most **recently** robbed house which is therefore always the most valuable; highest sum.

```java
public int rob(int[] nums) {
    if (nums.length == 0) return 0;
    int prev1 = 0;
    int prev2 = 0;
    for (int num : nums) {
        int tmp = prev1;
        prev1 = Math.max(prev2 + num, prev1);
        prev2 = tmp;
    }
    return prev1;
}
```

This runs in constant space and linear time.

P.S. These write-ups are predominantly for myself. They are not meant to be a work of art and can sometimes be a bit raw. If there is a mistake or you have any improvement please reach out to me on [LinkedIn](https://www.linkedin.com/in/aaronfarntrog/)