---
layout: post
title: 876. Middle of the Linked List - 3 Solutions.
---



### [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)


Given a non-empty, singly linked list with head node head, return a middle node of linked list.
If there are two middle nodes, return the second middle node.




```
Example 1:

Input: [1,2,3,4,5]
Output: Node 3 from this list (Serialization: [3,4,5])
The returned node has value 3.  (The judge's serialization of this node is [3,4,5]).
Note that we returned a ListNode object ans, such that:
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, and ans.next.next.next = NULL.
```

---

#### 1. One pass with storage: 
The first most inefficient way to do this would be to simply store all of the nodes in an arraylist and then return the middle value of the ArrayList
This solution requires O(n) space and O(n) time

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null) return head;
        ArrayList<ListNode> nodes = new ArrayList<>();
        
        while ( head != null) {
            nodes.add(head);
            head = head.next;
        }
        
        return nodes.get( nodes.size() / 2);
    }
}
```

---

#### 2. Two passes no storage:
Let's do better and get us to constant space. We will use the count method by making 2 passes through the list.
 The first pass we will count the amount of elements in the list by iterating through the entire list. Note, we must use a tmp pointer so as not to lose the reference to `head`.
 Then, in our second pass, we will move the pointer to the next node until we are at the middle node (size/2). We will move the pointer size/2 times.
This requires O(n) time and constant space.

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null) return head;
        int size = 0;
        
        ListNode tmp = head;
        while ( tmp != null) {
            size++;
            tmp = tmp.next;
        }
        for (int i = size/2; i > 0; i--)
            head = head.next;

        return head;
    }
}
```

---


#### 3. One pass no storage - Runner technique:
Let's do better and make only one pass through our list. Although this method results in only iterating O(n/2) nodes in terms of big-O it is still considered
to be O(n) like the previous solution. However, it is definitely more efficient. We will use the "runners technique". We maintain 2 pointers. A fast and a slow pointer.
The slow pointer moves one node at a time while the fast pointer moves two nodes at a time. So they will both start off at `head` we increment the slow 
one to `slow.next` and the fast one to `fast.next.next` (2 nodes forward.). When the fast pointer reaches the end of the list then the slow pointer
will be directly in the middle. Since it moves double as fast; slow will be at location: EndOfList / 2 which is the middle. Therefore, as soon as the fast
pointer reaches the end of the list we can return the `slow` pointer.
```java
class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null) return head;
        
        ListNode slow = head;
        ListNode fast = head;
        
        while (fast != null && fast.next != null) { // fast != null handle case when fast.next.next landed on a null.
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
}
```