## 1.二维数组中的查找

题目描述：在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

解题思路：最开始想到在二维数组中用dfs，数组array【0】【0】会是数组中最小的数，依次向右和下两个方向查找，知道找到target返回，或者右侧和下方数均大于target，返回False但是结果显示超时。

后来看了讨论部分，具体思路是从二维数组左下角bottom left(bl)入手，若target比bl大，向右移，若target比bl小，向上移，相等则返回，若搜索超出边界返回false。

代码：

```cpp
bool Find(int target, vector > array) {
        int m = array.size();
        if (!m) return false;
        int n = array[0].size();
        int i = m - 1, j = 0;
        for (; i >= 0 && j < n; ) {
            if (array[i][j] == target) return true;
            else if (array[i][j] < target) j++;
            else i--;
        }
        return false;
    }
```

## 2.替换空格

题目描述：请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。函数参数length为最大长度。

解题思路：首先遍历一次得到空格的数量cnt，得到str数组长度sum，可以得到返回str长度为sum+2cnt，一个space变成三个char字符，增加长度2.在str数组中从后往前遍历，遇到space变为%20，否则向后移，其中注意结束‘\0'的赋值。

代码：

```cpp
void replaceSpace(char *str, int length) {
    if (str == NULL) return;
    int cnt = 0, sum = 0;
    for (int i = 0; str[i] != '\0'; i++) {
        if (*(str + i) == ' ') {
            cnt++;
        }
        sum++;
    }
    cnt *= 2;
    if (sum + cnt > length) return;
    *(str + sum + cnt) = *(str + sum);
    for (int i = sum - 1; i >= 0; i--) {
        if (*(str + i) == ' ') {
            *(str + i + cnt) = '0';
            *(str + i + cnt - 1) = '2';
            *(str + i + cnt - 2) = '%';
            cnt -= 2;
        }
        else {
            *(str + i + cnt) = *(str + i);
        }
    }
}
```

## 3. 从尾到头打印链表

题目描述：输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

解题思路：这道题有很多种解法

1.  先遍历一次得到长度cnt，开辟长为cnt的数组，一边遍历链表一边从后往前赋值进数组中。
    
2.  先原地反转链表，时间复杂度o(N),空间复杂度0(1)，再从头到尾遍历，将值push进数组。
    
3.  开辟stack，一边遍历链表一边push in stack，遍历结束出栈进数组中。
    
4.  遍历链表并入数组，再reverse数组
    
5.  递归入数组。
    

代码：

```cpp
//先遍历一次得到长度cnt，开辟长为cnt的数组，一边遍历链表一边从后往前赋值进数组中。
vector printListFromTailToHead(ListNode* head) {
        ListNode* p = head;
        int cnt = 0;
        while (p) {
            cnt++;
            p = p->next;
        }
        vector v(cnt, 0);
        p = head;
        while (p) {
            v[--cnt] = p->val;
            p = p->next;
        }
        return v;
    }
//先原地反转链表，时间复杂度o(N),空间复杂度0(1)，再从头到尾遍历，将值push进数组。
vector printListFromTailToHead(ListNode* head) {
        vector v;
        if (!head) return v;
        ListNode* p = head->next, * r = p;
        if (!p) {
            v.push_back(head->val);
            return v;
        }
        head->next = NULL;
        while (p) {
            r = r->next;
            p->next = head;
            head = p;
            p = r;
        }
        while (head) {
            v.push_back(head->val);
            head = head->next;
        }
        return v;
    }
//递归入数组
vector v;
vector printListFromTailToHead(ListNode* head) {

    if (head) {

        printListFromTailToHead(head->next);

        v.push_back(head->val);

        return v;

    } else {

        return v;

    }
}
```

## 4.重建二叉树

题目描述：输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

解题思路：递归思想，首先找到根root（即pre数组pl位，其中pl表示pre数组左下标，pr表示pre数组右下标），再结合in数组找到root左子树部分和右子树部分，递归求解并返回节点。

代码：

```cpp
TreeNode* create(vector pre, int pl, int pr, vector in, int il, int ir) {
        if (pl > pr || il > ir) return NULL;
        TreeNode *root = new TreeNode(pre[pl]);
        int mid = il;
        for (; mid <= ir; mid++) {
            if (in[mid] == pre[pl]) break;
        }
        root->left = create(pre, pl+1, pl+mid-il, in, il, mid-1);
        root->right = create(pre, pl+mid-il+1, pr, in, mid+1, ir);

        return root;
    }
    TreeNode* reConstructBinaryTree(vector pre,vector in) {
        TreeNode* root = create(pre, 0, (int)pre.size()-1, in, 0, (int)in.size()-1);
        return root;
    }
```

## 5.用两个栈实现队列

题目描述：用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

题解思路：这道题比较简单，维持两个栈，st1用来装数据,push操作即在st1中入栈，pop操作：st1倒入st2，记录ans=st2.top(); st2出栈最顶端元素，再将st2倒入st1中以保持原来相对位置。题目没有表明判空返回值就省略了。

代码：

```cpp
class Solution
{
public:
    void push(int node) {
        st1.push(node);
    }

    int pop() {
        while (st1.size()) {
            st2.push(st1.top());
            st1.pop();
        }
        int ans = st2.top();
        st2.pop();
        while (st2.size()) {
            st1.push(st2.top());
            st2.pop();
        }
        return ans;
    }

private:
    stack st1;
    stack st2;
};
```

## 6.旋转数组的最小数字(不太会)

题目描述：把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

解题思路：找到数组中间的数，并用left表示左边比较数的下标，right表示右边比较数的下标。一般来说，数组旋转非零位且无重复数的情况下，v【left】>v[right】，将mid与left位数比较，如果中间元素大于第一个元素，则中间元素位于前递增数组中，如果中间元素小于第一个元素，则中间元素位于后递增数组中。如果第一个元素和最后一个元素数值相等，则无法比较中间元素位于哪部分，采用顺序遍历。

代码：

```cpp
int Find_Min(vector v, int left, int right) {
        int ans = INT_MAX;
        for (int i = left; i <= right; i++) {
            ans = min(ans, v[i]);
        }
        return ans;
    }
    int minNumberInRotateArray(vector v) {
        if (v.size() == 0) return 0;
        int left = 0, right = (int)v.size()-1, ans = v[0], mid;
        if (v[left] == v[right]) {
            ans = Find_Min(v, left, right);
        } else {
            while (left < right) {
                if (right - left == 1) {
                    ans = v[right];
                    break;
                }
                mid = left + (right - left)/2;
                if (v[mid] > v[left]) left = mid;
                else if (v[mid] < v[left]) right = mid;
                else {
                    ans = Find_Min(v, left, right);
                    break;
                }
            }
            ans = min(ans, v[right]);
        }
        return ans;
    }
```

## 7.斐波那契数列

题目描述：大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0，第1项是1），n<=39。

代码：

```cpp
int Fibonacci(int n) {
        vector v = {0, 1};
        if (n < 2) return v[n];
        int a = 0, b = 1;
        for (int i = 2; i <= n; i++) {
            b += a;
            a = b-a;
        }
        return b;
    }
```

## 8.跳台阶

题目描述：一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

代码：

```cpp
int jumpFloor(int number) {
        vectordp = {1, 1};
        for (int i = 2; i <= number; i++) {
            dp.push_back(dp[i-1]+dp[i-2]);
        }
        dp[0] = 0;

        return dp[number];
    }
//or
int jumpFloor(int number) {
        if (number == 0) return 0;

        if (number == 1) return 1;
        int a = 1, b = 1;
        for (int i = 2; i <= number; i++) {
            b = a+b; 
            a = b-a;
        }
        return b;
    }
//or
int jumpFloor(int number) {
        if (number == 0) return 0;
        if(number == 1) return 1;
        else return jumpFloor(number-1)+jumpFloor(number-2);
    }
```

## 9.变态跳台阶

题目描述：一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

解题思路：分析前几种情况，可以知道结果是有规律的，0,1,2,4,8,16...

代码：

```cpp
int jumpFloorII(int n) {
        if (n == 0) return 0;
        return pow(2, n-1);
    }
```

## 10.矩形覆盖

题目描述：我们可以用2x1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2x1的小矩形无重叠地覆盖一个2xn的大矩形，总共有多少种方法？

代码：

```cpp
int rectCover(int number) {
        if (number == 0) return 0;
        if (number == 1) return 1;
        if (number == 2) return 2;
        return rectCover(number-1)+rectCover(number-2);
    }
```

## 11.二进制中1的个数

题目描述：输入一个整数，输出该数32位二进制表示中1的个数。其中负数用补码表示。

代码：

```cpp
int  NumberOf1(int n) {
         int ans = 0;
         for (int i = 0; i < 32; i++) {
             ans += (n>>i)&0x1;
         }
         return ans;
     }
//another solution
int  NumberOf1(int n) {
         int ans = 0;
         while (n) {
             ans++;
             n = n & (n - 1);
         }
         return ans;
     }
```

## 12.数值的整数次方

题目描述：给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

保证base和exponent不同时为0

解题思路：将exponent拆分成几个数字的和，我们知道任何数字都可以用8421码表示，所以exponent一定可以拆分。如：2^3=2^(1+2)=2^(0011)=2^(0010+0001)=2^0010*2^0001

如何拆分exponent，可以看exponent二进制位中有几个1。

代码：

```cpp
double Power(double b, int e) {
        if (e == 0) return 1;
        int n = e;
        if (n < 0) n *= (-1);
        double ans = 1, k = 0;
        while (n) {
            if (n&1) ans *= b;
            n = n>>1;
            b *= b;
        }
        return e < 0 ? 1/ans: ans;
    }
```

## 13.调整数组顺序使奇数位于偶数前面

题目描述：输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

代码：

```cpp
void reOrderArray(vector &array) {
        int k = 0;
        vector tmp;
        for (int i = 0; i < array.size(); i++) {
            if (array[i] % 2 == 1) {
                array[k++] = array[i];
            } else {
                tmp.push_back(array[i]);
            }
        }
        for (int i = 0; i < tmp.size(); i++) {
            array[k++] = tmp[i];
        }
    }
//other solution
void reOrderArray(vector &array) {
        stable_partition(array.begin(), array.end(), [](int n) {return (n&1) == 1;});
    }//using stable_partition to put odd number before.
```

## 14.链表中倒数第k个结点

题目描述：输入一个链表，输出该链表中倒数第k个结点。

解题思路：首先遍历一次链表，得到长度len，再来顺序遍历找到第len-k+1个结点，即为结果结点。

还有种方法是，设置两个指针p和q，令p先走k步，此时p在第k个位置，q在初始位置，然后p和q一起往后移动，知道p到达结尾处，此时q指向的位置是倒数第k个位置。

代码：

```cpp
ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        int len = 0;
        ListNode* p = pListHead;
        while (p) {
            len++;
            p = p->next;
        }
        if (k > len) return NULL;
        p = pListHead;
        len = len - k;
        while (len) {
            p = p->next;
            len--;
        }
        return p;
    }
//another solution
ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        if (!pListHead || k == 0) return NULL;
        ListNode* p = pListHead, *q = pListHead;
        while (k>1) {
            if (p->next) p = p->next;
            else return NULL;
            k--;
        }
        while (p->next) {
            p = p->next;
            q = q->next;
        }
        return q;
    }
```

## 15.反转链表

题目描述：输入一个链表，反转链表后，输出新链表的表头。

代码：

```cpp
ListNode* ReverseList(ListNode* head) {
        if (!head) return NULL;
        ListNode* p = head->next, *r = p;
        if (!p) {
            return head;
        }
        head->next = NULL;
        while (p) {
            r = r->next;
            p->next = head;
            head = p;
            p = r;
        }
        return head;
    }
```

## 16.合并两个排序的链表

题目描述：输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

解题思路：第一直觉想到两个链表边移边比较，后面看了解析发现有递归版本，均写下。

代码：

```cpp
ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if (!pHead1) return pHead2;
        if (!pHead2) return pHead1;
        ListNode* head;
        if (pHead1->val <= pHead2->val) {
            head = pHead1;
            pHead1 = pHead1->next;
        } else {
            head = pHead2;
            pHead2 = pHead2->next;
        }
        ListNode* p = head;
        while (pHead1 && pHead2) {
            if(pHead1->val < pHead2->val) {
                p->next = pHead1;
                pHead1 = pHead1->next;
            } else {
                p->next = pHead2;
                pHead2 = pHead2->next;
            }
            p = p->next;
        }
        while (pHead1) {
            p->next = pHead1;
            pHead1 = pHead1->next;
            p = p->next;
        }
        while (pHead2) {
            p->next = pHead2;
            pHead2 = pHead2->next;
            p = p->next;
        }
        return head;
    }
//another solution
ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if (!pHead1) return pHead2;
        if (!pHead2) return pHead1;
        if (pHead1->val <= pHead2->val) {
            pHead1->next = Merge(pHead1->next, pHead2);
            return pHead1;
        } else {
            pHead2->next = Merge(pHead1, pHead2->next);
            return pHead2;
        }
    }
```

## 17.树的子结构

题目描述：输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

解题思路：遍历树A，每遇到一个节点与B进行比较，看B是否是它的子结构。leetcode上也有原题。

代码：

```cpp
bool Judge(TreeNode* p, TreeNode* pRoot2) {
        if (p && !pRoot2) return true;
        if (!p && pRoot2) return false;
        if (!p && !pRoot2) return true;
        if (p->val != pRoot2->val) return false;
        else return Judge(p->left, pRoot2->left) && Judge(p->right, pRoot2->right);
    }
    bool flag = false;
    void search(TreeNode* p, TreeNode* pRoot2) {
        if (p) {
            if (Judge(p, pRoot2)) {
                flag = true;
                return ;
            }
            search(p->left, pRoot2);
            search(p->right, pRoot2);
        }
    }

    bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        if (!pRoot2) return false;
        search(pRoot1, pRoot2);
        return flag;
    }
```

## 18.二叉树的镜像

题目描述：操作给定的二叉树，将其变换为源二叉树的镜像。（即反转二叉树左右节点，leetcode也有原题）

代码：

```cpp
void reverse(TreeNode* &pRoot) {
        if (!pRoot) return ;
        else {
            TreeNode* p = pRoot->left;
            pRoot->left = pRoot->right;
            pRoot->right = p;
            reverse(pRoot->left);
            reverse(pRoot->right);
        }
    }
    void Mirror(TreeNode *pRoot) {
        reverse(pRoot);
    }
```

## 19.顺时针打印矩阵

题目描述：输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

解题思路：设置四个变量表示左右上下，再分别从四个方向进行入数组操作。PAT有原题。

代码：

```cpp
vector printMatrix(vector > v) {
        int m = v.size(), n = v[0].size();
        vector ans;
        int left = 0, right = n-1, up = 0, down = m-1;
        while (up <= down && left <= right) {
            //left to right
            if (up <= down && left <= right) {
                for (int i = left; i <= right; i++) ans.push_back(v[up][i]);
                up++;
            }
            //up to down
            if (up <= down && left <= right) {
                for (int i = up; i <= down; i++) ans.push_back(v[i][right]);
                right--;
            }
            //right to left
            if (up <= down && left <= right) {
                for (int i = right; i >= left; i--) ans.push_back((v[down][i]));
                down--;
            }
            //down to up
            if (up <= down && left <= right) {
                for (int i = down; i >= up; i--) ans.push_back(v[i][left]);
                left++;
            }
        }
        return ans;
    }
```

## 20.包含min函数的栈(不太会)

题目描述：定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。注意：保证测试中不会当栈为空的时候，对栈调用pop()或者min()或者top()方法。

解题思路：这道题比较有意思，用到了辅助栈。

代码：

```cpp
class Solution {
public:
    stack st1, st2;
    void push(int value) {
        st1.push(value);
        if (st2.empty()) {
            st2.push(value);
        } else {
            if (value <= st2.top()) st2.push(value);
        }
    }
    void pop() {
        if (st1.top() == st2.top()) st2.pop();
        st1.pop();
    }
    int top() {
        return st1.top();
    }
    int min() {
        return st2.top();
    }
};
```
