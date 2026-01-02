# 算法-数组


#  数组/单链表系列算法

 单链表常考的技巧就是双指针
比如判断单链表是否成环，拍脑袋的暴力解是什么？就是用一个 HashSet 之类的数据结构来缓存走过的节点，遇到重复的就说明有环对吧。但我们用快慢指针可以避免使用额外的空间，这就是聪明地穷举嘛。

当然，对于找链表中点这种问题，使用双指针技巧只是显示你学过这个技巧，和遍历两次链表的常规解法从时间空间复杂度的角度来说都是差不多的。

数组常用的技巧有很大一部分还是双指针相关的技巧，说白了是教你如何聪明地进行穷举。

首先说二分搜索技巧，可以归为两端向中心的双指针。如果让你在数组中搜索元素，一个 for 循环穷举肯定能搞定对吧，但如果数组是有序的，二分搜索不就是一种更聪明的搜索方式么。


# 数组算法
# 双指针技巧
在处理数组和链表相关问题时，双指针技巧是经常用到的，双指针技巧主要分为两类：左右指针和快慢指针。
所谓左右指针，就是两个指针相向而行或者相背而行；而所谓快慢指针，就是两个指针同向而行，一快一慢。

## 一、快慢指针技巧
数组问题中比较常见的快慢指针技巧，是让你原地修改数组。

### 删除有序数组中的重复项
第 26 题[「 删除有序数组中的重复项」](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)
```java
class Solution {
  public int removeDuplicates(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }

        //此处slow 代表新数组的长度，至少是1起
        int slow = 1;
        //此处fast 代表比较指针，参与值比较
        int fast = 1;
        while (fast < len) {
            if (nums[fast] != nums[fast-1]) {
                nums[slow] = nums[fast];
                slow++;
            }
            fast++;
        }
        return slow;
    }
}
```
### 删除排序链表中的重复元素
第 83 题 [删除排序链表中的重复元素](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)
```java
    /**
     * 双指针
     */
    ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }
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

    /**
     * 单指针
     */
    ListNode deleteDuplicates1(ListNode head) {
        if (head == null) {
            return head;
        }

        ListNode cur = head;
        while (cur.next != null) {
            if (cur.val == cur.next.val) {
                cur.next = cur.next.next;
            } else {
                cur = cur.next;
            }
        }
        return head;
    }
```
除了让你在有序数组/链表中去重，题目还可能让你对数组中的某些元素进行「原地删除」。

### 移除元素
如力扣第 27 题[「 移除元素」](https://leetcode.cn/problems/remove-element/)
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
### 移动零-E
第 283 题[「 移动零」](https://leetcode.cn/problems/move-zeroes/)

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

请注意 ，必须在不复制数组的情况下原地对数组进行操作。
```java
class Solution {
    public void moveZeroes(int[] nums) {
       int tmp = 0;
        int last = nums.length - 1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                // nums[tmp] = nums[i];
                // tmp++;
                //等同于上述
                nums[tmp++] = nums[i];
            }
        }
        while (tmp < nums.length) {
            // nums[tmp] = 0;
            // tmp++;
            //等同于上述
            nums[tmp++] = 0;
        }
    }
}
```
### 合并两个有序数组
第 88 题 [合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/)

给你两个按 非递减顺序 排列的整数数组 nums1 和 nums2，另有两个整数 m 和 n ，分别表示 nums1 和 nums2 中的元素数目。

请你 合并 nums2 到 nums1 中，使合并后的数组同样按 非递减顺序 排列。

最终，合并后数组不应由函数返回，而是存储在数组 nums1 中。

```java
class Solution {
    /**
     * 方法1：双指针法 （低耗时）
     */
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int[] array = new int[m + n];
        int p1 = 0;
        int p2 = 0;
        int v;
        while (p1 < m || p2 < n) {
            if (p1 == m) {
                v = nums2[p2++];
            } else if (p2 == n) {
                v = nums1[p1++];
            } else if (nums1[p1] > nums2[p2]) {
                v = nums2[p2++];
            } else {
                v = nums1[p1++];
            }
            array[p1 + p2 - 1] = v;
        }
        for (int i = 0; i != m + n; ++i) {
            nums1[i] = array[i];
        }
    }

    /**
     * 方法2：直接合并后排序
     * 运用函数
     */
//    public void merge(int[] nums1, int m, int[] nums2, int n) {
//        for (int i = 0; i < n; i++) {
//            nums1[m + i] = nums2[i];
//        }
//        Arrays.sort(nums1);
//    }
}
```


        
## 二、左右指针的常用算法
1、二分查找
最简单的二分算法，旨在突出它的双指针特性：
```java
int binarySearch(int[] nums, int target) {
    // 一左一右两个指针相向而行
    int left = 0, right = nums.length - 1;
    while(left <= right) {
        int mid = (right + left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; 
        else if (nums[mid] > target)
            right = mid - 1;
    }
    return -1;
}
```

### 两数之和
2、两数之和
看下力扣第 167 题[「 两数之和 II」](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)
只要数组有序，就应该想到双指针技巧。这道题的解法有点类似二分查找，通过调节 left 和 right 就可以调整 sum 的大小：
```java
int[] twoSum(int[] nums, int target) {
    // 一左一右两个指针相向而行
    int left = 0, right = nums.length - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) {
            // 题目要求的索引是从 1 开始的
            return new int[]{left + 1, right + 1};
        } else if (sum < target) {
            left++; // 让 sum 大一点
        } else if (sum > target) {
            right--; // 让 sum 小一点
        }
    }
    return new int[]{-1, -1};
}
```

### 反转数组
一般编程语言都会提供 reverse 函数，其实这个函数的原理非常简单，力扣第 344 题[「 反转字符串」](https://leetcode.cn/problems/reverse-string/) 就是类似的需求，让你反转一个 char[] 类型的字符数组，
```java
void reverseString(char[] s) {
    // 一左一右两个指针相向而行
    int left = 0, right = s.length - 1;
    while (left < right) {
        // 交换 s[left] 和 s[right]
        char temp = s[left];
        s[left] = s[right];
        s[right] = temp;
        left++;
        right--;
    }
}
```
### 回文串判断
首先明确一下，回文串就是正着读和反着读都一样的字符串。

比如说字符串 aba 和 abba 都是回文串，因为它们对称，反过来还是和本身一样；反之，字符串 abac 就不是回文串。

现在你应该能感觉到回文串问题和左右指针肯定有密切的联系，比如让你判断一个字符串是不是回文串，你可以写出下面这段代码：
```java
boolean isPalindrome(String s) {
    // 一左一右两个指针相向而行
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```
那接下来我提升一点难度，给你一个字符串，让你用双指针技巧从中找出最长的回文串，你会做吗？

### 最长回文子串
这就是力扣第 5 题[「 最长回文子串」](https://leetcode.cn/problems/longest-palindromic-substring/)
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

### 第一个错误的版本
力扣第 278 题 [第一个错误的版本](https://leetcode.cn/problems/first-bad-version/)

你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。

你可以通过调用bool isBadVersion(version)接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。
```java
public class Solution extends VersionControl {
    /**
     * 二分查找
     * 注意到一个性质：当一个版本为正确版本，则该版本之前的所有版本均为正确版本；当一个版本为错误版本，则该版本之后的所有版本均为错误版本。我们可以利用这个性质进行二分查找。
     */
    public int firstBadVersion(int n) {
        int left=0,right=n;
        while(left<right){
            int middle =left+(right-left)/2;
            if(isBadVersion(middle)){
                right=middle;
            }else{
                left=middle+1;
            }
        }
        return left;   
    }
}
```
### 两个数组的交集 II-E【重复】
力扣第 350 题 [两个数组的交集 II](https://leetcode.cn/problems/intersection-of-two-arrays-ii/)
给你两个整数数组nums1 和 nums2 ，请你以数组形式返回两数组的交集。返回结果中每个元素出现的次数，应与元素在两个数组中都出现的次数一致（如果出现次数不一致，则考虑取较小值）。可以不考虑输出结果的顺序。

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        
        int len = nums1.length>nums2.length ? nums1.length : nums2.length;;
        int[] result = new int[len];
        
        int left1=0,left2=0;
        int i=0;
        while(left1<nums1.length && left2<nums2.length){
            
            if(nums1[left1]==nums2[left2]){
                result[i]=nums1[left1];
                i++;
                left1++;
                left2++;
            }else if(nums1[left1]>nums2[left2]){
                left2++;
            }else if(nums1[left1]<nums2[left2]){
                left1++;
            }
        }

        int[] res = new int[i];
        for(int n=0;n<i;n++){
            res[n]=result[n];
        }
        return res;       

    }
}
```


# 算法技巧：[前缀和]数组

- 前缀和主要适用的场景是原始数组不会被修改的情况下，频繁查询某个区间的累加和。
## 一维数组中的 [前缀和]
力扣第 303 题[「 区域和检索 - 数组不可变」](https://leetcode.cn/problems/range-sum-query-immutable/) ，让你计算数组区间内元素的和，这是一道标准的前缀和问题
```java
class NumArray {
    // 前缀和数组
    int[] sum;

    /* 输入一个数组，构造前缀和 */
    public NumArray(int[] nums) {
        int len = nums.length();
        sum = new int[len + 1];
        for (int i = 0; i < len; i++) {
            // 计算 nums 的累加和
            //注意，此处为 sum[i + 1]
            sum[i + 1] = sum[i] + nums[i];
        }
    }

    /* 查询闭区间 [left, right] 的累加和 */
    public int sumRange(int left, int right) {
        return sum[right + 1] - sum[left];
    }
}
```

## 二维矩阵中的 [前缀和]
力扣第 304 题[「 二维区域和检索 - 矩阵不可变」](https://leetcode.cn/problems/range-sum-query-2d-immutable/) ，其实和上一题类似，上一题是让你计算子数组的元素之和，这道题让你计算二维矩阵中子矩阵的元素之和。
```java

class NumMatrix {
    // 定义：preSum[i][j] 记录 matrix 中子矩阵 [0, 0, i-1, j-1] 的元素和
    int[][] preSum;

    public NumMatrix(int[][] matrix) {
        int height = matrix.length;
        int width = matrix[0].length;
        if (height == 0 || width == 0){
             return;
        }
        
        // 构造前缀和矩阵
        preSum = new int[height+1][width+1];
        for (int i = 1; i <= height; i++) {
            for (int j = 1; j <= width; j++) {
                // 计算每个矩阵 [0, 0, i, j] 的元素和
                preSum[i][j] = preSum[i - 1][j] + preSum[i][j - 1] - preSum[i - 1][j - 1] + matrix[i - 1][j - 1];
            }
        }

    }
    
    // 计算子矩阵 [x1, y1, x2, y2] 的元素和
    public int sumRegion(int row1, int col1, int row2, int col2) {
        // 目标矩阵之和由四个相邻矩阵运算获得 [此处注意坐标]
        return preSum[row2+1][col2+1] - preSum[row1][col2+1] - preSum[row2+1][col1] + preSum[row1][col1];
    }
}

```
# 算法技巧：差分数组
- 差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减。
- 差分数组对应的概念是前缀 和 数组，对于数组 [1,2,2,4]，其差分数组为 [1,1,0,2]
- 事例：[https://labuladong.gitee.io/algo/2/20/25/](https://labuladong.gitee.io/algo/2/20/25/) 

class NumMatrix {
// 定义：preSum[i][j] 记录 matrix 中子矩阵 [0, 0, i-1, j-1] 的元素和
int[][] preSum;

    public NumMatrix(int[][] matrix) {
        int height = matrix.length;
        int width = matrix[0].length;
        if (height == 0 || width == 0){
             return;
        }
        
        // 构造前缀和矩阵
        preSum = new int[height+1][width+1];
        for (int i = 1; i <= height; i++) {
            for (int j = 1; j <= width; j++) {
                // 计算每个矩阵 [0, 0, i, j] 的元素和
                preSum[i][j] = preSum[i - 1][j] + preSum[i][j - 1] - preSum[i - 1][j - 1] + matrix[i - 1][j - 1];
            }
        }

    }
    
    // 计算子矩阵 [x1, y1, x2, y2] 的元素和
    public int sumRegion(int row1, int col1, int row2, int col2) {
        // 目标矩阵之和由四个相邻矩阵运算获得 [此处注意坐标]
        return preSum[row2+1][col2+1] - preSum[row1][col2+1] - preSum[row2+1][col1] + preSum[row1][col1];
    }
}

比如说，我给你输入一个数组 nums，然后又要求给区间 nums[2..6] 全部加 1，再给 nums[3..9] 全部减 3，再给 nums[0..4] 全部加 2，再给…
一通操作猛如虎，然后问你，最后 nums 数组的值是什么？

常规的思路很容易，你让我给区间 nums[i..j] 加上 val，那我就一个 for 循环给它们都加上呗，还能咋样？这种思路的时间复杂度是 O(N)，由于这个场景下对 nums 的修改非常频繁，所以效率会很低下。

这里就需要差分数组的技巧，类似前缀和技巧构造的 prefix 数组，我们先对 nums 数组构造一个 diff 差分数组，diff[i] 就是 nums[i] 和 nums[i-1] 之差：

```java
int[] diff = new int[nums.length];
// 构造差分数组
diff[0] = nums[0];
for (int i = 1; i < nums.length; i++) {
    diff[i] = nums[i] - nums[i - 1];
}

```

通过这个 diff 差分数组是可以反推出原始数组 nums 的，代码逻辑如下：
```java
int[] res = new int[diff.length];
// 根据差分数组构造结果数组
res[0] = diff[0];
for (int i = 1; i < diff.length; i++) {
    res[i] = res[i - 1] + diff[i];
}
```

这样构造差分数组 diff，就可以快速进行区间增减的操作，如果你想对区间 nums[i..j] 的元素全部加 3，那么只需要让 diff[i] += 3，然后再让 diff[j+1] -= 3 即可;



力扣第 370 题[「 区间加法」](https://leetcode.cn/problems/range-addition/)  就直接考察了差分数组技巧
```java
int[] getModifiedArray(int length, int[][] updates) {
        // nums 初始化为全 0
        int[] nums = new int[length];
        // 构造差分解法
        Difference df = new Difference(nums);

        for (int[] update : updates) {
            int i = update[0];
            int j = update[1];
            int val = update[2];
            df.increment(i, j, val);
        }
        return df.result();
    }


    // 差分数组工具类
    class Difference {
        // 差分数组
        private int[] diff;

        /* 输入一个初始数组，区间操作将在这个数组上进行 */
        public Difference(int[] nums) {
            assert nums.length > 0;
            diff = new int[nums.length];
            // 根据初始数组构造差分数组
            diff[0] = nums[0];
            for (int i = 1; i < nums.length; i++) {
                diff[i] = nums[i] - nums[i - 1];
            }
        }

        /* 给闭区间 [i, j] 增加 val（可以是负数）*/
        public void increment(int i, int j, int val) {
            diff[i] += val;
            if (j + 1 < diff.length) {
                diff[j + 1] -= val;
            }
        }

        /* 返回结果数组 */
        public int[] result() {
            int[] res = new int[diff.length];
            // 根据差分数组构造结果数组
            res[0] = diff[0];
            for (int i = 1; i < diff.length; i++) {
                res[i] = res[i - 1] + diff[i];
            }
            return res;
        }
    }
```

## 航班预定

实际的算法题可能需要我们对题目进行联想和抽象，不会这么直接地让你看出来要用差分数组技巧，这里看一下力扣第 1109 题[「 航班预订统计」](https://leetcode.cn/problems/corporate-flight-bookings/)

```java
这里有 n 个航班，它们分别从 1 到 n 进行编号。

有一份航班预订表 bookings ，表中第 i 条预订记录 bookings[i] = [firsti, lasti, seatsi] 
意味着在从 firsti 到 lasti （包含 firsti 和 lasti ）的 每个航班 上预订了 seatsi 个座位。

请你返回一个长度为 n 的数组 answer，里面的元素是每个航班预定的座位总数。

示例 1：

输入：bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
输出：[10,55,45,25,25]
解释：
航班编号        1   2   3   4   5
预订记录 1 ：   10  10
预订记录 2 ：       20  20
预订记录 3 ：       25  25  25  25
总座位数：      10  55  45  25  25
因此，answer = [10,55,45,25,25]
```
```java
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
    // nums 初始化为全 0
    int[] nums = new int[n];
    // 构造差分解法
    Difference df = new Difference(nums);

    for (int[] booking : bookings) {
        // 注意转成数组索引要减一哦
        int i = booking[0] - 1;
        int j = booking[1] - 1;
        int val = booking[2];
        // 对区间 nums[i..j] 增加 val
        df.increment(i, j, val);
    }
    // 返回最终的结果数组
    return df.result();
    }
}

// 差分数组工具类
class Difference {
    // 差分数组
    private int[] diff;
    
    /* 输入一个初始数组，区间操作将在这个数组上进行 */
    public Difference(int[] nums) {
        diff = new int[nums.length];
        // 根据初始数组构造差分数组
        diff[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            diff[i] = nums[i] - nums[i - 1];
        }
    }

    /* 给闭区间 [i, j] 增加 val（可以是负数）*/
    public void increment(int i, int j, int val) {
        diff[i] += val;
        if (j + 1 < diff.length) {
            diff[j + 1] -= val;
        }
    }

    /* 返回结果数组 */
    public int[] result() {
        int[] res = new int[diff.length];
        // 根据差分数组构造结果数组
        res[0] = diff[0];
        for (int i = 1; i < diff.length; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}

```

## 拼车

还有一道很类似的题目是力扣第 1094 题[「 拼车」](https://leetcode.cn/problems/car-pooling/)
```java
class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        //注意此处的初始化定义
        // 最多有 1001 个车站
        int[] nums = new int[1001];
        // 构造差分解法
        Tools tools = new Tools(nums);

        for (int[] array : trips) {
            int v = array[0];
            // 第 trip[1] 站乘客上车
            int i = array[1];
            //注意此处赋值
            // 第 trip[2] 站乘客已经下车，
            // 即乘客在车上的区间是 [trip[1], trip[2] - 1]
            int j = array[2] - 1;
            tools.add(i, j, v);
        }
        int[] res = tools.result();
        // 客车自始至终都不应该超载
        for (int r : res) {
            if (r > capacity) {
                return false;
            }
        }
        return true;
    }
}

// 差分数组工具类
class Tools {
    int preSum[];

    public Tools(int[] array) {
        int len = array.length;
        preSum = new int[len];
        // 根据初始数组构造差分数组
        preSum[0] = array[0];
        for (int i = 1; i < array.length; i++) {
            preSum[i] = array[i] - array[i - 1];
        }
    }
    /* 给闭区间 [i, j] 增加 val（可以是负数）*/
    public void add(int i, int j, int v) {
        preSum[i] += v;
        if (j + 1 < preSum.length) {
            preSum[j + 1] -= v;
        }
    }
    /* 返回结果数组 */
    public int[] result() {
        int[] result = new int[preSum.length];
        // 根据差分数组构造结果数组
        result[0] = preSum[0];
        for (int i = 1; i < preSum.length; i++) {
            //注意此处为 preSum[i] + result[i - 1];
            result[i] = preSum[i] + result[i - 1];
        }
        return result;
    }
}
```

# 二维数组的花式遍历技巧
## 顺/逆时针旋转矩阵
### 旋转图像
对二维数组进行旋转是常见的笔试题，力扣第 48 题[「 旋转图像」](https://leetcode.cn/problems/rotate-image/) 就是很经典的一道：
```java
class Solution {
    // 将二维矩阵原地顺时针旋转 90 度
    public void rotate(int[][] matrix) {
        int high = matrix.length;
        int width = matrix[0].length;
        // 先沿对角线镜像对称二维矩阵
        for(int i=0;i<high;i++){
            for(int j=i;j<width;j++){
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = tmp;
            }
        }
        // 然后反转二维矩阵的每一行
        for(int[] array: matrix){
            int left=0,right=array.length-1;
            while(left<right){
                int tmp =array[left];
                array[left] =array[right];
                array[right] =tmp;
                left++;
                right--;
            }
        }        
    }
}
```

既然说道这里，我们可以发散一下，如何将矩阵逆时针旋转 90 度呢？

思路是类似的，只要通过另一条对角线镜像对称矩阵，然后再反转每一行，就得到了逆时针旋转矩阵的结果
```java
// 将二维矩阵原地逆时针旋转 90 度
void rotate2(int[][] matrix) {
    int n = matrix.length;
    // 沿左下到右上的对角线镜像对称二维矩阵
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n - i; j++) {
            // swap(matrix[i][j], matrix[n-j-1][n-i-1])
            int temp = matrix[i][j];
            matrix[i][j] = matrix[n - j - 1][n - i - 1];
            matrix[n - j - 1][n - i - 1] = temp;
        }
    }
        // 然后反转二维矩阵的每一行
    for(int[] array: matrix){
      int left=0,right=array.length-1;
      while(left<right){
          int tmp =array[left];
          array[left] =array[right];
          array[right] =tmp;
          left++;
          right--;
       }
     }
}    
```


## 矩阵的螺旋遍历
### 螺旋矩阵
力扣第 54 题[「 螺旋矩阵」](https://leetcode.cn/problems/spiral-matrix/) ，看一看二维矩阵可以如何花式遍历
```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> result = new ArrayList<>();
        //upper_bound  right_bound
        //left_bound   lower_bound
        int m = matrix.length;
        int n = matrix[0].length;

        int upper_bound = 0, right_bound = n - 1;
        int left_bound = 0, lower_bound = m - 1;

        // result.size() == m * n 则遍历完整个数组
        while (result.size() < m * n) {
            if (upper_bound <= lower_bound) {
                // 在顶部从左向右遍历
                for (int j = left_bound; j <= right_bound; j++) {
                    result.add(matrix[upper_bound][j]);
                }
                // 上边界下移
                upper_bound++;
            }

            if (left_bound <= right_bound) {
                // 在右侧从上向下遍历
                for (int i = upper_bound; i <= lower_bound; i++) {
                    result.add(matrix[i][right_bound]);
                }
                // 右边界左移
                right_bound--;
            }

            if (upper_bound <= lower_bound) {
                // 在底部从右向左遍历
                for (int j = right_bound; j >= left_bound; j--) {
                    result.add(matrix[lower_bound][j]);
                }
                // 下边界上移
                lower_bound--;
            }

            if (left_bound <= right_bound) {
                // 在左侧从下向上遍历
                for (int i = lower_bound; i >= upper_bound; i--) {
                    result.add(matrix[i][left_bound]);
                }
                // 左边界右移
                left_bound++;
            }
        }
        return result;
    }
}
```
### 螺旋矩阵II
力扣第 59 题[「 螺旋矩阵 II」](https://leetcode.cn/problems/spiral-matrix-ii/solution/luo-xuan-ju-zhen-ii-by-leetcode-solution-f7fp/) 也是类似的题目，只不过是反过来，让你按照螺旋的顺序生成矩阵
```java
class Solution {
    public int[][] generateMatrix(int n) {
        //upper_bound  right_bound
        //left_bound   lower_bound
        int[][] matrix=new int[n][n];

        int upper_bound = 0, right_bound = n - 1;
        int left_bound = 0, lower_bound = n - 1;

        int sum = 1;
        // result.size() == m * n 则遍历完整个数组
        while (sum <= n*n) {
            if (upper_bound <= lower_bound) {
                // 在顶部从左向右遍历
                for (int j = left_bound; j <= right_bound; j++) {
                    matrix[upper_bound][j] = sum++;
                }
                // 上边界下移
                upper_bound++;
            }

            if (left_bound <= right_bound) {
                // 在右侧从上向下遍历
                for (int i = upper_bound; i <= lower_bound; i++) {
                    matrix[i][right_bound] = sum++;
                }
                // 右边界左移
                right_bound--;
            }

            if (upper_bound <= lower_bound) {
                // 在底部从右向左遍历
                for (int j = right_bound; j >= left_bound; j--) {
                    matrix[lower_bound][j] = sum++;
                }
                // 下边界上移
                lower_bound--;
            }

            if (left_bound <= right_bound) {
                // 在左侧从下向上遍历
                for (int i = lower_bound; i >= upper_bound; i--) {
                    matrix[i][left_bound] = sum++;
                }
                // 左边界右移
                left_bound++;
            }
        }
        return matrix;
    }
}
```

# 滑动窗口
滑动窗口防滑记
```
链表子串数组题，用双指针别犹豫。
双指针家三兄弟，各个都是万人迷，

快慢指针最神奇，链表操作无压力。
归并排序找中点，链表成环搞判定。

左右指针最常见，左右两端相向行。
反转数组要靠它，二分搜索是弟弟。

滑动窗口老猛男，子串问题全靠它，
左右指针滑窗口，一前一后齐头进。

算法思想很简单，出了bug想升天。

labuladong稳若狗，一套框架不翻车。
一路漂移带闪电，算法变成默写题。
```
本文就解决一类最难掌握的双指针技巧：滑动窗口技巧。总结出一套框架，可以保你闭着眼睛都能写出正确的解法。

说起滑动窗口算法，很多读者都会头疼。这个算法技巧的思路非常简单，就是维护一个窗口，不断滑动，然后更新答案么。LeetCode 上有起码 10 道运用滑动窗口算法的题目，难度都是中等和困难。该算法的大致逻辑如下：
```java
int left = 0, right = 0;

while (right < s.size()) {
    // 增大窗口
    window.add(s[right]);
    right++;
    
    while (window needs shrink) {
        // 缩小窗口
        window.remove(s[left]);
        left++;
    }
}

```
这个算法技巧的时间复杂度是 O(N)，比字符串暴力算法要高效得多。

其实困扰大家的，不是算法的思路，而是各种细节问题。比如说如何向窗口中添加新元素，如何缩小窗口，在窗口滑动的哪个阶段更新结果。即便你明白了这些细节，也容易出 bug，找 bug 还不知道怎么找，真的挺让人心烦的。

所以今天我就写一套滑动窗口算法的代码框架，我连再哪里做输出 debug 都给你写好了，以后遇到相关的问题，你就默写出来如下框架然后改三个地方就行，还不会出 bug：

```java
/* 滑动窗口算法框架 */
void slidingWindow(string s) {
    unordered_map<char, int> window;
    
    int left = 0, right = 0;
    while (right < s.size()) {
        // c 是将移入窗口的字符
        char c = s[right];
        // 增大窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...

        /*** debug 输出的位置 ***/
        printf("window: [%d, %d)\n", left, right);
        /********************/
        
        // 判断左侧窗口是否要收缩
        while (window needs shrink) {
            // d 是将移出窗口的字符
            char d = s[left];
            // 缩小窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}

```
虽然滑动窗口代码框架中有一个嵌套的 while 循环，但算法的时间复杂度依然是 O(N)，其中 N 是输入字符串/数组的长度。

为什么呢？简单说，字符串/数组中的每个元素都只会进入窗口一次，然后被移出窗口一次，不会说有某些元素多次进入和离开窗口，所以算法的时间复杂度就和字符串/数组的长度成正比。


说句题外话，我发现很多人喜欢执着于表象，不喜欢探求问题的本质。比如说有很多人评论我这个框架，说什么散列表速度慢，不如用数组代替散列表；还有很多人喜欢把代码写得特别短小，说我这样代码太多余，影响编译速度，LeetCode 上速度不够快。

我的意见是，算法主要看时间复杂度，你能确保自己的时间复杂度最优就行了。至于 LeetCode 所谓的运行速度，那个都是玄学，只要不是慢的离谱就没啥问题，根本不值得你从编译层面优化，不要舍本逐末……


## 最小覆盖子串
先来看看力扣第 76 题[「 最小覆盖子串」](https://leetcode.cn/problems/minimum-window-substring/) 难度 Hard：
```java
class Solution {
    public String minWindow(String s, String t) {
        Map<Character, Integer> needMap = new HashMap<>();
        Map<Character, Integer> windowMap = new HashMap<>();
        for (char c : t.toCharArray()) {
            needMap.put(c, needMap.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        // 记录最小覆盖子串的起始索引 及 长度
        int startIndex = 0, lenStep = Integer.MAX_VALUE;
        while (right < s.length()) {
            // c 是将移入窗口的字符
            char c = s.charAt(right);
            // 扩大窗口
            right++;
            // 进行窗口内数据的一系列更新
            if (needMap.containsKey(c)) {
                windowMap.put(c, windowMap.getOrDefault(c, 0) + 1);
                //若窗口内某个字符 总数 和 needMap中的该字符的 数量相等时
                if (windowMap.get(c).equals(needMap.get(c))) {
                    valid++;
                }
            }

            // 判断左侧窗口是否要收缩 【窗口内的字符，包含needMap中的所有字符】
            while (valid == needMap.size()) {
                // 在这里更新最小覆盖子串, 
                if (right - left < lenStep) {
                    startIndex = left;
                    lenStep = right - left;
                }
                // d 是将移出窗口的字符
                char d = s.charAt(left);
                // 缩小窗口
                left++;
                // 进行窗口内数据的一系列更新
                if (needMap.containsKey(d)) {
                    if (windowMap.get(d).equals(needMap.get(d))) {
                        valid--;
                    }
                    windowMap.put(d, windowMap.getOrDefault(d, 0) - 1);
                }
            }
        }
        // 返回最小覆盖子串
        return lenStep == Integer.MAX_VALUE ? "" : s.substring(startIndex, startIndex + lenStep);
    }
}
```

## 字符串排列
力扣第 567 题[「 字符串的排列」](https://leetcode.cn/problems/permutation-in-string/) ，难度中等：

这种题目，是明显的滑动窗口算法，相当给你一个 S 和一个 T，请问你 S 中是否存在一个子串，包含 T 中所有字符且不包含其他字符？

首先，先复制粘贴之前的算法框架代码，然后明确刚才提出的几个问题，即可写出这道题的答案：

对于这道题的解法代码，基本上和最小覆盖子串一模一样，只需要改变几个地方：

1、本题移动 left 缩小窗口的时机是窗口大小大于 t.size() 时，应为排列嘛，显然长度应该是一样的。

2、当发现 valid == need.size() 时，就说明窗口中就是一个合法的排列，所以立即返回 true。

至于如何处理窗口的扩大和缩小，和最小覆盖子串完全相同。

```java
class Solution {
    /**
    滑动窗口解法
     */
    public boolean checkInclusion(String s1, String s2) {
        Map<Character, Integer> needMap = new HashMap<>();
        Map<Character, Integer> windowMap = new HashMap<>();
        for (char c : s1.toCharArray()) {
            needMap.put(c, needMap.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        // 记录最小覆盖子串的起始索引 及 长度
        int startIndex = 0, lenStep = Integer.MAX_VALUE;
        while (right < s2.length()) {
            // c 是将移入窗口的字符
            char c = s2.charAt(right);
            // 扩大窗口
            right++;
            // 进行窗口内数据的一系列更新
            if (needMap.containsKey(c)) {
                windowMap.put(c, windowMap.getOrDefault(c, 0) + 1);
                //若窗口内某个字符 总数 和 needMap中的该字符的 数量相等时
                if (windowMap.get(c).equals(needMap.get(c))) {
                    valid++;
                }
            }

            // 判断左侧窗口是否要收缩 [注意此处为 s1.length()]
            while (right-left >= s1.length()) {
               // 在这里判断是否找到了合法的子串
                if (valid==needMap.size()) {
                    return true;
                }
                // d 是将移出窗口的字符
                char d = s2.charAt(left);
                // 缩小窗口
                left++;
                // 进行窗口内数据的一系列更新
                if (needMap.containsKey(d)) {
                    if (windowMap.get(d).equals(needMap.get(d))) {
                        valid--;
                    }
                    windowMap.put(d, windowMap.getOrDefault(d, 0) - 1);
                }
            }
        }
        return false;
    }
}
```

## 找所有字母异位词
力扣第 438 题[「 找到字符串中所有字母异位词」](https://leetcode.cn/problems/find-all-anagrams-in-a-string/) ，难度中等：

呵呵，这个所谓的字母异位词，不就是排列吗，搞个高端的说法就能糊弄人了吗？

相当于，输入一个串 S，一个串 T，找到 S 中所有 T 的排列，返回它们的起始索引。

```java
class Solution {
    /**
    滑动窗口解法
     */
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> result = new ArrayList<>();
        Map<Character, Integer> needMap = new HashMap<>();
        Map<Character, Integer> windowMap = new HashMap<>();
        for (char c : p.toCharArray()) {
            needMap.put(c, needMap.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        // 记录最小覆盖子串的起始索引 及 长度
        int startIndex = 0, lenStep = Integer.MAX_VALUE;
        while (right < s.length()) {
            // c 是将移入窗口的字符
            char c = s.charAt(right);
            // 扩大窗口
            right++;
            // 进行窗口内数据的一系列更新
            if (needMap.containsKey(c)) {
                windowMap.put(c, windowMap.getOrDefault(c, 0) + 1);
                //若窗口内某个字符 总数 和 needMap中的该字符的 数量相等时
                if (windowMap.get(c).equals(needMap.get(c))) {
                    valid++;
                }
            }

            // 判断左侧窗口是否要收缩 [注意此处为 p.length()]
            while (right-left >= p.length()) {
               // 在这里判断是否找到了合法的子串
                if (valid==needMap.size()) {
                    result.add(left);
                }
                // d 是将移出窗口的字符
                char d = s.charAt(left);
                // 缩小窗口
                left++;
                // 进行窗口内数据的一系列更新
                if (needMap.containsKey(d)) {
                    if (windowMap.get(d).equals(needMap.get(d))) {
                        valid--;
                    }
                    windowMap.put(d, windowMap.getOrDefault(d, 0) - 1);
                }
            }
        }
        return result;
    }
}

```
## 最长无重复子串
力扣第 3 题[「 无重复字符的最长子串」](https://leetcode.cn/problems/longest-substring-without-repeating-characters/) ，难度中等：
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


滑动窗口算法模板就讲到这里，希望大家能理解其中的思想，记住算法模板并融会贯通。回顾一下，遇到子数组/子串相关的问题，你只要能回答出来以下几个问题，就能运用滑动窗口算法：

1、什么时候应该扩大窗口？

2、什么时候应该缩小窗口？

3、什么时候得到一个合法的答案？

旨在强化你对算法的理解和记忆，以后就再也不怕子串、子数组问题了。

` 算法就是这样，再高大上的技巧，都是在最简单最基本的原理之上构建的. ` 

# 滑动窗口算法延伸：RABIN KARP 字符匹配算法
[refer to](https://labuladong.gitee.io/algo/2/20/28/) 
## 高效寻找重复子序列
### 重复的 DNA 序列
看下力扣第 187 题[「 重复的 DNA 序列」](https://leetcode.cn/problems/repeated-dna-sequences/)  ，我简单描述下题目：
```java
class Solution {
    /**
        穷举
     */
    public List<String> findRepeatedDnaSequences(String s) {
        // 记录出现过的子串
        Set<String> total = new HashSet<>();
        // 记录那些重复出现多次的子串
        // 注意要用哈希集合，防止记录重复的结果
        Set<String> repeat = new HashSet<>();

        for(int i=0;i+10<= s.length();i++){
            String str= s.substring(i,i+10);
            if(total.contains(str)){
                repeat.add(str);
            }else{
                total.add(str);
            }
        }
        return new ArrayList<>(repeat);
    }
}
```


# 零、二分查找框架
先给大家讲个笑话乐呵一下：

有一天阿东到图书馆借了 N 本书，出图书馆的时候，警报响了，于是保安把阿东拦下，要检查一下哪本书没有登记出借。阿东正准备把每一本书在报警器下过一下，以找出引发警报的书，但是保安露出不屑的眼神：你连二分查找都不会吗？于是保安把书分成两堆，让第一堆过一下报警器，报警器响；于是再把这堆书分成两堆…… 最终，检测了 logN 次之后，保安成功的找到了那本引起警报的书，露出了得意和嘲讽的笑容。于是阿东背着剩下的书走了。

从此，图书馆丢了 N - 1 本书（手动狗头）。

```java
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;

    while(...) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}

```
分析二分查找的一个技巧是：不要出现 else，而是把所有情况用 else if 写清楚，这样可以清楚地展现所有细节。本文都会使用 else if，旨在讲清楚，读者理解后可自行简化。

另外提前说明一下，计算 mid 时需要防止溢出，代码中 left + (right - left) / 2 就和 (left + right) / 2 的结果相同，但是有效防止了 left 和 right 太大，直接相加导致溢出的情况。


## 寻找一个数（基本的二分搜索）
力扣第 704 题[「 二分查找」](https://leetcode.cn/problems/binary-search/)
```java
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意

    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意
        else if (nums[mid] > target)
            right = mid - 1; // 注意
    }
    return -1;
}

```

## 寻找左侧边界的二分搜索
```java

```

## 寻找右侧边界的二分查找
```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 这里改成收缩左侧边界即可
            left = mid + 1;
        }
    }
    // 最后改成返回 left - 1
    if (left - 1 < 0) return -1;
    return nums[left - 1] == target ? (left - 1) : -1;
}

```
# 带权重的随机选择算法
[题解](https://labuladong.gitee.io/algo/2/20/30/)

## 按权重随机选择 [没看懂]
力扣第 528 题[「 按权重随机选择」](https://leetcode.cn/problems/random-pick-with-weight/)

```java
class Solution {

     // 前缀和数组
    private int[] preSum;
    private Random rand = new Random();
    
    public Solution(int[] w) {
        int n = w.length;
        // 构建前缀和数组，偏移一位留给 preSum[0]
        preSum = new int[n + 1];
        preSum[0] = 0;
        // preSum[i] = sum(w[0..i-1])
        for (int i = 1; i <= n; i++) {
            preSum[i] = preSum[i - 1] + w[i - 1];
        }
    }
    
    public int pickIndex() {
        int n = preSum.length;
        // 在闭区间 [1, preSum[n - 1]] 中随机选择一个数字
        int target = rand.nextInt(preSum[n - 1]) + 1;
        // 获取 target 在前缀和数组 preSum 中的索引
        // 别忘了前缀和数组 preSum 和原始数组 w 有一位索引偏移
        return left_bound(preSum, target) - 1;
    }
    // 搜索左侧边界的二分搜索
    private int left_bound(int[] nums, int target) {
        if (nums.length == 0) return -1;
        int left = 0, right = nums.length;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                right = mid;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                right = mid;
            }
        }
        return left;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(w);
 * int param_1 = obj.pickIndex();
 */
```
# 田忌赛马背后的算法决策
力扣第 870 题[「 优势洗牌」](https://leetcode.cn/problems/advantage-shuffle/)

基本思路

这题就像田忌赛马的情景，nums1 就是田忌的马，nums2 就是齐王的马，数组中的元素就是马的战斗力，你就是谋士孙膑，请你帮田忌安排赛马顺序，使胜场最多。

最优策略是将齐王和田忌的马按照战斗力排序，然后按照战斗力排名一一对比：

如果田忌的马能赢，那就比赛，如果赢不了，那就换个垫底的来送人头，保存实力。
```java
public int[] advantageCount(int[] nums1, int[] nums2) {
        int n = nums1.length;
        // 给 nums2 降序排序
        PriorityQueue<int[]> maxpq = new PriorityQueue<>(
                (int[] pair1, int[] pair2) -> {
                    return pair2[1] - pair1[1];
                }
        );
        for (int i = 0; i < n; i++) {
            maxpq.offer(new int[]{i, nums2[i]});
        }
        // 给 nums1 升序排序
        Arrays.sort(nums1);

        // nums1[left] 是最小值，nums1[right] 是最大值
        int left = 0, right = n - 1;
        int[] res = new int[n];

        while (!maxpq.isEmpty()) {
            int[] pair = maxpq.poll();
            // maxval 是 nums2 中的最大值，i 是对应索引
            int i = pair[0], maxval = pair[1];
            if (maxval < nums1[right]) {
                // 如果 nums1[right] 能胜过 maxval，那就自己上
                res[i] = nums1[right];
                right--;
            } else {
                // 否则用最小值混一下，养精蓄锐
                res[i] = nums1[left];
                left++;
            }
        }
        return res;
    }
```
```java
    /**
     *主要的思路:
     * 将问题转化成 已知B马出场顺序 以及B马的能力值 如何调整A的出场顺序 使能赢的场次最大
     *
     * 将A马由大到小排序
     * B从大到小排序,并记录B马原来的出场顺序()
     * 比较A当前最小的马和B当前最小的马
     * 如果A最小的马<=B最小的马 则让A最小的马和B最大的马对
     * 如果A最小的马>B最小的马 则让A最小的马和B最小的马对
     *
     */
    public int[] advantageCount(int[] nums1, int[] nums2) {

        /*让nums1马从小到大排序*/
        Arrays.sort(nums1);

        int len = nums2.length;
        //0 -> num2的马力值， 1 -> num2的马力值对应的原数组坐标
        int[][] sort2 = new int[len][2];
        for (int i = 0; i < len; i++) {
            sort2[i][0] = nums2[i];
            sort2[i][1] = i;
        }

        /*让 nums2马从小到大排序*/
        Arrays.sort(sort2, (a, b) -> a[0] - b[0]);

        // num2最小值坐标
        int left = 0;
        // num2最大值坐标
        int right = len - 1;
        int[] result = new int[len];
        for (int i = 0; i < len; i++) {
            /*当前的马比最小马小, 就和他最大的马对*/
            if (nums1[i] <= sort2[left][0]) {
                result[sort2[right][1]] = nums1[i];
                right--;

            } else {
                result[sort2[left][1]] = nums1[i];
                left++;
            }
        }
        return result;
    }
```

# 常数时间删除/查找数组中的任意元素 [不懂]

力扣第 380 题[「 常数时间插入、删除和获取随机元素」](https://leetcode.cn/problems/insert-delete-getrandom-o1/)
```java

```

## 避开黑名单的随机数
力扣第 710 题[「 黑名单中的随机数」](https://leetcode.cn/problems/random-pick-with-blacklist/)

# 数组去重
力扣第 316 题[「 去除重复字母」](https://leetcode.cn/problems/remove-duplicate-letters/)

同 第 1081 题[「不同字符的最小子序列」 ](https://leetcode.cn/problems/smallest-subsequence-of-distinct-characters/) 的解法是完全相同的

#其他
## 有效的数独
力扣第 36 题 [有效的数独](https://leetcode.cn/problems/valid-sudoku/)

- 看每行、每列、每个9*9区块内，是否有重复的数字.
> 大多数的哈希表计数问题，都能转换为使用数组解决。
> 
> 虽然时间复杂度一样，但哈希表的更新和查询复杂度为均摊 O(1)O(1)，而定长数组的的更新和查询复杂度则是严格 O(1)O(1)。
> 
> 因此从执行效率上来说，数组要比哈希表快上不少。

```java
class Solution {
     /**
     * 数组解法，
     * 从执行效率上来说，数组要比哈希表快上不少。
     */
    public boolean isValidSudoku(char[][] board) {
        boolean[][] row = new boolean[10][10];
        boolean[][] col = new boolean[10][10];
        //9*9区块
        boolean[][] area = new boolean[10][10];

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                int c = board[i][j];
                if (c == '.') {
                    continue;
                }
                int u = c - '0';
                int idx = i / 3 * 3 + j / 3;
                if (row[i][u] || col[j][u] || area[idx][u]) {
                    return false;
                }
                row[i][u] = col[j][u] = area[idx][u] = true;
            }
        }
        return true;
    }

    /**
     * 哈希解法
     * 大多数的哈希表计数问题，都能转换为使用数组解决。
     */
    public boolean isValidSudoku2(char[][] board) {

        Map<Integer, Set<Integer>> rowMap = new HashMap();
        Map<Integer, Set<Integer>> cloumnMap = new HashMap();
        //最小区块map
        Map<Integer, Set<Integer>> subMap = new HashMap();

        //初始化定义
        for (int i = 0; i < 9; i++) {
            rowMap.put(i, new HashSet<>());
            cloumnMap.put(i, new HashSet<>());
            subMap.put(i, new HashSet<>());
        }

        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                char c = board[i][j];
                if (c == '.') {
                    continue;
                }
                Integer v = c - '0';
                Integer idx = i / 3 * 3 + j / 3;
                if (rowMap.get(i).contains(v) || cloumnMap.get(j).contains(v) || subMap.get(idx).contains(v)) {
                    return false;
                }
                rowMap.get(i).add(v);
                cloumnMap.get(j).add(v);
                subMap.get(idx).add(v);
            }
        }
        return true;
    }
}
```

## 多数元素-E
第 169 题 [多数元素](https://leetcode.cn/problems/majority-element/)

给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。
```java
class Solution {
    /**
     * 法1：比较
     */
    public int majorityElement(int[] nums) {
        int target = 0;
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            if (count == 0) {
                target = nums[i];
            }
            if (nums[i] == target) {
                count++;
            } else {
                count--;
            }
        }
        return target;
    }

    /**
     * 法2：sort后取值
     */
    public int majorityElement2(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length  / 2];
    }
}
```
## 搜索二维矩阵 II-M
第 240 题 [搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：
- 每行的元素从左到右升序排列。
- 每 列的元素从上到下升序排列。

```java
class Solution {
    /**
     * 法1：Z 字形查找
     * 从右上角开始， 向下->数字变大； 向左->数字变小；
     * 也可从左下角开始，只有两个方向可以做到一个变大，一个变小即可
     */
    public boolean searchMatrix(int[][] matrix, int target) {
        int width = matrix[0].length;
        int len = matrix.length;

        int i = 0, j = width - 1;
        while (i < len && j >= 0) {
            if (matrix[i][j] == target) {
                return true;
            } else if (matrix[i][j] > target) {
                j--;
            } else if (matrix[i][j] < target) {
                i++;
            }
        }
        return false;
    }

    /**
     * 法2：直接查找, 全遍历
     */
    public boolean searchMatrix2(int[][] matrix, int target) {
        for (int[] row : matrix) {
            for (int element : row) {
                if (element == target) {
                    return true;
                }
            }
        }
        return false;
    }
}
```
## 其他
### 存在重复元素-E

217题 [存在重复元素](https://leetcode.cn/problems/contains-duplicate/)

给你一个整数数组 nums 。如果任一值在数组中出现 至少两次 ，返回 true ；如果数组中每个元素互不相同，返回 false 。

```javascript
    /**
     * 方法1：HashSet 解法
     */
    public boolean containsDuplicate1(int[] nums) {
        Set<Integer> set = new HashSet<Integer>();
        for (int x : nums) {
            if (!set.add(x)) {
                return true;
            }
        }
        return false;
    }

    /**
     * 方法2：先排序，然后再比较相邻元素是否相等
     */
    public boolean containsDuplicate2(int[] nums) {
        Arrays.sort(nums);
        int i = 1;
        while (i < nums.length) {
            if (nums[i] == nums[i - 1]) {
                return true;
            }
        }
        return false;
    }
```

### 除自身以外数组的乘积-M
第 238 题 [除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/)

给你一个整数数组nums，返回 数组answer，其中answer[i]等于nums中除nums[i]之外其余各元素的乘积。

题目数据 保证 数组nums之中任意元素的全部前缀元素和后缀的乘积都在 32 位 整数范围内。

请不要使用除法，且在O(n) 时间复杂度内完成此题。

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
