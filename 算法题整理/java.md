# Java 基础语法汇总

## 基础语法与数组

### 输出打印

```Java
System.out.print();
System.out.println();
```

### 输入

```Java
// 定义输入扫描器
Scanner scanner = new Scanner(System.in);
// 输入一行
String line = scanner.nextLine();
String[] LinkNums = line1.split(" ");

// 输入一个Int
int num = scanner.nextInt();

```

### 数组

```Java
int[] nums = new int[n];

//数组长度
nums.length

// 使用Arrays.sort()方法配合比较器来逆置数组
Integer[] array = {3, 1, 4, 1, 5, 9};
Arrays.sort(array, Collections.reverseOrder());

public void sortIt(int[][] points){
    Arrays.sort(points, new Comparator<int[]>(){
        @Override
        public int compare(int[] a, int[] b){
            if(a[0] == b[0]){
               return Integer.compare(a[1], b[1]);
            }
            else{
                return Integer.compare(a[0],b[0]);
            }
        }
    });
}

```

### 常用函数

```Java
// 最小最大值
Math.min(a, b);
Math.max(a, b);
```

## 字符串

```Java
// 定义
String str = "Runoob";

// 字符串长度
int len = site.length();

// 转换为字符串数组
char[] c = str.toCharArray();

// 返回索引
char c = str.charAt(0);

// 字符串中获取单个单词
String str = "Hello World";
String[] words = str.split(" ");
// 先去掉字符串s两端的空白字符，然后根据一个或多个连续的空白字符将处理后的字符串分割成一个字符串数组words
String[] words = s.trim().split("\\s+");
System.out.println(words[0]); //输出结果为Hello

// 子串
s.substring(0, p); // 获得s下标从0到p-1的子串

// 字符串转数字
String str = "12345";
Integer num = Integer.parseInt(str);

// 数字数组转字符串
String numString = Arrays.toString(numArray);

// 逆置
String originalString = "Hello, World!";

StringBulder sb = new StringBuilder;
// 添加元素
sb.append()
// 删除元素
sb.deleteCharAt(sb.length() - 1);
// 逆置
sb.reverse()
```

## 无穷大与无穷小

```Java
double positiveInfinity = Double.POSITIVE_INFINITY;
double negativeInfinity = Double.NEGATIVE_INFINITY;
// 如果是int类型需要进行类型转换
```

## 哈希表

### HashMap

```Java
// 定义,注意类型只能使用包装类，而不能使用基础数据类型
HashMap<Integer, String> Sites = new HashMap<Integer, String>();

// 添加元素：put() 方法
Sites.put(1, "Google");

// 访问元素： get(key) 方法
Sites.get(3);

// 删除元素： remove(key) 方法
Sites.remove(4);

// 删除所有键值对(key-value)可以使用 clear 方法
Sites.clear();

// 判空
Sites.isEmpty();

// 迭代哈希表
    // 输出 key 和 value
    for(Integer i : Sites.keySet()) {
       System.out.println("key: " + i + " value: " + Sites.get(i));
    }
    // 返回所有 value 值
    for(String value: Sites.values()) {
       // 输出每一个value
       System.out.print(value + ", ");
    }
    
// 判断是否存在key/value
boolean exist = hashMap.containsKey("key1");
boolean exist = hashMap.containsValue(1);

//重写方法，比较对象
    public class CharType{
        int[] charSet;

        @Override
        public boolean equals(Object o){
            CharType that = (CharType) o;
            return Arrays.equals(charSet, that.charSet);
        }

        @Override
        public int hashCode() {
            return Arrays.hashCode(charSet);
        }

        CharType(int[] charSet){
            this.charSet = charSet;
        }
    }

```

### HashSet

```Java
// 定义
HashSet<String> sites = new HashSet<String>();

// 添加元素
sites.add("Google");

// 删除元素
sites.remove("Taobao");

// 判断元素是否存在
sites.contains("Taobao");

// 清空集合
sites.clear();

// 元素数量
sites.size();

// 迭代，可以用for-each
for(String i:sites){
    System.out.println(i);
}
```

## 栈

```Java
// 初始化栈
Stack<Integer> st=new Stack<>();

// 入栈
st.push(1);

// 获得栈顶元素
st.peek();

// 出栈
st.pop();

// 获得元素1到栈顶的距离
sta.search(1);

```

## 队列

```Java
// 初始化队列
Queue<String> queue = new LinkedList<>();

// 添加元素到队列
queue.add("Apple");

// 获取队列头部元素
String head = queue.peek();

// 移除队列头部元素
String removedElement = queue.remove();

// 队列大小
int size = queue.size();

// 判断队列是否为空
boolean isEmpty = queue.isEmpty();

```

## 链表

```Java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
```

## 顺序表

```Java
// 逆置
List<Integer> nowAns = new ArrayList<Integer>();
Collections.reverse(nowAns);

// 获取下标位置元素
Integer get(int index)

// 修改元素
void set(int index, Integer element)

// 查询状态
int size(); //返回列表中的元素数量。
boolean isEmpty(); //判断列表是否为空。
boolean contains(Object o); //判断列表是否包含指定元素。

// 排序
Collections.sort(list); // 使用 Collections.sort() 对集合升序排序
Collections.sort(boxedArr, Comparator.reverseOrder()); //降序排序 

```

## 优先队列

```Java
        Queue<Integer> priorityQueue = new PriorityQueue<>();
        int[] array = {1,3,2,6,5,7,8,9,10,0};
        for(int x : array) {
            // 1, 插入数据
            priorityQueue.offer(x);
        }
        // 2,获取优先级最高的数据
        int ret1 = priorityQueue.peek();
        System.out.println("获取优先级最高的数据:" + ret1);

        // 3,删除优先级最高的数据
        int ret2 = priorityQueue.poll();
        System.out.println("删除优先级最高的数据:" + ret2);

        // 4,获取优先级队列中数据个数
        int size = priorityQueue.size();
        System.out.println("数据个数:" + size);

        // 5, 清空优先级队列
        priorityQueue.clear();

        // 6, 查看队列是否为空
        boolean bl = priorityQueue.isEmpty();
        System.out.println("队列是否为空:" + bl);
        
        // 7,自定义优先级
        PriorityQueue<Animal> animalPq = new PriorityQueue<>(new Comparator<Animal>() {
        @Override
        public int compare(Animal a1, Animal a2) {
          return a1.getName().compareTo(a2.getName()); // 按名字的字典序排序
        }
        
        // 升序比较器
        Comparator.reverseOrder()

```