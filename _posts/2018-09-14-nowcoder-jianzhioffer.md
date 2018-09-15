---
layout: post
title: 牛客练习题目
category: blog
tags: [Java, 剑指offer]
description: 链表倒数k个节点。
---

  
输入一个链表，输出该链表中倒数第k个结点。  

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(head==null||k<=0){
            return null;
        }
        ListNode pre=head;    
        for(int i=1;i<k;i++){
            if(pre.next!=null){
                pre=pre.next;
            }else{
                return null;
            }
        }
        while(pre.next!=null){
            pre = pre.next;
            head=head.next;
        }
        return head;
    }
}
```

说明：

两个指针，先让第一个指针和第二个指针都指向头结点，然后再让第一个指正走(k-1)步，到达第k个节点。然后两个指针同时往后移动，当第一个结点到达末尾的时候，第二个结点所在位置就是倒数第k个节点了。

注意：注意输入的坑点  
1.k值小于0的情况    
2.k大于链表节点个数，应该取null值  
3.先行节点走k-1步，达到第k个节点  
4.对pre.next!=null的判断，而不是对pre的判断，否则pre会取到null  
5.new新节点的时候注意数值的初始化（自己运行代码中的错误，与本题无关）
