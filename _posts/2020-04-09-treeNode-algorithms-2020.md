---
layout:     post
title:      数据结构及算法--链表
subtitle:    "\"list node\""
date:       2020-04-07
author:     Jimmy
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - ListNode
---

#### 双指针场景

```
package com.huawei.mrs.linkedList;

public class TwoPointer {
    /**
     * 环形链表
     * 给定一个链表，判断链表中是否有环。
     * 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。
     * 思路：
     * 使用快慢指针来遍历，快指针每次移动2步，慢指针每次移动一步，如果环存在且长度为M，那么M次迭代后，快指针必比慢指针多绕环一周
     */
    public boolean hasCycle(ListNode head) {
        ListNode walker = head;  //慢指针
        ListNode runner = head;  //快指针
        while (runner != null && runner.next != null) {
            walker = walker.next;
            runner = runner.next.next;
            if (walker == runner) {
                return true;
            }
        }
        return false;
    }

    /**
     * 环形链表 II
     * 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
     * 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。
     * 说明：不允许修改给定的链表。
     *
     * 主要思想是：1、用快慢指针先找到交叉点
     * 2、两个指针，一个从head开始遍历，一个从cross遍历，返回交叉点
     * @param head
     * @return
     */
    public ListNode detectCycle(ListNode head) {
        ListNode walker = head;
        ListNode runner = head;
        while (runner != null && runner.next != null) {
            walker = walker.next;
            runner = runner.next.next;
            if (walker == runner) {
                break;
            }
        }
        if (runner == null || runner.next == null) {
            return null;
        }
        ListNode headWalker = head;
        ListNode crossWalker = walker;
        while (headWalker != crossWalker) {
            headWalker = headWalker.next;
            crossWalker = crossWalker.next;
        }
        return headWalker;
    }

    /**
     * 求链表环的长度，如果不存在环，则返回0
     * 快慢指针找到交叉点后，继续从该交叉点开始遍历并计数，直到下次重新相遇
     * @param head
     * @return
     */
    public int cycleLen(ListNode head) {
        ListNode walker = head;
        ListNode runner = head;
        while (runner != null && runner.next != null) {
            walker = walker.next;
            runner = runner.next.next;
            if (walker == runner) {
                break;
            }
        }
        int len = 0;
        while (runner != null && runner.next != null) {
            len ++;
            walker = walker.next;
            runner = runner.next.next;
            if (walker == runner) {
                break;
            }
        }
        return len;
    }

    /**
     * 相交链表
     * 编写一个程序，找到两个单链表相交的起始节点。
     * 思路：主要是要解决两个链表长度不一致的问题，填充长度：A+B，B+A
     * @param headA
     * @param headB
     * @return
     */
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }
        ListNode pa = headA;
        ListNode pb = headB;
        while (pa != pb) {
            pa = pa == null ? headB : pa.next;
            pb = pb == null ? headA : pb.next;
        }
        return pa;
    }

    /**
     * 删除链表的倒数第N个节点
     * 给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。
     * 快指针先走n步，然后两个指针一起走，快指针到最后一个节点位置即可
     * @param head
     * @param n
     * @return
     */
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode first = head;
        ListNode second = head;
        while (n > 0) {
            second = second.next;
            n--;
        }
        if (second == null) {
            return head.next;
        }
        while (second.next != null) {
            first = first.next;
            second = second.next;
        }
        first.next = first.next.next;
        return head;
    }

}
```


#### 其他经典场景

```
package com.huawei.mrs.linkedList;

import java.util.Stack;

public class ClassicProblems {
    /**
     * 反转链表：反转一个单链表。
     * 解决方法：
     * 1、如果没有要求空间复杂度的做法的话，直接用stack，先遍历链表入栈，然后出栈构造新的链表
     * 2、如果要求空间复杂度为O（1），那就不停地将head的next节点移到head前即可
     * @param head
     * @return
     */
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode preNode = null;
        ListNode nextNode = null;
        while (head != null) {
            nextNode = head.next;
            head.next = preNode;
            preNode = head;
            head = nextNode;
        }
        return preNode;
    }

    /**
     * 移除链表元素：删除链表中等于给定值 val 的所有节点。
     * 解决思路：新建一个哑节点，next指向head；pre指向哑节点；pre和cur进行同步操作
     * @param head
     * @param val
     * @return
     */
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode curNode = head;
        ListNode preNode = dummy;
        while (curNode != null) {
            if (curNode.val == val) {
                preNode.next = curNode.next;
            } else {
                preNode = preNode.next;
            }
            curNode = curNode.next;
        }
        return dummy.next;
    }


    /**
     * 回文链表
     * 输入: 1->2
     * 输出: false
     *
     * 输入: 1->2->2->1
     * 输出: true
     * 两种实现方式：
     * （1）使用stack来存储链表的前半部分，然后遍历后半部分比较
     * @param head
     * @return
     */
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }
        ListNode walker = head;
        ListNode runner = head;
        Stack<ListNode> stack = new Stack<>();

        stack.push(walker);
        while (runner.next != null && runner.next.next != null) {
            walker = walker.next;
            runner = runner.next.next;
            stack.push(walker);
        }
        // 链表长度为偶数
        if (runner.next != null) {
            walker = walker.next;
        }
        while (walker != null) {
            if (walker.val != stack.pop().val) {
                return false;
            }
            walker = walker.next;
        }
        return true;
    }

    /**
     * 回文链表
     * 输入: 1->2
     * 输出: false
     *
     * 输入: 1->2->2->1
     * 输出: true
     * 两种实现方式：
     * （2）找到中点，然后反转链表的后半段，然后与前半段对比
     * @param head
     * @return
     */
    public boolean isPalindrome1(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }
        ListNode walker = head;
        ListNode runner = head;

        while (runner.next != null && runner.next.next != null) {
            walker = walker.next;
            runner = runner.next.next;
        }

        ListNode middle = walker;
        ListNode firstNode = middle.next;
        ListNode preNode = null;
        while (firstNode != null) {
            ListNode next = firstNode.next;
            firstNode.next = preNode;
            preNode = firstNode;
            firstNode = next;
        }
        walker = head;
        runner = preNode;

        while (runner != null) {
            if (runner.val != walker.val) {
                return false;
            }
            runner = runner.next;
            walker = walker.next;
        }
        return true;
    }


    /**
     * 合并两个有序链表
     * 输入：1->2->4, 1->3->4
     * 输出：1->1->2->3->4->4
     * 递归实现
     * @param l1
     * @param l2
     * @return
     */
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }

        ListNode head = null;
        if (l1.val <= l2.val) {
            head = l1;
            head.next = mergeTwoLists(l1.next, l2);
        } else {
            head = l2;
            head.next = mergeTwoLists(l1, l2.next);
        }
        return head;
    }

    /**
     * 两数相加
     * 输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
     * 输出：7 -> 0 -> 8
     * 原因：342 + 465 = 807
     * @param l1
     * @param l2
     * @return
     */
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }
        ListNode p = l1, q = l2;
        ListNode dummy = new ListNode(0);
        ListNode curNode = dummy;
        int carry = 0;
        while (p != null || q != null) {
            int val1 = (p != null) ? p.val : 0;
            int val2 = (q != null) ? q.val : 0;
            int sum = val1 + val2 + carry;
            carry = sum / 10;
            curNode.next = new ListNode(sum % 10);
            curNode = curNode.next;

            if (p != null) {
                p= p.next;
            }
            if (q != null) {
                q = q.next;
            }
        }
        if (carry > 0) {
            curNode.next = new ListNode(carry);
        }
        return dummy.next;
    }

    /**
     * 旋转链表
     * 给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。
     *
     * 输入: 1->2->3->4->5->NULL, k = 2
     * 输出: 4->5->1->2->3->NULL
     * 解释:
     * 向右旋转 1 步: 5->1->2->3->4->NULL
     * 向右旋转 2 步: 4->5->1->2->3->NULL
     *
     * 输入: 0->1->2->NULL, k = 4
     * 输出: 2->0->1->NULL
     * 解释:
     * 向右旋转 1 步: 2->0->1->NULL
     * 向右旋转 2 步: 1->2->0->NULL
     * 向右旋转 3 步: 0->1->2->NULL
     * 向右旋转 4 步: 2->0->1->NULL
     *
     * 解决思路：
     * 首先遍历链表得到链表长度，然后对k进行处理
     * 然后快慢指针，找到k的位置，进行操作
     *
     * @param head
     * @param k
     * @return
     */
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || k <= 0) {
            return head;
        }

        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode walker = dummy;
        ListNode runner = dummy;
        int len = 0;
        while (walker.next != null) {
            len ++;
            walker = walker.next;
        }

        walker = dummy;

        k = (len + (k % len)) % len;
        if (k == 0) {
            return dummy.next;
        }
        while (k > 0) {
            runner = runner.next;
            k--;
        }
        while (runner.next != null) {
            runner = runner.next;
            walker = walker.next;
        }

        dummy.next = walker.next;
        runner.next = head;
        walker.next = null;
        return dummy.next;
    }
}
```
