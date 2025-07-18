# Golang 垃圾回收(GC)机制

## Go v1.3标记清除法

![alt text](images/image-18.png)

![alt text](images/image-19.png)

![alt text](images/image-20.png)

优化思路

![alt text](images/image-21.png)

## 三色标记法

![alt text](images/image-22.png)

![alt text](images/image-23.png)

一趟下来
  - 保留的白色结点是要回收的垃圾
  - 灰色节点是中间状态，最终会变为黑色结点
  - 黑色节点是幸存者

它是逐层逐层遍历的，而不是一次性遍历，这是它和标记清除法的根本区别。

如果没有stw保护，这种方法担心出现的情况：对象丢失

![alt text](images/image-24.png)

![alt text](images/image-25.png)

解决方案：破坏两个条件同时成立

## 强/弱三色不变式

![alt text](images/image-26.png)

![alt text](images/image-27.png)

![alt text](images/image-28.png)

## 屏障机制-插入写屏障

![alt text](images/image-29.png)

![alt text](images/image-30.png)

插入屏障不在栈对象上使用，为了保证栈的速度。因为栈的空间比较小，而且对速度要求高

![alt text](images/image-31.png)

![alt text](images/image-32.png)

![alt text](images/image-33.png)

![alt text](images/image-34.png)

**不足：**插入写屏障的不足就是需要stw来重新扫描栈。但这个stw时间已经被优化了

## 屏障机制-删除写屏障

![alt text](images/image-35.png)

![alt text](images/image-36.png)

![alt text](images/image-37.png)

删除屏障会保护一轮被保护的对象

![alt text](images/image-38.png)

## 屏障机制-混合写屏障

![alt text](images/image-39.png)

![alt text](images/image-40.png)

![alt text](images/image-41.png)

![alt text](images/image-42.png)