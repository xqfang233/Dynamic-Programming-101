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

```
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
### N Queens
rules: not any of two queens can stay in the same row, same column or same diagonal. 
### Game Trees

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
