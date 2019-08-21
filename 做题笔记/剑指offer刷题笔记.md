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

