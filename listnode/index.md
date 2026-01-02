# 算法-链表


# 链表

链表分带头结点的链表  和 不带头结点的链表；

所有的链表都有一个头指针head，带头结点的链表中head的数据项为空，而不带头的链表直接在头结点存入数据，那么当从头插入数据时，head需要时刻变化。

带头结点的单链表的的头结点一般数据域不存元素，指针域指向第一个结点，头指针（假设为pHead）指向头结点; 

不带头结点的单链表，头指针指向单链表的第一个结点;

如果把链表中的结点进行编号，带头结点的链表的头结点可以理解为是其第0个结点，头指针pHead指向头结点即第0个结点，不带头结点的指针指向第1个结点。

## 双指针技巧
### 删除排序链表中的重复元素
力扣第 83 题[「 删除排序链表中的重复元素」](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)

给定一个已排序的链表的头 head ， 删除所有重复的元素，使每个元素只出现一次 。返回 已排序的链表 。

```java
ListNode deleteDuplicates(ListNode head) {
    if (head == null) return null;
    ListNode slow = head, fast = head;
    while (fast != null) {
        if (fast.val != slow.val) {
            // nums[slow] = nums[fast];
            slow.next = fast;
            // slow++;
            slow = slow.next;
        }
        // fast++
        fast = fast.next;
    }
    // 断开与后面重复元素的连接
    slow.next = null;
    return head;
}
```

### 合并两个有序链表
这是最基本的链表技巧，力扣第 21 题 [「 合并两个有序链表」](https://leetcode.cn/problems/merge-two-sorted-lists/)
```java
class Solution {
    /**
     * 法1- 迭代
     * 参见讲解
     * https://www.bilibili.com/video/BV1pZ4y1Y76v?spm_id_from=333.337.search-card.all.click
     */
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {

        //定义新链表作为结果链表，首节点为哑结点，虚拟头结点
        ListNode dump = new ListNode(0);

        //定义新链表的当前节点，也可以理解为尾部节点，作为指针参与和其他数值做比较运算
        ListNode cur = dump;
        while (list1 != null && list2 != null) {
            // 比较 list1 和 list2 两个指针
            // 将值较小的的节点接到 cur 指针
            if (list2.val > list1.val) {
                cur.next = list1;
                list1 = list1.next;
            } else {
                cur.next = list2;
                list2 = list2.next;
            }
            // cur 指针不断前进
            cur = cur.next;
        }

        cur.next = (list1 == null) ? list2 : list1;
        return dump.next;
    }
}
```

```java
/**
 * 法2- 递归调用
 */
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        } else if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
```

### 单链表的分解
直接看下力扣第 86 题[「 分隔链表」：](https://leetcode.cn/problems/partition-list/)
```java
class Solution {
    public ListNode partition(ListNode head, int x) {

        // 存放小于 x 的链表的虚拟头结点
        ListNode dump1 = new ListNode(0);
        // 存放大于等于 x 的链表的虚拟头结点
        ListNode dump2 = new ListNode(0);

        // cur1, cur2 指针负责生成结果链表
        ListNode cur1 = dump1;
        ListNode cur2 = dump2;

        // 遍历原链表，类似合并两个有序链表的逻辑
        // 这里是将一个链表分解成两个链表
        while (head != null) {
            if (head.val < x) {
                cur1.next = head;
                cur1 = cur1.next;
            } else {
                cur2.next = head;
                cur2 = cur2.next;
            }
            // 断开原链表中的每个节点的 next 指针 ??? 没理解 ！！！
            ListNode tmp = head.next;
            head.next = null;
            head = tmp;
        }
        // 连接两个链表
        cur1.next = dump2.next;
        return dump1.next;
    }
}
```

### 合并 k 个有序链表
看下力扣第 23 题[「 合并K个升序链表」](https://leetcode.cn/problems/merge-k-sorted-lists/)
```java
class Solution {

    /**
     * 法1: 使用优先队列合并
     */
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }

        //使用排序队列
        PriorityQueue<ListNode> pq = new PriorityQueue<>(lists.length, (a, b) -> a.val - b.val);

        for (int i = 0; i < lists.length; i++) {
            if (lists[i] != null) {
                pq.add(lists[i]);
            }
        }

        ListNode dump = new ListNode(0);
        ListNode cur = dump;

        while (!pq.isEmpty()) {

            ListNode node = pq.poll();
            cur.next = node;
            cur = cur.next;

            if (node.next != null) {
                pq.add(node.next);
            }
        }
        return dump.next;
    }

    /**
     * 法2: 顺序合并
     */
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }
        ListNode ans = null;

        for (int i = 0; i < lists.length; i++) {
            // mergeTwoLists 参照合并两个有序链表的解法
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }
}
```

### （删除）单链表的倒数第 k 个节点
力扣第 19 题 [删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

进阶：你能尝试使用一趟扫描实现吗？

从前往后寻找单链表的第 k 个节点很简单，一个 for 循环遍历过去就找到了，但是如何寻找从后往前数的第 k 个节点呢？

那你可能说，假设链表有 n 个节点，倒数第 k 个节点就是正数第 n - k + 1 个节点，不也是一个 for 循环的事儿吗？

是的，但是算法题一般只给你一个 ListNode 头结点代表一条单链表，你不能直接得出这条链表的长度 n，而需要先遍历一遍链表算出 n 的值，然后再遍历链表计算第 n - k + 1 个节点。

也就是说，这个解法需要遍历两次链表才能得到出倒数第 k 个节点。

那么，我们能不能只遍历一次链表，就算出倒数第 k 个节点？可以做到的，如果是面试问到这道题，面试官肯定也是希望你给出只需遍历一次链表的解法。

这个解法就比较巧妙了，假设 k = 2，思路如下：

首先，我们先让一个指针 p1 指向链表的头节点 head，然后走 k 步：

现在的 p1，只要再走 n - k 步，就能走到链表末尾的空指针了对吧？

趁这个时候，再用一个指针 p2 指向链表头节点 head：

接下来就很显然了，让 p1 和 p2 同时向前走，p1 走到链表末尾的空指针时前进了 n - k 步，p2 也从 head 开始前进了 n - k 步，停留在第 n - k + 1 个节点上，即恰好停链表的倒数第 k 个节点上：

[详细解释 refer to](https://labuladong.github.io/algo/2/19/18/)

```java
class Solution {

    /**
     * 法1：双指针，单次链表遍历
     */
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // 虚拟头结点
        ListNode dump = new ListNode(0);
        //注意此处
        dump.next = head;

        // 删除倒数第 n 个，先找倒数第 n + 1 个节点 [注意此处 为n+1]
        ListNode objNode = findFromEnd(dump, n + 1);
        // 删掉倒数第 n 个节点
        objNode.next = objNode.next.next;

        return dump.next;
    }

    // 返回链表的倒数第 n 个节点
    public ListNode findFromEnd(ListNode head, int n) {
        ListNode p1 = head;
        // p1 先走 n 步
        for (int i = 0; i < n; i++) {
            p1 = p1.next;
        }

        ListNode p2 = head;
        // p1 和 p2 同时走 len - k 步
        while (p1 != null) {
            p1 = p1.next;
            p2 = p2.next;
        }
        // p2 现在指向第 len - n + 1 个节点，即倒数第 n 个节点
        return p2;
    }


    /**
     * 法2：2次链表遍历； 先循环查询链表总长度，在走到特定节点，操作；
     */
    public ListNode removeNthFromEnd2(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        int length = getLength(head);
        ListNode cur = dummy;
        for (int i = 1; i < length - n + 1; ++i) {
            cur = cur.next;
        }
        cur.next = cur.next.next;
        //注意：此处return不能是cur.next，原因？
        return dummy.next;
//        ListNode ans = dummy.next;
//        return ans;
    }

    public int getLength(ListNode head) {
        int length = 0;
        while (head != null) {
            ++length;
            head = head.next;
        }
        return length;
    }
}
```

### 单链表的中点 [错题]
力扣第 876 题[「 链表的中间结点」](https://leetcode.cn/problems/middle-of-the-linked-list/)

问题的关键也在于我们无法直接得到单链表的长度 n，常规方法也是先遍历链表计算 n，再遍历一次得到第 n / 2 个节点，也就是中间节点。

如果想一次遍历就得到中间节点，也需要耍点小聪明，使用「快慢指针」的技巧：

我们让两个指针 slow 和 fast 分别指向链表头结点 head。

每当慢指针 slow 前进一步，快指针 fast 就前进两步，这样，当 fast 走到链表末尾时，slow 就指向了链表中点。
```java
class Solution {
    ListNode middleNode(ListNode head) {
        // 快慢指针初始化指向 head
        ListNode slow = head, fast = head;
        // 快指针走到末尾时停止
        while (fast != null && fast.next != null) {
            // 慢指针走一步，快指针走两步
            slow = slow.next;
            fast = fast.next.next;
        }
        // 慢指针指向中点
        return slow;
    }
}

```

需要注意的是，如果链表长度为偶数，也就是说中点有两个的时候，我们这个解法返回的节点是靠后的那个节点。

另外，这段代码稍加修改就可以直接用到判断链表成环的算法题上。

### 判断链表是否包含环
```java
boolean hasCycle(ListNode head) {
    // 快慢指针初始化指向 head
    ListNode slow = head, fast = head;
    // 快指针走到末尾时停止
    while (fast != null && fast.next != null) {
        // 慢指针走一步，快指针走两步
        slow = slow.next;
        fast = fast.next.next;
        // 快慢指针相遇，说明含有环
        if (slow == fast) {
            return true;
        }
    }
    // 不包含环
    return false;
}

```

当然，这个问题还有进阶版：如果链表中含有环，如何计算这个环的起点？

### 链表包含环的起点
```java
ListNode detectCycle(ListNode head) {
    ListNode fast, slow;
    fast = slow = head;
    while (fast != null && fast.next != null) {
        fast = fast.next.next;
        slow = slow.next;
        if (fast == slow) break;
    }
    // 上面的代码类似 hasCycle 函数
    if (fast == null || fast.next == null) {
        // fast 遇到空指针说明没有环
        return null;
    }

    // 重新指向头结点
    slow = head;
    // 快慢指针同步前进，相交点就是环起点
    while (slow != fast) {
        fast = fast.next;
        slow = slow.next;
    }
    return slow;
}

```
可以看到，当快慢指针相遇时，让其中任一个指针指向头节点，然后让它俩以相同速度前进，再次相遇时所在的节点位置就是环开始的位置。

为什么要这样呢？这里简单说一下其中的原理。

我们假设快慢指针相遇时，慢指针 slow 走了 k 步，那么快指针 fast 一定走了 2k 步：

### 两个链表是否相交

力扣第 160 题[「 相交链表」](https://leetcode.cn/problems/intersection-of-two-linked-lists/)
```java
public class Solution {
    /**
     * 方法1 ： 双指针 On (a+b)
     */
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {

        // p1 指向 A 链表头结点，p2 指向 B 链表头结点
        ListNode p1 = headA, p2 = headB;

        while (p1 != p2) {
            // p1 走一步，如果走到 A 链表末尾，转到 B 链表
            if (p1 == null) {
                p1 = headB;
            } else {
                p1 = p1.next;
            }

            // p2 走一步，如果走到 B 链表末尾，转到 A 链表
            if (p2 == null) {
                p2 = headA;
            } else {
                p2 = p2.next;
            }
        }
        return p1;
    }

    /**
     * 方法2 哈希
     */
    public ListNode getIntersectionNode2(ListNode headA, ListNode headB) {

        Set<ListNode> set = new HashSet<>();

        ListNode temp = headA;
        while (temp != null) {
            set.add(temp);
            temp = temp.next;
        }

        temp = headB;
        while (temp != null) {
            if (set.contains(temp)) {
                return temp;
            }
            temp = temp.next;
        }
        return null;
    }
}
```

### 回文链表-E
力扣第 234 题[回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

给你一个单链表的头节点 head ，请你判断该链表是否为回文链表。如果是，返回 true ；否则，返回 false 。
```java
class Solution {
    /**
    * 1. 链表遍历 值存list
    * 2. 对list 进行左右双指针 回文判断
    */
    public boolean isPalindrome(ListNode head) {
        List<Integer> vals =new ArrayList<Integer>();
        ListNode dump = head;
        while(dump!=null){
            vals.add(dump.val);
            //注意此处赋值为 dump.next
            dump = dump.next;
        }

        int left=0, right=vals.size()-1;
        while(left<right){
             if(!vals.get(left).equals(vals.get(right))){
                return false;
             }
             left++;
             right--;
        }
        return true;
    }
}
```

### 环形链表-E
力扣第 141 题[环形链表](https://leetcode.cn/problems/linked-list-cycle/)

给你一个链表的头节点 head ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。
```java
public class Solution {

    /**
     * 法1： 哈希
     */
    public boolean hasCycle(ListNode head) {
        Set<ListNode> seen = new HashSet<ListNode>();
        while (head != null) {
            if (!seen.add(head)) {
                return true;
            }
            head = head.next;
        }
        return false;
    }

    /**
     * 法2：双指针
     */
    public boolean hasCycle2(ListNode head) {
        if (head == null || head.next == null) {
            return false;
        }
        ListNode slow = head;
        //注意：此处fast定义
        ListNode fast = head.next;
        //注意：循环条件
        while (slow != fast) {
            //注意：此处判断条件
            if (fast == null || fast.next == null) {
                return false;
            }
            slow = slow.next;
            fast = fast.next.next;
        }
        return true;
    }
}
```

## 递归魔法：反转单链表
什么叫反转单链表的一部分呢，就是给你一个索引区间，让你把单链表中这部分元素反转，其他部分不变。

看下力扣第 92 题[「 反转链表 II」：](https://leetcode.cn/problems/reverse-linked-list-ii/)


### 递归反转整个链表 【太绕，没理解！！】
这也是力扣第 206 题[「 反转链表」](https://leetcode.cn/problems/reverse-linked-list/)

可以使用递归的3个条件：
1. 大问题拆成2个子问题。
2. 子问题求解方式和大问题一样。
3. 存在最小子问题。

```java
class Solution {
    /**
     * 方法1： 迭代反转
     * 单链表反转视频讲解： https://leetcode.cn/problems/reverse-linked-list/solution/shi-pin-jiang-jie-die-dai-he-di-gui-hen-hswxy/
     */
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            //next 为临时变量，做数据临时存储
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }


    /**
     * 方法2：递归反转
     * 视频讲解 https://leetcode.cn/problems/reverse-linked-list/solution/shi-pin-jiang-jie-die-dai-he-di-gui-hen-hswxy/
     */
    public ListNode reverseList2(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode last = reverseList2(head.next);
        head.next.next = head;
        head.next = null;
        return last;

    }
}

```
### 反转链表前 N 个节点【没看懂！！】

解决思路和反转整个链表差不多，只要稍加修改即可：
```java
ListNode successor = null; // 后驱节点

// 反转以 head 为起点的 n 个节点，返回新的头结点
ListNode reverseN(ListNode head, int n) {
    if (n == 1) {
        // 记录第 n + 1 个节点
        successor = head.next;
        return head;
    }
    // 以 head.next 为起点，需要反转前 n - 1 个节点
    ListNode last = reverseN(head.next, n - 1);

    head.next.next = head;
    // 让反转之后的 head 节点和后面的节点连起来
    head.next = successor;
    return last;
}

```


### 反转链表的一部分
[refer to](https://labuladong.github.io/algo/2/19/19/)


## 总结
递归的思想相对迭代思想，稍微有点难以理解，处理的技巧是：不要跳进递归，而是利用明确的定义来实现算法逻辑。

处理看起来比较困难的问题，可以尝试化整为零，把一些简单的解法进行修改，解决困难的问题。

值得一提的是，递归操作链表并不高效。和迭代解法相比，虽然时间复杂度都是 O(N)，但是迭代解法的空间复杂度是 O(1)，而递归解法需要堆栈，空间复杂度是 O(N)。

所以递归操作链表可以作为对递归算法的练习或者拿去和小伙伴装逼，但是考虑效率的话还是使用迭代算法更好。


## K 个一组翻转链表
力扣第 25 题[「 K 个一组翻转链表」](https://leetcode.cn/problems/reverse-nodes-in-k-group/)
[refet to](https://labuladong.github.io/algo/2/19/20/)


# 其他
## 删除链表中的节点

力扣第 237 题[删除链表中的节点](https://leetcode.cn/problems/delete-node-in-a-linked-list/)

请编写一个函数，用于 删除单链表中某个特定节点 。在设计函数时需要注意，你无法访问链表的头节点 head ，只能直接访问 要被删除的节点 。

题目数据保证需要删除的节点 不是末尾节点 。
```java
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

