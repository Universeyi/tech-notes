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
