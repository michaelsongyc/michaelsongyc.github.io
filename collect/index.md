# 算法-搜集


## 1-容易-两数之和
1-容易-两数之和

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer/*value*/, Integer/*index*/> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            // map.put(nums[i], i); 一定放到if后面，不然就会可能存在 mums[i]+i=target, i是自己
            map.put(nums[i], i);
        }
        return null;
    }
}
```
## 2-中等-两数相加
2-中等-两数相加

给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        
        //定义一个新联表伪指针，用来指向头指针，返回结果
        ListNode prev = new ListNode(0);
        //定义一个进位数的指针，用来存储当两数之和大于10的时候，
        int carry = 0;
        //定义一个可移动的指针，用来指向存储两个数之和的位置
        ListNode cur = prev;
        //当l1 不等于null或l2 不等于空时，就进入循环
        while (l1 != null || l2 != null) {
            //如果l1 不等于null时，就取他的值，等于null时，就赋值0，保持两个链表具有相同的位数
            int x = l1 != null ? l1.val : 0;
            //如果l1 不等于null时，就取他的值，等于null时，就赋值0，保持两个链表具有相同的位数
            int y = l2 != null ? l2.val : 0;
            //将两个链表的值，进行相加，并加上进位数
            int sum = x + y + carry;
            //计算进位数
            carry = sum / 10;
            //计算两个数的和，此时排除超过10的请况（大于10，取余数）
            sum = sum % 10;
            //将求和数赋值给新链表的节点，
            //注意这个时候不能直接将sum赋值给cur.next = sum。这时候会报，类型不匹配。
            //所以这个时候要创一个新的节点，将值赋予节点
            cur.next = new ListNode(sum);
            //将新链表的节点后移
            cur = cur.next;
            //当链表l1不等于null的时候，将l1 的节点后移
            if (l1 != null) {
                l1 = l1.next;
            }
            //当链表l2 不等于null的时候，将l2的节点后移
            if (l2 != null) {
                l2 = l2.next;
            }
        }
        //如果最后两个数，相加的时候有进位数的时候，就将进位数，赋予链表的新节点。
        //两数相加最多小于20，所以的的值最大只能时1
        if (carry == 1) {
            cur.next = new ListNode(carry);
        }
        //返回链表的头节点
        return prev.next;
        
    }
}
```
## 3-中等-无重复字符的最长子串
3-中等-无重复字符的最长子串

给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。
```java
class Solution {
        /**
     * 滑动窗口
     */
    public int lengthOfLongestSubstring(String s) {

        Map<Character, Integer> windowMap = new HashMap<>();

        int left = 0, right = 0;
        int max = 0;
        while (right < s.length()) {
            // c 是将移入窗口的字符
            char c = s.charAt(right);
            // 扩大窗口
            right++;

            // 判断左侧窗口是否要收缩 [注意此处为 p.length()]
            windowMap.put(c, windowMap.getOrDefault(c, 0) + 1);
            while (windowMap.get(c) > 1) {
                // d 是将移出窗口的字符
                char d = s.charAt(left);
                // 缩小窗口
                left++;
                // 进行窗口内数据的一系列更新
                windowMap.put(d, windowMap.getOrDefault(d, 0) - 1);
            }
            max = Math.max(max, right - left);
        }
        return max;
    }
    // public int lengthOfLongestSubstring(String s) {

    //     int ans = 0;
	// 	int len = s.length();
	// 	Map<Character, Integer> map = new HashMap<>();
	// 	for (int i = 0, j = 0; j < len; j++) {
    //         //判断这个字母是否在map中。
	// 		if(map.containsKey(s.charAt(j))) {
	// 			//如果在，说明重复了，需要移动i了，将i移动到   s.charAt(j)  这个字母的下一位。
    //             //因为map中的key字母可以重复，所以，查找最大的i保证了i是在最右边。
	// 			i = Math.max(map.get(s.charAt(j)), i);
	// 		}
	// 		//如果不再，说明这位也是字符串的字母，需要加一。
	// 		ans = Math.max(ans, j-i+1);
	// //将键值对写入map，key是字母，位置是value。不用管重复的问题，只需要移动i到合适的位置即可。
	// 		map.put(s.charAt(j), j+1);
	// 	}
	// 	return ans;

 
    // }
}
```
## 5-中等-最长的回文子串
5-中等-最长的回文子串

给你一个字符串 s，找到 s 中最长的回文子串。
```java
class Solution {
     /**
     *    中心扩展算法
     *     找到以 s[i] 为中心的回文串
     *     找到以 s[i] 和 s[i+1] 为中心的回文串
     *     更新答案
     */
    public String longestPalindrome(String s) {
        String maxStr = "";
        for (int i = 0; i < s.length(); i++) {
            // 以 s[i] 为中心的最长回文子串
            String tmp = queryLongStr(s, i, i);
            maxStr = maxStr.length() > tmp.length() ? maxStr : tmp;

            // 以 s[i] 和 s[i+1] 为中心的最长回文子串
            tmp = queryLongStr(s, i, i + 1);
            maxStr = maxStr.length() > tmp.length() ? maxStr : tmp;
        }
        return maxStr;
    }

    // 在 s 中寻找以 s[l] 和 s[r] 为中心的最长回文串
    public String queryLongStr(String s, int l, int r) {
        // 防止索引越界
        while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)) {
            // 双指针，向两边展开
            l--;
            r++;
        }
        // 返回以 s[l] 和 s[r] 为中心的最长回文串
        return s.substring(l + 1, r);
    }
}
```
## 7-中等-整数反转
7-中等-整数反转

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。

假设环境不允许存储 64 位整数（有符号或无符号）。
```java
class Solution {
    public int reverse(int x) {
        int result = 0;
        while (x != 0) {
            //边界值判定，防止integer溢出
            if (result > Integer.MAX_VALUE / 10 || result < Integer.MIN_VALUE / 10) {
                return 0;
            }
            int n = x % 10;
            result = result * 10 + n;
            x = x / 10;
        }
        return result;
    }
}
```
## 8-简单-字符串转换整数 (atoi)
8-简单-字符串转换整数 (atoi)

请你来实现一个 myAtoi(string s) 函数，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的 atoi 函数）。

函数 myAtoi(string s) 的算法如下：

读入字符串并丢弃无用的前导空格
检查下一个字符（假设还未到字符末尾）为正还是负号，读取该字符（如果有）。 确定最终结果是负数还是正数。 如果两者都不存在，则假定结果为正。
读入下一个字符，直到到达下一个非数字字符或到达输入的结尾。字符串的其余部分将被忽略。
将前面步骤读入的这些数字转换为整数（即，"123" -> 123， "0032" -> 32）。如果没有读入数字，则整数为 0 。必要时更改符号（从步骤 2 开始）。
如果整数数超过 32 位有符号整数范围 [−231,  231 − 1] ，需要截断这个整数，使其保持在这个范围内。具体来说，小于 −231 的整数应该被固定为 −231 ，大于 231 − 1 的整数应该被固定为 231 − 1 。
返回整数作为最终结果。
注意：

本题中的空白字符只包括空格字符 ' ' 。
除前导空格或数字后的其余字符串外，请勿忽略 任何其他字符。
```java
class Solution {
    /**
     * 1.删除开头空白字符
     * 2.排除全部为空的极端case
     * 3.设置符号
     * 4.超出integer上下边界处理
     */
    public int myAtoi(String s) {
        char[] arrays = s.toCharArray();
        int index = 0;
        // 1、去除前导空格
        while (index < arrays.length && arrays[index] == ' ') {
            index++;
        }

        // 2、如果已经遍历完成（针对极端用例 "      "）
        if (index == arrays.length) {
            return 0;
        }

        //3.判断符号, 如果出现符号字符，仅第 1 个有效，并记录正负
        int sign = 1;
        char firstChar = arrays[index];
        if (firstChar == '+') {
            index++;
        } else if (firstChar == '-') {
            sign = -1;
            index++;
        }

        // 4、将后续出现的数字字符进行转换
        // 不能使用 long 类型，这是题目说的
        int result = 0;
        while (index < arrays.length) {
            char currChar = arrays[index];
            // 4.1 先判断不合法的情况 如"+-42"
            if (currChar < '0' || currChar > '9') {
                break;
            }
            //integer 边界值判断
            if (result > Integer.MAX_VALUE / 10 ||
                    (result == Integer.MAX_VALUE / 10 && (currChar - '0') > Integer.MAX_VALUE % 10)) {
                return Integer.MAX_VALUE;
            }
            //注意为 -(Integer.MIN_VALUE % 10)  而不是 -Integer.MIN_VALUE % 10 ; 需要加括号
            if (result < Integer.MIN_VALUE / 10 ||
                    (result == Integer.MIN_VALUE / 10 && (currChar - '0') > -(Integer.MIN_VALUE % 10))) {
                return Integer.MIN_VALUE;
            }

            //注意 (currChar-'0')
            result = result * 10 + (currChar - '0') * sign;
            index++;
        }

        return result;
    }
}
```
## 9-中等-回文数
9-中等-回文数

给你一个整数 x ，如果 x 是一个回文整数，返回 true ；否则，返回 false 。

回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

例如，121 是回文，而 123 不是。
```java
class Solution {
    /**
     * (反转一半数字) 取出后半段数字进行翻转。
     * 为了避免数字反转可能导致的溢出问题，为什么不考虑只反转 \text{int}int 数字的一半？毕竟，如果该数字是回文，其后半部分反转后应该与原始数字的前半部分相同。
     * <p>
     * 这里需要注意的一个点就是由于回文数的位数可奇可偶，所以当它的长度是偶数时，它对折过来应该是相等的；当它的长度是奇数时，那么它对折过来后，有一个的长度需要去掉一位数（除以 10 并取整）。
     * <p>
     * 具体做法如下：
     * <p>
     * 每次进行取余操作 （ %10），取出最低的数字：y = x % 10
     * 将最低的数字加到取出数的末尾：revertNum = revertNum * 10 + y
     * 每取一个最低位数字，x 都要自除以 10
     * 判断 x 是不是小于 revertNum ，当它小于的时候，说明数字已经对半或者过半了
     * 最后，判断奇偶数情况：如果是偶数的话，revertNum 和 x 相等；如果是奇数的话，最中间的数字就在revertNum 的最低位上，将它除以 10 以后应该和 x 相等。
     * 链接：https://leetcode.cn/problems/palindrome-number/solution/dong-hua-hui-wen-shu-de-san-chong-jie-fa-fa-jie-ch/
     */
    public boolean isPalindrome(int x) {
        // 特殊情况：
        // 如上所述，当 x < 0 时，x 不是回文数。
        // 同样地，如果数字的最后一位是 0，为了使该数字为回文，
        // 则其第一位数字也应该是 0
        // 只有 0 满足这一属性
        if (x < 0 || (x != 0 && x % 10 == 0)) {
            return false;
        }
        int reverseNum = 0;
        // 由于整个过程我们不断将原始数字除以 10，然后给反转后的数字乘上 10，所以，当原始数字小于或等于反转后的数字时，就意味着我们已经处理了一半位数的数字了。
        while (x > reverseNum) {
            reverseNum = reverseNum * 10 + x % 10;
            x /= 10;
        }

        // 当数字长度为奇数时，我们可以通过 revertedNumber/10 去除处于中位的数字。
        // 例如，当输入为 12321 时，在 while 循环的末尾我们可以得到 x = 12，revertedNumber = 123，
        // 由于处于中位的数字不影响回文（它总是与自己相等），所以我们可以简单地将其去除。
        return x == reverseNum || x == reverseNum / 10;
    }

    /**
     * 简单粗暴，看看就行 (不可取)
     */
    public boolean isPalindrome2(int x) {
        String compare = (new StringBuilder(x + "")).reverse().toString();
        return (x + "").equals(compare);
    }
}
```
## 11-中等-盛最多水的容器
11-中等-盛最多水的容器

给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

说明：你不能倾斜容器。
```java
//    方法1：暴力解法
//    双for循环，时间复杂度o（n方），空间复杂度n；
//    耗时严重，废弃；
//    public int maxArea(int[] height) {
//        int max = 0;
//        for (int i = 0; i < height.length; i++) {
//            for (int j = i+1; j < height.length; j++) {
//                max = Math.max(max, (j - i) * Math.min(height[i],height[j]));
//            }
//        }
//        return max;
//    }

    /**
     *  双指针法， 源于数学公示
     * @param height
     * @return
     */
    public int maxArea(int[] height) {
        int l = 0, r = height.length - 1;
        int result = 0;
        while (l < r) {
            int area = (r - l) * Math.min(height[l], height[r]);
            result = Math.max(result, area);
            if (height[l] <= height[r]) {
                l++;
            } else {
                r--;
            }
        }
        return result;
    }
```
## 14-简单-最长公共前缀
14-简单-最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。
```java
class Solution {
    /**
     * 横向扫描
     */
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        //取数组第1个值作为预比较对象
        String preStr = strs[0];
        //注意此处循环 从1开始；
        for (int i = 1; i < strs.length; i++) {
            preStr = compare(strs[i], preStr);
            if (preStr.length() == 0) {
                break;
            }
        }
        return preStr;
    }

    public String compare(String s1, String s2) {
        int len = Math.min(s1.length(), s2.length());
        int index = 0;
        while (index < len && s1.charAt(index) == s2.charAt(index)) {
            index++;
        }
        return s1.substring(0, index);
    }
}
```
## 19-中等-单链表的倒数第 k 个节点
19-中等-单链表的倒数第 k 个节点

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。
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
## 20-简单-有效的括号
20-简单-有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
每个右括号都有一个对应的相同类型的左括号。
```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '{' || c == '[') {
                stack.push(c);
            } else {
                // 字符 c 是右括号
                if (!stack.isEmpty() && stack.peek() == queryMatch(c)) {
                    stack.pop();
                }else{
                     // 和最近的左括号不匹配
                     return false;
                }
            }
        }
        // 是否所有的左括号都被匹配了
        return stack.isEmpty();
    }

    public Character queryMatch(Character c) {
        if (c == ')') {
            return '(';
        }
        if (c == ']') {
            return '[';
        }
        if (c == '}') {
            return '{';
        }
        return null;
    }
}
```
## 21-简单-合并两个有序链表
21-简单-合并两个有序链表

将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。
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
## 23-困难-合并 k 个有序链表
23-困难-合并 k 个有序链表

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。
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
## 27-简单-移除元素
27-简单-移除元素

给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。
```java
class Solution {
    /**
     * 移动完成后， nums[0]-nums[slow-1] 之间不包含val；  nums[slow]-nums[nums.Length-1] 之间包含val  
     */
    public int removeElement(int[] nums, int val) {
        int slow = 0 ;
        int len = nums.length;
        for (int i = 0; i < len; i++) {
            if (nums[i] != val) {
                // nums[slow] = nums[i];
                // slow++;
                //等同于上面
                nums[slow++] = nums[i];
            }
        }
        return slow;
    }
}
```
## 28-简单-实现 strStr()
28-简单-实现 strStr()

给你两个字符串haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回 -1 。
```java
class Solution {

    /**
     * 法1： 朴素解法-匹配-代码优化
     */
    public int strStr(String haystack, String needle) {
        int len = haystack.length();
        int lengthNeeded = needle.length();
        for (int i = 0; i <= len - lengthNeeded; i++) {
            int a = i;
            int b = 0;
            //注意边界判定 b<lengthNeeded
            while (b<lengthNeeded && haystack.charAt(a) == needle.charAt(b)) {
                a++;
                b++;
            }
            if (b == lengthNeeded) {
                return i;
            }
        }
        return -1;
    }


    /**
     * KMP 解法
     */
    public int strStr2(String haystack, String needle) {
        
    }

    /**
     * 暴力解法
     */
    public int strStr3(String haystack, String needle) {
        if (needle.length() == 0 || needle.length() > haystack.length()) {
            return -1;
        }
        int objLength = needle.length();
        int left = 0, right = left + objLength - 1;

        while (right < haystack.length()) {
            if (compare(haystack, needle, left, right)) {
                break;
            }
            left++;
            right = left + objLength - 1;

        }
        return left != -1 ? left : -1;
    }

    public boolean compare(String haystack, String needle, int start, int end) {
        int i = 0;
        boolean result = true;
        while (start <= end) {
            if (haystack.charAt(start++) != needle.charAt(i++)) {
                result = false;
                break;
            }
        }
        return result;
    }
}
```
## 34-中等-在排序数组中查找元素的第一个和最后一个位置
34-中等-在排序数组中查找元素的第一个和最后一个位置

给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。
```java
class Solution {
    /**
     * 先找>=target的第一个
     * 再找>target的第一个
     * 我真是这辈子都不想看见这题
     */
    public int[] searchRange(int[] nums, int target) {
        //先找>=target的第一个
        int l = search(nums, target);
        //再找>target的第一个
        int r = search(nums, target + 1);
        if (l == nums.length || nums[l] != target) {
            return new int[]{-1, -1};
        }
        //注意此处 r-1
        return new int[]{l, r - 1};
    }

    /**
     * 找>=target的第一个
     */
    public int search(int[] nums, int target) {
        int l = 0, r = nums.length;
        while (l < r) {
            int mid = (r + l) >> 1;
            if (nums[mid] >= target)
                r = mid;
            else
                l = mid + 1;
        }
        return l;
    }
}
```
## 35-简单-搜索插入位置
35-简单-搜索插入位置

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 O(log n) 的算法。
```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            // 特别注意，此处需要+left
            int middle = (right - left) / 2 + left;
            if (nums[middle] > target) {
                right = middle - 1;
            } else if (nums[middle] == target) {
                return middle;
            } else if (nums[middle] < target) {
                left = middle + 1;
            }
        }
        //注意此处：返回值是left，想想为什么？
        return left;
    }
}
```
## 66-简单-加一
66-简单-加一

给定一个由 整数 组成的 非空 数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。
```java
class Solution {
    /**
     * 不用纠结某一位是不是9，而应该去判断加1之后是不是0
     */
    public int[] plusOne(int[] digits) {
        int len = digits.length;
        for (int i = len - 1; i >= 0; i--) {
            //每一位数，加1
            digits[i] = (digits[i] + 1) % 10;
            //若为+1后为0，则进行下一循环，继续+1
            if (digits[i] != 0) {
                return digits;
            }
        }
        //此时，digits内数值已均为0
        //对所有为均为9的特殊情况
        digits = new int[len + 1];
        digits[0] = 1;
        return digits;
    }
}
```
## 83-简单-删除排序链表中的重复元素
83-简单-删除排序链表中的重复元素

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
## 86-中等-单链表的分解
86-中等-单链表的分解

给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。

你应当 保留 两个分区中每个节点的初始相对位置。
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
## 92-中等-反转链表 II
92-中等-反转链表 II

给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。
```java
1
```
## 104-简单-二叉树的最大深度
104-简单-二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。
```java
/**
 * 递归解法
 */
public int maxDepth(TreeNode root) {
    //当root为0是，则代表tree不存在
    if (root == null) {
       return 0;
    }

    // 利用定义，计算左右子树的最大深度
    int maxLeft = maxDepth(root.left);
    int maxRight = maxDepth(root.right);

    // 整棵树的最大深度等于左右子树的最大深度取最大值，
    // 然后再加上根节点自己
    return Math.max(maxLeft, maxRight) + 1;
}
```
## 106-简单-相交链表
106-简单-相交链表

给定一个头结点为 head 的非空单链表，返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。
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
## 114-中等-将二叉树展开为链表
114-中等-将二叉树展开为链表

给你二叉树的根结点 root ，请你将它展开为一个单链表：

展开后的单链表应该同样使用 TreeNode ，其中 right 子指针指向链表中下一个结点，而左子指针始终为 null 。
展开后的单链表应该与二叉树 先序遍历 顺序相同。
```java
class Solution {
    // 定义：将以 root 为根的树拉平为链表
    void flatten(TreeNode root) {
        // base case
        if (root == null) return;
        
        // 利用定义，把左右子树拉平
        flatten(root.left);
        flatten(root.right);

        /**** 后序遍历位置 ****/
        // 1、左右子树已经被拉平成一条链表
        TreeNode left = root.left;
        TreeNode right = root.right;
        
        // 2、将左子树作为右子树
        root.left = null;
        root.right = left;

        // 3、将原先的右子树接到当前右子树的末端
        TreeNode p = root;
        while (p.right != null) {
            p = p.right;
        }
        p.right = right;
    }
}
```
## 116-中等-填充节点的右侧指针
116-中等-填充节点的右侧指针

给定一个 完美二叉树 ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

struct Node {
int val;
Node *left;
Node *right;
Node *next;
}
填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

初始状态下，所有 next 指针都被设置为 NULL。

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/populating-next-right-pointers-in-each-node
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
```java
class Solution {

    /**
     * 法1：层次遍历
     */
    public Node connect(Node root) {
        if (root == null) {
            return root;
        }
        // 初始化队列同时将第一层节点加入队列中，即根节点
        Queue<Node> queue = new LinkedList<Node>();
        queue.add(root);

        // 外层的 while 循环迭代的是层数
        while (!queue.isEmpty()) {
            // 记录当前队列大小
            int size = queue.size();
            // 遍历这一层的所有节点
            for (int i = 0; i < size; i++) {

                // 从队首取出元素
                Node node = queue.poll();
                // 连接
                if (i < size - 1) {
                    node.next = queue.peek();
                }

                // 拓展下一层节点
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
        }
        // 返回根节点
        return root;
    }

    /**
     * 法2：三叉树 解法
     * 1. 连接相同父节点的两个子节点
     * 2. 连接跨越父节点的两个子节点
     */
    public Node connect2(Node root) {
        if (root == null) {
            return null;
        }
        traverse(root.left, root.right);
        return root;
    }

    /**
     * 三叉树遍历框架
     */
    public void traverse(Node node1, Node node2) {
        if (node1 == null || node2 == null) {
            return;
        }
        /**** 前序位置 ****/
        // 将传入的两个节点穿起来
        node1.next = node2;

        // 连接相同父节点的两个子节点
        traverse(node1.left, node1.right);
        traverse(node2.left, node2.right);
        // 连接跨越父节点的两个子节点
        traverse(node1.right, node2.left);
    }
}
```
## 125-简单-验证回文串
125-简单-验证回文串

如果在将所有大写字符转换为小写字符、并移除所有非字母数字字符之后，短语正着读和反着读都一样。则可以认为该短语是一个 回文串 。

字母和数字都属于字母数字字符。

给你一个字符串 s，如果它是 回文串 ，返回 true ；否则，返回 false 。
```java
class Solution {
    /**
     * 左右双指针
     */
    public boolean isPalindrome(String s) {
        int n = s.length();
        int left = 0, right = n - 1;
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
                ++left;
            }
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
                --right;
            }
            if (left < right) {
                if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
                    return false;
                }
                ++left;
                --right;
            }
        }
        return true;
    }
}
```
## 136-简单-只出现一次的数字
136-简单-只出现一次的数字

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。
说明：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？
```java
class Solution {
     /**
     * 使用异或运算，将所有值进行异或
     * 异或运算，相异为真，相同为假，所以 a^a = 0 ;0^a = a
     * 因为异或运算 满足交换律 a^b^a = a^a^b = b 所以数组经过异或运算，单独的值就剩下了
     */
    public int singleNumber(int[] nums) {
        int value = 0;
        for (int num : nums) {
            //注意异或运算符
            value ^= num;
        }
        return value;
    }
}
```
## 141-简单-环形链表
141-简单-环形链表

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
## 151-中等-反转字符串中的单词
151-中等-反转字符串中的单词

给你一个字符串 s ，请你反转字符串中 单词 的顺序。

单词 是由非空格字符组成的字符串。s 中使用至少一个空格将字符串中的 单词 分隔开。

返回 单词 顺序颠倒且 单词 之间用单个空格连接的结果字符串。

注意：输入字符串 s中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。
```java
class Solution {
    /**
     * 1. 去除多余空格
     * 2. 翻转整个字符串
     * 3. 逐个按单词再翻转
     */
    public String reverseWords(String s) {

        StringBuilder sb = trimSpaces(s);

        // 翻转字符串
        reverse(sb, 0, sb.length() - 1);

        // 翻转每个单词
        reverseEachWord(sb);

        return sb.toString();

    }

    public StringBuilder trimSpaces(String s) {
        int left = 0, right = s.length() - 1;
        // 去掉字符串开头的空白字符，优秀想法
        while (s.charAt(left) == ' ') {
            left++;
        }
        // 去掉字符串末尾的空白字符，优秀想法
        while (s.charAt(right) == ' ') {
            right--;
        }

        // 将字符串间多余的空白字符去除
        StringBuilder sb = new StringBuilder();
        while (left <= right) {
            char c = s.charAt(left);
            //s.charAt(str.length() - 1) != ' ' 是为了判断 不连续2个空格
            if (c != ' ' || sb.charAt(sb.length() - 1) != ' ') {
                sb.append(c);
            }
            //别漏了
            left++;
        }
        return sb;
    }

    public void reverse(StringBuilder sb, int left, int right) {
        while (left < right) {
            char tmp = sb.charAt(left);
            sb.setCharAt(left++, sb.charAt(right));
            sb.setCharAt(right--, tmp);
        }
    }

    public void reverseEachWord(StringBuilder sb) {
        int n = sb.length();
        int start = 0, end = 0;

        while (start < n) {
            // 循环至单词的末尾
            while (end < n && sb.charAt(end) != ' ') {
                ++end;
            }
            // 翻转单词
            reverse(sb, start, end - 1);
            // 更新start，去找下一个单词
            start = end + 1;
            ++end;
        }
    }
}
```
## 155-中等-最小栈
155-中等-最小栈

设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

实现 MinStack 类:

MinStack() 初始化堆栈对象。
void push(int val) 将元素val推入堆栈。
void pop() 删除堆栈顶部的元素。
int top() 获取堆栈顶部的元素。
int getMin() 获取堆栈中的最小元素。
```java
/**
 *  法1：辅助栈 
 */
class MinStack {
    Deque<Integer> xStack;
    Deque<Integer> minStack;

    public MinStack() {
        xStack = new LinkedList<Integer>();
        minStack = new LinkedList<Integer>();
        minStack.push(Integer.MAX_VALUE);
    }

    public void push(int x) {
        xStack.push(x);
        //辅助栈，每次都新增数值        
        minStack.push(Math.min(minStack.peek(), x));
    }

    public void pop() {
        xStack.pop();
        minStack.pop();
    }

    public int top() {
        return xStack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }
}

/**
 *  法2：辅助栈 
 */
class MinStack {
    // 记录栈中的所有元素
    Stack<Integer> nStack;
    // 阶段性记录栈中的最小元素
    Stack<Integer> mStack;

    public MinStack() {
        nStack = new Stack<>();
        mStack = new Stack<>();
    }

    public void push(int val) {
        nStack.push(val);
        //维护 minStk 栈顶为全栈最小元素
        if (mStack.isEmpty() || val <= mStack.peek()) {
            // 新插入的这个元素就是全栈最小的
            mStack.push(val);
        }
    }

    public void pop() {
        // 比较 Integer 相等 用 equal
        if (nStack.peek().equals(mStack.peek())) {
            mStack.pop();
        }
        nStack.pop();
    }

    public int top() {
        return nStack.peek();
    }

    public int getMin() {
        // minStk 栈顶为全栈最小元素
        return mStack.peek();
    }
}
```
## 204-中等-计数质数
204-中等-计数质数

给定整数 n ，返回 所有小于非负整数 n 的质数的数量 。
```java
/**
     * 法1：优化【反向思维】
     */
    public int countPrimes(int n) {
        int[] array = new int[n + 2];
        for (int i = 2; i < n; ++i) {
            if (array[i] == 0) {
                //注意 j += i
                for (int j = 2 * i; j < n; j += i) {
                    array[j] = 1;
                }
            }
        }
        int ans = 0;
        for (int i = 2; i < n; i++) {
            if (array[i] == 0) {
                ans++;
            }
        }
        return ans;
    }


    /**
     * 法2：暴力, 超出时间限制
     */
    public int countPrimes2(int n) {
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            ans += isPrime(i) ? 1 : 0;
        }
        return ans;
    }

    public boolean isPrime(int x) {
        for (int i = 2; i * i <= x; ++i) {
            if (x % i == 0) {
                return false;
            }
        }
        return true;
    }
```
## 226-简单-翻转二叉树
226-简单-翻转二叉树

给你一棵二叉树的根节点 root ，翻转这棵二叉树，并返回其根节点。
```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        
        if(root==null){
            return root;
        }
        // 1. 利用函数定义，先翻转左右子树
        TreeNode leftNode= invertTree(root.left);
        TreeNode rightNode= invertTree(root.right);
        
        // 2. 交换左右子节点
        root.left=rightNode;
        root.right=leftNode;
        return root;
    }
}
```
## 231-简单-2的幂
231-简单-2的幂

给你一个整数 n，请你判断该整数是否是 2 的幂次方。如果是，返回 true ；否则，返回 false 。

如果存在一个整数 x 使得 n == 2x ，则认为 n 是 2 的幂次方。
```java
class Solution {
    public boolean isPowerOfTwo(int n) {
    while (n != 0 && n % 2 == 0) {
            n = n / 2;
        }
        return n == 1;
    }
}
```
## 234-简单-回文链表
234-简单-回文链表

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
## 237-中等-删除链表中的节点
237-中等-删除链表中的节点

请编写一个函数，用于 删除单链表中某个特定节点 。在设计函数时需要注意，你无法访问链表的头节点 head ，只能直接访问 要被删除的节点 。

题目数据保证需要删除的节点 不是末尾节点 。
```java
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```
## 238-中等-除自身以外数组的乘积
238-中等-除自身以外数组的乘积

给你一个整数数组 nums，返回 数组 answer ，其中 answer[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积 。

题目数据 保证 数组 nums之中任意元素的全部前缀元素和后缀的乘积都在  32 位 整数范围内。

请不要使用除法，且在 O(n) 时间复杂度内完成此题。
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int len = nums.length;
        // L 和 R 分别表示左右两侧的乘积列表
        int[] leftValue = new int[len];
        int[] rightValue = new int[len];
        int[] result = new int[len];

        // L[i] 为索引 i 左侧所有元素的乘积
        // 对于索引为 '0' 的元素，因为左侧没有元素，所以 L[0] = 1
        leftValue[0] = 1;
        for (int i = 1; i < len; i++) {
            leftValue[i] = nums[i-1] * leftValue[i - 1];
        }

        // R[i] 为索引 i 右侧所有元素的乘积
        // 对于索引为 'length-1' 的元素，因为右侧没有元素，所以 R[length-1] = 1
        rightValue[len-1] = 1;
        for (int i = len - 2; i >= 0; i--) {
            rightValue[i] = nums[i + 1] * rightValue[i + 1];
        }

        // 对于索引 i，除 nums[i] 之外其余各元素的乘积就是左侧所有元素的乘积乘以右侧所有元素的乘积
        for (int i = 0; i < len; i++) {
            result[i] = leftValue[i] * rightValue[i];
        }
        return result;
    }
}
```
## 242-简单-有效的字母异位词
242-简单-有效的字母异位词

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

注意：若 s 和 t 中每个字符出现的次数都相同，则称 s 和 t 互为字母异位词。
```java
class Solution {
    /**
     * 方法1：
     * 1. 26个英文字母数字长度，每个索引的值进行 先加1，后减1， 当所有均为0时，才相等
     * t 是 s 的异位词等价于「两个字符串中字符出现的种类和次数均相等」
     */
    public boolean isAnagram(String s, String t) {
        int[] record = new int[26];
        for (char c : s.toCharArray()) {
            record[c - 'a'] += 1;
        }
        for (char c : t.toCharArray()) {
            record[c - 'a'] -= 1;
        }
        for (int i : record) {
            // record数组如果有的元素不为零0，说明字符串s和t 一定是谁多了字符或者谁少了字符。
            if (i != 0) {
                return false;
            }
        }
        // record数组所有元素都为零0，说明字符串s和t是字母异位词
        return true;
    }

    /**
     * 方法2：
     * 1.数组排序
     * 2.比较
     */
    public boolean isAnagram2(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        char[] str1 = s.toCharArray();
        char[] str2 = t.toCharArray();
        Arrays.sort(str1);
        Arrays.sort(str2);
        return Arrays.equals(str1, str2);
    }
}
```
## 268-简单-丢失的数字
268-简单-丢失的数字

给定一个包含 [0, n] 中 n 个数的数组 nums ，找出 [0, n] 这个范围内没有出现在数组中的那个数。
```java
class Solution {
    /**
     * 法1：异或运算，异或运算具有交换律
     */
    public int missingNumber(int[] nums) {
        int num=0;
        // 先和新补的索引异或一下
        num^=nums.length;
        // 和其他的元素、索引做异或
        for(int i=0;i<nums.length;i++){
            num^=i^nums[i];
        }
        return num;
    }
    /**
     * 法2：等差数列求和，减去所有值
     */
    public int missingNumber2(int[] nums) {
        int length = nums.length;
        int sum = (0 + length) * (length + 1) / 2;
        for (int i = 0; i < length; i++){
            sum -= nums[i];
        }         
        return sum;
    }
}
```
## 326-简单-3的幂
326-简单-3的幂

给定一个整数，写一个函数来判断它是否是 3 的幂次方。如果是，返回 true ；否则，返回 false 。
```java
class Solution {
    /**
     * 方法1： while循环
     */
    public boolean isPowerOfThree(int n) {
        while (n != 0 && n % 3 == 0) {
            n = n / 3;
        }
        return n == 1;
    }

    /**
     * 方法二：判断是否为最大 33 的幂的约数
     * <p>
     * 我们还可以使用一种较为取巧的做法。
     * 在题目给定的 3232 位有符号整数的范围内，最大的 33 的幂为 3^{19} = 11622614673
     * =1162261467。我们只需要判断 nn 是否是 3^{19} 的约数即可。
     */
    public boolean isPowerOfThree(int n) {
        return n > 0 && 1162261467 % n == 0;
    }

    /**
     * 方法3：哈希表
     */
    static Set<Integer> set = new HashSet<>();
    static {
        int cur = 1;
        set.add(cur);
        while (cur <= Integer.MAX_VALUE / 3) {
            cur *= 3;
            set.add(cur);
        }
    }
    public boolean isPowerOfThree(int n) {
        return n > 0 && set.contains(n);
    }
}
```
## 342-简单-4的幂
342-简单-4的幂

给定一个整数，写一个函数来判断它是否是 4 的幂次方。如果是，返回 true ；否则，返回 false 。
```java
class Solution {
    public boolean isPowerOfFour(int n) {
        while (n != 0 && n % 4 == 0) {
            n = n / 4;
        }
        return n == 1;
    }
}
```
## 384-中等-打乱数组
384-中等-打乱数组

给你一个整数数组 nums ，设计算法来打乱一个没有重复元素的数组。打乱后，数组的所有排列应该是 等可能 的。

实现 Solution class:

Solution(int[] nums) 使用整数数组 nums 初始化对象
int[] reset() 重设数组到它的初始状态并返回
int[] shuffle() 返回数组随机打乱后的结果
```java
class Solution {

    int[] array;
    public Solution(int[] nums) {
        array=nums;
    }
    
    public int[] reset() {
        return array;
    }
    
    public int[] shuffle() {
        //注意 array.clone();
        int[] tmp= array.clone();
        Random random =new Random();
        int len =tmp.length;
        for(int i=0;i<len;i++){
            //i+random.nextInt(len-i); 
            int j= i+random.nextInt(len-i); 
            int t= tmp[j];
            tmp[j]=tmp[i];
            tmp[i]=t;
        }
        return tmp;
    }

}
```
## 387-简单-字符串中的第一个唯一字符
387-简单-字符串中的第一个唯一字符

给定一个字符串 s ，找到 它的第一个不重复的字符，并返回它的索引 。如果不存在，则返回 -1 。
```java
public int firstUniqChar(String s) {
        Map<Character, Integer> frequency = new HashMap<Character, Integer>();
        for (int i = 0; i < s.length(); ++i) {
            char ch = s.charAt(i);
            frequency.put(ch, frequency.getOrDefault(ch, 0) + 1);
        }
        for (int i = 0; i < s.length(); ++i) {
            if (frequency.get(s.charAt(i)) == 1) {
                return i;
            }
        }
        return -1;
    }
    /**
     * 法2：数组统计，高效
     */
    public int firstUniqChar(String s) {
        int[] arr = new int[26];
        int n = s.length();
        for (int i = 0; i < n; i++) {
           arr[s.charAt(i)-'a']++ ;
        }
        for (int i = 0; i < n; i++) {
           if (arr[s.charAt(i)-'a'] == 1) {
              return i;
           }
        }
        return -1;
  }
```
## 412-简单-Fizz Buzz
412-简单-Fizz Buzz

给你一个整数 n ，找出从 1 到 n 各个整数的 Fizz Buzz 表示，并用字符串数组 answer（下标从 1 开始）返回结果，其中：

answer[i] == "FizzBuzz" 如果 i 同时是 3 和 5 的倍数。
answer[i] == "Fizz" 如果 i 是 3 的倍数。
answer[i] == "Buzz" 如果 i 是 5 的倍数。
answer[i] == i （以字符串形式）如果上述条件全不满足。
```java
/**
     * 法1：优化【反向思维】
     */
    public int countPrimes(int n) {
        int[] array = new int[n + 2];
        for (int i = 2; i < n; ++i) {
            if (array[i] == 0) {
                //注意 j += i
                for (int j = 2 * i; j < n; j += i) {
                    array[j] = 1;
                }
            }
        }
        int ans = 0;
        for (int i = 2; i < n; i++) {
            if (array[i] == 0) {
                ans++;
            }
        }
        return ans;
    }


    /**
     * 法2：暴力, 超出时间限制
     */
    public int countPrimes2(int n) {
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            ans += isPrime(i) ? 1 : 0;
        }
        return ans;
    }

    public boolean isPrime(int x) {
        for (int i = 2; i * i <= x; ++i) {
            if (x % i == 0) {
                return false;
            }
        }
        return true;
    }
```
## 543-简单-二叉树的直径
543-简单-二叉树的直径

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。
```java
class Solution {
    // 记录最大直径的长度
    int max = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        maxDepth(root);
        return max;
    }

    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = cal(root.left);
        int right = cal(root.right);

        // 后序位置，顺便计算最大直径 : left+right
        max = Math.max(max, left + right);

        return Math.max(left, right) + 1;
    }
}
```
## 640-中等-求解方程
640-中等-求解方程

求解一个给定的方程，将x以字符串 "x=#value" 的形式返回。该方程仅包含 '+' ， '-' 操作，变量 x 和其对应系数。

如果方程没有解或存在的解不为整数，请返回 "No solution" 。如果方程有无限解，则返回 “Infinite solutions” 。

题目保证，如果方程中只有一个解，则 'x' 的值是一个整数。
```java
class Solution {
    public String solveEquation(String s) {
        int x = 0, num = 0, length = s.length();
        char[] array = s.toCharArray();
        //定义op为操作符，若+号则为1，-号则为-1；
        for (int i = 0, op = 1; i < length; ) {
            if (array[i] == '+') {
                op = 1;
                i++;
            } else if (array[i] == '-') {
                op = -1;
                i++;
            } else if (array[i] == '=') {
                //若为等号开始，马上右边
                x *= -1;
                num *= -1;
                op = 1;
                i++;
            } else {
                int j = i;
                while (j < length && array[j] != '+' && array[j] != '-' && array[j] != '=') {
                    j++;
                }
                if (array[j - 1] == 'x') {
                    x += (i < j - 1 ? Integer.parseInt(s.substring(i, j - 1)) : 1) * op;
                } else {
                    num += Integer.parseInt(s.substring(i, j)) * op;
                }
                i = j;
            }
        }
        if (x == 0) {
            return num == 0 ? "Infinite solutions" : "No solution";
        } else {
            return "x=" + (num / -x);
        }
    }
}
```
## 654-中等-最大二叉树
654-中等-最大二叉树

给定一个不重复的整数数组 nums 。 最大二叉树 可以用下面的算法从 nums 递归地构建:

创建一个根节点，其值为 nums 中的最大值。
递归地在最大值 左边 的 子数组前缀上 构建左子树。
递归地在最大值 右边 的 子数组后缀上 构建右子树。
返回 nums 构建的 最大二叉树 。
```java
class Solution {
    /**
     * 递归解法
     */
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return build(nums, 0, nums.length - 1);
    }

    TreeNode build(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }
        int maxValIndex = left;
        //注意：i<=right
        //1. 找出区间最大值索引
        for (int i = left; i <= right; i++) {
            if (nums[i] > nums[maxValIndex]) {
                maxValIndex = i;
            }
        }
        //2. 节点赋值
        TreeNode ans = new TreeNode(nums[maxValIndex]);
        //3. 左右树递归
        ans.left = build(nums, left, maxValIndex - 1);
        ans.right = build(nums, maxValIndex + 1, right);
        return ans;
    }
}
```
## 876-简单-单链表的中点
876-简单-单链表的中点

给定一个头结点为 head 的非空单链表，返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。
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
## 10001-简单-树的前序遍历结果
10001-简单-树的前序遍历结果

输入一棵二叉树的根节点，返回这棵树的前序遍历结果
```java
// 定义：输入一棵二叉树的根节点，返回这棵树的前序遍历结果
List<Integer> preorderTraverse(TreeNode root) {
    List<Integer> res = new LinkedList<>();
    if (root == null) {
        return res;
    }
    // 前序遍历的结果，root.val 在第一个
    res.add(root.val);
    // 利用函数定义，后面接着左子树的前序遍历结果
    res.addAll(preorderTraverse(root.left));
    // 利用函数定义，最后接着右子树的前序遍历结果
    res.addAll(preorderTraverse(root.right));
    return res;
}
```

