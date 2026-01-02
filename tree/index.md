# 算法-二叉树

# 二叉树

`二叉树题型主要是用来培养递归思维的`

## 二叉树解题的思维模式分两类：

1、是否可以通过遍历一遍二叉树得到答案？如果可以，用一个 traverse 函数配合外部变量来实现，这叫「遍历」的思维模式。

2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值，这叫「分解问题」的思维模式。

无论使用哪种思维模式，你都需要思考：

如果单独抽出一个二叉树节点，它需要做什么事情？需要在什么时候（前/中/后序位置）做？其他的节点不用你操心，递归函数会帮你在所有节点上执行相同的操作。

二叉树的构造问题一般都是使用「分解问题」的思路：构造整棵树 = 根节点 + 构造左子树 + 构造右子树。


## 二叉树的重要性
举个例子，比如两个经典排序算法 快速排序 和 归并排序，对于它俩，你有什么理解？

如果你告诉我，`快速排序`就是个二叉树的`前序遍历`，`归并排序`就是个二叉树的`后序遍历`，那么我就知道你是个算法高手了。


## 深入理解前中后序
几个问题，请默默思考 30 秒：

1、你理解的二叉树的前中后序遍历是什么，仅仅是三个顺序不同的 List 吗？

2、请分析，后序遍历有什么特殊之处？

3、请分析，为什么多叉树没有中序遍历？

二叉树遍历框架
```java
void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // 前序位置
    traverse(root.left);
    // 中序位置
    traverse(root.right);
    // 后序位置
}
```

> 单链表和数组的遍历可以是迭代的，也可以是递归的，二叉树这种结构无非就是二叉链表，由于没办法简单改写成迭代形式，所以一般说二叉树的遍历框架都是指递归的形式。

你也注意到了，只要是递归形式的遍历，都可以有前序位置和后序位置，分别在递归之前和递归之后。

所谓前序位置，就是刚进入一个节点（元素）的时候，后序位置就是即将离开一个节点（元素）的时候，那么进一步，你把代码写在不同位置，代码执行的时机也不同：

比如说，如果让你倒序打印一条单链表上所有节点的值，你怎么搞？

实现方式当然有很多，但如果你对递归的理解足够透彻，可以利用后序位置来操作：
```java
/* 递归遍历单链表，倒序打印链表元素 */
void traverse(ListNode head) {
    if (head == null) {
        return;
    }
    traverse(head.next);
    // 后序位置
    print(head.val);
}
```

为什么多叉树没有中序位置，因为二叉树的每个节点只会进行唯一一次左子树切换右子树，而多叉树节点可能有很多子节点，会多次切换子树去遍历，所以多叉树节点没有「唯一」的中序遍历位置。

二叉树的所有问题，就是让你在前中后序位置注入巧妙的代码逻辑，去达到自己的目的，你只需要单独思考每一个节点应该做什么，其他的不用你管，抛给二叉树遍历框架，递归会在所有节点上做相同的操作。

## 两种解题思路
二叉树题目的递归解法可以分两类思路，第一类是遍历一遍二叉树得出答案，第二类是通过分解问题计算出答案，这两类思路分别对应着 回溯算法核心框架 和 动态规划核心框架。

一棵二叉树的前序遍历结果 = 根节点 + 左子树的前序遍历结果 + 右子树的前序遍历结果。

# 题解
## 二叉树的最大深度-E
力扣第 104 题 [二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

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

## 树的前序遍历结果

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
中序和后序遍历也是类似的，只要把 add(root.val) 放到中序和后序对应的位置就行了。

这个解法短小精干，但为什么不常见呢？

一个原因是这个算法的复杂度不好把控，比较依赖语言特性。

Java 的话无论 ArrayList 还是 LinkedList，addAll 方法的复杂度都是 O(N)，所以总体的最坏时间复杂度会达到 O(N^2)，除非你自己实现一个复杂度为 O(1) 的 addAll 方法，底层用链表的话并不是不可能。

当然，最主要的原因还是因为教科书上从来没有这么教过……

上文举了两个简单的例子，但还有不少二叉树的题目是可以同时使用两种思路来思考和求解的，这就要`靠你自己多去练习和思考`，不要仅仅满足于一种熟悉的解法思路。

综上，遇到一道二叉树的题目时的通用思考过程是：

1、是否可以通过遍历一遍二叉树得到答案？如果可以，用一个 traverse 函数配合外部变量来实现。

2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值。

3、无论使用哪一种思维模式，你都要明白二叉树的每一个节点需要做什么，需要在什么时候（前中后序）做。

## 后序位置的特殊之处
前序位置的代码执行是自顶向下的，而后序位置的代码执行是自底向上的：

前序位置的代码只能从函数参数中获取父节点传递来的数据，而后序位置的代码不仅可以获取参数数据，还可以获取到子树通过函数返回值传递回来的数据。

举具体的例子，现在给你一棵二叉树，两个简单的问题：

1、如果把根节点看做第 1 层，如何打印出每一个节点所在的层数？

2、如何打印出每个节点的左右子树各有多少节点？

第一个问题可以这样写代码：
```java
// 二叉树遍历函数
void traverse(TreeNode root, int level) {
    if (root == null) {
        return;
    }
    // 前序位置
    printf("节点 %s 在第 %d 层", root, level);
    traverse(root.left, level + 1);
    traverse(root.right, level + 1);
}

// 这样调用
traverse(root, 1);
```

第二个问题可以这样写代码：
```java
// 定义：输入一棵二叉树，返回这棵二叉树的节点总数
int count(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int leftCount = count(root.left);
    int rightCount = count(root.right);
    // 后序位置
    printf("节点 %s 的左子树有 %d 个节点，右子树有 %d 个节点",
            root, leftCount, rightCount);

    return leftCount + rightCount + 1;
}

```
这两个问题的根本区别在于：
- 一个节点在第几层，你从根节点遍历过来的过程就能顺带记录；
- 而以一个节点为根的整棵子树有多少个节点，你需要遍历完子树之后才能数清楚。

结合这两个简单的问题，你品味一下后序位置的特点，只有后序位置才能通过返回值获取子树的信息。

那么换句话说，一旦你发现题目和子树有关，那大概率要给函数设置合理的定义和返回值，在后序位置写代码了。

## 二叉树的直径-E
力扣第 543 题 [ 二叉树的直径 ](https://leetcode.cn/problems/diameter-of-binary-tree/) ，让你计算一棵二叉树的最长直径长度。

所谓二叉树的「直径」长度，就是任意两个结点之间的路径长度。最长「直径」并不一定要穿过根结点，比如下面这棵二叉树

解决这题的关键在于，每一条二叉树的「直径」长度，就是一个节点的左右子树的最大深度之和。

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

## 层序遍历
`二叉树题型主要是用来培养递归思维的`，而层序遍历属于迭代遍历，也比较简单，这里就过一下代码框架吧：
```java
// 输入一棵二叉树的根节点，层序遍历这棵二叉树
void levelTraverse(TreeNode root) {
    if (root == null) return;
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);

    // 从上到下遍历二叉树的每一层
    while (!q.isEmpty()) {
        int sz = q.size();
        // 从左到右遍历每一层的每个节点
        for (int i = 0; i < sz; i++) {
            TreeNode cur = q.poll();
            // 将下一层节点放入队列
            if (cur.left != null) {
                q.offer(cur.left);
            }
            if (cur.right != null) {
                q.offer(cur.right);
            }
        }
    }
}

```
这里面 while 循环和 for 循环分管从上到下和从左到右的遍历.


## 翻转二叉树-E
力扣第 226 题 [翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

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

## 填充节点的右侧指针-M
力扣第 116 题 [填充每个二叉树节点的右侧指针](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/) ，看下题目：

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

## 将二叉树展开为链表 【不懂】
力扣第 114 题 [将二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/) ，看下题目

给你二叉树的根结点 root ，请你将它展开为一个单链表：
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

## 最大二叉树-M[复习]
力扣第 654 题 [最大二叉树](https://leetcode.cn/problems/maximum-binary-tree/) ，看下题目

给定一个不重复的整数数组nums 。最大二叉树可以用下面的算法从nums 递归地构建:

创建一个根节点，其值为nums 中的最大值。
递归地在最大值左边的子数组前缀上构建左子树。
递归地在最大值 右边 的子数组后缀上构建右子树。
返回nums 构建的 最大二叉树。

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

## 从前序与中序遍历序列构造二叉树-M【不理解】

力扣第 105 题 [从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) ，看下题目

给定两个整数数组preorder 和 inorder，其中preorder 是二叉树的先序遍历， inorder是同一棵树的中序遍历，请构造二叉树并返回其根节点。

```java

```

## 通过后序和中序遍历结果构造二叉树-M [不理解]
类似上一题，这次我们利用后序和中序遍历的结果数组来还原二叉树，这是力扣第 106 题 [从后序和中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) 
```java

```

## 根据前序和后序遍历构造二叉树-M[不理解]
这是力扣第 889 题 [根据前序和后序遍历构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/) ，给你输入二叉树的前序和后序遍历结果，让你还原二叉树的结构。

通过前序中序，或者后序中序遍历结果可以确定唯一一棵原始二叉树，但是通过前序后序遍历结果无法确定唯一的原始二叉树。
```java

```

## 寻找重复的子树
力扣第 652 题 [寻找重复的子树](https://leetcode.cn/problems/find-duplicate-subtrees/)
给定一棵二叉树 root，返回所有重复的子树。

对于同一类的重复子树，你只需要返回其中任意一棵的根结点即可。

如果两棵树具有相同的结构和相同的结点值，则它们是重复的。
```java

```
