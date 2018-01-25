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
