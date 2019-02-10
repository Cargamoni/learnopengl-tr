---
title: Hata Ayıklama
keywords: 
last_updated: 
tags: []
permalink: /in_practice/debugging.html
sidebar: main_sidebar
---

# Hata Ayıklama
 Graphics programming can be a lot of fun, but it can also be a large source of frustration whenever something isn't rendering right or perhaps not even rendering at all! Seeing as most of what we do involves manipulating pixels it can be difficult to figure out the cause of error whenever something doesn't work the way it's supposed to. Debugging these kind of visual errors is different than what you're used to when debugging errors on the CPU. We have no console to output text to, no breakpoints to set on our GLSL code and no way of easily checking the state of GPU execution.

In this tutorial we'll look into several techniques and tricks of debugging your OpenGL program. Debugging in OpenGL is not too difficult to do and getting a grasp of its techniques definitely pays out in the long run. 

## glGetError()
The moment you incorrectly use OpenGL (like configuring a buffer without first binding any) it will take notice and generate one or more user error flags behind the scenes. We can query these error flags using a function named glGetError that simply checks the error flag(s) set and returns an error value if OpenGL got violently abused. 

```cpp
GLenum glGetError(); 
```
The moment glGetError is called it returns either an error flag or no error at all. The error codes that glGetError can return are listed below:


| Flag         | Code           | Description  |
| :------------- |:-------------:| :-----|
| GL_NO_ERROR      | 0 |  	 	No user error reported since last call to glGetError. |
|GL_INVALID_ENUM 	|1280| 	Set when an enumeration parameter is not legal.|
|GL_INVALID_VALUE 	|1281| 	Set when a value parameter is not legal.|
|GL_INVALID_OPERATION 	|1282| 	Set when the state for a command is not legal for its given parameters.|
|GL_STACK_OVERFLOW 	|1283| 	Set when a stack pushing operation causes a stack overflow.|
|GL_STACK_UNDERFLOW 	|1284| 	Set when a stack popping operation occurs while the stack is at its lowest point.|
|GL_OUT_OF_MEMORY 	|1285| 	Set when a memory allocation operation cannot allocate (enough) memory.|
|GL_INVALID_FRAMEBUFFER_OPERATION 	|1286| 	Set when reading or writing to a framebuffer that is not complete.|

