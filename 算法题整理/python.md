# Python 基础语法汇总

## 打印

```Python
print()  # 默认自动换行
print(end='')  # 不换行
```

## 输入

```Python
# 输入一行
line = input()

# 输入一个int
num = int(input())

```

## 列表

```Python
# 初始化数组
nums:list[int] = [3, 1, 4, 1, 5, 9]
# 创建一个长度为 n 的初始化数组（例如长度为5，初始值为0）
n = 5
nums = [0] * n  # [0, 0, 0, 0, 0]
# 逆置列表
nums.reverse()  # 原地反转
reversed_nums: list[int] = nums[::-1]  # 返回新反转列表

# 二维列表排序
points: list[list[int]] = [[2, 3], [1, 2], [2, 1]]
points.sort(key=lambda x: (x[0], x[1]))  # 按照第一个元素升序，第二个元素升序排序

```

## 常用函数

```Python
min_val: int = min(a, b)
max_val: int = max(a, b)

```

## 字符串

```Python
# 定义
s: str = "Runoob"

# 字符串长度
length: int = len(s)

# 访问字符
char: str = s[0]

# 分割字符串
parts: list[str] = s.split(',')  # 按逗号分割

# 子串
sub_str: str = s[:p]  # 从0开始取p个字符

# 字符串转数字
num: int = int(s)

# 数字转字符串
str_num: str = str(number)

# 数字列表转字符串
nums: list[int] = [1, 2, 3]
str_nums: str = ''.join(str(n) for n in nums)

# 逆置字符串
reversed_str: str = s[::-1]
```

## 无穷大与无穷小

```Python
import math

inf: float = math.inf  # 正无穷
neg_inf: float = -math.inf  # 负无穷

# 最大/最小整数值（取决于系统）
int_max: int = (1 << 63) - 1
int_min: int = -(1 << 63)
```

## 哈希表

### dict

```Python
sites: dict[int, str] = {}

# 添加元素
sites[1] = "Google"

# 访问元素（推荐使用get方法避免KeyError）
val: str | None = sites.get(3)

# 删除元素
if 4 in sites:
    del sites[4]

# 清空
sites.clear()

# 判空
is_empty: bool = not sites

# 遍历
for key, val in sites.items():
    print(f"key: {key}, value: {val}")

# 判断存在
exists: bool = 3 in sites

# 自定义类作为键：需实现 __hash__ 和 __eq__
class MyKey:
    def __init__(self, values):
        self.values = values
    def __hash__(self):
        return hash(tuple(self.values))
    def __eq__(self, other):
        return self.values == other.values

custom_dict: dict[MyKey, str] = {}
```

### set

```Python
sites_set: set[str] = set()

# 添加元素
sites_set.add("Google")

# 删除元素
sites_set.discard("Taobao")  # 推荐比remove安全，不会抛异常

# 判断存在
exists: bool = "Taobao" in sites_set

# 清空
sites_set.clear()

# 大小
size: int = len(sites_set)

# 遍历
for elem in sites_set:
    print(elem)
```

## 栈

```Python
stack: list[int] = []

# 入栈
stack.append(1)

# 获取栈顶
top: int = stack[-1] if stack else None

# 出栈
if stack:
    stack.pop()

# 大小
size: int = len(stack)
```

## 队列

```Python
from collections import deque

queue: deque[str] = deque()

# 入队
queue.append("Apple")

# 获取队首
front: str = queue[0] if queue else None

# 出队
if queue:
    queue.popleft()

# 大小
size: int = len(queue)

# 判空
is_empty: bool = not queue
```

## 链表

```Python
from typing import Optional

class ListNode:
    def __init__(self, val=0, next: Optional['ListNode'] = None):
        self.val = val
        self.next = next
```

## 列表

```Python
now_ans: list[int] = []

# 逆置
now_ans.reverse()
reversed_list: list[int] = now_ans[::-1]

# 访问元素
element: int = now_ans[index]

# 修改元素
now_ans[index] = new_val

# 查询状态
size: int = len(now_ans)
is_empty: bool = not now_ans

# 排序
now_ans.sort()  # 升序
now_ans.sort(reverse=True)  # 降序
```

## 优先队列

```Python
import heapq

# 小顶堆（默认）
min_heap: list[int] = []
heapq.heappush(min_heap, 1)
top: int = min_heap[0]  # 查看堆顶元素
heapq.heappop(min_heap)  # 弹出最小元素

# 大顶堆（插入负值模拟）
max_heap: list[int] = []
heapq.heappush(max_heap, -10)
top: int = -max_heap[0]

# 自定义比较器（无法直接实现，需封装成类或元组排序）
# 示例：按字符串排序的小顶堆
data: list[tuple[str, 'Animal']] = []
heapq.heappush(data, ("cat", animal1))
heapq.heappush(data, ("dog", animal2))
_, top_animal = heapq.heappop(data)
```