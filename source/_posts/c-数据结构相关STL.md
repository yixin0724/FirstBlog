---
title: c++数据结构相关STL
date: 2023-09-15 09:07:15
tags: 
  - C++
  - 数据结构
cover: https://wltc2-1258834326.cos.ap-guangzhou.myqcloud.com/2023/09/15/6503aed0caa07.png
categories: 技术记录
description: 数据结构是程序员少不了的坎，需要认认真真的态度将其拿下！
---









简单学习一下 STL



## ✨前言

  数据结构还有与之相辅相成的算法是想要获得高薪的必经之路，对待这一部分还是要脚踏实地的学下来。

## string

> 字符串

#### string 初始化

```
string s1;
string s2(s1);
string s3("value");
string s4 = s3;
string s5 = "abc";
string s6 = (2, 'a'); // aa
Copy
```

#### string 操作

```
// cin >> s，读取遇到空白停止
// getline(cin, s)，读取一整行，直至末尾
// s.empty()，字符串为空返回 true，反之 false
// s.size()，返回字符串的长度
// s[n]
Copy
// string 赋值
s1 = "hello";
s1.assign("abcdef", 4); // abcd
s2.assigh(s1, 0, 3); // abc
s2.length(); // 3

// string 取值，[]越界直接报错，at越界抛出异常
s1[0];
s1.at(0);

// string 拼接，字面值和 string 是不同类型
string s1 = "我";
string s2 = "你";
s1 += s2; // "我你"
s1.append("ta");
string s3 = s1 + "爱" + "你";
string s4 = "我" + "爱" + s2; // 错误，不允许两个字面值相加


// string 查找
string s1 = "abcdef";
s1.find("ab"); // 0
s1.find("ac"); // -1
s1.rfind("ab"); // 0

// string 替换
string s3 = "hello";
s3.replace(1, 3, "1111"); // h1111o

// string 比较
string s1 = "abc";
string s2 = "abc";
string s3 = "bbc";
s1.compare(s2); // 0
s1.compare(s3); // -1
s3.compare(s1); // 1

// string 子串
string s1 = "abcdef";
s1.substr(1, 3); // bc

// string 插入
string s1 = "hello";
s1.insert(1, "111"); // h111ello

// string 删除
s1.erase(1, 3);

// string 转 c-style
string s1 = "abc";
const char * p = s1.c_str();

// c-style 转 string
string s2(p);
Copy
```

## vector

> 动态数组

#### vector 初始化

```
// 创建一个空的 vector
vector<int> v1;

// 拷贝构造
vector<int> v2(v1);      

// array 转 vector，指定长度
int arr[5] = {1, 2, 3, 4, 5};
vector<int> v3(arr, arr + 5);

// 初始化元素个数为 5，每个值为 0
vector<int> v4(5);  // {0,0,0,0,0}

// 初始化元素个数为 5，每个值为 1
vector<int> v5(5, 1);  // {1,1,1,1,1}

// 赋值初始化，等同于 vector<int> v6 = {1,2,3,4,5}
vector<int> v6{1,2,3,4,5};

// {} 用来进行列表初始化，如果其中的值无法用于列表初始化，编译器则用默认值初始化 vector 对象
vector<int> v7{2};           // {2}
vector<string> v8{2};        // 初始化长度为 2，每个值为空，{"", ""}
vector<string> v9{2, "hi"};  // 初始化长度为 2，每个值为 hi，{"hi", "hi"}
Copy
```

#### vector 操作

```
// v.push_back(t)，将值为 t 元素追加到 vector 尾部
// v.empty()，vector 为空返回 true
// v.size()，返回 vector 大小
// v[n]，用于访问已存在的元素，而不能用于添加元素
Copy
// vector 赋值
vector<int> v1(4, 3);
vector<int> v2 = v1;
printVector(v2); // 3 3 3 3 

vector<int> v4;
v4.assign(v2.begin(), v2.end());
printVector(v2); // 3 3 3 3 

vector<int> v5 = {1, 2, 3, 4, 5};
v4.swap(v5);
printVector(v4); // 1 2 3 4 5

// vector 大小

cout << v4.size() << endl; // 5
if(v4.empty()){
    cout << "v4 为空" << endl;
}
else{
    cout<< "v4 不为空" << endl;
}
cout << v4.capacity() << endl; // 5
v4.resize(7);
printVector(v4); // 1 2 3 4 5 0 0
v4.resize(10, 3);
printVector(v4); // 1 2 3 4 5 0 0 3 3 3
v4.resize(3);
printVector(v4); // 1 2 3

// vector 取值
vector<int> v;
v.push_back(1);
v.push_back(2);
v.push_back(3);
v.push_back(4);

cout << v[0] << endl; // 1
cout << v.at(1) << endl; // 2
cout << v.front() << endl; // 1
cout << v.back() << endl; // 4

// vector 插入
v.insert(v.begin(), 100); 
printVector(v); // 100 1 2 3 4
v.insert(v.begin(), 2, 100); 
printVector(v); // 100 100 100 1 2 3 4

v.push_back(10);
printVector(v); // 100 100 100 1 2 3 4 10

// vector 删除
v.pop_back();
printVector(v); // 100 100 100 1 2 3 4

v.erase(v.begin());
printVector(v); // 100 100 1 2 3 4 

// v.erase(v.begin(), v.end());
v.clear();
Copy
```

#### vector 遍历

```
// for 语句体中不应改变其所遍历序列的大小
// 循环体内部含有向 vector 对象添加元素的语句，则不能使用 for
// 通过索引
for(int i = 0; i < v.size(); ++i)
{
    cout << v[i] << " ";
}
cout << endl;

// for loop
for (int i: v)
{
    cout << i << " ";
}
cout << endl;

// 迭代器
for (vector<int>::iterator i = v.begin(); i != v.end(); i++)
{
    cout << *i << " ";
}
cout << endl;
Copy
```

## deque

> 双端队列

```
// deque 构造方法
deque<int>  d;

deque<int> d2(d);

deque<int> d4(5, 1);

deque<int> d3(d2.begin(), d2.end());

// deque 赋值
deque<int> d;
d.assign(3, 1);
printDeque(d); // 1 1 1

deque<int> d2;
d2.assign(d.begin(), d.end());
printDeque(d2); // 1 1 1

deque<int> d3;
d3.push_back(1);
d3.push_back(2);
d3.push_front(3);
printDeque(d3); // 3 1 2
d3.swap(d2);
printDeque(d3); // 1 1 1
printDeque(d2); // 3 1 2

// deque 大小
deque<int> d;
d.push_back(1);
d.push_back(2);
d.push_back(3);
d.push_back(4);

cout << d.size() << endl;  // 4
cout << d.empty() << endl;  // 0

d.resize(10, 2);
printDeque(d);  // 1 2 3 4 2 2 2 2 2 2

// deque 取值
cout << d[0] << endl;  // 1
cout << d.at(0) << endl;  // 1

cout << d.front() << endl;  // 1
cout << d.back() << endl;  // 4

// deque 插入
deque<int> d;
d.push_back(1);
d.push_back(2);
d.push_front(3);
printDeque(d);  // 3 1 2

d.insert(d.begin(), 1);
printDeque(d);  // 1 3 1 2
d.insert(d.begin(), 3, 2);
printDeque(d);  // 2 2 2 1 3 1 2


// deque 删除
d.pop_back();

d.pop_front();

d.erase(d.begin(), d.end());
d.clear();
Copy
```

## stack

> 先进后出

```
 stack<int> s;
s.push(10);
s.push(20);
s.push(30);

cout << s.size() << endl;  // 3

cout << s.top() << endl;  // 30
s.pop();
cout << s.top() << endl;  // 20
s.pop();
cout << s.top() << endl;  // 10
s.pop();

cout << s.empty() << endl;  // 1
cout << s.size() << endl;  // 0
Copy
```

## queue

> 先进先出

```
// queue 构造方法
queue<int> q;
q.push(1);  // 往队尾放元素
q.push(2);
q.push(3);
q.push(4);

cout << q.size() << endl;  // 4

while(!q.empty()){
    cout << q.front() << " ";
    q.pop();  // 弹出队头元素
}
cout << endl;
Copy
```

## list

> 双向链表

```
// list 构造方法
list<int> l;
list<int> l1(3, 10);
list<int> l2(l1.begin(), l1.end());
printList(l2);  // 10 10 10 

// list 插入
list<int> l3;
l3.push_back(1);
l3.push_back(2);
l3.push_front(3);
l3.push_front(4);
printList(l3);  // 4 3 1 2

l3.insert(l3.begin(), 2, 100);
printList(l3); // 100 100 4 3 1 2 

// list 删除
l3.pop_back();
l3.pop_front();
printList(l3);  // 100 4 3 1 

l3.remove(4);
printList(l3);  // 100 3 1
l3.erase(l3.begin(), l3.end());
l3.clear();

// list 赋值
list<int> l;
l.assign(3, 10);
printList(l);  // 10 10 10
l.push_back(99);
l.push_front(100);

cout << l.size() << endl;  // 5
cout << l.empty() << endl;  // 0
cout << l.front() << endl;  // 100
cout << l.back() << endl;  // 99
Copy
```

## set

> 不允许重复的值，元素不允许修改

```
set<int> s1;
s1.insert(8);
s1.insert(3);
s1.insert(2);
s1.insert(7);

printSet(s1);  // 2 3 7 8
cout << s1.size() << endl;  // 4
cout << s1.empty() << endl;  // 0

s1.erase(2);
printSet(s1);  // 3 7 8

s1.erase(s1.begin(), s1.end());
s1.clear();

// set 查找

set<int> s1;
s1.insert(1);
s1.insert(5);
s1.insert(7);
s1.insert(2);
s1.insert(3);
s1.insert(5);

printSet(s1);  // 1 2 3 5 7
set<int>::iterator pos =  s1.find(1);
if(pos != s1.end()){
    cout << "找到了 1 :" << *pos << endl;
}
else{
    cout << "未找到" << endl;
}

cout << s1.count(5) << endl; // 1

set<int>::iterator low = s1.lower_bound(3);
if(low != s1.end()){
    cout << "找到了 lower_bound(3):" << *low << endl;
}
else{
    cout << "未找到" << endl;
}
set<int>::iterator up = s1.upper_bound(3);
if(up != s1.end()){
    cout << "找到了 upper_bound(3):" << *up << endl;
}
else{
    cout << "未找到" << endl;
}

pair<set<int>::iterator, set<int>::iterator> ret = s1.equal_range(3);

cout << *(ret.first) << endl;  // 3 相当于 lower_bound
cout << *(ret.second) << endl;  // 5 相当于 upper_bound

// set 排序
set<int, MyCompare> s;
s.insert(10);
s.insert(4);
s.insert(6);
s.insert(4);
s.insert(7);
s.insert(1);

for(int num : s){
    cout << num << " ";
}
cout << endl;

// 10 7 6 4 1
Copy
```

## map

> 键值对

```
// map 构造方法
map<int, int> m;

// map 插入
m.insert(pair<int, int>(1, 10));
m.insert(make_pair(2, 20));  // 推荐
m.insert(map<int, int>::value_type(3, 30));
m[4] = 40;

for(map<int, int>::iterator it = m.begin(); it != m.end(); it++){
    cout << "key:" << it->first << "value:" << it->second << endl;
}

// map 大小
cout << m.size() << endl;
cout << m.empty() << endl;

// map 删除
m.erase(m.begin());  // 删除第一个
m.erase(2);  // 删除 key 为 2 的键值对

m.erase(m.begin(), m.end());
m.clear();

// map 查找
map<int, int> m;
m.insert(make_pair(1, 10));
m.insert(make_pair(2, 20));
m.insert(make_pair(3, 30));

map<int, int>::iterator pos = m.find(1);
if(pos != m.end()){
    cout << "找到" << endl;
}else{
    cout << "未找到"  << endl;
}

int num = m.count(3);
cout << "键为3的数量为：" << num << endl;

map<int, int>::iterator ret1 = m.lower_bound(2);  // 小于等于 3 的第一个元素的迭代器
map<int, int>::iterator ret2 = m.upper_bound(2);  // 大于 3 的第一个元素的迭代器

cout << ret1->first  << ret1->second << endl;  // 2 20
cout << ret2->first  << ret2->second << endl;  // 3 30
Copy
```

------

