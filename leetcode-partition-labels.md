BRUTE force - first thought. **Terrible** solution.
   here is the generall idea
   You begin by finding the last occurance of the first letter in the original string
   you then partition the string by that last occurance
   you shoul now have two strings
   a left partion and a right partition
   Let us handle the left partition
   Alhtough you found the last occurance of the first letter that left partition may contain other letters that are ALSO in the right half.
   so we must EXPAND our left string to include them. here is how
   after we partition the left side, we add all those chars in the left string to a SET. this way we can loop over the set and for each letter
   in the set we will AGAIN find that last occurence and then partiiton the string.
   IF this new partition is larger then our leftString then that means we must EXAPAND the left partition. So we
   update our left partition
   however, now there is a new problem and that is if we expand the left partition we may INTRODUCE a new char that was NOT in the original leftString and therfore
   not in the current charSet. This will cause a problem becuase that means we will not be finding the last occurance of the NEW CHAR.
   So we have to update our hashset with any NEW letters that were introduced when we expanded our string
   however, we CANNOT update our hashset while iterating over it.
   so we will introduce a Stack and then add ALL the charset values into the stack.
   we will go through every letter in the stack until it is empty and then check if we need to expand our string. If we
   expand our string then we will take all those chars and PUSH them onto our stack ONLY if they are NOT already in the hashset. (Note: when we push a new char onto the stack
   we will then add it to the char set so that we will not by mistake add it again to our stack) */
   
   | Tables        | Are           | Cool  |
   | ------------- |:-------------:| -----:|
   
| a | b | a 
 --- | ---  | ---  
| 0 | 1 | 2 |


a | b | a | b | c | b | a | c | a | d | e  | f  | e  | g  | d  | e  | h  | i  | j  | h  | k  | l  | i  | j  | 
  --- |   --- |   --- |   --- |   --- |   --- |   --- |   --- |   --- |   --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- |    --- | 
0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 
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