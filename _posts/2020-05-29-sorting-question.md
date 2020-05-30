---
layout: post
title: Sorting Question 四题
date: 2020-05-29
Author: Connie 
tags: [leetcode, medium]
comments: false
toc: true
---

Source: 我的happy girl ZT~
https://www.youtube.com/watch?v=MzoL02wd5ag

我的“努力”: 
1. #33. Search in Rotated Sorted Array (no duplicates)
2. #81. Search in Rotated Sorted Array II (no duplicates)
3. #153. Find Minimum in Rotated Sorted Array (no duplicates)
4. $154. Find Minimum in Rotated Sorted Array II (with duplicates) 

我各自看了几个ZT和Algo的解题, 可以说12和34可以当做两组, 13和24也可以当做两组.

34里面要注意的是, while loop 不是 left <= right 而是 left < right. 因为13里面需要这个等于号, 因为它们是有可能没有solution要返回-1的, 而34里必然会回一个solution, which is nums[left]/nums[right] does not matter 因为while loop走到最后都是left==right了.