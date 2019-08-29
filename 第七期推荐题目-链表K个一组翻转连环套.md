## 推荐题目

> 其实是来自一道今日头条面试的进阶题目,最后会提到



这次来推荐一类题目,链表K个一组翻转问题,比如两个一组翻转:

```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```

再进一步比如3个一组翻转

```
给定这个链表：1->2->3->4->5

当 k = 2 时，应当返回: 2->1->4->3->5

当 k = 3 时，应当返回: 3->2->1->4->5
```



| |      |      |
| ------------------------------------------------------------ | ---- | ---- |
| `24. Swap Nodes in Pairs` | <https://leetcode.com/problems/swap-nodes-in-pairs/> | |
| `24. 两两交换链表中的节点` | <https://leetcode-cn.com/problems/swap-nodes-in-pairs/> | |
|                                 `25. Reverse Nodes in k-Group`                             |  < https://leetcode.com/problems/reverse-nodes-in-k-group/>   |  |
| `25. K 个一组翻转链表` | <https://leetcode-cn.com/problems/reverse-nodes-in-k-group/> |      |



### 推荐思考

如何用最简代码实现这个问题.是否有链表常用的一些工具类方法可以记住?

## 模版



### 反转链表模板

1->2-3  翻转为3->2-1.

这个反转方法不多说了,基本操作,很多链表题都会用到,建议可以随手写出.

```java
 ListNode[] reverse(ListNode head) {
      
        ListNode pre = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
     
        return pre;

    }
```

### 反转链表模板升级

`res[0]`返回初始头节点,`res[1]`返回最终头节点.在一些题中有很大用处.

```java
 ListNode[] reverse(ListNode head) {
        ListNode[] res = new ListNode[2];
        res[0] = head;
        ListNode pre = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        res[1]=pre;
        return res;

    }
```



### 解决2个翻转

可以解决K个翻转问题,

我们先看2个一组翻转.

> 这有个hit是,我们经常需要删除链表第一个节点/翻转第一个节点.可以我们经常定义一个dummy(虚假)节点,dummy.next=head.这样我们可以方便的进行一些操作,把所有节点当成非头节点处理.
>
> 最后返回dummy.next



dummy(pre)->1->2->3-4的操作过程如下:

头结点开始

dummy(pre)->1(当前)->2->3->4

向后走一步

dummy(pre)->1->2(当前)->3(next)->4

截断,保存next

dummy(pre)->1->2(当前)->null     3(next)->4

反转链表

dummy(pre)->1    null<-1<-2(当前)     3(next)->4

连接

dummy(pre)    2(当前) ->1->3(next)->4

连接

dummy(pre)->2(当前) ->1->3(next)->4

移动pre

dummy->2->1(pre)->3(next)->4  

移动

dummy->2->1->3(当前)->4

继续操作



```java
public ListNode swapPairs(ListNode head) {
        //边界条件判断
        if (head == null || head.next == null) return head;
        // 至少有两个节点
        ListNode dummy = new ListNode(-1);
        dummy.next = head;

        ListNode t = head;
        ListNode pre = dummy;
        int i = 1;

        while (t != null) {
            if (i % 2 == 0) {
                //保存后一个节点
                ListNode next = t.next;
                t.next = null;
                ListNode[] res = reverse(pre.next);

                res[0].next = next;
                pre.next = res[1];
                pre = res[0];
                
                //继续移动
                t = next;
                i++;

            } else {
                t = t.next;
                i++;
            }
        }


        return dummy.next;
    }
  // 反转工具方法,熟练到随手可写
    ListNode[] reverse(ListNode head) {
        ListNode[] res = new ListNode[2];
        res[0] = head;
        ListNode pre = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        res[1]=pre;
        return res;

    }
```

### 解决K个翻转

上面的代码,只要你传入k,再` if (i % 2 == 0) `改为`if (i % k == 0`就可以解决翻转k的问题.







## 今日头条的一道进阶题

这其实是一道**变形**的链表反转题，大致描述如下

给定一个单链表的头节点 head,实现一个调整单链表的函数，使得每K个节点之间为一组进行逆序，**并且从链表的尾部开始组起**，头部剩余节点数量不够一组的不需要逆序。（不能使用队列或者栈作为辅助）

例如： 链表:1->2->3->4->5->6->7->8->null, K = 3。那么 6->7->8，3->4->5，1->2各位一组。

调整后：1->2->5->4->3->8->7->6->null。其中 1，2不调整，因为不够一组。



这道题的难点在于，是从链表的尾部开始组起的，而不是从链表的**头部**，如果是头部的话，那我们还是比较容易做的，因为你可以遍历链表，每遍历 k 个就拆分为一组来逆序。



**但其实这道题一点不难**



1、先进行逆序

8->7->6->5->4->3->2->1->null

2、 K=3个一组翻转

6->7->8->3->4->5->2->1->null

3、再进行逆序

1->2->5->4->3->8->7->6->null



只是多进行两次逆序而已,我们已经写好了反转链表工具方法!

其余和上边的一样.

```java
public ListNode solve(ListNode head, int k) {
    // 调用逆序函数
    head = reverse(head);
    // 调用每 k 个为一组的逆序函数（从头部开始组起）
    head = reverseKGroup(head, k);
    // 在逆序一次
    head = reverse(head);
    return head;
    
}

```

