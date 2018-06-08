# leetcode notes
### 695 Max Area of island
```python
  class Solution(object):
    def areaAroundThisPoint(self,grid,x,y):
        if(x>-1 and y>-1 and x<len(grid) and y<len(grid[0])) and grid[x][y]: #update gird value should be 1 before start recursive adding
            grid[x][y]=0
            return 1+ self.areaAroundThisPoint(grid,x-1,y) + self.areaAroundThisPoint(grid,x,y-1) + self.areaAroundThisPoint(grid,x+1,y) + self.areaAroundThisPoint(grid,x,y+1)
        return 0
    
    
    def maxAreaOfIsland(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        res = 0
        for x in range(len(grid)):
            for y in range(len(grid[0])):
                if(grid[x][y]==1):
                    res = max(res,self.areaAroundThisPoint(grid,x,y))
                    
        return res
```
Largely improve the runspeed.
### 238. Product of Array Except Self
Honestly speaking, the algorithom is not "that easy" to understand at the first glance. Basiclly, it runs two turns in which performs a accumulate product along one direction except itself. All the accumulative product start as 1 from each end to represant null start.
```python
class Solution(object):
    def productExceptSelf(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        res = [ 1 for i in range(len(nums))]
        for i in range(1,len(nums)):
            res[i] = res[i-1] * nums[i-1]
        
        right = 1
        
        for i in range(1,len(nums)+1):
            j = len(nums)-i
            res[j] = res[j] * right
            right = right * nums[j]
            
        return res
```
### 717. 1-bit and 2-bit Characters
This problem is emmmmmmm. "Paper tiger" will be the best word to describe it. 
```python
class Solution(object):
    def isOneBitCharacter(self, bits):
        """
        :type bits: List[int]
        :rtype: bool
        """
        N = len(bits)
        i=0
        while(i<N-1):
            if(bits[i]==0):
                i = i+1
            else:
                i = i+2
        return i==N-1
```
找规律很重要
### 122. Best Time to Buy and Sell Stock II
My solution:
1. Greedy
2. Sell at highest and buy at lowest.
```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        buy=0
        sell=1
        N = len(prices)
        if(N==0):
            return 0
        if(N==1):
            return 0
        profit = 0
        max_price_now = 0
        min_price_now = 0
        while(buy< N and sell<N ):
            max_price_now = prices[buy]
            while(prices[sell] >= max_price_now):
                max_price_now = prices[sell]
                sell = sell + 1
                if sell>N-1 :
                    profit  = profit + prices[sell-1]-prices[buy]
                    return profit
                
            profit  = profit + prices[sell-1]-prices[buy]
            min_price_now = prices[sell-1]
            
            while(prices[sell] <=  min_price_now):
                min_price_now = prices[sell]
                sell = sell +1
                if sell>N-1:
                    return profit
            buy = sell - 1
        return profit
```
Other possible solution -- Keep on adding the profit obtained from every consecutive transaction.
```java
class Solution {
    public int maxProfit(int[] prices) {
        int maxprofit = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1])
                maxprofit += prices[i] - prices[i - 1];
        }
        return maxprofit;
    }
}
```
### 697. Degree of an Array
这道题自己的方法po费苦工，思路如下：
1. 利用count函数统计数组中各元素出现的个数，并从大到小排序。
2. 分析出现最多的元素（一个或者多个），返回他们的最短子数组长度。
3. 最短子数组长度的原理是找第一个出现，最后一个出现的序号，然后做减。
4. 通过了一半的测试用例，说明其正确性可以得到保证。然而悲剧的是，超时了。
Code is attached below:
```python
class Solution:
    def findShortestSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        count_info = [[i,nums.count(i)] for i in nums]
        pair_peak = max(count_info,key= lambda x:x[1])
        pair_set = [ a for a,b in count_info if b==pair_peak[1]]
        def small_lengh(num):
            l = 0
            r = len(nums)-1
            while(True):
                if(nums[l]!=num):
                    l = l +1
                else:
                    break;
            while(True):
                if(nums[r]!=num):
                    r = r -1
                else:
                    break;
            return r-l+1
        return min(list(map(small_lengh,pair_set)))
```
参考答案的原理和我相似，在数据结构的处理上比我更加巧妙。
譬如，对于找到`left most`和`right most`和`count`这三件事情，采用了dict数据结构，只用了一个循环便搞定（当然，`count()`内部可不是常数时间的运算) 代码如下
```python
class Solution(object):
    def findShortestSubArray(self, nums):
        left, right, count = {}, {}, {}
        for i, x in enumerate(nums):
            if x not in left: left[x] = i #只在首次更新
            right[x] = i #重复更新，以满足“最右边”的要求
            count[x] = count.get(x, 0) + 1 #另存一个数组来相加

        ans = len(nums)
        degree = max(count.values())
        for x in count:
            if count[x] == degree:
                ans = min(ans, right[x] - left[x] + 1) #考虑多个候选人的情况，求最短距离

        return ans
```
#### 小总结，数字group类
考虑使用到
1. `dict`
2. `dict.get(key)`
3. 另开一个`count` dict 来记录出现的频率
4. `left most`与`right most`更新策略的问题
### 217. Contains Duplicate
这道题我大材小用了，设置了一个字典专门来计数，最后统计最大的个数是否大于2，这个是没有必要的。更高效的做法是，只需判断0，1（即存在与否）。现附代码余下供参考。但也有值得肯定的地方，譬如熟练掌握dict方法😆
My submission
```python
class Solution(object):
    def containsDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        if(len(nums)==0 or len(nums)==1):
            return False
        stat = {}
        for i in nums:
            stat[i] = stat.get(i,0) + 1
        return max(stat.values())>=2
```

Sample submission
```python
class Solution(object):
    def containsDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        s = set()
        for n in nums:
            if n in s:
                return True
            s.add(n)
        return False
```
直接用数据结构`set()`方便快捷
### 39. Combination Sum 
终于来啦，DFS三件套例题，带你轻松入坑。

Sample java submission
```java
public List<List<Integer>> combinationSum(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start){
    if(remain < 0) return;
    else if(remain == 0) list.add(new ArrayList<>(tempList));
    else{ 
        for(int i = start; i < nums.length; i++){
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, remain - nums[i], i); // not i + 1 because we can reuse same elements
            tempList.remove(tempList.size() - 1);
        }
    }
}
```
My Submission in python
```python
class Solution:
    
    
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        Solution.res = []
        candidates.sort()
        self.conbination(Solution.res,[],target,candidates,0)
        return Solution.res
    
    def conbination(self,rest,tempList,remain,candidate,start):
            if remain<0:
                return
            elif remain==0:
                Solution.res.append(tempList)
            else :
                for i in range(start,len(candidate)):
                    #tempList.append(candidate[i])
                    self.conbination(Solution.res,tempList+[candidate[i]],remain-candidate[i],candidate,i)
                    #tempList.pop()
```
思路
1. 递归搜索，回溯搜索

额外收获

* 在python class中递归，一定要考虑全局变量的选定，不然这种动态语言很危险。

疑惑

* 至今未明白，为何注释那两行不可用，难道py后台会自动并行for loop导致`tempList`变量公用，进程变化？

### 508. Most Frequent Subtree Sum
递归+字典计数+查找最大频率（有重复值）
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def findFrequentTreeSum(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        if root==None:
            return []
        
        self.countDict = {}
        self.countFrequency(root)
        res = []
        max_fre = 0
        max_index = 0
        for num in self.countDict:
            if self.countDict[num]>max_fre:
                max_fre = self.countDict[num]
                max_index = num
        res.append(max_index)
        
        for num in self.countDict:
            if self.countDict[num]==max_fre and num!=max_index:
                res.append(num)
        return res
        
        
    def countFrequency(self,root):
        if root==None:
            return 0
        sum_val = root.val + self.countFrequency(root.right) + self.countFrequency(root.left)
        self.countDict[sum_val] = self.countDict.get(sum_val,0) + 1
        return sum_val
```
### 513. Find Bottom Left Tree Value
#### 亮点
BFS的py方法
```python
class Solution:
    def findBottomLeftValue(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        que = [root]
        for node in que:
            que+=filter(None,[node.right,node.left]) #filter out the none value in children list, tricky use in python filter() function
        return node.val
```
1. 加入队列，返回最后的node值。
2. BFS的顺序是从右至左。所以最后一个node值是最下最左
### 637. Average of Levels in Binary Tree
### 要点
1. BFS
2. 通过设置两个队列，来处理层间迭代的问题

You can refer to standard BFS method used in 107 as it perfctly solved tree BFS problem and have a great machanism to sperate each layer. 
#### 方法二
DFS

采用count[i] 数组表示第i层的节点的个数
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public List < Double > averageOfLevels(TreeNode root) {
        List < Integer > count = new ArrayList < > ();
        List < Double > res = new ArrayList < > ();
        average(root, 0, res, count);
        for (int i = 0; i < res.size(); i++)
            res.set(i, res.get(i) / count.get(i));
        return res;
    }
    public void average(TreeNode t, int i, List < Double > sum, List < Integer > count) {
        if (t == null)
            return;
        if (i < sum.size()) {
            sum.set(i, sum.get(i) + t.val);
            count.set(i, count.get(i) + 1);
        } else {
            sum.add(1.0 * t.val);
            count.add(1);
        }
        average(t.left, i + 1, sum, count);
        average(t.right, i + 1, sum, count);
    }
}
```
### 特辑 树当中 BFS,DFS 代码组织思路
1. BFS

通常有一层循环（对应一个队列），用于增减队列，循环继续的条件是队列不为空
2. DFS 

通常没有循环，利用对子树递归实现遍历，注意对递归函数的base condition精心设计，一般函数的第一行为null判断
### 653. Two Sum IV - Input is a BST
1. 我的解法：

第一层循环，利用DFS遍历每个节点，用`help()`函数在BST中寻找是否有匹配值，考虑到了元素重复使用的问题，进行二次检查。其中对BST的遍历时采用单侧DFS.
#### 点评
思路直接但是复杂度极高，超过了递归的限度，无法AC。
#### code
```python
class Solution:
    def findTarget(self, root, k):
        """
        :type root: TreeNode
        :type k: int
        :rtype: bool
        """
        
        if self.help(k-root.val,root,k):
            return True
        elif root.left!=None:
            return self.findTarget(root,k-root.left.val)
        elif root.right!=None:
            return self.findTarget(root,k-root.right.val)
        else:
            return False
    
    def help(self,remain,root,k):
        if root==None :
            return False
        
        if root.val==remain:
            if root.val!=k-remain: 
                return True
            else:
                return False
        elif root.val<remain:
            return self.help(remain,root.right,k)
        elif root.val>remain:
            return self.help(remain,root.left,k)
        
        return False
```
2. `set()` + `DFS` 实现

在利用DFS遍历的时候，同时维护一个`set`判断有无前序匹配，无则加入当前元素，递归子树。
```python
class Solution(object):
    def findTarget(self, root, k):
        """
        :type root: TreeNode
        :type k: int
        :rtype: bool
        """
        sett = []
        return self.help(root,k,sett)
    
    def help(self,root,k,sset):
        if root==None:
            return False
        if k-root.val in sset:
            return True
        
        sset.append(root.val)
        
        return self.help(root.left,k,sset) or self.help(root.right,k,sset)
```        
### 683. K Empty Slots
这题折腾了我90分钟。最后自己代码的成绩是 通过测试用例 25/61 ，剩余部分超时。基本思想为，滑动窗口找符合条件的解，并利用自定义函数进行条件判断。最后返回最近日期。
```python
class Solution:
    def kEmptySlots(self, flowers, k):
        """
        :type flowers: List[int]
        :type k: int
        :rtype: int
        """
        N = len(flowers)
        res = []
        for i in range(0,N):
            for j in range(1,N-i):
                if(abs(flowers[j+i]-flowers[i])-1==k and self.help(flowers,k,flowers[j+i],flowers[i])):
                    res.append(j+i+1)
        if len(res)==0:
            return -1
        else:
            return min(res)
                        
        
    def help(self,flowers,k,loc1,loc2):
        r = max(loc1,loc2)
        l = min(loc1,loc2)
        late = flowers.index(loc1)
        count = 0
        for n in range(l+1,r):
            if flowers.index(n)>late:
                count+=1
        
        if count==k:
            return True
        else:
            return False
```
优秀样本解：
```python
class Solution(object):
    def kEmptySlots(self, flowers, k):
        active = []
        for day, flower in enumerate(flowers, 1):
            i = bisect.bisect(active, flower)
            for neighbor in active[i-(i>0):i+1]:
                if abs(neighbor - flower) - 1 == k:
                    return day
            active.insert(i, flower)
        return -1
```
### 366. Find Leaves of Binary Tree
Sample codes
```java
public class Solution {
    public List<List<Integer>> findLeaves(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        height(root, res);
        return res;
    }
    private int height(TreeNode node, List<List<Integer>> res){
        if(null==node)  return -1;
        int level = 1 + Math.max(height(node.left, res), height(node.right, res));
        if(res.size()<level+1)  res.add(new ArrayList<>());
        res.get(level).add(node.val);
        return level;
    }
}
```
My submission
```python
class Solution(object):
    def findLeaves(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        self.res = []
        self.helper(root,self.res)
        return self.res
    def helper(self,root,res):
        if root==None:
            return -1
        level = 1+ max(self.helper(root.left,self.res),self.helper(root.right,self.res))
        
        if level>len(res)-1:
            self.res.append([root.val])
        else:
            self.res[level].append(root.val)
        return level
```
新意

1. DFS，层数（离叶子的距离，最小为零）计算。
2.  用层数来区分返回结果的空间大小。
3. DFS每层循环其实是Bottom Up操作，从叶子端的操作向上。
### 582. Kill Process
可以用树解决的问题，我们需要构建树，空间换时间。

我的朴素想法--Time exceeded 
```python
class Solution:
    def killProcess(self, pid, ppid, kill):
        """
        :type pid: List[int]
        :type ppid: List[int]
        :type kill: int
        :rtype: List[int]
        """
        if len(pid)==1:
            return pid
        
        res = []
        que = [kill]
        while len(que)!=0:
            for kil in que:
                cpid = [i for i,k in enumerate(ppid) if k==kil ]
                cp = list(map(lambda x:pid[x],cpid))
                res.append(que.pop(0))
                que.extend(cp)
                             
        return res
```
New submission:

1. 建立pid -> Node的映射 (1d list to (i,Node) dict)。
2. 通过ppid构建Node之间的关系，形成树。
3. 递归调用findAllChild方法，完成操作。
```python
class Solution:
    class Node(object):
        children = []
        def __init__(self,val):
            self.val =val
            
    def killProcess(self, pid, ppid, kill):
        """
        :type pid: List[int]
        :type ppid: List[int]
        :type kill: int
        :rtype: List[int]
        """
        
        pidDict = {} #val,node_with_val
        self.res = [kill]
        for j in pid:
            pidDict[j]=self.Node(j) #j==val
        
        for i in range(len(ppid)):
            if ppid[i]>0:
                parent = ppid[i]
                pidDict[parent].children.append(pidDict[pid[i]])
                
        self.findAllHaiZi(pidDict[kill],self.res)
        
        return self.res
        
    def findAllHaiZi(self,node,res):
        if len(node.children)==0:
            return
        for child in node.children:
            self.res.append(child.val)
            self.findAllHaiZi(child,res)
```
可惜python递归限制次数比java少太多了，此算法效率低下，无法AC。

考虑
### 96. Unique Binary Search Trees
这道题是一道数理推断题，核心思想如下：

设`G(n)`为`n`节点可以构成的独特BST的总数(其中规定 `G(0)=G(1)=1`)，那么

`G(n) = F(1,n) + ... + F(n,n)` 其中i代表根的位置，从1开始计数。因为节点不同，因此是不一样的树

我们还发现：

`F(i,n) = G(i-1) * G(n-i)` (i>=2) 左右两侧子树，笛卡尔积

因此，`G(n)`可以表达成为

`G(n) = G(0)* G(n) + G(1)*(n-1) + ... + G(n-1) * G(0)`

因此，此题的代码解为
```python
class Solution:
    def numTrees(self, n):
        """
        :type n: int
        :rtype: int
        """
        G = [0]*(n+1)
        G[0]=G[1]=1
        
        for i in range(2,n+1):
            for j in range(1,n+1):
                G[i] += G[j-1]*G[i-j]
        
        return G[n]
```
### 235. Lowest Common Ancestor of a Binary Search Tree
思路，若要求有共同祖先，必定位于某根部节点的两个子树旁，或者所给两点本身就有父子关系。

只对 节点值均大于根值，节点值均小于根值的情况进行迭代，迭代的方向利用BST的大小特性。

如何保证是最下祖先呢？如果两个节点的`(root.val-p.val)*(root.val-q.val) >0`成立，那么他们一定位于节点的一侧子树中，那么它们一定有更下的共同祖先（一左一右，上式乘积为负数）或者自身构成父子关系（其中一个作差为零）。
```python
class Solution(object):
    def lowestCommonAncestor(self, root, p, q):
        """
        :type root: TreeNode
        :type p: TreeNode
        :type q: TreeNode
        :rtype: TreeNode
        """
        while (root.val-p.val)*(root.val-q.val) >0:
            root =  (root.left,root.right)[root.val<q.val] #元组判断法
       
        return root
```

小知识：
1. 上文采用的元组判断法不推荐使用，因为元组会全部计算构建元组再做条件判断，然而经典的if-else条件判断顺序与之相反。
2. 不过这样确实很酷
### 650. 2 Keys Keyboard

这道题最终把问题转化为`n`的素数分解(prime factorization)

1. 所有的操作部分都可以分组为素数长度的解 CPCPP -> [CP] [CPP]
2. 如果不是素数长度，可以再分，结果等价为素数长度的组
3. 对于copy paste问题，我们发现素数组的长度正好对应"素数最小组操作“的长度。 CPP -> 三倍
4. 因此这题转化为对给定的`n`的质数分解
```python
class Solution(object):
    def minSteps(self, n):
        """
        :type n: int
        :rtype: int
        """
        d=2
        ans=0
        while(n>1):
            while n%d==0:
                ans+=d
                n/=d
            d+=1
        return ans
```        
### 739. Daily Temperatures
```python
class Solution(object):
    def dailyTemperatures(self, temperatures):
        """
        :type temperatures: List[int]
        :rtype: List[int]
        """
        
        sstack=[]
        ans = [0]*len(temperatures)
        for i in range(len(temperatures)):
            while len(sstack)!=0 and temperatures[i]>temperatures[sstack[-1]]:
                idx = sstack.pop()
                ans[idx] = i-idx
            sstack.append(i)
        
        return ans
```

这道题学到一个新概念－－“单调栈”。

我的第一种解法，利用两次循环去遍历，找到第一个匹配的结果便写入ans然后break。用Python然后就超时了，2333。此题时间复杂度n^2。

第二种解法，单调栈，使用单调栈可以找到元素向左遍历第一个比他小的元素，也可以找到元素向左遍历第一个比他大的元素。单调栈的操作复杂度是O(n)。对于本题的时间复杂度亦是O(n^2)

不知道为什么效率会提升~~
### 547. Friend Circles
```python
class Solution:
    def findCircleNum(self, M):
        """
        :type M: List[List[int]]
        :rtype: int
        """
        visited = [0] *len(M)
        cout = 0
        for i in range(len(M)):
            if visited[i]==0 :
                self.dfs(i,M,visited)
                cout +=1
        return cout
    
    def dfs(self,i,M,visited):
        for j in range(len(M)):
            if M[i][j]==1 and visited[j]==0:
                visited[j]=1
                self.dfs(j,M,visited)
```
朋友圈，DFS。递归找朋友，深度优先。值得记忆理解的模范DFS代码。
### 199. Binary Tree Right Side View
```python
class Solution:
    def rightSideView(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        if root == None:
            return []
        res = [root.val]
        que =[root]
        while(len(que)!=0):
            temp = []
            while(len(que)!=0):
                node = que.pop()
                if node.left!=None:
                    temp.append(node.left)
                if node.right!=None:
                    temp.append(node.right)
                if len(temp)!=0:
                    res.append(temp[-1].val)
            que = temp
        return res
```
待解答

参考答案，非常优秀的DFS，兼顾level，右侧优先。
```python
class Solution:
    def rightSideView(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        if root ==None:
            return []
        
        res = []
        self.help(root,0,res)
        return res
    def help(self,node,level,res):
        if node==None:
            return
        if level==len(res):
            res.append(node.val)
        self.help(node.right,level+1,res)
        self.help(node.left,level+1,res)
```        
### 107. Binary Tree Level Order Traversal II
BFS 分层遍历模板,请按照这个来处理层间问题。
```python
class Solution:
    def levelOrderBottom(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        if root==None:
            return []
        que = [root]
        res = []
        
        while(len(que)!=0):
            temp = []
            N = len(que)
            for i in range(N):
                node = que.pop(0)
                if node.left!=None:
                    que.append(node.left)
                if node.right!=None:
                    que.append(node.right)
                temp.append(node.val)
            res.insert(0,temp)
        return res
```        
### 101. Symmetric Tree

Yes, it's one of the most interesting quesiton solved by a beautiful symmertic and recersive way.
```python
class Solution:
    def isSymmetric(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        #the most exciting recersive problem I have ever seen
        return root==None or self.help(root.left,root.right)
    def help(self,left,right):
        if left==None or right ==None:
            return left==right
        
        if left.val!=right.val:
            return False
        
        return self.help(left.left,right.right) and self.help(left.right,right.left)
```        
### 338. Counting Bits
The most straight forward way to solve this problem is to travel every number and counts its binary bins.
```python
res.append(bin(i).count("1"))
```
However, we may find out that, they have a patern as followed

dp[0] = 0;

dp[1] = dp[1-1] + 1;

dp[2] = dp[2-2] + 1;

dp[3] = dp[3-2] +1;

dp[4] = dp[4-4] + 1;

dp[5] = dp[5-4] + 1;

dp[6] = dp[6-4] + 1;

dp[7] = dp[7-4] + 1;

dp[8] = dp[8-8] + 1;

Therefore, we can build up a dynamic solving system and use the number we have previously calculated to save time.
```java
public int[] countBits(int num) {
    int result[] = new int[num + 1];
    int offset = 1;
    for (int index = 1; index < num + 1; ++index){
        if (offset * 2 == index){ //update offset when n is some power of 2.
            offset *= 2;
        }
        result[index] = result[index - offset] + 1;
    }
    return result;
}
```
### 647. Palindromic Substrings
**DP**每一个回文都是从中心开始向四周延展，回文字符串按照奇偶长度分为两种。因此第一层对字符串的遍历是回文中心点。第二层遍历式扩展回文判断，直到左右边界不满足相等。
```python
class Solution(object):
    def countSubstrings(self, s):
        """
        :type s: str
        :rtype: int
        """
        self.res = 0
        for i in range(len(s)):
            self.helper(s,i,i)
            self.helper(s,i,i+1)
            
        return self.res
    def helper(self,s,left,right):
        while left>=0 and right<len(s) and s[left]==s[right]:
            left=left-1
            right=right+1
            self.res = self.res + 1
```
