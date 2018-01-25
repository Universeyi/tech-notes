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
    
