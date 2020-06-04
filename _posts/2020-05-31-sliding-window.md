---
layout: post
title: Sliding Window的一天
date: 2020-05-31
Author: Connie 
tags: [leetcode]
comments: false
toc: true
---
### Link
[Collection+solution](https://leetcode.com/discuss/general-discussion/657507/sliding-window-for-beginners-problems-template-sample-solutions)

[Collection](https://leetcode.com/list/x17aw7vm/)

这老哥是个好人, 今天有题目做啦! 容我研究一波. 

我疯了, 我发现11道题里面我只做过1道, 还是八个月以前做的, 提交上去的都是wrong answer...我到底是有多怕sliding window problems啊哈哈哈哈.


485. Max Consecutive Ones
487. Max Consecutive Ones II
1004. Max Consecutive Ones III
3. Longest Substring Without Repeating Character
209. Minimum Size Subarray Sum (算是第一次自己做出了, 开心)

### 6/2 Update
昨天刷题的时候突然发现一个问题, 为什么 487 和 3 都是Sliding Window, 却有些地方是i - j 有些地方却是 i - j + 1呢？ 迷惑. 又研究了一下, 发现是这样的. 照理来说两个pointer之间的长度就应该是 i - j + 1, 但是当我们以for loop来循环input array的时候, 在最后的for loop结束时, i 会变得比实际大一 (最后一轮的i ++), 所以导致不需要再加那个1了. 而我们用while loop的时候不需要在意这个case.

快乐. 然后我又改写了3的用for loop里面while的版本, 舒服. 那个可以return either `max = Math.max(max, i - j)` or `max = Math.max(max, set.size())` 的地方, 也是我先把 i++ 了, 才不是常规的 i - j + 1.