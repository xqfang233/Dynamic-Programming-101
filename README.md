# Dynamic-Programming-101

This long long article is written for myself, who've been struggling with recursion, backtracking and dynamic programming for a long time. The funny thing is, these three topics are not abstract while terribly obscure(at least for me), so here comes this article.

Most of the examples were cited from http://jeffe.cs.illinois.edu/teaching/algorithms/. I also attached some related leetcode exercises for practice to deepen my understanding.

"It was the spring of hope, it was the winter of despair." The heaven or the other way it was, dynamic programming.

***

## Recursion

### reduction
+ The goal: to replace the problem we manage to solve with another problem (easier to solve) and utilize the solution to solve the original problem
+ Reduce x --> y: first we find an algorithm that can solve problem y, and then we use y as a subroutine for solving x.

### Tower of Hanoi
![image](https://user-images.githubusercontent.com/81652429/120408896-eba72600-c31d-11eb-8ee6-25bc3ed9a492.png)
*http://jeffe.cs.illinois.edu/teaching/algorithms/ p24*
#### problem description: 
+ 3 rods, n disks of different size stacked on one rod in decreasing size from bottom to top
+ we need to move all the disks from the 1st rod to the 3rd and stack them in the original order
+ restrictions: 
  + only one disk can be moved at a time
  + you can't put a disk on top of any disk smaller than it

#### How to reduce the problem?
From left to right, we denote the rods by rod 1, rod 2 and rod 3.
from top to bottom, we denote the disk by 1~n.
Obviously, to move the nth disk to rod 3, we must ensure the (n-1) disks on its top were stacked on rod 2, thus the nth disk can be moved from rod 1 to rod 3 without any trouble.

The solution could be divided into 2 steps:
1. move the top (n-1) disks from rod 1 to rod 2
   move the nth disk from rod 1 to rod 3.
2. move the top (n-1) disks from rod 2 to rod 3

the java version 

```java 
public class Hanoi {
    public static void h(Int n, String src, String temp, String des) {
        if(n==1) {
            System.out.println(src+" move to "+des);
        }else{
            h(n-1, src, des, temp);
            System.out.println(src+" move to "+des);
            h(n-1, temp, src, des);
        }
    }
}
```
time complexity: O(2^n - 1) for moving n disks


***
## Backtracking
+ What is Backtracking?

As summarized in [this video](https://www.youtube.com/watch?v=Zq4upTEaQyM), backtracking is to **make choices** under some **constraints** to reach a specific **goal**.
Let's take the [word search](https://leetcode.com/problems/word-search/) problem as an example. In this problem, we are asked to traverse a board of character grids to find out if a specific word string exists in this board. Below is an example given and the problem description:
![image](https://user-images.githubusercontent.com/81652429/120573101-0f846d80-c3eb-11eb-9b65-96b029cb1c53.png)
```
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
Output: true
```
```
Given an m x n grid of characters board and a string word, return true if word exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.
```
Let's see what choices, constraints and goal are in this problem.

+ choices: when travering the board, the grid you choose to go for the subsequent search. You have 4 directions to choose from based on your current point.
+ constraints: you can take only 1 step each time, you can only choose from adjacent cells for next step, the same letter grid can be used only once. 
+ goals: find a path that goes through all the characters in the given word in order

Then what about **reduction**? Instead of starting with the whole board, we can reduce the scale of the problem. Every choices is made at each single cell, so let's start from the single cell.
We can apply the following steps to solve a single cell:
1. check the character in it. If it's not the character we expected, simply end here. 
3. If it has the character we want, we can continue our search from here. There are 4 adjacent cells we can visit(ideally). But was any of them used before? do their index get out of the range of board? if both the answers are No, is the character in this adjacent cell the character we expected? 
4. If any adjacent cell satisfy the above requirements, it becomes the new start point for our search
5. If none of the adjacent cell satisfies the above requirements, we are done with this cell, and we need to go back, to "backtracking" the cell that leads to the cell we are currently at, and change to another direction for our search.

Here is the python solution for this problem:
```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        if len(board[0])==0 or len(board)==0:
            return False
        m = len(board)
        n = len(board[0])
        # memorize if a grid is visited to avoid duplicate usage
        visited = [[False]*n for _ in range(m)]
        
        # for search in 4 directions (left, down, right, up)
        row = [0,1,0,-1]
        col = [-1,0,1,0]
        
        
        def dfs(x,y,idx):
            # no need to continue search, stop here and backtrack
            if board[x][y]!=word[idx]:
                return False
            # reach the end of the target string
            if idx == len(word)-1:
                return True
            # set the current grid we are at as visited, could be reversed back to unvisited later if this path doesn't work
            visited[x][y] = True
            # based on the current point, search in 4 directions
            for i in range(4):
            # index for next grid for visiting
                nx = x+row[i]
                ny = y+col[i]
                # nx and ny should always in the range of board
                if nx>=0 and nx<m and ny>=0 and ny<n and not visited[nx][ny] and dfs(nx,ny,idx+1):
                    return True
            # if none of the neighbors satisfies the requirements, backtrack. remove the current grid from visited
            visited[x][y] = False
            return False
            
        # start from board[0][0], traverse each grid and use it as the possible start point for dfs.
        for x in range(m):
            for y in range(n):
                if dfs(x,y,0):
                    return True
        # didn't return true before finish traversing the whole board, the given string doesn't exist in this board.
        return False
```
This solution applied DFS. Of course we can use BFS for this problem. BFS has the same time complexity as DFS for solving this problem, but might cost higher in space, since it can add all potential grids for next step's search at once, while DFS sticks to only one grid and its neighbor each time. 

The backtracking videos on this channel: [Back to Back SWE](https://www.youtube.com/channel/UCmJz2DV1a3yfgrR7GqRtUUA) are great references.

### the template of backtracking
Let's focus on this code segment from the dfs function above in the python code:

```python
visited[x][y] = True
############# start recursion ##############
for i in range(4):
  nx = x+row[i]
  ny = y+col[i]
  if nx>=0 and nx<m and ny>=0 and ny<n and not visited[nx][ny] and dfs(nx,ny,idx+1):
  return True
############# end recursion ##############
visited[x][y] = False

```
The above code segment could be abstracted as:

make the choice
############# start recursion ##############
recursion part
############# end recursion ##############
revoke the choice

This is how backtracking works in this solution. Here is a simple, general template for backtracking in python:
```python
# some container to store the result
result = []

def backtrack(path, choice):
    if (termination condition is met):
        result.add(path) # path is consisted of all the choices you made until then
        return

    for choice in [choices]:
        # make a choice
        backtrack(path, [choices]) # recursively call the backtrack function
        # revoke the choice

```

*the above template refers to [this article written in Chinese](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247484709&idx=1&sn=1c24a5c41a5a255000532e83f38f2ce4&chksm=9bd7fb2daca0723be888b30345e2c5e64649fc31a00b05c27a0843f349e2dd9363338d0dac61&scene=21#wechat_redirect)*


### sudoku
Another problem [Valid Soduku](https://leetcode.com/problems/valid-sudoku/) can be solved with the techniques mentioned above. 

problem description:
```
Determine if a 9 x 9 Sudoku board is valid. Only the filled cells need to be validated according to the following rules:

Each row must contain the digits 1-9 without repetition.
Each column must contain the digits 1-9 without repetition.
Each of the nine 3 x 3 sub-boxes of the grid must contain the digits 1-9 without repetition.
Note:

A Sudoku board (partially filled) could be valid but is not necessarily solvable.
Only the filled cells need to be validated according to the mentioned rules.
```
![image](https://user-images.githubusercontent.com/81652429/120576198-617bc200-c3f0-11eb-9205-122491ed4545.png)

output: True




### N Queens
rules: not any of two queens can stay in the same row, same column or same diagonal. 

### Subset Sum

### general patterns

### string parsing

### complexity analysis

### longest increasing subsequence

### optimal binary search trees


***

## Dynamic Programming









**reference**

http://jeffe.cs.illinois.edu/teaching/algorithms/
