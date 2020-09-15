---
layout: post
title: Leetcode 58. Length of Last Word - Solution.
---
### [58. Length of Last Word](https://leetcode.com/problems/length-of-last-word/)

Given a string s consists of upper/lower-case alphabets and empty space characters ' ', return the length of last word (last word means the last appearing word if we loop from left to right) in the string.

If the last word does not exist, return 0.

Note: A word is defined as a maximal substring consisting of non-space characters only.

Example:

```
Input: "Hello World"
Output: 5
```

---
---

The solution here is fairly straight forward. 
We loop through the string and have a count for the word length.
If we reach a `' '` then we reset counter at the end we return the `len`.
However, we must account for some edge cases. What if the string is `"a "` if we just reset the counter when we reach a `' '` and then return the len
then we would end up with a len of 0 but it should really be 1 since the last "word" was `"a"`
Therefore we would have a lastLen variable that would be updated to the most recent **valid** len. Only if that len is greater then 0
This way when we return we can return the the most recent valid len if our current `len` is 0 like in our edge case.





```java
class Solution {
    public int lengthOfLastWord(String s) {
        int len = 0;
        int lastLen = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == ' ') {
                lastLen = len > 0 ? len : lastLen;
                len = 0;
             } else {
                len++;
               }
                
        }
        return len > 0 ? len : lastLen;
    }
}
```

This runs in constant space and linear time - O(N).

P.S. These write-ups are predominantly for myself. They are not meant to be a work of art and can sometimes be a bit raw. If there is a mistake or you have any improvement please reach out to me on [LinkedIn](https://www.linkedin.com/in/aaronfarntrog/)