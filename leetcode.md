# leetcode notes
### 695 Max Area of island
```python
  class Solution(object):
    def areaAroundThisPoint(self,grid,x,y):
        if(x>-1 and y>-1 and x<len(grid) and y<len(grid[0])):
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
However, it won't pass the oj system as it reaches the max recursion depth. Java version of brute force recoursion can pass while python cannot.
The shortage of last algorithom is that it has repeat calculations over the same grid(because no system tracks wheather it's been checked or not. Therefore we have a improved version than the code above.
```python
class Solution(object):
    def maxAreaOfIsland(self, grid):
        seen = set() # for the record of "seen" location
        def area(r, c):
            if not (0 <= r < len(grid) and 0 <= c < len(grid[0])
                    and (r, c) not in seen and grid[r][c]): #for outbound, 0 grid and checked situations
                return 0
            seen.add((r, c))
            return (1 + area(r+1, c) + area(r-1, c) +
                    area(r, c-1) + area(r, c+1))

        return max(area(r, c)
                   for r in range(len(grid))
                   for c in range(len(grid[0]))) # double for loop and max the outcome
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
