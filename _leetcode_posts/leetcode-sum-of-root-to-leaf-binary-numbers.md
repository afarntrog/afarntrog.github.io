---
layout: post
title: Leetcode Partition Labels - 2 Solutions.
---


##   Sum of Root To Leaf Binary Numbers

The genral idea of this solution is to get a list of **all** the paths.
Then for each path get the sum in decimal by using the helper method bin_to_dec() and add path sum to totalSum
then return totalSum

```java
class Solution {
    public int sumRootToLeaf(TreeNode root) {
        
        ArrayList<String> pathList = new ArrayList<>();
        preorder(root, "", pathList);
        
        int totalSum = 0;
        for (String path : pathList) {
            totalSum += bin_to_dec(path);
        }
        
        return totalSum;
    }

    
    public void preorder(TreeNode node, String path, ArrayList<String> pathList) {
        if (node == null) return;
        if (node.left == null && node.right == null) pathList.add(path + node.val);
        preorder(node.left, path + node.val, pathList);
        preorder(node.right, path + node.val, pathList);
    }
    
    private int bin_to_dec(String path) {
        int total = 0;
        int pathLength = path.length();
        for (int i = 0; i < pathLength; i++) {
            total +=  Character.getNumericValue(path.charAt(i)) * Math.pow(2, (pathLength - 1 - i) );
        }
        
        return total;
    }
}
```
Let's make a slight improvement on the above code. Instead of using **all** that extra space to store the list
and then also looping over the entire list to call bin_to_dec() we will replace the list with a running 
sum:

```java
class Solution {
    public int sumRootToLeaf(TreeNode root) {
        return preorder(root, "", 0);
    }

    
    public int preorder(TreeNode node, String path, int num) {
        if (node == null) return 0;
        if (node.left == null && node.right == null) 
            return bin_to_dec(path + node.val);
        int pathsToLeft = preorder(node.left, path + node.val, num);
        int pathsToRight = preorder(node.right, path + node.val, num);
        return pathsToLeft + pathsToRight;
    }
    
    private int bin_to_dec(String path) {
        int result = 0;
        int pathLength = path.length();
        for (int i = 0; i < pathLength; i++) {
            result +=  Character.getNumericValue(path.charAt(i)) * Math.pow(2, (pathLength - 1 - i) );
        }
        return result;
    }
}
```
Just to make the `preorder()` a bit leaner let's replace
```java
int pathsToLeft = preorder(node.left, path + node.val, num);
int pathsToRight = preorder(node.right, path + node.val, num);
return pathsToLeft + pathsToRight;
```
with 
```java
return preorder(node.left, path + node.val, num) + preorder(node.right, path + node.val, num);
```

We saved some space and improved the length of the code. However there are still some inefficiences
.
1. We are constantly rebuilding a string to keep the path.
2. We are looping through the entire path so that we can convert binary to decimal.


Let's fix these inefficiencies! Instead of looping over the entire

If we can convert binary to decimal another way then we don't need the helper method at all and we don't either need the path.
Instead of converting bin to dec by finding the unit place of the value and then multiplying it by the appropriate power of 2.

We go down the tree keeping a running value of the path. When we hit a leaf we return that val and then continue our recursive method
with the value that it was before this leaf.
I found this better solution [here](https://leetcode.com/problems/sum-of-root-to-leaf-binary-numbers/discuss/270025/JavaC%2B%2BPython-Recursive-Solution). We can keep a running total of the decimal sum for this path by simply multipying running val
 by 2 and then adding the current root val `val * 2 + root.val`

 We end up with a solution that has a Time complexity of O(N) since we need to touch every node in the tree and Space complexity of O(H) for recursion
 since at any given point in time we have a linear call stack that doesn't exceed height of the tree.
```java
class Solution {
    public int sumRootToLeaf(TreeNode root) {
        return dfs(root, 0);
    }
    
    private int dfs(TreeNode root, int val){
        if(root == null) return 0;
        val = val * 2 + root.val;
        if(root.left == null && root.right == null){
            return val;
        } else {
            return dfs(root.left, val) + dfs(root.right, val);
        }
    }
}
```