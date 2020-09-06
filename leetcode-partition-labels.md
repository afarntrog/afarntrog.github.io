---
layout: post
title: Leetcode Partition Labels - 2 Solutions.
---



### [763. Partition Labels](https://leetcode.com/problems/partition-labels/)


A string S of lowercase English letters is given. We want to partition this string into as many parts as possible so that each letter appears in at most one part, and return a list of integers representing the size of these parts.

 

Example 1:

```Input: S = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.
```

---
---

### Brute force - first thought. 
This is **Terrible** solution but I am writing it here because it was my first thought. You should probably skip to bottom solution.

The general idea:


   You begin by finding the last occurrence of the first letter in the original string.
   Then partition the string by that last occurrence. You should now have two strings: a left partition, and a right partition.
   Let us handle the left partition. Although you found the last occurrence of the first letter, the left partition may contain other letters that are **also** in the right half.
   So we must **expand** our left string to include those letters. 
   After we partition the left side, we add all of the `leftString` chars into the `charSet`.
   This allows us to loop over the set and for each letter in the set we will **again** find that last occurrence and then partition the string.
   **If** this new partition is larger than our `leftString` then that means we must **expand** the left partition. So we **update** our left partition.
   However, now there is a new problem: if we expand the left partition we may **introduce** a new letter that was **not** in the original `leftString` and therefore not in the current `charSet`.
   That means we will not be finding the last occurrence of the **new letter**.
   So we have to **update** our hashset with any **new** letters that were introduced when we expanded our string.
   However, we **cannot** update our hashset while iterating over it without getting a `ConcurrentModificationException`.
   So we will introduce a `Stack` and then add **all** the `charSet` values into the stack.
   We will now go through every letter in the stack until it is empty and then check if we need to expand our string. If we
   expand our string then we will take all those chars and **push** them onto our stack **only** if they are **not** already in the `charSet`. (Note: when we push a new char onto the stack
   we will then add it to the char set so that we will not by mistake add it again to our stack).
   The above solution is coded out below.
   
```java
class Solution {
        public static List<Integer> partitionLabels(String originalString) {

            List<Integer> resultList = new ArrayList<>();

            while (originalString.length() > 0) {
                String leftString = splitStringByLastOccurrence(originalString, originalString.charAt(0));

                // add all chars of partitioned string to charset so we can know what unique values to search through
                Set<Character> charSet = new HashSet<>();
                leftString.chars().forEach(s -> charSet.add((char)s));

                // add all the charset values to stack so we can dynamically add and remove values until it's empty
                Stack<Character> stackSet = new Stack<>();
                charSet.forEach(s -> stackSet.add(s));

                while (!stackSet.isEmpty()) {
                    char c = stackSet.pop();
                    String temp = splitStringByLastOccurrence(originalString, c);
                    if (temp.length() > leftString.length())
                        leftString = temp;
                    // Add any NEW chars that may have been added becuase we expanded our string
                    for (int i = 0; i < temp.length(); i++) {
                        if (!charSet.contains(leftString.charAt(i))){
                            charSet.add(leftString.charAt(i));
                            stackSet.push(leftString.charAt(i));
                        }
                    }
                }

                resultList.add(leftString.length());    // add the size to resultList to be returned.

                originalString = originalString.substring(leftString.length());
            }
            return resultList;
        }

        // I can optimize this by passing in a start position for i.
        public static String splitStringByLastOccurrence(String string, char c) {
            int lastOccurrence = 0;

            for (int i = 0; i < string.length(); i++) {
                if (string.charAt(i) == c)
                    lastOccurrence = i;
            }
            // optimize the 0 to instead be 'start'
            return string.substring(0, lastOccurrence + 1);       // include the i'th element in the new string.
        }
}
```



---

### Greedy solution - O(n)

This table should be referenced when reading the solution.

a | b | a | b | c | b | a | c | a | d | e  | f  | e  | g  | d  | e  | h  | i  | j  | h  | k  | l  | i  | j  | 
  --- |   --- |   --- |   --- |   --- |   --- |   --- |   --- |   --- |   --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- | 
0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | LastIdx (8) | 9 | 10 | 11 | 12 | 13 | 14 | LastIdx (15) | 16 | 17 | 18 | 19 | 20 | 21 | 22 | LastIdx (23) | 


This solution is a very efficient and element solution. It does two passes each of O(n) time which results in a time complexity of O(n).
The general idea is to:
 1. Make one pass through the string and record the **last** index for each letter. Use a HashMap to do this. In our case the lastIndex
for `a` will be `8` as noted in the above table.

2. Make a second pass that will go through the string. First it will store the last index of that char. We now with confidence that we can break our string until we **at least**
reach that `lastIndex`. However, there may be an even **further** index between `firstIndex` and current `lastIndex`. If so we have to **expand** our string. So we continue
looping and getting the last index for each char. If there is a further index then our current `lastIndex` then we update `lastIndex` to be equal to the current
 furthest index. We continue looping until `i` (the index of the element we are up to) is equal to `lastIndex`. Because then we know we reached the end. (Again reference the above table.)
 We can now grab the length of the current string partition by easily taking the difference between `firstIndex` and `lastIndex`.


```java
class Solution {
    public List<Integer> partitionLabels(String S) {
        if(S == null || S.length() == 0){
            return null;
        }

        // record the last index of the each char
        Map<Character, Integer> indexMap = new HashMap<>();  

        for(int i = 0; i < S.length(); i++){
            indexMap.put(S.charAt(i), i);
        }

        // second pass to get the partitions
        List<Integer> list = new ArrayList<>();
        int lastIndex = 0;
        int firstIndex = 0;
        for(int i = 0; i < S.length(); i++){
            lastIndex = Math.max(lastIndex, indexMap.get(S.charAt(i)));
            if(lastIndex == i){
                list.add(lastIndex - firstIndex + 1);
                firstIndex = lastIndex + 1;
            }
        }
        return list;
    }
}
```