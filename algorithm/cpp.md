# CPP 基础语法汇总

## 基础语法与数组

### 输出打印

```C++
#include <iostream>
using namespace std;
cout << ...;
cout << ... << endl;
```

### 输入

```C++
#include <string>
#include <sstream>
#include <vector>

// 输入一行
string line;
getline(cin, line);

// 分割字符串
vector<string> split(const string &s, char delim) {
    vector<string> res;
    stringstream ss(s);
    string item;
    while (getline(ss, item, delim)) {
        res.push_back(item);
    }
    return res;
}

// 输入一个int
int num;
cin >> num;
```

### 数组

```C++
// 一维数组
vector<int> nums(n);

// 数组长度
nums.size();

// 逆置数组
sort(array.rbegin(), array.rend());

// 二维数组自定义排序
bool comparePersons(const Person &a, const Person &b) {
    if (a.age != b.age) {
        return a.age < b.age; // 年龄小的在前
    } else {
        return a.name < b.name; // 名字字典序小的在前
    }
}

void sortIt(vector<vector<int>>& points) {
    sort(points.begin(), points.end(), [](const vector<int>& a, const vector<int>& b) {
        return a[0] != b[0] ? a[0] < b[0] : a[1] < b[1];
    });
}
```

### 常用函数

```C++
#include <algorithm>
min(a, b);
max(a, b);
```

## 字符串

```C++
#include <string>
#include <algorithm>
#include <sstream>

// 定义
string str = "Runoob";

// 字符串长度
int len = str.size();

// 转换为字符数组
char c[str.size()+1];
strcpy(c, str.c_str());

// 访问字符
char c = str[0];

// 分割字符串（需实现split函数，参考输入部分）

// 子串
str.substr(0, p); // 获得从0开始长度为p的子串

// 字符串转数字
int num = stoi(str);

// 数字转字符串
string strNumber = to_string(number);

// 数字数组转字符串
stringstream ss;
for (auto num : numArray) ss << num;
string numString = ss.str();

// 逆置
reverse(str.begin(), str.end());
```

## 无穷大与无穷小

```C++
#include <limits>
int max_val = numeric_limits<int>::max();
int min_val = numeric_limits<int>::min();
double inf = numeric_limits<double>::infinity();
```

## 哈希表

### unordered_map

```C++
#include <unordered_map>

unordered_map<int, string> Sites;

// 添加元素
Sites[1] = "Google";

// 访问元素（需检查存在性）
if (Sites.count(3)) auto val = Sites[3];

// 删除元素
Sites.erase(4);

// 清空
Sites.clear();

// 判空
Sites.empty();

// 遍历
for (auto& [key, val] : Sites) {
    cout << "key: " << key << " value: " << val << endl;
}

// 判断存在
bool exist = Sites.count(key);

// 自定义类型作为key
struct MyHash {
    size_t operator()(const vector<int>& v) const {
        size_t hash = 0;
        for (int i : v) {
            hash ^= hash << 6 ^ i;
        }
        return hash;
    }
};
unordered_map<vector<int>, string, MyHash> customMap;
```

### unordered_set

```C++
#include <unordered_set>

unordered_set<string> sites;

// 添加元素
sites.insert("Google");

// 删除元素
sites.erase("Taobao");

// 判断存在
sites.count("Taobao");

// 清空
sites.clear();

// 大小
sites.size();

// 遍历
for (auto& elem : sites) {
    cout << elem << endl;
}
```

### multiset

```C++
// multiSet底层使用了平衡二叉树
# include<set>
multiset<int> multiSet;
// 插入元素
multiSet.insert(10);
// 删除元素
multiSet.erase(20)  // 删除值为20的所有实例
multiSet.erase(multiSet.find(10)); // 删除值为10的一个实例
// 获取最大/最小元素
*multiSet.begin()  // 最小元素
*(--multiSet.end())  // 最大元素
// 计数元素
int count = multiSet.count(10);
// 获取特定范围内元素
// equal_range
std::multiset<int> myMultiset = {10, 20, 20, 30, 40};
auto range = myMultiset.equal_range(20);
for(auto it = range.first; it != range.second; ++it){
    std::cout << *it << " "; // 输出: 20 20
}
// lower_bound() 返回一个迭代器，指向第一个不小于给定值的元素（即大于或等于该值）。
std::multiset<int> myMultiset = {10, 20, 20, 30, 40};
auto it = myMultiset.lower_bound(20);
std::cout << *it; // 输出: 20 (第一个20)
// upper_bound() 返回一个迭代器，指向第一个大于给定值的元素。
std::multiset<int> myMultiset = {10, 20, 20, 30, 40};
auto it = myMultiset.upper_bound(20);
std::cout << *it; // 输出: 30 (第一个大于20的元素)



```

## 栈

```C++
#include <stack>

stack<int> st;

// 入栈
st.push(1);

// 获取栈顶
int top = st.top();

// 出栈
st.pop();

// 大小
st.size();
```

## 队列

```C++
#include <queue>

queue<string> q;

// 入队
q.push("Apple");

// 获取队首
string front = q.front();

// 出队
q.pop();

// 大小
q.size();

// 判空
q.empty();
```

## 链表

```C++
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};
```

## 顺序表（vector）

```C++
#include <vector>
#include <algorithm>

vector<int> nowAns;

// 逆置
reverse(nowAns.begin(), nowAns.end());

// 访问元素
nowAns[index];

// 修改元素
nowAns[index] = new_val;

// 查询状态
nowAns.size();
nowAns.empty();

// 排序
sort(nowAns.begin(), nowAns.end()); // 升序
sort(nowAns.rbegin(), nowAns.rend()); // 降序
```

## 优先队列

```C++
#include <queue>

priority_queue<int> pq; // 大顶堆
priority_queue<int, vector<int>, greater<int>> min_pq; // 小顶堆

// 插入数据
pq.push(1);

// 获取顶部元素
int top = pq.top();

// 删除顶部元素
pq.pop();

// 大小
pq.size();

// 清空（需要手动pop）
while (!pq.empty()) pq.pop();

// 判空
pq.empty();

// 自定义比较器
struct Compare {
    bool operator()(const Animal& a1, const Animal& a2) {
        return a1.name > a2.name; // 小顶堆效果
    }
};
priority_queue<Animal, vector<Animal>, Compare> custom_pq;
```