### 1、二位数组中的查找

在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        if(array == null){
            return false;
        }
         
        int row = 0;
        int col = array[0].length - 1;
         
        while(col >= 0 && row < array.length){
            if(array[row][col] == target){
                return true;
            }
            if(array[row][col] < target){
               row++;
            }else{
                col--;
            }
        }
        return false;
    }
}
```

### 2、替换空格

请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
        int spaceCount = 0;
        //查找有多少个空格
        for(int i = 0;i < str.length();i++){
            if(str.charAt(i) == ' '){
                spaceCount++;
            }
        }
         
        int first = str.length() - 1;
        int second = spaceCount * 2 + str.length() - 1;
        str.setLength(second + 1);
         
        while(first >= 0){
            if(str.charAt(first) == ' '){
                str.setCharAt(second--,'0');
                str.setCharAt(second--,'2');
                str.setCharAt(second--,'%');
            }else{
                str.setCharAt(second--,str.charAt(first));
            }
            first--;
        }
       return str.toString();
    }
}	
```

### 3、从尾到头打印链表

输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。

```java
/**
*    public class ListNode {
*        int val;
*        ListNode next = null;
*
*        ListNode(int val) {
*            this.val = val;
*        }
*    }
*
*/
import java.util.ArrayList;
import java.util.Stack;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        Stack<Integer> stack = new Stack<>();
        ArrayList<Integer> result = new ArrayList<>();
        ListNode current = listNode;
        while(current != null){
            stack.push(current.val);
            current = current.next;
        }
        while(!stack.isEmpty()){
            result.add(stack.pop());
        }
        return result;
    }
}
```

### 4、重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

```java
/**
 * Definition for binary tree
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        return process(pre,0,pre.length - 1,in,0,in.length - 1);
    }
     
    private TreeNode process(int [] pre,int pL,int pR,int []in ,int iL,int iR){
        if(pL > pR || iL > iR){
            return null;
        }
        TreeNode root = new TreeNode(pre[pL]);
        int lLen = 0;
        for (int i = iL; i <= iR && in[i] != pre[pL]; i++, lLen++) ;
        root.left = process(pre,pL + 1,pL + lLen,in,iL,iL + lLen - 1);
        root.right = process(pre,pL + lLen + 1,pR,in,iL + lLen + 1,iR);
       return root;
    }
}
```

### 5、用两个栈实现队列

用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

```java
import java.util.Stack;
 
public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
     
    public void push(int node) {
        stack1.push(node);
    }
     
    public int pop() {
        if(stack1.isEmpty() && stack2.isEmpty()){
            throw new RuntimeException("Queue is empty!");
        }
        if(stack2.isEmpty()){
            while(!stack1.isEmpty()){
                stack2.push(stack1.pop());
            }
        }
        return stack2.pop();
    }
}
```

### 6、旋转数组中最小的数字

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
**NOTE：**给出的所有元素都大于0，若数组大小为0，请返回0。

```java
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if(array.length == 0){
            return 0;
        }
        int l = 0;
        int r = array.length - 1;
        while(array[l] >= array[r]){
            if(r - l == 1){
                return array[r];
            }
             
            int mid = l + (r - l) / 2;
             
            if(array[mid] >= array[l]){
                l = mid;
            }else{
                r = mid;
            }
             
            if(array[mid] == array[l] && array[mid] == array[r]){
                for(int i = l + 1;i <= r;i++){
                    if(array[i] < array[i - 1]){
                        return array[i];
                    }
                }
            }
        }
        return array[l];
    }
}
```

### 7、斐波那契数列

大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。n<=39

```java
public class Solution {
    /** 方法一 递归 O(n^2)
    public int Fibonacci(int n) {
        if(n < 1){
            return 0;
        }
        if(n == 1 || n == 2){
            return 1;
        }
        return Fibonacci(n - 1) + Fibonacci(n - 2);
    }
    **/
     
    //方法二 O(n)
    public int Fibonacci(int n) {
        if(n < 1){
            return 0;
        }
        if(n == 1 || n == 2){
            return 1;
        }
        int a[] = new int[n + 1];
        a[1] = a[2] = 1;
         
        for(int i = 2;i <= n;i++){
            a[i] = a[i - 1] + a[i - 2];
        }
        return a[n];
    }
}
```

### 8、跳台阶

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

```java
public class Solution {
    
    /** 方法1  552ms
    public int JumpFloor(int target) {
        if(target < 1){
            return 0;
        }
        if(target == 1 || target == 2){
            return target;
        }
         return JumpFloor(target - 1) + JumpFloor(target - 2);
    }
    **/
     
    //方法2  15ms
    public int JumpFloor(int target){
        if(target < 1){
            return 0;
        }
        if(target == 1 || target == 2){
            return target;
        }
        int []a = new int[target + 1];
        a[1] = 1;
        a[2] = 2;
         
        for(int i = 3;i <= target;i++){
            a[i] = a[i - 1] + a[i - 2];
        }
         
        return a[target];
    }
}

```

### 9、变态跳台阶

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```java
public class Solution {
    public int JumpFloorII(int target) {
        if(target < 1){
            return 0;
        }
        if(target == 1 || target == 2){
            return target;
        }
        int sum = 1;
        for(int i = 1;i < target;i++){
            sum += JumpFloorII(i);
        }
        return sum;
    }
}
```

### 10、矩形覆盖

我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

```java
public class Solution {
    /**
     
    public int RectCover(int target) {
        if(target < 1){
            return 0;
        }
        if(target == 1 || target == 2){
            return target;
        }
        return RectCover(target - 1) + RectCover(target - 2);
    }
    **/
     
    public int RectCover(int target){
        if(target < 1){
            return 0;
        }
        if(target == 1 || target == 2){
            return target;
        }
        int a[] = new int[target + 1];
        a[0] = 0;
        a[1] = 1;
        a[2] = 2;
         
        for(int i = 3;i <= target;i++){
            a[i] = a[i - 1] + a[i - 2];
        }
         
        return a[target];
    }
}
```

### 11、二进制中1的个数

输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

```java
public class Solution {
    public int NumberOf1(int n) {
        int sum = 0;
        int flag = 1;
        while(flag != 0){
            if((n & flag) != 0){
                sum++;
            }
            flag <<= 1;
        }
        return sum;
    }
}
```

### 12、调整数组顺序使奇数位于偶数前面

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

```java
public class Solution {
    public void reOrderArray(int[] array) {
        int L = 0,R;
        while(L < array.length){
            while(L < array.length && odd(array[L])){ // 先找到第一个偶数
                L++;
            }
            R = L + 1;
            while(R < array.length && !odd(array[R])){ // 再在L 的后面开始找到第一个奇数
                R++;
            }
            // 注意此时arr[L]是偶数　　arr[R]是奇数　-->将 [L,..R-1]中的数　向后移动一个位置
            if(R < array.length){
                int t = array[R];
                for(int i = R-1; i >= L; i--)
                    array[i+1] = array[i];
                array[L] = t;
                L++;
            }else
                break;//查找失败 说明此时后面的都是偶数，可以退出了
        }
    }
    private boolean odd(int n){
        return (n&1) == 1 ? true : false;
    }
}

```

### 13、链表中倒数第k个节点

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
        ListNode cur = head;
        int len = 0;
        while(cur != null){
            len++;
            cur = cur.next;
        }
         
        if(k > len){
            return null;
        }
         
        cur = head;
        for(int i = 0;i < len - k;i++){
            cur = cur.next;
        }
        return cur;
    }
}

```

### 14、反转链表

输入一个链表，反转链表后，输出新链表的表头。

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
    public ListNode ReverseList(ListNode head) {
        if(head == null){
            return null;
        }
        ListNode cur = head;
        ListNode pre = null;
        ListNode next = null;
        while(cur != null){
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
}
```

### 15、合并两个排序链表

输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

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
    public ListNode Merge(ListNode list1,ListNode list2) {
        ListNode p1 = list1;
        ListNode p2 = list2;
        ListNode temp = new ListNode(-1);
        ListNode p3 = temp;
         
        while(p1 != null && p2 != null){
            if(p1.val < p2.val){
                p3.next = p1;
                p1 = p1.next;
            }else{
                p3.next = p2;
                p2 = p2.next;
            }
            p3 = p3.next;
        }
      //      while(p1 != null){
     //           p3.next = p1;
      //          p1 = p1.next;
       //         p3 = p3.next;
       //     }
             
        //    while(p2 != null){
        //        p3.next = p2;
        //        p2 = p2.next;
         //       p3 = p3.next;
        //    }
        if(p1 != null){
            p3.next = p1;
        }
        if(p2 != null){
            p3.next = p2;
        }
         
        return temp.next;
    }
}
```

### 16、树的子结构

输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
 
    public TreeNode(int val) {
        this.val = val;
 
    }
 
}
*/
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        if(root1 == null || root2 == null){
            return false;
        }
        boolean result = false;
        if(root1.val == root2.val){
            result = AcontainsB(root1,root2);
        }
        if(!result){
            result = HasSubtree(root1.left,root2);
        }
        if(!result){
            result = HasSubtree(root1.right,root2);
        }
        return result;
    }
     
    //判断
    private boolean AcontainsB(TreeNode r1,TreeNode r2){
        if(r2 == null){
            return true;
        }
        if(r1 == null){
            return false;
        }
        return r1.val == r2.val && AcontainsB(r1.left,r2.left) && AcontainsB(r1.right,r2.right);
    }
} 
```

### 17、二叉树的镜像

操作给定的二叉树，将其变换为源二叉树的镜像。

```
二叉树的镜像定义：源二叉树 
    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9 11
    	镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7  5
```

```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
 
    public TreeNode(int val) {
        this.val = val;
 
    }
 
}
*/
public class Solution {
    public void Mirror(TreeNode root) {
        if(root == null){
            return;
        }
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        Mirror(root.left);
        Mirror(root.right);
    }
}	
```

### 18、顺时针打印矩阵

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
        
        if(matrix == null || matrix.length < 0 || matrix[0].length < 0){
            return null;
        }
        ArrayList<Integer> result = new ArrayList<Integer>();
        int ar = 0;int ac = 0;
        int br = matrix.length - 1;  int bc = matrix[0].length - 1;
        while(ar <= br && ac <= bc){
            print(ar++,ac++,br--,bc--,matrix,result);
        }
        return result;
    }
     
    private void print
        (int ar, int ac, int br, int bc, int[][] matrix, ArrayList<Integer> result){
        if(ar == br){
            for(int i = ac;i <= bc;i++){
                 result.add(matrix[ar][i]);
            }
        }else if(ac == bc){
            for(int i = ar;i <= br;i++){
                result.add(matrix[i][ac]);
            }
        }else{
            for(int i = ac;i < bc;i++){
                result.add(matrix[ar][i]);
            }
            for(int i = ar;i < br;i++){
                result.add(matrix[i][bc]);
            }
            for(int i = bc;i > ac;i--){
                result.add(matrix[br][i]);
            }
            for(int i = br;i > ar;i--){
                result.add(matrix[i][ac]);
            }
        }
    }
}
```

### 19、包含min函数的栈

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

```java
import java.util.Stack;
 
public class Solution {
     
   Stack<Integer> stackData = new Stack<>();// 数据栈
   Stack<Integer> stackMin = new Stack<>();// 辅助栈
     
    public void push(int node) {
        stackData.push(node);
        if(stackMin.isEmpty()){
            stackMin.push(node);
        }else {
            if(node <= stackMin.peek()){
                stackMin.push(node);
            }
        }
    }
     
    public void pop() {
        int top = stackData.pop();
        if(top == stackMin.peek())
            stackMin.pop();
    }
     
    public int top() {
        if(stackData.isEmpty())
            throw new RuntimeException("stack is empty!");
        return stackData.peek();
    }
     
    public int min() {
        if(stackMin.isEmpty())
            throw new RuntimeException("stack is empty!");
        return stackMin.peek();
    }
}
```

### 20、栈的压入和弹出序列

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

```java
import java.util.ArrayList;
import java.util.Stack;
 
public class Solution {
    public boolean IsPopOrder(int[] pushA,int[] popA) {
        Stack<Integer>stack = new Stack<>();
        int popIndex = 0;
        for(int i = 0; i < pushA.length; i++){
            if(pushA[i] == popA[popIndex])
                popIndex++;
            else {
                stack.push(pushA[i]);
            }
        }
        while(!stack.isEmpty()){
            if(stack.pop() != popA[popIndex++])
                return false;
        }
        return true;
    }   
}
```

### 21、从上往下打印二叉树

从上往下打印出二叉树的每个节点，同层节点从左至右打印。

```java
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
 
    public TreeNode(int val) {
        this.val = val;
 
    }
 
}
*/
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        if(root != null){
            Queue<TreeNode> queue = new LinkedList<TreeNode>();
            TreeNode cur = null;
            queue.add(root);
            while(!queue.isEmpty()){
                cur = queue.poll();
                result.add(cur.val);
                if(cur.left != null){
                    queue.add(cur.left);
                }
                if(cur.right != null){
                    queue.add(cur.right);
                }
            }
        }
        return result;
    }
}
```

### 22、二叉搜索树的后序遍历序列

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

```java
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if(sequence == null || sequence.length == 0){
            return false;
        }
        return process(sequence,0,sequence.length - 1);
    }
     
    private boolean process(int [] sequence, int L, int R){
        if(L >= R){
            return true;
        }
        int root = sequence[R];
        int i = L;
        while(i <= R - 1 && sequence[i] < root){
            i++;
        }
        int mid = i;
        while(i <= R - 1){
            if(sequence[i] < root){
                return false;
            }
            i++;
        }
         
        return process(sequence,L,mid - 1) && process(sequence,mid,R - 1);
    }
}

```

### 23、二叉树中和为某一值的路径

输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

```java
import java.util.ArrayList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
 
    public TreeNode(int val) {
        this.val = val;
 
    }
 
}
*/
public class Solution {
    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root,int target) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        if(root == null){
            return result;
        }
        ArrayList<Integer> path = new ArrayList<Integer>();
        process(root, target, 0, result, path);
        return result;
    }
    private void process(TreeNode root,int target,
                            int sum,ArrayList<ArrayList<Integer>> result,ArrayList<Integer> path){
        if(root == null){
            return;
        }
        path.add(root.val);
        if(root.val + sum == target && root.left == null && root.right == null){
            result.add(new ArrayList<Integer>(path));
        }
        process(root.left, target, sum+root.val, result, path);
        process(root.right, target, sum+root.val, result, path);
         
        path.remove(path.size() - 1); //关键的一步 回溯
    }
}	
```

### 24、复杂链表的复制

输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

```java
import java.util.HashMap;
/*
public class RandomListNode {
    int label;
    RandomListNode next = null;
    RandomListNode random = null;
 
    RandomListNode(int label) {
        this.label = label;
    }
}
*/
public class Solution {
    public RandomListNode Clone(RandomListNode pHead)
    {
        if(pHead == null){
            return null;
        }
        HashMap<RandomListNode,RandomListNode> map = new HashMap<RandomListNode,RandomListNode>();
        RandomListNode cur = pHead;
        while(cur != null){
            map.put(cur,new RandomListNode(cur.label));
            cur = cur.next;
        }
         
        cur = pHead;
        while(cur != null){
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur = cur.next;
        }
        return map.get(pHead);
    }
}
```

### 25、二叉搜索树和双向链表

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

```java
import java.util.Stack;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
 
    public TreeNode(int val) {
        this.val = val;
 
    }
 
}
*/
public class Solution {
    public TreeNode Convert(TreeNode pRootOfTree) {
        if(pRootOfTree == null){
            return null;
        }
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode pre = null;
        TreeNode result = null;
        TreeNode cur = pRootOfTree;
        boolean isFirst = true;
         
        while(cur != null || !stack.isEmpty()){
            if(cur != null){
                stack.push(cur);
                cur = cur.left;
            }else{
                cur = stack.pop();
                 
                if(isFirst){
                    isFirst = false;
                    result = cur;
                    pre = cur;
                }else{
                    pre.right = cur;
                    cur.left = pre;
                    pre = cur;
                }
            cur = cur.right;
            }
        }
        return result;
    }
}
```

### 26、数组中出现次数超过一半的数字

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

```java
import java.util.HashMap;
 
public class Solution {
    public int MoreThanHalfNum_Solution(int [] array) {
        if(array.length == 0 && array == null){
            return 0;
        }
        if(array.length == 1){
            return array[0];
        }
        HashMap<Integer,Integer> map = new HashMap<Integer,Integer>();
        for(int i = 0;i < array.length;i++){
            if(map.containsKey(array[i])){
                map.put(array[i],map.get(array[i]) + 1);
                if(map.get(array[i]) > array.length / 2){
                    return array[i];
                }
            }else{
                map.put(array[i],1);
            }
        }
        return 0;
    }
}
	
```

### 27、最小的K个数

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

```java
//方法一：用java中内置的优先级队列（大根堆）
/**
import java.util.ArrayList;
import java.util.PriorityQueue;
import java.util.Comparator;
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        if(input == null || k <= 0 || k > input.length){
            return result;
        }
        PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer>(
        new Comparator<Integer>(){
            public int compare(Integer a, Integer b){
                return a < b ? 1 : (a == b ? 0 : -1);
            }
        }
        );
         
        for(int i = 0;i < input.length;i++){
            if(maxHeap.size() < k){
                maxHeap.add(input[i]);
            }else if(input[i] < maxHeap.peek()){
                maxHeap.poll();
                maxHeap.add(input[i]);
            }
        }
         
        for(Integer i : maxHeap){
            result.add(i);
        }
        return result;
    }
}
**/
 
//方法二：自己写一个大根堆
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        if(input == null || k <= 0 || k > input.length){
             return result;
         }
        int[] maxHeap = new int[k];
        for(int i = 0;i < k;i++){
            siftUp(maxHeap,input[i],i);
        }
        for(int i = k;i < input.length;i++){
            if(maxHeap[0] > input[i]){
                maxHeap[0] = input[i];
                siftDown(maxHeap,0,k);
            }
        }
         
        for(int i = 0;i < k;i++){
            result.add(maxHeap[i]);
        }
        return result;
    }
     
    private void siftUp(int[] maxHeap,int num,int index){
        maxHeap[index] = num;
        while(maxHeap[index] > maxHeap[(index - 1) / 2]){
            swap(maxHeap,index,(index - 1) / 2);
            index = (index - 1) / 2;
        }
    }
     
    private void siftDown(int[] maxHeap,int i,int heapSize){
        int L = 2 * i + 1;
        while(L < heapSize){
            int maxIndex = L + 1 < heapSize && maxHeap[L + 1] > maxHeap[L] ? L + 1 : L;
            maxIndex = maxHeap[i] > maxHeap[maxIndex] ? i : maxIndex;
            if(maxIndex == i){
                break;
            }
            swap(maxHeap,maxIndex,i);
            i = maxIndex;
            L = 2 * i + 1;
        }
    }
     
    private void swap(int[] arr, int a ,int b){
        int temp = arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }
}
```

