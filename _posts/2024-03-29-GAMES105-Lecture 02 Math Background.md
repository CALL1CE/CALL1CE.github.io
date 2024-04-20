---
title: GAMES105-Lecture 02 Math Background
date: 2024-03-29 18:30:00 +0800
categories: [Computer Graphics, GAMES105]
tags: [图形学, 学习笔记]

pin: false
author: 
    name: CALL1CE
    link: https://space.bilibili.com/9330604
toc: true
comments: true
math: true
mermaid: true

---
pdf地址：[Math Background](https://games-105.github.io/ppt/02%20-%20Math%20Background.pdf)

## Matrix
[![pFobHZF.jpg](https://s21.ax1x.com/2024/03/29/pFobHZF.jpg)](https://imgse.com/i/pFobHZF)

[![pFobqIJ.jpg](https://s21.ax1x.com/2024/03/29/pFobqIJ.jpg)](https://imgse.com/i/pFobqIJ)

## Representations of 3D Rotation

* 对于旋转矩阵，因为是正交阵，所以旋转矩阵乘它的转置是单位阵，能得到6个式子，但有9个参数，所以自由度是3
* 因为旋转矩阵的行列式是+1，引入了一些约束，但只是让解空间从两瓣变成一瓣，并没有改变自由度

### Interpolation
[![pFobOi9.jpg](https://s21.ax1x.com/2024/03/29/pFobOi9.jpg)](https://imgse.com/i/pFobOi9)
* 平移的插值可以由线性插值解决
* 但是旋转插值不能简单地由线性插值表示
* 好的旋转插值：
  * 希望在每一时刻的旋转是合法的（不会变形）
  * 旋转是以一个常数的速度进行插值（速度不会乱变）
* 旋转矩阵
  * 难以构造
  * 容易应用旋转
  * 难以插值
* 欧拉角
  * 两种公约：一是沿着局部坐标系的xyz轴旋转，另一种是沿着世界坐标系的xyz轴旋转
  * 万向锁问题：在转的时候，两个轴共线，丢失一个自由度
  * 需要三个参数、十二种顺序、以及两种公约
[![pFobXGR.jpg](https://s21.ax1x.com/2024/03/29/pFobXGR.jpg)](https://imgse.com/i/pFobXGR)
  * 容易构造、容易应用旋转、容易插值
  * 万向锁问题
  * 如果插值在Π和-Π之间插值，有可能会瞬间转一圈
* 旋转向量/轴角表示
[![pFobba4.jpg](https://s21.ax1x.com/2024/03/29/pFobba4.jpg)](https://imgse.com/i/pFobba4)
  * 因为轴是个单位向量，角度是个常数，所以可以相乘得到一个旋转向量
  * 想要旋转的话，需要先把这个旋转向量转化成旋转矩阵，然后用罗德里格旋转公式
  * 难以做两个旋转向量的旋转组合，需要先变换成旋转矩阵然后相乘后再变回旋转向量
  * 容易做插值，但不能保证旋转速度是恒定的，可以通过复杂点的计算来保证
  * 容易构造、难以应用旋转（需要先转换为旋转矩阵）、容易插值、没有万向锁问题
* 四元数
[![pFobjR1.jpg](https://s21.ax1x.com/2024/03/29/pFobjR1.jpg)](https://imgse.com/i/pFobjR1)
  * 对二维旋转的拓展
  * 单位四元数和轴角表示涉及的信息是相同的
    * 任何一个三维的旋转可以转为轴角表示
    * 轴角表示可以转化为四元数表示
  * 使用单位四元数对向量进行旋转，为什么是三明治乘法，乘了两个四元数，因为单位四元数里面是θ/2
[![pFobzM6.jpg](https://s21.ax1x.com/2024/03/29/pFobzM6.jpg)](https://imgse.com/i/pFobzM6)
  * 乘法表示不唯一
  * 旋转组合是两个单位四元数的积
  * 四元数的插值
    * 在四维球壳上找到一个曲线
    * 线性插值后进行单位化得到一个合法四元数
[![pFobvxx.jpg](https://s21.ax1x.com/2024/03/29/pFobvxx.jpg)](https://imgse.com/i/pFobvxx)
    * 缺点是旋转速度不是恒定的
    * SLER：Spherical Linear Interpolation来解决速度问题
[![pkS4RSO.jpg](https://s21.ax1x.com/2024/04/20/pkS4RSO.jpg)](https://imgse.com/i/pkS4RSO)
  * 容易构造、容易应用旋转、容易插值、没有万向锁