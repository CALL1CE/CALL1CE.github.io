---
title: GAMES105-Lecture 03 Character Kinematics
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

## Character Kinematics

### Skeleton

* 围绕joint旋转
* joint之间叫body、bone、link
### Kinematics of Chain
* 正向运动学：每个关节局部坐标系的方向等价于夫关节的局部坐标系的方向乘以当前坐标系的旋转
* Q就是每个关节局部坐标系的一个朝向
* 每个关节有一个局部坐标系，当Q0关节发生旋转，会带动后面所有关节旋转，此时Q1相对于Q0局部坐标系的相对位置是不会改变的，如果要计算Q1局部坐标系原点在世界坐标系的位置，等价于做一个变换，将相对于Q0局部坐标系下的位置转换到世界坐标系

### Kinematics of a Character
* 可以把角色建模为由多个链条连接到同一个根节点构成的树形的一个结构
* 通常会把根节点设置在腰部
* 每个关节有不同的性质，比如旋转的范围和方式
* 膝盖、手肘：hinge、revolute joint（铰链、转动关节）
* 髋、肩：ball-and-socket joint(球状关节)
* 自由度：是指一个物理系统需要多少参数可以唯一、准确地描述它当前的状态
  * xyz的位移和旋转，就是6个自由度
* Pose Parameters：root决定角色整体的位置和方向、每个内部关节旋转的表示
  * 为了计算方便，对顺序也有要求，一个关节在参数列表的顺序要比它所有的子关节都要靠前
### motion data in a file
* 在文件开头会先定义一个角色，在每个关节旋转=0的时候（通常叫做T-Pose），每个关节和他的父关节的相对位置关系，接下来就会描述在之前这个定义的基础之上，关节的旋转是多少
* BVH files
  * 用欧拉角表示关节旋转，所以也会定义欧拉角的顺序
* Format
* FC
* AMC
* FBX
## Inverse Kinematics
* 应用比正向运动学更广泛
* 指定末端点的朝向，通过逆向运动学的算法自动算出每个关节的旋转
* 