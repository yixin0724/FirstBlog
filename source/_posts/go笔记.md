---
title: go笔记
date: 2025-06-07 09:07:39
cover: https://pic.imgdb.cn/item/66ebef4bf21886ccc0d0e972.jpg
tags:
  - Go
categories: 技术记录
description: 记录一下学习go的笔记。
---











# GO语言学习笔记

## 一、Go的安装

gopath下的3个目录：

​	src：里面的每一个子目录，就是一个包。包内是Go的源文件

​	pkg：编译后生成的，包的目标文件

​	bin：生成的可执行文件



### 1.1 Windows系统

安装方法:

前往[Go官网](https://golang.google.cn/dl/)下载安装包(后缀为`msi`)

或者去[Go中文社区](http://go.p2hp.com/go.dev/dl/)下载

> 下载完运行，一直下一步就可以

在配置`Go`的一些环境

### 1.2 Linux系统

同上下载安装包**(`tar.gz`)**

将安装包移动到`/usr/local`下

```bash
$ sudo mv 安装包名称 /usr/local
```



使用命令解压安装包:

```bash
$ tar -zxvf 下载的go安装包名称
```

**配置环境:**

在`环境变量配置`文件`/etc/profile`中添加以下内容:

打开文件命令:

```bash
$ sudo vim /etc/profile
```

```bash
# Go PATH
export PATH=$PATH:/usr/local/go/bin
```

输入----->按`i`进行编辑,完成后按`esc`退出编辑,输入`:wq`退出

让配置文件生效:

```bash
$ source /etc/profile
```

查看Go版本

```bash
$ go version
```

若显示Go版本就表示成功了



## 二、基础语法

### 第一个go程序

在控制台打印输出`hello world`

```go
package main
import "fmt"
func main(){
    fmt.Println("hello world")
}
```

#### 2.1.1 将go程序编译为可执行文件

> Windows 系统编译为`.exe`文件
>
> Linux 系统为二进制文件

##### 2.1.1.1 编译单个文件

```bash
# 进入需要编译的文件目录
$ cd 目录地址
# 编译文件
$ go build XXX.go
```

##### 2.1.1.2 编译整个项目

```bash
# 进入项目更目录
$ cd 地址
# 进行编译
$ go build
```

### 变量

> 在`go` 里面 定义的变量必须要用，如果你不想用可以使用`_`作为变量名，也就是匿名变量。
>
> 在go中，默认不赋值的变量，默认值都为0

#### 2.2.1 匿名变量

```go
package main

import "fmt"

func foo() (int, int) {
	return 1, 2
}

//匿名函数用于后面函数 固定返回两个值，但是我们只需要一个数据  可以使用匿名函数将数值隐下去
/*
用_作为匿名函数

*/
func main() {
	var a, _ = foo()
	fmt.Println(a)
}

```

#### 2.2.2 变量定义

声明变量的一般形式是使用 var 关键字

​	第一种，指定变量类型，声明后若不赋值，使用默认值0。

​	第二种，根据值自行判定变量类型。

​	第三种，省略var, 注意 :=左侧的变量不应该是已经声明过的，否则会导致编译错误。

```
package main
import "fmt"
func main() {
        //第一种 使用默认值
        var a int
        fmt.Printf("a = %d\n", a)

        //第二种
        var b int = 10
        fmt.Printf("b = %d\n", b)

        //第三种 省略后面的数据类型,自动匹配类型
        var c = 20
        fmt.Printf("c = %d\n", c)

        //第四种 省略var关键字
        d := 3.14
        fmt.Printf("d = %f\n", d)
}
```

​	第四种，多变量声明，如下：

```go
package main
import "fmt"

var x, y int
var ( //这种分解的写法,一般用于声明全局变量
        a int
        b bool
)


var c, d int = 1, 2
var e, f = 123, "liudanbing"


//这种不带声明格式的只能在函数体内声明
//g, h := 123, "需要在func函数体内实现"
func main() {
        g, h := 123, "需要在func函数体内实现"
        fmt.Println(x, y, a, b, c, d, e, f, g, h)


        //不能对g变量再次做初始化声明
        //g := 400


        _, value := 7, 5  //实际上7的赋值被废弃，变量 _  不具备读特性
        //fmt.Println(_) //_变量的是读不出来的
        fmt.Println(value) //5
}
```



### 常量

常量是一个简单值的标识符，在程序运行时，不会被修改的量。

常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

定义格式：const identifier [type] = value

可以省略类型说明符 [type]，因为编译器可以根据变量的值来推断其类型。

显式类型定义：const b string = "abc"

隐式类型定义：const b = "abc"

常量可以用len(), cap(), unsafe.Sizeof()常量计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不过

```
常量还可以用作枚举
const (
    Unknown = 0
    Female = 1
    Male = 2
)
```

#### 优雅的常量 iota

在 golang 中，一个方便的习惯就是使用`iota`标示符，它简化了常量用于增长数字的定义，给以上相同的值以准确的分类。

```
const (
    CategoryBooks = iota // 0
    CategoryHealth       // 1
    CategoryClothing     // 2
)
```

`iota`可以做更多事情，而不仅仅是 increment。更精确地说，`iota`总是用于 increment，但是它可以用于表达式，在常量中的存储结果值。

```
type Allergen int
const (
    IgEggs Allergen = 1 << iota         // 1 << 0 which is 00000001
    IgChocolate                         // 1 << 1 which is 00000010
    IgNuts                              // 1 << 2 which is 00000100
    IgStrawberries                      // 1 << 3 which is 00001000
    IgShellfish                         // 1 << 4 which is 00010000
)
```

```
fmt.Println(IgEggs | IgChocolate | IgShellfish)
// output:
// 19
type ByteSize float64

const (
    _           = iota                   // ignore first value by assigning to blank identifier
    KB ByteSize = 1 << (10 * iota)       // 1 << (10*1)
    MB                                   // 1 << (10*2)
    GB                                   // 1 << (10*3)
    TB                                   // 1 << (10*4)
    PB                                   // 1 << (10*5)
    EB                                   // 1 << (10*6)
    ZB                                   // 1 << (10*7)
    YB                                   // 1 << (10*8)
)
```







### 基本数据类型

> 在`go`语言中数据类型分为了**`基本数据`**和**`复杂数据类型`**

|  数据类型  |                           取值范围                           |
| :--------: | :----------------------------------------------------------: |
|    bool    |                        true 或 false                         |
|   string   |                     任何UTF-8编码字符串                      |
|    int     |       有符号整数，取值范围为 -2147483648 至 2147483647       |
|    int8    |            有符号8位整数，取值范围为 -128 至 127             |
|   int16    |          有符号16位整数，取值范围为 -32768 至 32767          |
|   int32    |     有符号32位整数，取值范围为 -2147483648 至 2147483647     |
|   int64    | 有符号64位整数，取值范围为 -9223372036854775808 至 9223372036854775807 |
|    uint    |            无符号整数，取值范围为 0 至 4294967295            |
|   uint8    |              无符号8位整数，取值范围为 0 至 255              |
|   uint16   |            无符号16位整数，取值范围为 0 至 65535             |
|   uint32   |          无符号32位整数，取值范围为 0 至 4294967295          |
|   uint64   |     无符号64位整数，取值范围为 0 至 18446744073709551615     |
|  uintptr   |                 无符号整数，用于存储一个指针                 |
|  float32   |                          32位浮点数                          |
|  float64   |                          64位浮点数                          |
| complex64  |                由32位实数和32位虚数组成的复数                |
| complex128 |                由64位实数和64位虚数组成的复数                |

#### 2.4.1 转义符

|  \   |                  反斜杠用于转义下一个字符                  |
| :--: | :--------------------------------------------------------: |
|  ’   |     单引号用于在单引号括起的字符字面值中表示单引号字符     |
|  "   |    双引号用于在双引号括起的字符串字面值中表示双引号字符    |
|  \a  |                          蜂鸣声符                          |
|  \b  |                退格符，将当前位置移到前一列                |
|  \f  |               换页符，将当前位置移到下页开头               |
|  \n  |              换行符，将当前位置移到下一行开头              |
|  \r  |               回车符，将当前位置移到本行开头               |
|  \t  |         水平制表符，将当前位置移到下一个制表符位置         |
|  \v  |        垂直制表符，将当前位置移到下一垂直制表符位置        |
| \xhh | 十六进制转义符，其中 hh 代表一个或多个表示十六进制值的数字 |

#### 2.4.2 占位符

|  占位符  |                           用途                           |
| :------: | :------------------------------------------------------: |
|    %v    |                    根据变量的值来输出                    |
|   %+v    | 在 %v 基础上，对结构体、切片等类型，将字段名与值一起输出 |
|   %#v    |                 输出值的 Go 语法表示方式                 |
|    %T    |                      输出变量的类型                      |
|    %%    |           输出百分号本身，不进行任何格式化操作           |
|    %t    |                    输出 bool 类型变量                    |
| %d or %i |                      输出十进制整数                      |
|    %o    |                       输出八进制数                       |
| %x or %X |                      输出十六进制数                      |
|    %U    |                 输出 Unicode 字符集格式                  |
| %e or %E |              输出浮点型数据的科学计数法表示              |
|    %f    |              输出浮点型数据的标准计数法表示              |
|    %q    |                 输出双引号引起来的字符串                 |
|    %s    |                        输出字符串                        |

#### 2.4.3 整型

```go
package main

import (
	"fmt"
	//reflect 为打印数据类型的包
	"reflect"
)

func main() {

	//整形
	/*
		int8 : -127~127   \\\\\2的8次方个数
		uint8 : 0~255
		等等
		int  在32位系统上就用的int32
		int  在64位系统上就用的int64
		int8: 一个字节  [-127~128]
		uint8 : 从0开始[0~255]
	*/
	var s int
    var i int8
    ....
}
```

#### 2.4.4 浮点型

```go
package main

import (
	"fmt"
    "reflect"
)
func main(){
    /*
		浮点型

		float类型
		float类型分为float32和float64两种类型，这两种浮点型数据格式遵循 IEEE 754 标准。
		单精度浮点数占用4个字节（32位）存储空间来存储一个浮点数。而双精度浮点数使用 8个字节（64位）存储空间来存储一个浮点数。
		单精度浮点数最多有7位十进制有效数字，如果某个数的有效数字位数超过7位，当把它定义为单精度变量时，超出的部分会自动四舍五入。双精度浮点数可以表示十进制的15或16位有效数字，超出的部分也会自动四舍五入。
		浮点类型默认声明为float64。
	*/
	var f1 float32
	f1 = 3.15487464
	//reflect.TypeOf  打印数据类型
	fmt.Println(f1, reflect.TypeOf(f1))
}
```

#### 2.4.5 布尔型

```go
package main

import (
	"fmt"
    "reflect"
)
func main(){
	/*
		布尔类型
		布尔类型是最基本数据类型之一，只有两个值：true和false，分别代表逻辑判断中的真和假，主要应用在条件判断中
	*/
	var b bool // 声明b是一个布尔类型
	b = true
	b = false // 该类型只有true和false两个值，分别代表真假两种状态
	fmt.Println(b, reflect.TypeOf(b))

	fmt.Println(1 == 1) // 比较运算符的结果是一个布尔值
	// fmt.Println(1 == "1") // 报错，mismatched types不能比较
	fmt.Println(3 > 1)

	var name = "yuan"
	var b2 = name == "rain"
	//false ****
	fmt.Println(b2)
}
```

#### 2.4.6 字符类型

```go
package main

import (
	"fmt"
    "reflect"
)
func main(){
/*
					字符串
				字符串是最基本也是最常用的数据类型，是通过双引号将多个字符按串联起来的一种数据，用于展示文本
			单引号只能标识字符
		    注意：

		        索引从零开始计数
		        go语言不支持负索引
	*/
	var s = "hello zhang"
	fmt.Println(s)

	// （1）索引取值 slice[index]
	a := s[2]
	fmt.Println(string(a))

	// （2）切片取值slice[start:end], 取出的元素数量为：结束位置 - 开始位置；
	b1 := s[2:5] //
	fmt.Println(b1)
	b2 := s[0:] // 当缺省结束位置时，表示从开始位置到整个连续区域末尾；
	fmt.Println(b2)
	b3 := s[:8] // 当缺省开始位置时，表示从连续区域开头到结束位置；
	fmt.Println(b3)

	// （3）字符串拼接
	var s1 = "hello"
	var s2 = "zhang"
	var s3 = s1 + s2 // 生成一个新的字符串
	fmt.Println(s3)
}
```

#### 2.4.7 进制转换

```go
package main

import (
	"fmt"
)
func main(){
/*
		进制转换
	*/
	//	十进制转化
	var n int = 10
	//printf 传输数据进去
	fmt.Printf("%d \n", n) //%d  表示十进制
	fmt.Printf("%o \n", n) // 占位符%o表示八进制
	fmt.Printf("%b \n", n) //占位符%b表示二进制
	fmt.Printf("%x \n", n) //占位符%x表示十六进制
	// 八进制转化
	var b int = 020
	fmt.Printf("%o \n", b) // 20
	fmt.Printf("%d \n", b) // 16
	fmt.Printf("%x \n", b) // 10
	fmt.Printf("%b \n", b) // 10000
	// 十六进制转化
	var c = 0x12
	fmt.Printf("%d \n", c) // 18
	fmt.Printf("%o \n", c) // 22
	fmt.Printf("%x \n", c) // 12
	fmt.Printf("%b \n", c) // 10010
}
```

### 字符串的常用方法

字符串类型在 go 里是个结构, 包含指向底层数组的指针和长度,这两部分每部分都是 8 个字节，所以字符串类型大小为 16 个字节。

```go
package main

import (
	"fmt"
	"reflect"
	"strings"
)

/*

方法 	介绍
len(str) 	求长度
strings.ToUpper,strings.ToLower 	生成一个新的全部大写的字符串,生成一个新的全部小写的字符串
strings.ReplaceAll 	生成一个新的原字符串被指定替换后的字符串
strings.Contains 	判断是否包含
strings.HasPrefix,strings.HasSuffix 	前缀/后缀判断
strings.Trim、 	去除字符串两端匹配的内容
strings.Index(),strings.LastIndex() 	子串出现的位置
strings.Split 	分割，将字符串按指定的内容分割成数组
strings.Join(a[]string, sep string) 	join操作，将数组按指定的内容拼接成字符串

*/

func main() {
	//action
	action()
}
func allstring() {
	var a string
	a = "hello,Zhang,tian,hao"
	//获取字符串长度
	fmt.Println(len(a))
	//	一个全部大写的字符串
	fmt.Println(strings.ToUpper(a))
	//全部小写
	fmt.Println(strings.ToLower(a))
	//	判断是否包含
	fmt.Println(strings.Contains(a, "tianhao"))
	//前缀判断
	fmt.Println(strings.HasPrefix(a, "zth"))
	//后缀判断
	fmt.Println(strings.HasSuffix(a, "hao"))
	//	是否包含
	fmt.Println(strings.Contains(a, "tian"))
	//	去除字符串两端匹配的内容
	s := strings.Trim(a, "o")
	fmt.Println(s)
	//子串出现的位置
	fmt.Println(strings.Index(a, "Z")) //没有找到就返回-1
	//最后一次出现的位置
	fmt.Println(strings.LastIndex(a, "o"))
	//分割，将字符串按指定的内容分割成数组
	a2 := strings.Split(a, ",")
	//fmt.Println(reflect.TypeOf(a2))
	fmt.Println(a2)
	//将数组拼接成字符串
	var set = strings.Join(a2, "-")
	fmt.Println(set, "\n", reflect.TypeOf(set))
}
func action() {
	s := "mysql … -u root -p 123"
	uindex := strings.Index(s, "-u")
	pindex := strings.Index(s, "-p")
	user := s[uindex+3 : pindex-1]
	psd := s[pindex+3:]
	fmt.Println("用户名为", user, "密码为", psd)
}

```

### 数据类型转换

```go
package main

import (
	"fmt"
	"reflect"
	"strconv"
)

func main() {
	//（1）整型之间的转换 int8 int16
	var x int8 = 10
	var y int16 = 20
	fmt.Println(x + int8(y))
	// （2）字符串与整型之间的转换 strconv库
	var agestr = "32"
	//var name = 12
	//字符串转整型
	var age, _ = strconv.Atoi(agestr)
	fmt.Println(age)
	//fmt.Println("err: ", err) // <nil>空对象
	price := 100
	//整形转字符
	price_str := strconv.Itoa(price)
	fmt.Println(price_str, reflect.TypeOf(price_str))

	//strconv parse系列函数
	//字符串转整型 base进制   bitSize是比特位 8---int8
	ret, _ := strconv.ParseInt("28", 10, 8)
	fmt.Println(ret, reflect.TypeOf(ret))
	//字符串转换为浮点型
	floats, _ := strconv.ParseFloat("3.1415926", 64)
	fmt.Println(floats, reflect.TypeOf(floats))
	//字符串转换为布尔值
	b, _ := strconv.ParseBool("0")
	b1, _ := strconv.ParseBool("-1")
	b2, _ := strconv.ParseBool("true")
	b3, _ := strconv.ParseBool("T")
	fmt.Println(b, b1, b2, b3)
}

```

### 运算符

|        运算符        |                             描述                             |
| :------------------: | :----------------------------------------------------------: |
|    **算数运算符**    |     **用于执行基本的数学运算，如加法、减法、乘法和除法**     |
|          +           |                   加法运算符，将两个数相加                   |
|          -           |             减法运算符，从第一个数中减去第二个数             |
|          *           |                   乘法运算符，将两个数相乘                   |
|          /           |              除法运算符，将第一个数除以第二个数              |
|          %           |          取模运算符，返回第一个数除以第二个数的余数          |
| **自增和自减运算符** |          **自增或自减运算符用于递增或递减变量的值**          |
|          ++          |                  自增运算符，将变量的值加一                  |
|          –           |                  自减运算符，将变量的值减一                  |
|    **比较运算符**    |         **用于比较两个值之间的关系，返回一个布尔值**         |
|          ==          |    等于运算符，如果两个值相等则返回 true，否则返回 false     |
|          !=          |  不等于运算符，如果两个值不相等则返回 true，否则返回 false   |
|          >           | 大于运算符，如果左边的值大于右边的值则返回 true，否则返回 false |
|          <           | 小于运算符，如果左边的值小于右边的值则返回 true，否则返回 false |
|          >=          | 大于等于运算符，如果左边的值大于等于右边的值则返回 true，否则返回 false |
|          <=          | 小于等于运算符，如果左边的值小于等于右边的值则返回 true，否则返回 false |
|    **逻辑运算符**    |            **用于执行逻辑运算，如AND、OR、和NOT**            |
|          &&          | 逻辑 AND 运算符，两个操作数都为 true 时才返回 true，否则返回 false |
|         \|\|         | 逻辑 OR 运算符，两个操作数有一个为 true 时返回 true，否则返回 false |
|          !           | 逻辑 NOT 运算符，如果条件为 true 则返回 false，否则返回 true |
|     **位运算符**     |  **用于执行二进制位运算，例如 按位与、按位或、按位异或等**   |
|          &           | 按位 AND 运算符，参与运算的两个值都为 1 时才返回 1，否则返回 0 |
|          \|          | 按位 OR 运算符，参与运算的两个值有一个为 1 时就返回 1，否则返回 0 |
|          ^           | 按位异或运算符，参与运算的两个值只有一个为 1 时才返回 1，否则返回 0 |
|          <<          | 左移运算符，将第一个操作数的二进制位向左移动第二个操作数指定的位数 |
|          >>          | 右移运算符，将第一个操作数的二进制位向右移动第二个操作数指定的位数 |
|    **赋值运算符**    |             **用于将一个值赋给一个变量或表达式**             |
|          =           |        简单赋值运算符，将右边表达式的值赋给左边的变量        |
|          +=          |               加法赋值运算符，等价于 a = a + b               |
|          -=          |               减法赋值运算符，等价于 a = a - b               |
|          *=          |               乘法赋值运算符，等价于 a = a * b               |
|          /=          |               除法赋值运算符，等价于 a = a / b               |
|          %=          |               取余赋值运算符，等价于 a = a % b               |
|         <<=          |              左移赋值运算符，等价于 a = a << b               |
|         >>=          |              右移赋值运算符，等价于 a = a >> b               |
|          &=          |            按位 AND 赋值运算符，等价于 a = a & b             |

```go
package main

import "fmt"

func main() {
	// 和python运算符一样

	//计算一个数是为奇数还是偶数
	x, y := 10, 20
	fmt.Println(x%2 == 0, y)

	//关系运算符 与python相同 == != >= <= 返回布尔值
	fmt.Println(x >= y)
	//逻辑运算符
	//与或非运算
	/*
		与&&: 真真-真，真假-假，假假-假 ，
		或||： 真或真-真，真或假为真,假或假为假
		非运算 !：非真为假，非假为真  取反
	*/
	fmt.Println(true && false)
	fmt.Println(true || false)
	fmt.Println(!true || false)
	//database:root 123
	username := "zhang"
	password := 123
	fmt.Println(username == "root" && password == 123)
	/*
		赋值运算
		和python一样
	*/
	var a = 12
	a += 1
	fmt.Println(a)
	var b = 10
	//自加一
	b++
	fmt.Println(b)
	//优先级
	var q, w, z = 1, 2, 3
	fmt.Println(q, w, z)
	var t = q + w
	fmt.Println(t)
}
```



### 函数

Go 函数可以返回多个值，例如：

```
package main
import "fmt"

func swap(x, y string) (string, string) {
   return y, x
}

func main() {
   a, b := swap("Mahesh", "Kumar")
   fmt.Println(a, b)
}
```

#### init函数与import

init 函数可在package main中，可在其他package中，可在同一个package中出现多次。

main 函数只能在package main中。

建议用户在一个package中每个文件只写一个init函数。

**执行顺序**

golang里面有两个保留的函数：init函数（能够应用于所有的package）和main函数（只能应用于package main）。这两个函数在定义时不能有任何的参数和返回值。

go程序会自动调用init()和main()，所以你不需要在任何地方调用这两个函数。每个package中的init函数都是可选的，但package main就必须包含一个main函数。

程序的初始化和执行都起始于main包。

如果main包还导入了其它的包，那么就会在编译时将它们依次导入。有时一个包会被多个包同时导入，那么它只会被导入一次（例如很多包可能都会用到fmt包，但它只会被导入一次，因为没有必要导入多次）。

当一个包被导入时，如果该包还导入了其它的包，那么会先将其它包导入进来，然后再对这些包中的包级常量和变量进行初始化，接着执行init函数（如果有的话），依次类推。

等所有被导入的包都加载完毕了，就会开始对main包中的包级常量和变量进行初始化，然后执行main包中的init函数（如果存在的话），最后执行main函数。

**说明如果一个包会被多个包同时导入，那么它只会被导入一次，而先输出lib2是因为main包中导入Lib1时，Lib1又导入了Lib2，会首先初始化Lib2包的东西**



#### 函数参数

函数如果使用参数，该变量可称为函数的形参。

形参就像定义在函数体内的局部变量。

调用函数，可以通过两种方式来传递参数：



#### 值拷贝

值传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。

默认情况下，Go 语言使用的是值传递，即在调用过程中不会影响到实际参数。

> 这是`go`语言的一个特性,与`python`等动态语言不一样
>
> `go`中每个变量是不同的地址

```go
package main

import "fmt"

func main() {
	//与python不同   python一变则全变
	//go中为拷贝 是完全不同的内存地址
	var x = 10
	var y = x
	x = 20
	//x= 20 y= 10
	fmt.Println("x=", x, "y=", y)
	sum()
}

func sum() {
	var a = 1 + 1
	var b = a
	var c = a * b
	fmt.Println(a, b, c)
}
```



#### 引用传递(指针传递)

变量是一种使用方便的占位符，用于引用计算机内存地址。

Go 语言的取地址符是 &，放到一个变量前使用就会返回相应变量的内存地址。

引用传递是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

函数中引用传递*

变量指向地址&

```
package main
import "fmt"

func main() {
   var a int = 10   
   fmt.Printf("变量的地址: %x\n", &a  )
}
```



### 输入输出函数

#### 2.8.1 输出函数

> `go`中常用的输入输出函数，在`fmt`包中

```go
package main

import "fmt"

func main() {
	//输出函数
	//print println
	var name, age = "yuan", 32
	fmt.Println("hello world")
	fmt.Println(name)
	fmt.Println(age)
	fmt.Println("姓名:", name, "年龄", age)
	//fmt.Print(name)
	//fmt.Print(age)
	var isMarried = false
	fmt.Printf("姓名:%s,年龄:%d,婚否:%t\n", name, age, isMarried)
	//sprintf:
	s := fmt.Sprintf("姓名:%s,年龄:%d,婚否:%t", name, age, isMarried)
	fmt.Println(s)
}

```

#### 2.8.2 输入函数

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	//输入函数 IO函数
	scan()
}
func action() {
	var (
		birth string
	)
	fmt.Println("输入生日格式如:1988-3-16")
	fmt.Scan(&birth)
	birthslice := strings.Split(birth, "-")
	fmt.Printf("您的生日是%s年-%s月-%s日", birthslice[0], birthslice[1], birthslice[2])

}
func scan() {
	//(1)fmt.scan
	//var name string
	//var age int
	//fmt.Scan(&name, &age)
	//fmt.Println(name, age)
	//(2)fmt.scanln

	//(3)fmt.scanf  按照指定的格式输入
	var a, b int
	fmt.Scanf("%d+%d", &a, &b)
	fmt.Println(a + b)
}
func action2() {
	var name string
	fmt.Println("请输入一个英文名: ")
	fmt.Scan(&name)
	var b = (strings.HasPrefix(name, "a")) || (strings.HasPrefix(name, "A"))
	fmt.Println(b)
}

```













### 流程控制语句

#### 2.9.1 if语句

```mermaid
graph TD;
  start[开始]-->ifElse{是否满足条件?};
  ifElse--满足条件-->statement1[执行语句1];
  
  ifElse--不满足条件-->ifElseNext{是否满足另一条件?};
  ifElseNext--满足条件-->statement2[执行语句2];
  
  ifElseNext--不满足条件-->statement3[执行语句3];
  statement1[执行语句1]-->结束;
  statement2[执行语句2]-->结束;
  statement3[执行语句3]-->结束;

```

```go
package main

import "fmt"

func main() {
	twoIf()
}
func twoIf() {
	//双分支if else
	var (
		passworld any
		input     string
	)
	passworld = "123"
	fmt.Println("输入密码:")
	fmt.Scan(&input)
	if passworld == input {
		fmt.Println("密码正确")
	} else {
		fmt.Println("密码错误")
	}
}
```

多分支

```go
package main

import "fmt"

func main() {
	weeks()
}

func scanres() {
	//判断成绩
	var scanre int
	fmt.Println("输入你的成绩")
	fmt.Scan(&scanre)
	if scanre > 100 || scanre < 0 {
		fmt.Println("输入数字不合法")
	} else if scanre >= 90 {
		fmt.Println("优秀")
	} else if scanre >= 70 {
		fmt.Println("良好")
	} else if scanre >= 60 {
		fmt.Println("及格")
	} else {
		fmt.Println("不及格")
	}
}
func weeks() {
	var week int
	fmt.Println("输入1-7的数字")
	fmt.Scan(&week)
	if week == 1 {
		fmt.Printf("星期%d", week)
	} else if week == 2 {
		fmt.Printf("星期%d", week)
	} else if week == 3 {
		fmt.Printf("星期%d", week)
	} else if week == 4 {
		fmt.Printf("星期%d", week)
	} else if week == 5 {
		fmt.Printf("星期%d", week)
	} else if week == 6 {
		fmt.Printf("星期%d", week)
	} else if week == 7 {
		fmt.Printf("星期%d", week)
	} else {
		fmt.Println("非法输入")
	}
}

```









#### 2.9.2 switch语句

```mermaid
graph TD;
  start[开始]-->switchCond{选择变量值};
  switchCond--case 1-->statement1[执行语句1];
  switchCond--case 2-->statement2[执行语句2];
  switchCond--case 3-->statement3[执行语句3];
  switchCond--default-->defaultState[执行默认语句];
  statement1-->结束;
  statement2-->结束;
  statement3-->结束;
  defaultState-->结束;
```

```go
package main

import "fmt"

func main() {
	weeks()
}

func weeks() {

	switch week {
	case 1:
		fmt.Printf("星期%d", week)
	case 2:
		fmt.Printf("星期%d", week)
	case 3:
		fmt.Printf("星期%d", week)
	case 4:
		fmt.Printf("星期%d", week)
	case 5:
		fmt.Printf("星期%d", week)
	case 6:
		fmt.Printf("星期%d", week)
	case 7:
		fmt.Println("星期日")
	default:
		fmt.Println("非法输入")
	}
}

```







#### 2.9.3 流程控制语句练习

> 星座判断

```go
package main

import "fmt"

func main() {
	var (
		month   int
		day     int
		xingZuo string
	)
	fmt.Println("输入月和日")
	fmt.Scan(&month, &day)
	if month > 12 && day > 31 && day < 1 {
		fmt.Println("输入有误")
	} else {
		switch month {
		case 1:
			if day >= 1 && day <= 19 {
				xingZuo = "摩羯座"
			} else {
				xingZuo = "水瓶座"
			}
		case 2:
			// 日判断
			if day >= 1 && day <= 18 {
				xingZuo = "水瓶座"
			} else {
				xingZuo = "双鱼座"
			}
		case 3:
			// 日判断
			if day >= 1 && day <= 20 {
				xingZuo = "双鱼座"
			} else {
				xingZuo = "白羊座"
			}
		case 4:
			// 日判断
			if day >= 1 && day <= 19 {
				xingZuo = "白羊座"
			} else {
				xingZuo = "金牛座"
			}
		case 5:
			// 日判断
			if day >= 1 && day <= 20 {
				xingZuo = "金牛座"
			} else {
				xingZuo = "双子座"
			}
		case 6:
			// 日判断
			if day >= 1 && day <= 21 {
				xingZuo = "双子座"
			} else {
				xingZuo = "巨蟹座"
			}
		case 7:
			// 日判断
			if day >= 1 && day <= 22 {
				xingZuo = "巨蟹座"
			} else {
				xingZuo = "狮子座"
			}
		case 8:
			// 日判断
			if day >= 1 && day <= 22 {
				xingZuo = "狮子座"
			} else {
				xingZuo = "处女座"
			}
		case 9:
			// 日判断
			if day >= 1 && day <= 22 {
				xingZuo = "处女座"
			} else {
				xingZuo = "天秤座"
			}
		case 10:
			// 日判断
			if day >= 1 && day <= 23 {
				xingZuo = "天秤座"
			} else {
				xingZuo = "天蝎座"
			}
		case 11:
			// 日判断
			if day >= 1 && day <= 22 {
				xingZuo = "天蝎座"
			} else {
				xingZuo = "射手座"
			}
		case 12:
			// 日判断
			if day >= 1 && day <= 21 {
				xingZuo = "射手座"
			} else {
				xingZuo = "摩羯座"
			}
		default:
			fmt.Println("输入的月份有问题")
		}

		fmt.Println("您的星座是：", xingZuo)
	}
}

```







### 循环语句

> 在不少实际问题中有许多具有规律性的重复操作，因此在程序中就需要重复执行某些语句。一组被重复执行的语句称之为循环体，
> 能否继续重复，决定循环的终止条件。
>
> 与其它主流编程语言不同的的是，`Go`语言中的循环语句只支持` for` 关键字，而不支持` while` 和 `do-while `结构。

```mermaid
graph TD;
  start[开始]-->forLoop;
  forLoop-->condCheck{检查条件};
  condCheck--条件成立-->statement[执行语句];
  statement-->endCheck{检查是否为最后一次循环};
  endCheck--不是最后一次-->updateLoop[更新循环变量];
  updateLoop-->condCheck;
  endCheck--是最后一次-->结束;
```







#### 2.10.1 for循环

```go
package main

import "fmt"

func main() {
	//var a = 100
	//for a > 0 {
	//	a--
	//	fmt.Println(a)
	//}
	//	三要素for循环
	/*
		var a = 100//初始语句
		a > 0//条件语句
		a--//步进语句
	*/
	//for count := 0; count < 10; count++ {
	//	fmt.Println(count)
	//}
	var s = 0
	for i := 1; i <= 100; i++ {
		s += i
	}
	fmt.Println(s)
}

```





#### 2.10.2循环分支嵌套

```go
package main

import "fmt"

func main() {
	//for_switch()
	//if_for()
}
func for_if() {
	for count := 1; count <= 100; count++ {
		if count%2 == 0 {
			fmt.Println("偶数有:", count)
		}
	}
}

func if_for() {
	var num int
	fmt.Println("(1)从大到小,(2)从小到大")
	fmt.Scan(&num)
	if num == 1 {
		for count := 1; count <= 100; count++ {
			fmt.Println(count)
		}
	} else if num == 2 {
		for count := 100; count <= 1; count-- {
			fmt.Println(count)
		}
	} else {
		fmt.Println("输入不规范")
	}
}
```







#### 2.10.3 退出循环

```go
package main

import "fmt"

func main() {
	//for_switch()
}

func breaks() {
	for i := 0; i < 10; i++ {
		if i == 6 {
			break
		}
		fmt.Println(i)
	}
	fmt.Println("退出了循环")
}
func for_switch() {
	var num int
	fmt.Println(`
		1,普通攻击
		2,技能攻击
		3,逃跑
		4，求助
		5,退出
		`)

	for true {
		fmt.Scan(&num)
		if num == 5 {
			break
		}
		switch num {
		case 1:
			fmt.Println("普通攻击")
		case 2:
			fmt.Println("技能攻击")
		case 3:
			fmt.Println("逃跑")
		case 4:
			fmt.Println("求助")
		default:
			fmt.Println("输入有误")
		}
	}
}
func continues() {
	for count := 0; count < 10; count++ {
		if count == 6 {
			continue
		}
		fmt.Println("这是continue", count)
	}
}
func action() {
	for i := 1; i <= 100; i++ {

	}
}
```







### 复杂数据类型

> 以下为go语言常见复杂数据类型

|   数据类型    |                             描述                             |
| :-----------: | :----------------------------------------------------------: |
|     数组      |         存储具有相同数据类型的固定尺寸元素的连续集合         |
|     指针      |                通过数据地址取值,或者操作地址                 |
|     切片      |    动态大小的、可变长度的序列，可以看作是对数组的抽象封装    |
|   映射(Map)   |        一种无序的键值对的集合，其中所有的键都是唯一的        |
|    结构体     | 用于表示一组不同类型的数据的集合，通过一个唯一的名字来区分不同的字段 |
|     接口      |      一种类型，指定了一组方法的集合，可以实现多态的效果      |
| 通道(Channel) | 一种数据类型，用于在不同goroutine之间传递数据，类似于管道(pipe) |





#### 2.11.1 指针类型

> go的指针只有两个操作
>
> & 变量：取址符，返回存储的变量的所在地址
>
> *：取值符，返回存储变量的所在地址的值。

```go
package main

import (
	"fmt"
	"reflect"
)

func main() {
	//获取地址  &
	//var x = 10
	//fmt.Printf("赋值前x的地址%p\n", &x)
	//x = 100
	//fmt.Printf("赋值后的地址%p\n", &x)
	var x = 10                          //x称为整型变量
	fmt.Println(&x, reflect.TypeOf(&x)) //地址是一个 *int(整型指针类型)
	//地址赋值
	var p *int
	p = &x // 也可以var p =&x
	fmt.Println(p, &p)

	//取值操作 :   *指针变量
	fmt.Println(*p, reflect.TypeOf(*p))
	*p = 100
	fmt.Println(x)

	//var a = 1
	//var b = a //值拷贝
	//b = 100   //不会影响a

	//	b影响a,a影响b(通过指针来实现)
	//var b = &a
	////a = 102
	//*b = 100
	//fmt.Println(*b)
	//指针案例3
	var a = 100
	var b = &a
	var c = &b
	**c = 200
	fmt.Println(a)
}

// 交换a和b的值
func swap(a *int, b *int){
    var temp int
    temp = *a
    *a = *b
    *b = temp
}
swap(&变量1,&变量2)
```

##### 2.11.1.1 new函数

```go
package main

import "fmt"

func main() {
	//指针属于引用类型
	// 引用类型 当声明未赋值之前是没有开辟空间的，即没有默认值
	// 要用new一个(类型)
	var a = new(int)
	*a = 10
	fmt.Println(*a)
}

```

#### 2.11.2 数组

##### 2.11.2.1 数组的声明

```go
// 数组的声明
var arr [5]int	//静态数组，在作为函数的参数时是值拷贝传递
fmt.Println(arr) //[0 0 0 0 0]

var arr [5]int{1,2,3}	//静态数组，在作为函数的参数时是值拷贝传递
arr := []int{1,2,3}	//动态数组，切片slice，在作为函数的参数时是引用传递，传的是数组的首地址指针
fmt.Println(arr) //[1 2 3 0 0]

//遍历数组1
for i:=0; i<len(arr); i++{
    fmt.Println(i)
}

//遍历数组1
for index,value:=range arr{
    fmt.Println("index=",index , "value=",value)
}

//查看数组类型
fmt.Printf("type= %T\n",arr)
```

##### 2.11.2.2 数组赋值

```go
package main

import "fmt"

func main() {
	// 数组的声明
	var arr [5]int
	fmt.Println(arr) //[0 0 0 0 0]
	//赋值  数组[索引]
	fmt.Println(arr[0])
	fmt.Println(arr[1])
	fmt.Println(arr[2])
	//索引赋值
	arr[0] = 25
	fmt.Println(arr)
	arr[1] = 26
	arr[2] = 27
	arr[3] = 28
	fmt.Println(arr) //[25 26 27 28 0]

	// 数组的声明并赋值
	var names = [3]string{"ran", "zhang", "shi"}
	fmt.Println(names)
	var age = [3]int{1, 2, 3}
	fmt.Println(age)

	//	省略长度赋值
	var name = [...]string{"ran", "zhang", "shi", "jjj", "aaa"}

	fmt.Println(name)
	//索引赋值
	var name1 = [...]string{0: "zhang", 1: "shi"}
	fmt.Println(name1)
	//go len函数 :计算容器长度
	fmt.Println(len(name))
}
```

##### 2.11.2.3数组操作

###### 2.11.2.3.1索引操作

```go
var names = [3]string{"yuan", "rain", "alvin"}
//索引操作
fmt.Println(names[2])
names[2] = "Rain"
fmt.Println(names)
```

###### 2.11.2.3.2切片操作

```go
var arr = [...]int{11, 12, 13, 45, 75, 64, 78, 66, 54}
s := arr[0:3]
fmt.Println(s, reflect.TypeOf(s)) //[11 12 13]  []int
s1 := arr[1:]
fmt.Println(s, reflect.TypeOf(s)) //[11 12 13]  []int
s2 := arr[:3]
fmt.Println(s, reflect.TypeOf(s)) //[11 12 13]  []int
s3 := arr[2:4]
fmt.Println(s, reflect.TypeOf(s)) //[11 12 13]  []int
fmt.Println(s1, s2, s3)
```

###### 2.11.2.3.3 遍历数组

```go
var arr2 = [...]int{1, 2, 3, 4, 5}
for i, i2 := range arr2 {
    //  相当于python中的en...函数
    // i为下标 i2为数据
    fmt.Println(i, i2)
}
fmt.Println("======================================================================")
//用for三要素提取
for i := 0; i < len(arr2); i++ {
    fmt.Println(i, arr2[i])
}
```

##### 2.11.2.3 切片

> 切片是对数组的引用，也就是动态数组

```go
//动态数组，切片slice，在作为函数的参数时是引用传递，传的是数组的首地址指针
var a = [5]int{1, 2, 3, 4, 5}
var slice = a[:] //起始地址 长度 容量(cap)
fmt.Println(len(slice), cap(slice))

var slice2 = a[:2]
fmt.Println(len(slice2), cap(slice2))
newSlicec := slice[1:3]
newSlicec[1] = 1000 // [1 2 1000 4 5]
fmt.Println(a)
fmt.Println(slice) // [1 2 1000 4 5]
fmt.Println(slice2)
```



###### 2.11.2.3.1 声明切片

```go
// 声明并初始化切片，默认值1，2，3，长度为3
arr := []int{1,2,3}
fmt.Printf("len=%d,slice=&v\n",len(slice1),slice1)

// 声明一个空切片，需要手动分配空间，不分配空间之前不能赋值
var slice1 []int
slice1 = make([]int , 3)

//声明并初始化且分配空间的切片
slice1 := make([]int , 3)

slice1 = append(slice1,2)	//向其中追加元素2

//切片截取，若对切片的片段进行值修改，则原本的也会改，若想要不修改，则需要创建一个新的切片进行copy函数
var s = []int{10, 11, 12, 13, 14}
s1 := s[1:4]	//左闭右开，11，12，13
fmt.Println(len(s1), cap(s1))
s2 := s[3:]
fmt.Println(len(s2), cap(s2))
s3 := s1[1:2]	// 11
fmt.Println(len(s3), cap(s3))
```

示例：

```go
func action() {
	var a = [...]int{1, 2, 3, 4, 5, 6}
	a1 := a[0:3]
	a2 := a[0:5]
	a3 := a[1:5]
	fmt.Println(a1)
	fmt.Println(a2)
	fmt.Println(a3)
}
```

###### 2.11.2.3.2 make函数

```go
package main

import (
	"fmt"
)

func main() {
	//var s []int
	//初始化创建空间 ，长度为5，10为容量，当长度超过容量时，默认容量会扩容到2倍
	var s = make([]int, 5, 10)
	fmt.Println(len(s), cap(s))
	fmt.Println(s)
	s[0] = 100
	fmt.Println(s)
}

```

##### 2.11.2.4 map类型

> 相当于python的字典类型
> 在go中叫做map类型
>
> 作为函数参数是引用传递

1. 先声明后赋值

   ```go
   // 声明myMap是一种map类型，key是string，值为string
   var myMap map[string]string
   myMap = make(map[string]string, 10)		//分配空间
   
   
   // 声明并初始化
   myMap := make(map[string]string)
   myMap["name"] = "zhang"
   myMap["age"] = "18"
   fmt.Println(myMap)	//是乱序
   
   myMap := make(map[string]string){
       "name": "zhang"
       "age": "18"
   }
   ```

2. 直接声明赋值,

   ```go
   info := map[string]string{"name": "yuan", "age": "20", "city": "China"}
   ```

 3. map增删改查

    ```go
    //查
    info := map[string]string{"name": "yuan", "age": "20", "city": "China"}
    fmt.Println(info)
    Map()
    //改,增
    info2 := map[string]string{"name": "yuan", "age": "18", "gender": "male"}
    m2 := Map02(info2)
    fmt.Println(m2)
    //	删
    m3 := Map03(info2)
    fmt.Println(m3)
    ```

4. map容量

   ```go
   //	map容量
   m := make(map[string]float32, 100)
   m4 := Map04(m)
   fmt.Println(m4)
   ```

5. map的综合应用

   ```go
   func Map() {
   	//	map增删改查
   	//	通过key访问  查
   	info := map[string]string{"name": "yuan", "age": "18", "gender": "male"}
   	val := info["name"]
   	val, is_exist := info["name"] // 判断某个键是否存在map数据中
   	if is_exist {
   		fmt.Println(val)
   		fmt.Println(is_exist)
   	} else {
   		fmt.Println("键不存在！")
   	}
   	//循环访问所有键值
   	for s, s2 := range info {
   		fmt.Println(s, s2)
   	}
   	noSortMap := map[int]int{
   		1: 1,
   		2: 2,
   		3: 3,
   		4: 4,
   		5: 5,
   		6: 6,
   	}
   	//range顺序随机
   	for i, i2 := range noSortMap {
   		fmt.Println(i, i2)
   	}
   }
   func Map02(s map[string]string) map[string]string {
   	s["height"] = "180cm" // 键不存在，则是添加键值对
   	s["age"] = "22"       // 键存在，则是更新键的值
   	return s
   	//	利用函数写的map02输入map类型 返回map类型
   }
   func Map03(s map[string]string) any {
   	//删
   	delete(s, "name")
   	return s
   }
   func Map04(s map[string]float32) any {
   	//	容量
   	return len(s)
   }
   func Map05() {
   	//	map的灵活运用
   	data := map[string][]string{"hebei": []string{"廊坊市", "石家庄", "邯郸"}, "beijing": []string{"朝阳", "丰台", "海淀"}}
   	// 打印河北的第二个城市
   	// 循环打印每个省份的名字和城市数量
   	// 添加一个新的省份和城市的key-value
   	// 删除北京的key-value
   	s := data["hebei"][1]
   	fmt.Println("河北的第二个城市", s)
   	for s2, _ := range data {
   		for _, s3 := range data[s2] {
   			fmt.Println(s2, len(data[s2]), s3)
   		}
   
   	}
   	//ss := map[string][]string{"sicuan": {"meishan", "chengdu"}}
   	//fmt.Println(ss)
   	var zz = []string{"眉山", "成都"}
   	data["chengdu"] = zz
   	fmt.Println(data)
   	delete(data, "beijing")
   	fmt.Println(data)
   }
   ```

##### 2.11.2.5 map底层原理

1. `Go`语言中的`Map`基本数据结构是一张哈希表，其中每个哈希表的元素是一个桶（`Bucket`）

   ```mermaid
   graph TD;
     Map[Map];
     Bucket1[Bucket1];
     Bucket2[Bucket2];
     Bucket3[Bucket3];
     Map-->Bucket1;
     Map-->Bucket2;
     Map-->Bucket3;
   ```

2. `桶（Bucket）`是一个包含一些键值对的可拓展数组（`slicemap`），`key`和`value`都占用8个字节的空间。

   ```mermaid
   graph TD;
     Bucket[Bucket];
     KeyValue1[KeyValue];
     KeyValue2[KeyValue];
     KeyValue3[KeyValue];
     KeyValue4[KeyValue];
     KeyValue5[KeyValue];
     Bucket-->KeyValue1;
     Bucket-->KeyValue2;
     Bucket-->KeyValue3;
     Bucket-->KeyValue4;
     Bucket-->KeyValue5;
   ```

   

3. 当我们使用`Key`在Map中存储或获取值时，`Go`语言中的`Map`会自动进行哈希计算以获取桶索引，然后在对应的桶中查找对应的值。

   ```mermaid
   graph TD;
     Map[Map];
     Key1(Key1);
     Key2(Key2);
     Index1(Index1);
     Index2(Index2);
     Bucket1(Bucket1);
     KeyValue(KeyValue);
     Value(Value);
     Key1-->Hash(Hash);
     Hash-->Index1;
     Map-->Bucket1;
     Index1-->Bucket1;
     Bucket1-->KeyValue;
     KeyValue-->Value;
     Value-->GetResult(Get Result);
     Key2-->Hash;
     Hash-->Index2;
     Index2-->Result(Result不存在);
   ```

   

> 需要注意的是，`Go`语言中的`Map`底层实现可以随着版本、编译器和运行环境等因素的不同而略有差异。但是在任何情况下，`Map`都会使用基于哈希表和桶的底层实现机制来实现其存储和查找功能。

##### 2.11.2.6 append函数

> append 追加值,给切片追加，在后面追加数据

```go
package main

import "fmt"

func main() {
	var s []int
	//append 追加值,给切片追加，在后面追加数据
	s1 := append(s, 1)
	fmt.Println(s1)
	s2 := append(s1, 2, 3, 4)
	fmt.Println(s2)

	var t = []int{2, 3, 4}
	t1 := append(t, 1)
	fmt.Println(t1)

	var s4 = make([]int, 3, 10) //初始化完，[0 0 0]
	s5 := append(s4, 100)
	fmt.Println(s5) //[0 0 0 100]
	//Append()
	var c = 255
	fmt.Println(c >> 10)
}
```

***append 底层原理(面试题)***

```go
func Append() {
	//append 重新开辟内存
	var a1 = make([]int, 2, 10)
	a2 := append(a1, 1, 23, 4)
	fmt.Println(a1)
	fmt.Println(a2)
	//[0 0]
	//[0 0 1 23 4]
	//因为a1空间为2，所以给他添加3个内容时，append会重新开辟内存，

}
```



### defer语句

> defer类似于java中的finally，在函数内的语句全部执行完之后执行。即使在发生错误或提前返回的情况下也能保证执行。
>
> defer语句被用于预定对一个函数的调用。可以把这类被defer语句调用的函数称为延迟函数。
>
> 如果一个函数中有多个defer语句，它们会以LIFO（后进先出）的顺序执行。
>
> defer在return之后执行
>
> defer作用： 
>
> - 释放占用的资源
> - 捕捉处理异常
> - 输出日志

```
func main(){
	defer fmt.Println("main end1")	// 多个defer语句，会按照顺序依次压入栈
	defer fmt.Println("main end2")
	
	fmt.Println("hello")
}

//输出hello、end2、end1
```



### recover错误拦截

运行时panic异常一旦被引发就会导致程序崩溃。

Go语言提供了专用于“拦截”运行时panic的内建函数“recover”。它可以是当前的程序从运行时panic的状态中恢复并重新获得流程控制权。

**注意：**recover只有在defer调用的函数中有效。

```
func recover interface{}
```

```
package main

import "fmt"

func Demo(i int) {
	//定义10个元素的数组
	var arr [10]int
	//错误拦截要在产生错误前设置
	defer func() {
		//设置recover拦截错误信息
		err := recover()
		//产生panic异常  打印错误信息
		if err != nil {
			fmt.Println(err)
		}
	}()
	//根据函数参数为数组元素赋值
	//如果i的值超过数组下标 会报错误：数组下标越界
	arr[i] = 10

}

func main() {
	Demo(10)
	//产生错误后 程序继续
	fmt.Println("程序继续执行...")
}
```



### slice

Go 语言切片是对数组的抽象。

Go 数组的长度不可改变，在特定场景中这样的集合就不太适用，Go中提供了一种灵活，功能强悍的内置类型切片`("动态数组")`,与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大。

#### 定义切片

声明一个未指定大小的数组来定义切片：var identifier []type

切片不需要说明长度。

或使用make()函数来创建切片：

```
var slice1 []type = make([]type, len)
也可以简写为
slice1 := make([]type, len)

也可以指定容量，其中capacity为可选参数。
make([]T, length, capacity)
这里 len 是数组的长度并且也是切片的初始长度。
```

#### 切片初始化

```
s :=[] int {1,2,3 }
直接初始化切片，[]表示是切片类型，{1,2,3}初始化值依次是1,2,3.其cap=len=3

s := arr[:]
初始化切片s,是数组arr的引用

s := arr[startIndex:endIndex]
将arr中从下标startIndex到endIndex-1 下的元素创建为一个新的切片

s := arr[startIndex:]
缺省endIndex时将表示一直到arr的最后一个元素

s := arr[:endIndex]
缺省startIndex时将表示从arr的第一个元素开始

s1 := s[startIndex:endIndex]
通过切片s初始化切片s1

s :=make([]int,len,cap)
通过内置函数make()初始化切片s,[]int 标识为其元素类型为int的切片
```

#### len() 和 cap() 函数

切片是可索引的，并且可以由 len() 方法获取长度。

切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。

```
package main
import "fmt"

func main() {
   var numbers = make([]int,3,5)
   printSlice(numbers)
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```

#### 空(nil)切片

一个切片在未初始化之前默认为 nil，长度为 0，实例如下：

```
package main
import "fmt"

func main() {
   var numbers []int
   printSlice(numbers)
   if(numbers == nil){
      fmt.Printf("切片是空的")
   }
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```

#### 切片截取

可以通过设置下限及上限来设置截取切片*[lower-bound:upper-bound]*，实例如下：

```
package main
import "fmt"

func main() {
   /* 创建切片 */
   numbers := []int{0,1,2,3,4,5,6,7,8}   
   printSlice(numbers)

   /* 打印原始切片 */
   fmt.Println("numbers ==", numbers)

   /* 打印子切片从索引1(包含) 到索引4(不包含)*/
   fmt.Println("numbers[1:4] ==", numbers[1:4])

   /* 默认下限为 0*/
   fmt.Println("numbers[:3] ==", numbers[:3])

   /* 默认上限为 len(s)*/
   fmt.Println("numbers[4:] ==", numbers[4:])

   numbers1 := make([]int,0,5)
   printSlice(numbers1)

   /* 打印子切片从索引  0(包含) 到索引 2(不包含) */
   number2 := numbers[:2]
   printSlice(number2)

   /* 打印子切片从索引 2(包含) 到索引 5(不包含) */
   number3 := numbers[2:5]
   printSlice(number3)

}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```

#### append() 和 copy() 函数

如果想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。

下面的代码描述了从拷贝切片的 copy 方法和向切片追加新元素的 append 方法。

```
package main
import "fmt"

func main() {
   var numbers []int
   printSlice(numbers)

   /* 允许追加空切片 */
   numbers = append(numbers, 0)
   printSlice(numbers)

   /* 向切片添加一个元素 */
   numbers = append(numbers, 1)
   printSlice(numbers)

   /* 同时添加多个元素 */
   numbers = append(numbers, 2,3,4)
   printSlice(numbers)

   /* 创建切片 numbers1 是之前切片的两倍容量*/
   numbers1 := make([]int, len(numbers), (cap(numbers))*2)

   /* 拷贝 numbers 的内容到 numbers1 */
   copy(numbers1,numbers)
   printSlice(numbers1)   
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```



### map

map和slice类似，只不过是数据结构不同，下面是map的一些声明方式。

```
package main
import (
    "fmt"
)

func main() {
    //第一种声明
    var test1 map[string]string
    //在使用map前，需要先make，make的作用就是给map分配数据空间
    test1 = make(map[string]string, 10) 
    test1["one"] = "php"
    test1["two"] = "golang"
    test1["three"] = "java"
    fmt.Println(test1) //map[two:golang three:java one:php]

    //第二种声明
    test2 := make(map[string]string)
    test2["one"] = "php"
    test2["two"] = "golang"
    test2["three"] = "java"
    fmt.Println(test2) //map[one:php two:golang three:java]

    //第三种声明
    test3 := map[string]string{
        "one" : "php",
        "two" : "golang",
        "three" : "java",
    }
    fmt.Println(test3) //map[one:php two:golang three:java]
    
    language := make(map[string]map[string]string)
    language["php"] = make(map[string]string, 2)
    language["php"]["id"] = "1"
    language["php"]["desc"] = "php是世界上最美的语言"
    language["golang"] = make(map[string]string, 2)
    language["golang"]["id"] = "2"
    language["golang"]["desc"] = "golang抗并发非常good"
   
    fmt.Println(language) //map[php:map[id:1 desc:php是世界上最美的语言] golang:map[id:2 desc:golang抗并发非常good]]

    //增删改查
    // val, key := language["php"]  //查找是否有php这个子元素
    // if key {
    //     fmt.Printf("%v", val)
    // } else {
    //     fmt.Printf("no");
    // }

    //language["php"]["id"] = "3" //修改了php子元素的id值
    //language["php"]["nickname"] = "啪啪啪" //增加php元素里的nickname值
    //delete(language, "php")  //删除了php子元素
    fmt.Println(language)
}
```



### 结构体

> 结构体是一种自定义的复合数据类型，它由各种属性或字段（Fields）组成，每个字段都有自己的数据类型。在Go语言中，结构体可以包含任意数量的字段，且每个字段可以是任何可比较类型（包括基本数据类型和自定义类型等）。
>
> 可以通过初始化结构体的字段来创建一个新的结构体实例。另外，结构体实例的各个字段可以通过点（.）运算符来访问和修改，这使得结构体非常灵活和易于使用。结构体类型也可以实现方法和接口，以增强其功能和可用性。

> 定义结构体

```go
// 定义结构体，这里的type可以理解为给struct起了别名
type Employee struct

//如果直接把结构体作为函数参数，那只是传了一个副本，传指针才能对原本的进行修改
```

```go
package main

import "fmt"

func main() {
	Task()
	Madd()
}

// 定义结构体
type Employee struct {
	ID        int
	Name      string
	Address   string
	Position  string
	Salary    int
	ManagerID int
}

// 定义结构体变量
var dilbert Employee

// 定义并初始化结构体变量
dilbert := Employee{Name:"zhangsan"}

// dilber的成员用点来访问

func Task() {
	//可以直接赋值
	dilbert.Name = "张天昊"
	//	也可以通过地址访问
	position := &dilbert.Position
	*position = "Senir" + *position
	fmt.Println(*position)
	//	点号可以直接运用到结构体指针上
	var employeeOfTheMonth *Employee = &dilbert
	(*employeeOfTheMonth).Position += "(pass team player)"
	fmt.Println(employeeOfTheMonth) //&{0 张天昊  Senir(pass team player) 0 0}

}

// 给定参数ID然后返回Employee结构体的指针，可以用点号来访问他的成员变量

// 也可以作为map键

type address struct {
	hostname string
	port     int
}

func Madd() {
	hits := make(map[address]int)
	hits[address{"golang.org", 403}]++
	fmt.Println(hits) //map[{golang.org 403}:1]
}

// 结构体嵌套
type Point struct {
	x, y int
}
type Cir struct {
	Center Point
	Rauid  int
}
type Whell struct {
	Cir   Cir
	Spoke int
}

// 这个程序更清晰，但是访问whell成员更麻烦了
func Run() {
	var w Whell
	w.Cir.Center.x = 8
	w.Cir.Center.y = 8
	w.Cir.Rauid = 5
	w.Spoke = 20

}

// Go允许我们定义不带名称的结构体成员，叫做匿名成员
type Circle struct {
	Point
	Rauins int
}
type wheel struct {
	Circle
	Spokes int
}

func Run2() {
	var w wheel
	w.x = 8 //等价于上面  w.Cir.Center.x = 8
	w.y = 8
}

```

#### 标签

```
package main
import{
    "fmt"
    "reflect"
}

type resume struct{
	Name	string `info:"name" doc:"我的名字"`
	Sex		string `info:"sex"`
}	
	
func findTag(str interface{}){
	t := reflect.Type0f(str).Elem()
	
    for i:=0; i<t.NumField(); i++{
    	taginfo := t.Field(i).Tag.Get("info")
    	tagdoc := t.Field(i).Tag.Get("doc")
    	fmt.Println("info:",taginfo,"doc:",tagdoc)
	}
}

func main(){
	var re resume
	findTag(&re)
}


应用

import{
	"encoding/json"
	"fmt"
}

type Movie struct{
	Title	string	`json:"title"`
	Year	int	`json:"year"`
	Price	int	`json:"rmb"`
	Actors	[]string	`json:"actors"`
func main(){
	movie := Movie{"喜剧之王"，2000，10,[]string{"xingye", “zhangbozhi"}}
	
    //编码的过程 结构体---> json
    jsonStr,err := json.Marshal(movie)
    if err != nil {
        fmt.Println("json marshal error", err)
        return
    }
    fmt.Printf("jsonstr =%s\n",jsonstr)
    
    
    //解码的过程 jsonstr ---> 结构体
    //jsonstr = {"title":"喜剧之","yoor":288,"rmb":10,"arters"': ["xinyye","zhangbozhi"]}
    myMovie := Movie{}
    err = json.Unmarshal(jsonstr,&myMovie)
    if err != nil {
    	fmt.Println("json unmarshal error", err)
    	return
    }
    fmt.Printf("%v\n",myMovie)
}



```







## 三、进阶

### 类

```go
//使用结构体进行类的表示，类名首字母大写表示为public，函数和变量同样如此
type Student struct {
	ID        int
	Name      string
    address   string	//私有属性
}

func (this *Student) Show(){	
	fmt.Println("ID=",this.ID)
    fmt.Println("Name=",this.Name)
}

func (this *Student) GetName(){	
    return this.Name
}

func (this *Student) SetName(newName string){	
    this.Name = newName
}

//定义对象
s1 := Student{ID: 1,Name:"zhangsan",address:"ShangHai"}
```



#### 继承

```
type Class struct {
	Student		//Class类继承了该类的方法
	Name      string
}
```



### interface与类型断言

Golang的语言中提供了断言的功能。golang中的所有程序都实现了interface{}的接口，这意味着，所有的类型如string,int,int64甚至是自定义的struct类型都就此拥有了interface{}的接口，这种做法和java中的Object类型比较类似。那么在一个数据通过func funcName(interface{})的方式传进来的时候，也就意味着这个参数被自动的转为interface{}的类型。

```
func funcName(a interface{}) string {
     return string(a)
}
编译器会返回
cannot convert a (type interface{}) to type string: need type assertion
```

此时，意味着整个转化的过程需要类型断言。类型断言有以下几种形式：

1）直接断言使用

但是如果断言失败一般会导致panic的发生。所以为了防止panic的发生，我们需要在断言前进行一定的判断
但是如果断言失败一般会导致panic的发生。所以为了防止panic的发生，我们需要在断言前进行一定的判断
如果断言失败，那么ok的值将会是false,但是如果断言成功ok的值将会是true,同时value将会得到所期待的正确的值。

```
package main
import "fmt"

/*
func funcName(a interface{}) string {
        return string(a)
}
*/

func funcName(a interface{}) string {
        value, ok := a.(string)
        if !ok {
                fmt.Println("It is not ok for type string")
                return ""
        }
        fmt.Println("The value is ", value)
        return value
}

func main() {
        //      str := "123"
        //      funcName(str)
        //var a interface{}
        //var a string = "123"
        var a int = 10
        funcName(a)
}
```

2）配合switch使用

```
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %T", t)       // %T prints whatever type t has
case bool:
    fmt.Printf("boolean %t\n", t)             // t has type bool
case int:
    fmt.Printf("integer %d\n", t)             // t has type int
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t) // t has type *bool
case *int:
    fmt.Printf("pointer to integer %d\n", *t) // t has type *int
}

或者如下使用方法
func sqlQuote(x interface{}) string {
    if x == nil {
        return "NULL"
    } else if _, ok := x.(int); ok {
        return fmt.Sprintf("%d", x)
    } else if _, ok := x.(uint); ok {
        return fmt.Sprintf("%d", x)
    } else if b, ok := x.(bool); ok {
        if b {
            return "TRUE"
        }
        return "FALSE"
    } else if s, ok := x.(string); ok {
        return sqlQuoteString(s) // (not shown)
    } else {
        panic(fmt.Sprintf("unexpected type %T: %v", x, x))
    }
}
```





### 接口

> 接口是一种类型，它定义了一组方法，这些方法不包含实现代码，只是定义了函数名、参数列表和返回值类型。也就是说，接口描述了一个对象可以做什么，但是并不能描述这些功能是如何实现的。具体的实现代码需要由实现接口的类型来完成。
>
> Go语言中，只要一个类型包含了接口中定义的所有方法，那么该类型就是实现了该接口。这种面向接口的编程方式可以实现高度抽象、低耦合的代码，让代码更具可维护性和扩展性。
>
> interface{} 空接口是万能数据类型，比如使用空接口作为函数参数，func get(arg interface{}){	}。可以通过value,ok := arg.(string)判断arg是否是string类型

#### 3.2.1 接口实现与定义

> 定义接口

```go
//本质是一个指针
type XXXX interface{
	//空方法声明
}
```

```go
package main

import "fmt"

// 接口

type View_terface interface {
	Facks() []rune
}

type runs string

func (x runs) Facks() []rune {
	var View []rune
	for _, runes := range x {
		if runes == 's' || runes == 'a' || runes == 'c' {
			View = append(View, runes)
		}
	}
	return View
}

func main() {
	name := runs("My string is name")
	var v View_terface
	v = name
	fmt.Printf("Voiews are %c", v.Facks())
}
```

> 在上面程序第8行中，创建了一个名为 VowelsFinder 的接口类型，它有一个方法 FindVowels() []rune。
> 在下一行中创建一个类型 MyString 它只是 string 的包装类。
> 在第15行中，我们将方法 FindVowels()[]rune 添加到接收方类型 MyString 中。现在 MyString 被认为实现了 VowelsFinder 接口。
> 这与 Java 等其他语言非常不同，在 Java 中，类必须使用 implements 关键字显式地声明它实现了接口。如果类型包含接口中声明的所有方法，则 go 和 go 接口将隐式实现。
> 在第28行，我们将 MyString 类型的 name 赋给 v 类型的 VowelsFinder。这是可能的，因为 MyString 实现了 VowelsFinder。下一行调用 MyString 类型上的 FindVowels 方法，
> 并打印字符串中所有的元音 My string is name。这个程序输出的 Vowels are [s s a]
> 这样已经创建并实现了第一个接口。

#### 3.2.2 接口继承

```go
package main

import (
	"fmt"
	"strings"
)

//接口的继承，同类的继承一样
type New_interface interface {
	Animo()
	Water()
}

type Cat struct {
	New_interface
}

func (x Cat) Water(i, v int) (n int) {
	n = i + v
	return
}

func (z Cat) Animo(i, v string) (t, x string) {
	s := strings.Split(i, "")
	f := strings.Split(v, "")
	for _, n := range s {
		for _, k := range f {
			if n == k {
				t = ""
				x = ""
			} else if len(v) >= 2 && len(i) >= 2 {
				t = i[1:]
				x = v[2:]
			} else {
				t = "错误"
				x = "error"
			}
		}
	}
	return
}

func Run_one() {
	var c Cat
	fmt.Println(c.Water(10, 20))
	fmt.Println(c.Animo("run", "rusn"))
}

func main() {
	Run_one()
}

```

#### 3.2.3 多态

```
var animal AnimalIF
animal = &Cat{"Green"}
animal = &Dog{"Red"}
```





### 反射

在计算机科学领域，反射是指一类应用，它们能够自描述和自控制。也就是说，这类应用通过采用某种机制来实现对自己行为的描述（self-representation）和监测（examination），并能根据自身行为的状态和结果，调整或修改应用所描述行为的状态和相关的语义。

每种语言的反射模型都不同，并且有些语言根本不支持反射。Golang语言实现了反射，反射机制就是在运行时动态的调用对象的方法和属性，官方自带的reflect包就是反射相关的，只要包含这个包就可以使用。

Golang的gRPC也是通过反射实现的。

#### interface 和 反射

在讲反射之前，先来看看Golang关于类型设计的一些原则

- 变量包括（type, value）两部分
- type 包括 `static type`和`concrete type`. 简单来说 `static type`是你在编码是看见的类型(如int、string)，`concrete type`是`runtime`系统看见的类型
- 类型断言能否成功，取决于变量的`concrete type`，而不是`static type`. 因此，一个 `reader`变量如果它的`concrete type`也实现了`write`方法的话，它也可以被类型断言为`writer`.

接下来要讲的`反射`，就是建立在类型之上的，Golang的指定类型的变量的类型是静态的（也就是指定int、string这些的变量，它的type是static type），在创建变量的时候就已经确定，反射主要与Golang的interface类型相关（它的type是concrete type），只有interface类型才有反射一说。

在Golang的实现中，每个interface变量都有一个对应pair，pair中记录了实际变量的值和类型:

```
(value, type)
```

value是实际变量值，type是实际变量的类型。一个interface{}类型的变量包含了2个指针，一个指针指向值的类型【对应concrete type】，另外一个指针指向实际的值【对应value】。

例如，创建类型为*os.File的变量，然后将其赋给一个接口变量r：

```
tty, err := os.OpenFile("/dev/tty", os.O_RDWR, 0)

var r io.Reader
r = tty
```

接口变量r的pair中将记录如下信息：(tty, *os.File)，这个pair在接口变量的连续赋值过程中是不变的，将接口变量r赋给另一个接口变量w:

```
var w io.Writer
w = r.(io.Writer)
```

接口变量w的pair与r的pair相同，都是:(tty, *os.File)，即使w是空接口类型，pair也是不变的。

interface及其pair的存在，是Golang中实现反射的前提，理解了pair，就更容易理解反射。反射就是用来检测存储在接口变量内部(值value；类型concrete type) pair对的一种机制。

```
package main

import (
	"fmt"
	"io"
	"os"
)


func main() {
	tty, err := os.OpenFile("/dev/tty", os.O_RDWR, 0)
	if err != nil {
		fmt.Println("open file error", err)
		return
	}

	var r io.Reader
	r = tty

	var w io.Writer
	w = r.(io.Writer)
	w.Write([]byte("HELLO THIS IS A TEST!!!\n"))
}
```

再比如

```
package main

import "fmt"

type Reader interface {
	ReadBook()
}

type Writer interface {
	WriteBook()
}

//具体类型
type Book struct {
}

func (this *Book) ReadBook() {
	fmt.Println("Read a book.")
}

func (this *Book) WriteBook() {
	fmt.Println("Write a book.")
}

func main() {
	b := &Book{}

	var r Reader
	r = b

	r.ReadBook()

	var w Writer
	w = r.(Writer)
	w.WriteBook()
}
```

#### Golang的反射reflect

**reflect的基本功能TypeOf和ValueOf**

既然反射就是用来检测存储在接口变量内部(值value；类型concrete type) pair对的一种机制。那么在Golang的reflect反射包中有什么样的方式可以让我们直接获取到变量内部的信息呢？ 它提供了两种类型（或者说两个方法）让我们可以很容易的访问接口变量内容，分别是reflect.ValueOf() 和 reflect.TypeOf()。

reflect.TypeOf()是获取pair中的type，reflect.ValueOf()获取pair中的value，

```
package main

import (
    "fmt"
    "reflect"
)

func main() {
    var num float64 = 1.2345

    fmt.Println("type: ", reflect.TypeOf(num))
    fmt.Println("value: ", reflect.ValueOf(num))
}

运行结果:
type:  float64
value:  1.2345
```

说明

1. reflect.TypeOf： 直接给到了我们想要的type类型，如float64、int、各种pointer、struct 等等真实的类型
2. reflect.ValueOf：直接给到了我们想要的具体的值，如1.2345这个具体数值，或者类似&{1 "Allen.Wu" 25} 这样的结构体struct的值
3. 也就是说明反射可以将“接口类型变量”转换为“反射类型对象”，反射类型指的是reflect.Type和reflect.Value这两种

**从relfect.Value中获取接口interface的信息**

当执行reflect.ValueOf(interface)之后，就得到了一个类型为”relfect.Value”变量，可以通过它本身的Interface()方法获得接口变量的真实内容，然后可以通过类型判断进行转换，转换为原有真实类型。不过，我们可能是已知原有类型，也有可能是未知原有类型，因此，下面分两种情况进行说明。

已知类型后转换为其对应的类型的做法如下，直接通过Interface方法然后强制转换，如下：

```
realValue := value.Interface().(已知的类型)
```

实例：

```
package main

import (
    "fmt"
    "reflect"
)

func main() {
    var num float64 = 1.2345

    pointer := reflect.ValueOf(&num)
    value := reflect.ValueOf(num)

    // 可以理解为“强制转换”，但是需要注意的时候，转换的时候，如果转换的类型不完全符合，则直接panic
    // Golang 对类型要求非常严格，类型一定要完全符合
    // 如下两个，一个是*float64，一个是float64，如果弄混，则会panic
    convertPointer := pointer.Interface().(*float64)
    convertValue := value.Interface().(float64)

    fmt.Println(convertPointer)
    fmt.Println(convertValue)
}

运行结果：
0xc42000e238
1.2345
```

说明

1.  转换的时候，如果转换的类型不完全符合，则直接panic，类型要求非常严格！ 
2.  转换的时候，要区分是指针还是指 
3.  也就是说反射可以将“反射类型对象”再重新转换为“接口类型变量” 

很多情况下，我们可能并不知道其具体类型，那么这个时候，该如何做呢？需要我们进行遍历探测其Filed来得知，示例如下:

```
package main

import (
    "fmt"
    "reflect"
)

type User struct {
    Id   int
    Name string
    Age  int
}

func (u User) ReflectCallFunc() {
    fmt.Println("Allen.Wu ReflectCallFunc")
}

func main() {

    user := User{1, "Allen.Wu", 25}

    DoFiledAndMethod(user)

}

// 通过接口来获取任意参数，然后一一揭晓
func DoFiledAndMethod(input interface{}) {

    getType := reflect.TypeOf(input)
    fmt.Println("get Type is :", getType.Name())

    getValue := reflect.ValueOf(input)
    fmt.Println("get all Fields is:", getValue)

    // 获取方法字段
    // 1. 先获取interface的reflect.Type，然后通过NumField进行遍历
    // 2. 再通过reflect.Type的Field获取其Field
    // 3. 最后通过Field的Interface()得到对应的value
    for i := 0; i < getType.NumField(); i++ {
        field := getType.Field(i)
        value := getValue.Field(i).Interface()
        fmt.Printf("%s: %v = %v\n", field.Name, field.Type, value)
    }

    // 获取方法
    // 1. 先获取interface的reflect.Type，然后通过.NumMethod进行遍历
    for i := 0; i < getType.NumMethod(); i++ {
        m := getType.Method(i)
        fmt.Printf("%s: %v\n", m.Name, m.Type)
    }
}

运行结果：
get Type is : User
get all Fields is: {1 Allen.Wu 25}
Id: int = 1
Name: string = Allen.Wu
Age: int = 25
ReflectCallFunc: func(main.User)
```

说明

通过运行结果可以得知获取未知类型的interface的具体变量及其类型的步骤为：

1. 先获取interface的reflect.Type，然后通过NumField进行遍历
2. 再通过reflect.Type的Field获取其Field
3. 最后通过Field的Interface()得到对应的value

通过运行结果可以得知获取未知类型的interface的所属方法（函数）的步骤为：

1. 先获取interface的reflect.Type，然后通过NumMethod进行遍历
2. 再分别通过reflect.Type的Method获取对应的真实的方法（函数）
3. 最后对结果取其Name和Type得知具体的方法名
4. 也就是说反射可以将“反射类型对象”再重新转换为“接口类型变量”
5. struct 或者 struct 的嵌套都是一样的判断处理方式

##### 通过reflect.Value设置实际变量的值

reflect.Value是通过reflect.ValueOf(X)获得的，只有当X是指针的时候，才可以通过reflec.Value修改实际变量X的值，即：要修改反射类型的对象就一定要保证其值是“addressable”的。

```
package main

import (
    "fmt"
    "reflect"
)

func main() {

    var num float64 = 1.2345
    fmt.Println("old value of pointer:", num)

    // 通过reflect.ValueOf获取num中的reflect.Value，注意，参数必须是指针才能修改其值
    pointer := reflect.ValueOf(&num)
    newValue := pointer.Elem()

    fmt.Println("type of pointer:", newValue.Type())
    fmt.Println("settability of pointer:", newValue.CanSet())

    // 重新赋值
    newValue.SetFloat(77)
    fmt.Println("new value of pointer:", num)

    ////////////////////
    // 如果reflect.ValueOf的参数不是指针，会如何？
    pointer = reflect.ValueOf(num)
    //newValue = pointer.Elem() // 如果非指针，这里直接panic，“panic: reflect: call of reflect.Value.Elem on float64 Value”
}

运行结果：
old value of pointer: 1.2345
type of pointer: float64
settability of pointer: true
new value of pointer: 77
```

说明

1. 需要传入的参数是* float64这个指针，然后可以通过pointer.Elem()去获取所指向的Value，**注意一定要是指针**。
2. 如果传入的参数不是指针，而是变量，那么 

- - 通过Elem获取原始值对应的对象则直接panic
    - 通过CanSet方法查询是否可以设置返回false

1. newValue.CantSet()表示是否可以重新设置其值，如果输出的是true则可修改，否则不能修改，修改完之后再进行打印发现真的已经修改了。
2. reflect.Value.Elem() 表示获取原始值对应的反射对象，只有原始对象才能修改，当前反射对象是不能修改的
3. 也就是说如果要修改反射类型对象，其值必须是“addressable”【对应的要传入的是指针，同时要通过Elem方法获取原始值对应的反射对象】
4. struct 或者 struct 的嵌套都是一样的判断处理方式

##### 通过reflect.ValueOf来进行方法的调用

这算是一个高级用法了，前面我们只说到对类型、变量的几种反射的用法，包括如何获取其值、其类型、如果重新设置新值。但是在工程应用中，另外一个常用并且属于高级的用法，就是通过reflect来进行方法【函数】的调用。比如我们要做框架工程的时候，需要可以随意扩展方法，或者说用户可以自定义方法，那么我们通过什么手段来扩展让用户能够自定义呢？关键点在于用户的自定义方法是未可知的，因此我们可以通过reflect来搞定

```
package main

import (
    "fmt"
    "reflect"
)

type User struct {
    Id   int
    Name string
    Age  int
}

func (u User) ReflectCallFuncHasArgs(name string, age int) {
    fmt.Println("ReflectCallFuncHasArgs name: ", name, ", age:", age, "and origal User.Name:", u.Name)
}

func (u User) ReflectCallFuncNoArgs() {
    fmt.Println("ReflectCallFuncNoArgs")
}

// 如何通过反射来进行方法的调用？
// 本来可以用u.ReflectCallFuncXXX直接调用的，但是如果要通过反射，那么首先要将方法注册，也就是MethodByName，然后通过反射调动mv.Call

func main() {
    user := User{1, "Allen.Wu", 25}
    
    // 1. 要通过反射来调用起对应的方法，必须要先通过reflect.ValueOf(interface)来获取到reflect.Value，得到“反射类型对象”后才能做下一步处理
    getValue := reflect.ValueOf(user)

    // 一定要指定参数为正确的方法名
    // 2. 先看看带有参数的调用方法
    methodValue := getValue.MethodByName("ReflectCallFuncHasArgs")
    args := []reflect.Value{reflect.ValueOf("wudebao"), reflect.ValueOf(30)}
    methodValue.Call(args)

    // 一定要指定参数为正确的方法名
    // 3. 再看看无参数的调用方法
    methodValue = getValue.MethodByName("ReflectCallFuncNoArgs")
    args = make([]reflect.Value, 0)
    methodValue.Call(args)
}


运行结果：
ReflectCallFuncHasArgs name:  wudebao , age: 30 and origal User.Name: Allen.Wu
ReflectCallFuncNoArgs
```

说明

1. 要通过反射来调用起对应的方法，必须要先通过reflect.ValueOf(interface)来获取到reflect.Value，得到“反射类型对象”后才能做下一步处理
2. reflect.Value.MethodByName这.MethodByName，需要指定准确真实的方法名字，如果错误将直接panic，MethodByName返回一个函数值对应的reflect.Value方法的名字。
3. []reflect.Value，这个是最终需要调用的方法的参数，可以没有或者一个或者多个，根据实际参数来定。
4. reflect.Value的 Call 这个方法，这个方法将最终调用真实的方法，参数务必保持一致，如果reflect.Value'Kind不是一个方法，那么将直接panic。
5. 本来可以用u.ReflectCallFuncXXX直接调用的，但是如果要通过反射，那么首先要将方法注册，也就是MethodByName，然后通过反射调用methodValue.Call

#### Golang的反射reflect性能

Golang的反射很慢，这个和它的API设计有关。在 java 里面，我们一般使用反射都是这样来弄的。

```
Field field = clazz.getField("hello");
field.get(obj1);
field.get(obj2);
```

这个取得的反射对象类型是 java.lang.reflect.Field。它是可以复用的。只要传入不同的obj，就可以取得这个obj上对应的 field。

但是Golang的反射不是这样设计的:

```
type_ := reflect.TypeOf(obj)
field, _ := type_.FieldByName("hello")
```

这里取出来的 field 对象是 reflect.StructField 类型，但是它没有办法用来取得对应对象上的值。如果要取值，得用另外一套对object，而不是type的反射

```
type_ := reflect.ValueOf(obj)
fieldValue := type_.FieldByName("hello")
```

这里取出来的 fieldValue 类型是 reflect.Value，它是一个具体的值，而不是一个可复用的反射对象了，每次反射都需要malloc这个reflect.Value结构体，并且还涉及到GC。

Golang reflect慢主要有两个原因

1. 涉及到内存分配以及后续的GC；
2. reflect实现里面有大量的枚举，也就是for循环，比如类型之类的.

#### 总结

上述详细说明了Golang的反射reflect的各种功能和用法，都附带有相应的示例，相信能够在工程应用中进行相应实践，总结一下就是：

- 反射可以大大提高程序的灵活性，使得interface{}有更大的发挥余地 

- - 反射必须结合interface才玩得转
    - 变量的type要是concrete type的（也就是interface变量）才有反射一说

- 反射可以将“接口类型变量”转换为“反射类型对象” 

- - 反射使用 TypeOf 和 ValueOf 函数从接口中获取目标对象信息

- 反射可以将“反射类型对象”转换为“接口类型变量 

- - reflect.value.Interface().(已知的类型)
    - 遍历reflect.Type的Field获取其Field

- 反射可以修改反射类型对象，但是其值必须是“addressable” 

- - 想要利用反射修改对象状态，前提是 interface.data 是 settable,即 pointer-interface

- 通过反射可以“动态”调用方法
- 因为Golang本身不支持模板，因此在以往需要使用模板的场景下往往就需要使用反射(reflect)来实现





### 闭包

> 闭包函数   每次调用都会增加//重新调用会重置



```go
package main

import "fmt"

// 闭包函数   每次调用都会增加//重新调用会重置
func add() func(int) int {
	var x int
	return func(y int) int {
		x += y
		return x
	}
}

/*
10
20
30
---------------
40
50

*/

func main() {
	var f = add()
	fmt.Println(f(10))
	fmt.Println(f(10))
	fmt.Println(f(10))
	fmt.Println("---------------")
	fmt.Println(f(10))
	fmt.Println(f(10))

	f1 := add()
	fmt.Println(f1(10))
}

```

### 高阶函数

> 在go中，如果函数名首字母是大写，则表示为public，若首字母为小写，则只能在当前包内访问

#### 3.4.1 函数作为参数

```go
func sayHello(name string) {
	fmt.Printf("say,%s", name)
}

func test(name string, f func(string)) {
	f(name)
}
```

#### 3.4.2 函数作为返回值

```go
func add(x, y int) int {
	return x + y
}
func sub(x, y int) int {
	return x - y
}
func cal(operator string) func(int, int) int {
	switch operator {
	case "+":
		return add
	case "-":
		return sub
	default:
		return nil
	}
}
```

#### 3.4.3 匿名函数

```go
func Lamda() int {
	dd := func(a, b int) int {
		if a > b {
			return a - b
		} else {
			return b - a
		}
	}
	return dd(5, 8)
}
```

#### 3.4.4 自己调用自己(递归)

```go
func Run(x uint64) (y uint64) {
	if x > 0 {
		y = x * Run(x-1)
		return y
	}
	return 1

}
```

### 通道channel

> 通道是Go的一种数据类型  用chan定义
> ch <- v     把 v 发送到通道 ch
> v := <-ch   从 ch 接收数据
>        并把值赋给 v
>
> 使用 channel <- 语法 发送 一个新的值到通道中。
> 使用 <-channel 语法从通道中 接收 一个值。

#### 通道基础以及通道缓冲

```go
func Sum(n []int, ch chan int) {
	sum := 0
	for _, v := range n {
		sum += v
	}
	ch <- sum //sum发送到通道c

}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}
	c := make(chan int)
	go Sum(s[:len(s)/2], c)
	go Sum(s[len(s)/2:], c)
	x, y := <-c, <-c
	fmt.Println(x, y, x+y)
	//	通道缓冲
	Ch_free()
}
func Ch_free() {
	ch := make(chan int, 3)
	ch <- 1
	ch <- 2
	ch <- 3
	fmt.Println(<-ch)
	fmt.Println(<-ch)
	fmt.Println(<-ch)
	close(ch)
}
```

> 带缓冲区的通道允许发送端的数据发送和接收端的数据获取处于异步状态，就是说发送端发送的数据可以放在缓冲区里面，可以等待接收端去获取数据，而不是立刻需要接收端去获取数据。
> 不过由于缓冲区的大小是有限的，所以还是必须有接收端来接收数据的，否则缓冲区一满，数据发送端就无法再发送数据了。
> 注意：如果通道不带缓冲，发送方会阻塞直到接收方从通道中接收了值。如果通道带缓冲，发送方则会阻塞直到发送的值被拷贝到缓冲区内；
> 如果缓冲区已满，则意味着需要等待直到某个接收方获取到一个值。接收方在有值可以接收之前会一直阻塞。

#### 通道关闭和遍历

##### 通道关闭

```go
package main

import "fmt"

func main() {
	jobs := make(chan int, 5)
	done := make(chan bool)

	go func() {
		for {
			j, more := <-jobs
			if more {
				fmt.Println("received job", j)
			} else {
				fmt.Println("received all jobs")
				done <- true
				return
			}
		}
	}()

	for j := 1; j <= 3; j++ {
		jobs <- j
		fmt.Println("sent job", j)
	}
//通道关闭
	close(jobs)
	fmt.Println("sent all jobs")

	<-done
}

```



##### 通道遍历

```go
package main

import "fmt"

func main() {
	//先关闭再遍历
	quemit := make(chan string, 2)
	quemit <- "one"
	quemit <- "two"
	close(quemit)
	for s := range quemit {
		fmt.Println(s)
	}
	New_Queue()
}

//range 迭代从 queue 中得到每个值。 因为我们在前面 close 了这个通道，所以，这个迭代会在接收完 2 个值之后结束。

func New_Queue() {
	quemit := make(chan int, 5)
	for i := 0; i < 5; i++ {
		quemit <- i
	}
	close(quemit)

	for s := range quemit {
		fmt.Println(s)
	}
}

```

通道关闭与遍历

```go
package main

import (
	"fmt"
)

func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	// range 函数遍历每个从通道接收到的数据，因为 c 在发送完 10 个
	// 数据之后就关闭了通道，所以这里我们 range 函数在接收到 10 个数据
	// 之后就结束了。如果上面的 c 通道不关闭，那么 range 函数就不
	// 会结束，从而在接收第 11 个数据的时候就阻塞了。
	for i := range c {
		fmt.Println(i)
	}
}

```

#### 超时处理

> Go语言的`select`语句是用来选择某个`channel`上的数据，当多个`channel`都有数据时，
> select会随机选择其中一个`channel`来处理数据。它通常与`goroutine`一起使用，
> 可以实现非常高效的并发处理。`select`语句可以用来监听多个`channel`，
> 并在其中任意一个`channel`有数据可读时立即处理该数据，从而避免了阻塞等待的情况。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	c1 := make(chan string, 1)
	go func() {
		time.Sleep(2 * time.Second)
		c1 <- "msg"
	}()
	select {
	case res := <-c1:
		fmt.Println(res)
	case <-time.After(1 * time.Second):
		fmt.Println("time out1")
	}
	//改为三秒就能接收到
	c2 := make(chan string)
	go func() {
		time.Sleep(2 * time.Second)
		c2 <- "msg"
	}()
	select {
	case res := <-c2:
		fmt.Println(res)
	case <-time.After(3 * time.Second):
		fmt.Println("time out2")
	}
}

```



#### 非阻塞通道操作

```go
package main

import "fmt"

func main() {
	//加个缓冲区，就会输出
	/*
		no message received
		sent message hi
		received message hi
	
	*/
	messages := make(chan string)
	signals := make(chan bool)

	select {
	case msg := <-messages:
		fmt.Println("received message", msg)
	default:
		fmt.Println("no message received")
	}

	msg := "hi"
	select {
	case messages <- msg:
		fmt.Println("sent message", msg)
	default:
		fmt.Println("no message sent")
	}

	select {
	case msg := <-messages:
		fmt.Println("received message", msg)
	case sig := <-signals:
		fmt.Println("received signal", sig)
	default:
		fmt.Println("no activity")
	}
}

```

#### 通道同步

> 其中go xxxx() 请看[go并发](#391-并发)

```go
package main

import (
    "fmt"
    "time"
)

// worker函数向标准输出打印"working..."，然后休眠1秒钟并调用Adds函数进行迭代输出
// 最后再输出"done"，最后将true存入通道done中
func worker(done chan bool) {
    fmt.Print("working...")
    time.Sleep(time.Second)
    go Adds() // 使用goroutine调用Adds函数，使其并发执行
    time.Sleep(time.Second)
    fmt.Println("done")

    done <- true // 向通道done中存入true，表示worker函数执行完毕
}

// Adds函数用于迭代输出数字1~9
func Adds() {
    for i := 0; i < 10; i++ {
        fmt.Println(i)
    }
}

// main函数创建缓冲大小为1的通道done，
// 然后使用go关键字启动一个协程调用worker函数，并将done通道作为参数传递给worker，
// 最后主线程阻塞并等待done通道中的数据
func main() {
    done := make(chan bool, 1) // 使用make函数创建一个缓冲大小为1的通道done
    go worker(done) // 启动一个goroutine，使用chan通道与主线程通信
    time.Sleep(time.Second)

    <-done // 主线程阻塞并等待done通道中的数据
}

```

#### 通道选择器

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	c1 := make(chan string)
	c2 := make(chan string)
	go func() {
		time.Sleep(1 * time.Second)
		c1 <- "one"
	}()
	go func() {
		time.Sleep(2 * time.Second)
		c2 <- "two"
	}()
	for i := 0; i < 2; i++ {
		select {
		case msg1 := <-c1:
			fmt.Println(msg1)
		case msg2 := <-c2:
			fmt.Println(msg2)
		}
	}
}
```

> 各个通道将在一定时间后接收一个值， 通过这种方式来模拟并行的协程执行（例如，RPC 操作）时造成的阻塞（耗时）
> 注意，程序总共仅运行了两秒左右。因为 1 秒 和 2 秒的 Sleeps 是并发执行的，





### 定时器

> 我们经常需要在未来的某个时间点运行 Go 代码，或者每隔一定时间重复运行代码。 Go 内置的 定时器 和 打点器 特性让这些变得很简单。 我们会先学习定时器，然后再学习[打点器](#37-打点器)。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// 创建一个2秒的定时器
	timer := time.NewTimer(2 * time.Second)

	// 等待定时器的到期
	<-timer.C
	fmt.Println("Timer 1 fired")

	// 创建一个1秒的定时器，并启动一个goroutine来等待该定时器到期
	timer2 := time.NewTimer(time.Second)
	go func() {
		<-timer2.C
		fmt.Println("Timer 2 fired")
	}()

	// 若定时器在到期前被停止，返回true，否则false
	if timer2.Stop() {
		fmt.Println("timer 2 Stop")
	}

	// 主线程休眠2秒，使得上面的goroutine有时间输出信息
	time.Sleep(2 * time.Second)
}

```



### 打点器 

> **定时器** 是当你想要在未来某一刻执行一次时使用的 -
> **打点器** 则是为你想要以固定的时间间隔重复执行而准备的。 这里是一个打点器的例子，它将定时的执行，直到我们将它停止。

```go
func main() {
	// 创建一个每隔500毫秒触发一次的定时器
	ticker := time.NewTicker(500 * time.Millisecond)

	// 创建一个缓存大小为1的通道done
	done := make(chan bool)

	// 启动一个goroutine，调用select语句阻塞等待通道done被关闭或者定时器ticker触发
	go func() {
		for {
			select {
			case <-done: // 通道done关闭时结束goroutine
				return
			case t := <-ticker.C: // 定时器ticker触发时执行如下代码
				fmt.Println("Ticker is", t)
			}
		}
	}()

	// 主线程休眠1600毫秒，等待定时器触发
	time.Sleep(1600 * time.Millisecond)

	// 停止定时器ticker，并向通道done写入true，通知goroutine结束
	ticker.Stop()
	done <- true

	// 输出定时器已经停止
	fmt.Println("ticker is stop")
}
```

### 工作池

```go
package main

import (
	"fmt"
	"time"
)

func Worker(id int, jobs <-chan int, results chan<- int) {
	// 从jobs通道中读取作业，执行并输出信息
	for j := range jobs {
		fmt.Println("worker", id, "started  job", j)
		time.Sleep(time.Second)
		fmt.Println("worker", id, "finished job", j)
		results <- j * 2 // 将结果存入results通道中
	}
}

func main() {
	const Number = 5
	jobs := make(chan int, Number) // 创建由Number个缓存的jobs通道
	results := make(chan int, Number) // 创建由Number个缓存的results通道

	// 创建3个goroutine，并将它们交给Worker函数处理
	for w := 1; w <= 3; w++ {
		go Worker(w, jobs, results)
	}

	// 向jobs通道里添加共Number个作业
	for j := 1; j <= Number; j++ {
		jobs <- j
	}

	close(jobs) // 关闭jobs通道

	// 从results通道中读取之前goroutine处理结果的数量，并丢弃
	for a := 1; a <= Number; a++ {
		<-results
	}
}
```

### 协程

内核线程就是一般的线程叫thread，用户线程叫协程co-routine，go里面的协程叫做**goroutine**。

N个内核线程thread可以通过协程调度器使用M个协程goroutine。

与传统的系统级线程和进程相比，协程最大的优势在于“轻量级”。可以轻松创建上万个而不会导致系统资源衰竭。而线程和进程通常很难超过1万个。这也是协程别称“轻量级线程”的原因。

一个线程中可以有任意多个协程，但某一时刻只能有一个协程在运行，**多个协程分享该线程分配到的计算机资源**。

在协程中，调用一个任务就像调用一个函数一样，消耗的系统资源最少！但能达到进程、线程并发相同的效果。

一个进程可能几GB，一个线程可能几MB，go的协程是几KB。在一次并发任务中，进程、线程、协程均可以实现。从系统资源消耗的角度出发来看，进程相当多，线程次之，协程最少。

#### GO并发

> Go语言为简单地，高效地实现并发编程提供了原生态支持。在Go语言中实现并发编程最常用的方式就是使用`goroutine`（协程）。`Goroutine` 是一种轻量级线程，可以由Go的运行时环境调度执行。Go语言还提供了 `channel` 用来实现` goroutine` 之间的通信，这也是Go语言的另一个特色，通过`channel`可以方便的完成`goroutine`之间的协作和数据同步

##### goroutine

使用go关键字后面跟函数进行启动协程

```go
package main

import (
	"fmt"
	"time"
)

// 创建一个名为SayHello的函数，接受一个字符串参数
func SayHello(s string) {
	// 循环输出字符串
	for i := 0; i < 7; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

// 主函数
func main() {
	// 使用go关键字启动一个协程，调用SayHello函数输出"hello"
	go SayHello("hello")

	// 在主进程中调用SayHello函数输出"WORD"
	SayHello("WORD")
}

//使用runtime.Goexit()退出协程
```

Go 在语言级别支持协程，叫goroutine。Go 语言标准库提供的所有系统调用操作（包括所有同步IO操作），都会出让CPU给其他goroutine。这让轻量级线程的切换管理不依赖于系统的线程和进程，也不需要依赖于CPU的核心数量。

Go语言为并发编程而内置的上层API基于顺序通信进程模型CSP(communicating sequential processes)。这就意味着显式锁都是可以避免的，因为Go通过相对安全的通道发送和接受数据以实现同步，这大大地简化了并发程序的编写。



Go语言中的并发程序主要使用两种手段来实现。goroutine和channel。

**创建Goroutine**

只需在函数调⽤语句前添加 **go** 关键字，就可创建并发执⾏单元。开发⼈员无需了解任何执⾏细节，调度器会自动将其安排到合适的系统线程上执行。

在并发编程中，我们通常想将一个过程切分成几块，然后让每个goroutine各自负责一块工作，当一个程序启动时，主函数在一个单独的goroutine中运行，我们叫它main goroutine。新的goroutine会用go语句来创建。而go语言的并发设计，让我们很轻松就可以达成这一目的。示例代码：

```go
package main
 
import (
    "fmt"
    "time"
)
 
func newTask() {
    i := 0
    for {
        i++
        fmt.Printf("new goroutine: i = %d\n", i)
        time.Sleep(1*time.Second) //延时1s
    }
}
 
func main() {
    //创建一个 goroutine，启动另外一个任务
    go newTask()
    i := 0
    //main goroutine 循环打印
    for {
        i++
        fmt.Printf("main goroutine: i = %d\n", i)
        time.Sleep(1 * time.Second) //延时1s
    }
}
```

**Goroutine特性**

主goroutine退出后，其它的工作goroutine也会自动退出。示例代码：

```
package main
 
import (
"fmt"
"time"
)
 
func newTask() {
    i := 0
    for {
        i++
        fmt.Printf("new goroutine: i = %d\n", i)
        time.Sleep(1 * time.Second) //延时1s
    }
}
 
func main() {
    //创建一个 goroutine，启动另外一个任务
    go newTask()
 
    fmt.Println("main goroutine exit")
}
```

**Goexit函数**

调用 runtime.Goexit() 将立即终止当前 goroutine 执⾏，调度器确保所有已注册 defer 延迟调用被执行。示例代码：

```
package main
 
import (
"fmt"
"runtime"
)
 
func main() {
    go func() {
        defer fmt.Println("A.defer")
 
        func() {
            defer fmt.Println("B.defer")
            runtime.Goexit() // 终止当前 goroutine, import "runtime"
            fmt.Println("B") // 不会执行
        }()
 
        fmt.Println("A") // 不会执行
    }()       //不要忘记()
 
    //死循环，目的不让主goroutine结束
    for {
    }
}
```





##### channel

channel是Go语言中的一个**核心类型**，可以把它看成管道。并发核心单元通过它就可以发送或者接收数据进行通讯，这在一定程度上又进一步降低了编程的难度。

channel是一个数据类型，主要用来解决go程的同步问题以及go程之间数据共享（数据传递）的问题。两个goroutine之前交流需要通过channel来进行。

goroutine运行在相同的地址空间，因此访问共享内存必须做好同步。goroutine 奉行通过通信来共享内存，而不是共享内存来通信。

引⽤类型 channel可用于多个 goroutine 通讯。其内部实现了同步，确保并发安全。

分为带缓冲channel和不带缓冲channel



**定义channel变量**

和map类似，channel也一个对应make创建的底层数据结构的**引用**。

当我们复制一个channel或用于函数参数传递时，我们只是拷贝了一个channel引用，因此调用者和被调用者将引用同一个channel对象。和其它的引用类型一样，channel的零值也是nil。

定义一个channel时，也需要定义发送到channel的值的类型。channel可以使用内置的make()函数来创建：

**chan**是创建channel所需使用的关键字。Type 代表指定channel收发数据的类型。

```
make(chan Type)  //等价于make(chan Type, 0)
make(chan Type, capacity)
```

当我们复制一个channel或用于函数参数传递时，我们只是拷贝了一个channel引用，因此调用者和被调用者将引用同一个channel对象。和其它的引用类型一样，channel的零值也是nil。

当 参数capacity= 0 时，channel 是无缓冲阻塞读写的；当capacity > 0 时，channel 有缓冲、是非阻塞的，直到写满 capacity个元素才阻塞写入。

channel非常像生活中的管道，一边可以存放东西，另一边可以取出东西。channel通过操作符 <- 来接收和发送数据，发送和接收数据语法：

```
channel <- value      //发送value到channel
<-channel             //接收并将其丢弃
x := <-channel        //从channel中接收数据，并赋值给x
x, ok := <-channel    //功能同上，同时检查通道是否已关闭或者是否为空
```

默认情况下，channel接收和发送数据都是阻塞的，除非另一端已经准备好，这样就使得goroutine同步变的更加的简单，而**不需要显式的lock。**

示例代码：

```
package main
 
import (
    "fmt"
)
 
func main() {
    c := make(chan int)
 
    go func() {
        defer fmt.Println("子go程结束")
 
        fmt.Println("子go程正在运行……")
 
        c <- 666 //666发送到c
    }()
 
    num := <-c //从c中接收数据，并赋值给num
 
    fmt.Println("num = ", num)
    fmt.Println("main go程结束")
}
```

**无缓冲的channel**

无缓冲的通道（unbuffered channel）是指在接收前没有能力保存任何数据值的通道。

这种类型的通道要求发送goroutine和接收goroutine同时准备好，才能完成发送和接收操作。否则，通道会导致先执行发送或接收操作的 goroutine 阻塞等待。

这种对通道进行发送和接收的交互行为本身就是同步的。其中任意一个操作都无法离开另一个操作单独存在。

**阻塞：**由于某种原因数据没有到达，当前go程（线程）持续处于等待状态，直到条件满足，才解除阻塞。

**同步：**在两个或多个go程（线程）间，保持数据内容一致性的机制。

下面步骤展示两个 goroutine 如何利用无缓冲的通道来共享一个值：

-  在第 1 步，两个 goroutine 都到达通道，但哪个都没有开始执行发送或者接收。 
-  在第 2 步，左侧的 goroutine 将它的手伸进了通道，这模拟了向通道发送数据的行为。这时，这个 goroutine 会在通道中被锁住，直到交换完成。 
-  在第 3 步，右侧的 goroutine 将它的手放入通道，这模拟了从通道里接收数据。这个 goroutine 一样也会在通道中被锁住，直到交换完成。 
-  在第 4 步和第 5 步，进行交换，并最终，在第 6 步，两个 goroutine 都将它们的手从通道里拿出来，这模拟了被锁住的 goroutine 得到释放。两个 goroutine 现在都可以去做其他事情了。 

**无缓冲的channel创建格式：**

```
make(chan Type)   //等价于make(chan Type, 0)
如果没有指定缓冲区容量，那么该通道就是同步的，因此会阻塞到发送者准备好发送和接收者准备好接收。
```

示例代码：

```
package main
 
import (
    "fmt"
    "time"
)
 
func main() {
    c := make(chan int, 0) //创建无缓冲的通道 c 
 
    //内置函数 len 返回未被读取的缓冲元素数量，cap 返回缓冲区大小
    fmt.Printf("len(c)=%d, cap(c)=%d\n", len(c), cap(c))
 
    go func() {
        defer fmt.Println("子go程结束")
 
        for i := 0; i < 3; i++ {
            c <- i
            fmt.Printf("子go程正在运行[%d]: len(c)=%d, cap(c)=%d\n", i, len(c), cap(c))
        }
    }()
 
    time.Sleep(2 * time.Second) //延时2s
 
    for i := 0; i < 3; i++ {
        num := <-c //从c中接收数据，并赋值给num
        fmt.Println("num = ", num)
    }
 
    fmt.Println("main进程结束")
}
```

**有缓冲的channel**

有缓冲的通道（buffered channel）是一种在被接收前能存储一个或者多个数据值的通道。

这种类型的通道并不强制要求 goroutine 之间必须同时完成发送和接收。通道会阻塞发送和接收动作的条件也不同。

只有通道中没有要接收的值时，接收动作才会阻塞。

只有通道没有可用缓冲区容纳被发送的值时，发送动作才会阻塞。

这导致有缓冲的通道和无缓冲的通道之间的一个很大的不同：无缓冲的通道保证进行发送和接收的 goroutine 会在同一时间进行数据交换；有缓冲的通道没有这种保证。

下面步骤展示两个 goroutine 如何利用缓冲的通道来共享一个值：

- 在第 1 步，右侧的 goroutine 正在从通道接收一个值。 
- 在第 2 步，右侧的这个 goroutine独立完成了接收值的动作，而左侧的 goroutine 正在发送一个新值到通道里。 
- 在第 3 步，左侧的goroutine 还在向通道发送新值，而右侧的 goroutine 正在从通道接收另外一个值。这个步骤里的两个操作既不是同步的，也不会互相阻塞。 
- 最后，在第 4 步，所有的发送和接收都完成，而通道里还有几个值，也有一些空间可以存更多的值。 

**有缓冲的channel创建格式**：

```
make(chan Type, capacity)
```

如果给定了一个缓冲区容量，通道就是异步的。只要缓冲区有未使用空间用于发送数据，或还包含可以接收的数据，那么其通信就会无阻塞地进行。

借助函数 **len(ch)** 求取缓冲区中剩余元素个数， **cap(ch)** 求取缓冲区元素容量大小。

示例代码：

```
func main() {
    c := make(chan int, 3) //带缓冲的通道
 
    //内置函数 len 返回未被读取的缓冲元素数量， cap 返回缓冲区大小
    fmt.Printf("len(c)=%d, cap(c)=%d\n", len(c), cap(c))
 
    go func() {
        defer fmt.Println("子go程结束")
 
        for i := 0; i < 3; i++ {
            c <- i
            fmt.Printf("子go程正在运行[%d]: len(c)=%d, cap(c)=%d\n", i, len(c), cap(c))
        }
    }()
 
    time.Sleep(2 * time.Second) //延时2s
    for i := 0; i < 3; i++ {
        num := <-c //从c中接收数据，并赋值给num
        fmt.Println("num = ", num)
    }
    fmt.Println("main进程结束")
}
```

**关闭channel**

如果发送者知道，没有更多的值需要发送到channel的话，那么让接收者也能及时知道没有多余的值可接收将是有用的，因为接收者可以停止不必要的接收等待。这可以通过内置的close函数来关闭channel实现。

示例代码：

```
package main
 
import (
    "fmt"
)
 
func main() {
    c := make(chan int)
 
    go func() {
        for i := 0; i < 5; i++ {
            c <- i
        }
        close(c)
    }()
 
    for {
        //ok为true说明channel没有关闭，为false说明管道已经关闭
        if data, ok := <-c; ok {
            fmt.Println(data)
        } else {
            break
        }
    }
 
    fmt.Println("Finished")
}
```

**注意：**

channel不像文件一样需要经常去关闭，只有当你确实没有任何发送数据了，或者你想显式的结束range循环之类的，才去关闭channel；

关闭channel后，无法向channel 再发送数据(引发 panic 错误后导致接收立即返回零值)；

关闭channel后，可以继续从channel接收数据；

对于nil channel，无论收发都会被阻塞。

可以使用 **range** 来迭代不断操作channel：

```
package main
 
import (
    "fmt"
)
 
func main() {
    c := make(chan int)
 
    go func() {
        for i := 0; i < 5; i++ {
            c <- i
        }
        close(c)
    }()
 
    for data := range c {
        fmt.Println(data)
    }
    fmt.Println("Finished")
}
```

**单向channel及应用**

默认情况下，通道channel是双向的，也就是，既可以往里面发送数据也可以同里面接收数据。

但是，我们经常见一个通道作为参数进行传递而只希望对方是单向使用的，要么只让它发送数据，要么只让它接收数据，这时候我们可以指定通道的方向。

单向channel变量的声明非常简单，如下：

```
var ch1 chan int       // ch1是一个正常的channel，是双向的
var ch2 chan<- float64 // ch2是单向channel，只用于写float64数据
var ch3 <-chan int     // ch3是单向channel，只用于读int数据
```

chan<- 表示数据进入管道，要把数据写进管道，对于调用者就是输出。

<-chan 表示数据从管道出来，对于调用者就是得到管道的数据，当然就是输入。

可以将 channel 隐式转换为单向队列，只收或只发，不能将单向 channel 转换为普通 channel：

```
    c := make(chan int, 3)
    var send chan<- int = c // send-only
    var recv <-chan int = c // receive-only
    send <- 1
    //<-send //invalid operation: <-send (receive from send-only type chan<- int)
    <-recv
    //recv <- 2 //invalid operation: recv <- 2 (send to receive-only type <-chan int)
 
    //不能将单向 channel 转换为普通 channel
    d1 := (chan int)(send) //cannot convert send (type chan<- int) to type chan int
    d2 := (chan int)(recv) //cannot convert recv (type <-chan int) to type chan int
```

示例代码：

```
//   chan<- //只写
func counter(out chan<- int) {
    defer close(out)
    for i := 0; i < 5; i++ {
        out <- i //如果对方不读 会阻塞
    }
}
 
//   <-chan //只读
func printer(in <-chan int) {
    for num := range in {
        fmt.Println(num)
    }
}
 
func main() {
    c := make(chan int) //   chan   //读写
 
    go counter(c) //生产者
    printer(c)    //消费者
 
    fmt.Println("done")
}
```

##### select

Go里面提供了一个关键字select，通过select可以监听channel上的数据流动。

有时候我们希望能够借助channel发送或接收数据，并避免因为发送或者接收导致的阻塞，尤其是当channel没有准备好写或者读时。select语句就可以实现这样的功能。

select的用法与switch语言非常类似，由select开始一个新的选择块，每个选择条件由case语句来描述。

与switch语句相比，select有比较多的限制，其中最大的一条限制就是每个**case语句里必须是一个IO操作**，大致的结构如下：

```
    select {
    case <- chan1:
        // 如果chan1成功读到数据，则进行该case处理语句
    case chan2 <- 1:
        // 如果成功向chan2写入数据，则进行该case处理语句
    default:
        // 如果上面都没有成功，则进入default处理流程
    }
```

在一个select语句中，Go语言会按顺序从头至尾评估每一个发送和接收的语句。

如果其中的任意一语句可以继续执行(即没有被阻塞)，那么就从那些可以执行的语句中任意选择一条来使用。

如果没有任意一条语句可以执行(即所有的通道都被阻塞)，那么有两种可能的情况：

l 如果给出了default语句，那么就会执行default语句，同时程序的执行会从select语句后的语句中恢复。

l 如果没有default语句，那么select语句将被阻塞，直到至少有一个通信可以进行下去。

示例代码：

```
package main
 
import (
    "fmt"
)
 
func fibonacci(c, quit chan int) {
    x, y := 1, 1
    for {
        select {
        case c <- x:
            x, y = y, x+y
        case <-quit:
            fmt.Println("quit")
            return
        }
    }
}
 
func main() {
    c := make(chan int)
    quit := make(chan int)
 
    go func() {
        for i := 0; i < 6; i++ {
            fmt.Println(<-c)
        }
        quit <- 0
    }()
 
    fibonacci(c, quit)
}
运行结果：
1
1
2
3
5
8
quit
```



#### 协程池

```go
package main

import (
	"fmt"
	"strconv"
	"time"
)

// 创建一个Procshop结构体，代表商店向顾客输送的产品
type Procshop struct {
	Name string
}

// 定义一个生产函数，接收一个chan，每隔一秒向其中写入一个Procshop类型的数据
func Shop(procshop chan<- Procshop) {
	for {
		chanshop := Procshop{
			Name: "产品" + strconv.Itoa(time.Now().Second()), // 生成唯一的产品名称
		}
		procshop <- chanshop // 将产品放入通道中
		fmt.Println("生产者生产了", chanshop) // 输出产品信息
		time.Sleep(1 * time.Second) // 睡眠1秒
	}
}

// 定义一个消费函数，接收两个chan，从第一个通道中读取产品并输出，在第二个通道中写入数据判断是否已经消费完毕
func Vist(procshop <-chan Procshop, vis chan<- int) {
	for {
		pordacket := <-procshop // 从通道中读取数据
		fmt.Println("消费者吃掉了: ", pordacket) // 输出产品信息
		vis <- 1 // 将1存储到vis通道中，代表已经消费了1个产品
	}
}

// 主函数
func main() {
	// 创建两个chan，分别用于存储产品和消费数量
	procshop := make(chan Procshop, 5)
	vister := make(chan int, 5)

	// 启动生产和消费两个协程，进行并发执行
	go Shop(procshop)
	go Vist(procshop, vister)

	// 循环读取vis通道，直到已经消费完5个产品
	for i := 0; i < 5; i++ {
		<-vister
	}

	// 输出主函数结束
	fmt.Println("main over")
}
```

##### 练习(小项目)

```go
package main

import (
	"fmt"
	"strconv"
	"time"
)

//书籍管理系统  有学生从书店借书出去

type BookStore struct {
	BookName string
}

func BookShop(BookLlist chan<- BookStore) {
	for {
		br := BookStore{BookName: "书籍编号" + strconv.Itoa(time.Now().Second())}
		BookLlist <- br
		fmt.Println("书店有书", br)
		time.Sleep(1 * time.Second)
	}

}

func VistShop(BookList <-chan BookStore, VistList chan<- int) {
	for {
		vitsd := <-BookList
		fmt.Println("借了", vitsd)
		VistList <- 1
	}
}

func main() {
	BookList := make(chan BookStore)
	VistList := make(chan int)
	go BookShop(BookList)
	go VistShop(BookList, VistList)
	for i := 0; i < 20; i++ {
		<-VistList
	}
	fmt.Println("书已借完")
}

```

#### 等待多个协程完成

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func Worker(id int) {
	fmt.Printf("Worker %d starting\n", id)
	time.Sleep(time.Second)
	fmt.Printf("Worker %d done\n", id)
}

func main() {
	var wg sync.WaitGroup
	for i := 1; i <= 5; i++ {
		wg.Add(1)
		i := i
		go func() {
			defer wg.Done()
			Worker(i)
		}()
	}
	wg.Wait()
}
```



> 这段代码主要实现了并发执行多个Worker的过程。
> 首先，Worker函数模拟了一些需要执行的工作，例如在实际应用中可能是从数据库中获取数据、执行一些计算，或是与外部系统交互等等。
> main函数通过调用sync.WaitGroup来等待所有启动的Worker完成后才能结束整个程序。
> WaitGroup用于协调多个goroutine的执行，里面包含了一个计数器，计数器的值可以随时增加或减少。
> 在for循环中，main函数启动了5个Worker，每个Worker都在goroutine中执行。
> 这个for循环在启动每个Worker之前会先对WaitGroup进行+1的操作，表示有一个Worker进入了工作状态，而每个Worker执行完成之后会调用WaitGroup的Done方法将计数器减1，
> 表示有一个Worker已经完成工作，该方法通常用于必须等待的操作（如等待所有子goroutine完成执行）的末尾。
> 值得注意的是，在启动每个Worker的goroutine中，使用了闭包技术将循环索引i的值赋给了局部变量，避免了在Worker函数调用时可能出现的变量共享问题。
> 这个技巧是在并发编程中经常使用的，可以避免一些潜在的问题出现。

#### 速率限制

```go
package main

import (
	"fmt"
	"time"
)

//时间转换为上海时间

func change_timer(n time.Time) {
	loc, err := time.LoadLocation("Asia/Shanghai")
	if err != nil {
		return
	}

	now := n.In(loc)
	fmt.Println(now)
}

func main() {
	// 第一部分，使用time.Tick来限制操作速率
	requests := make(chan int, 5)
	for i := 1; i <= 5; i++ {
		requests <- i
	}
	close(requests)
	limiter := time.Tick(200 * time.Millisecond) // 定义一个每200ms发送一个值的通道
	for req := range requests {
		<-limiter // 等待通道的值，以控制操作速率
		fmt.Println("requests: ", req, time.Now())
	}

	// 第二部分，使用一个带缓冲通道来模拟服务总线，并同时限制请求速率
	bus := make(chan time.Time, 3) // 定义一个带缓冲的通道，可以缓存3个时间戳
	for i := 0; i < 3; i++ {
		bus <- time.Now() // 初始化通道
	}
	go func() { // 创建一个新的goroutine，每200ms往bus通道中写入当前时间戳
		for t := range time.Tick(200 * time.Millisecond) {
			bus <- t
		}
	}()
	busr := make(chan int, 5) // 定义一个带缓冲的通道，可以缓存5个请求
	for i := 1; i <= 5; i++ {
		busr <- i // 将1~5的数字写入通道
	}
	close(busr)             // 关闭通道
	for req := range busr { // 循环读取busr通道中的请求
		<-bus // 等待总线的时间戳信号，控制操作速率
		fmt.Println("request", req, time.Now())
	}
}

/*
首先，这段代码提供了两种速率限制的方式。第一种方式基于time包提供的time.Tick函数和通道的机制来控制请求的频率。
具体来说，代码中我们创建了一个缓冲大小为5的带缓冲通道requests，并使用for循环向其中放入1到4的数字，最后关闭通道。
接着，我们利用time.Tick函数创建了一个每200毫秒发送一个值的通道limiter，这个通道的作用是控制操作的速率，从而实现速率限制。
在主循环中，我们从requests通道中读取请求，然后在每次执行请求之前，使用<-limiter读取通道中的值，从而限制请求的速率。
在输出请求后，我们继续循环，每次等待limiter通道的下一个值。这种方式将数字输出的频率限制为每200毫秒一次。


第二种方式是基于通道的机制模拟服务总线，并利用通道来实现速率限制。
具体来说，代码中定义了一个带缓冲大小为3的通道bus，用于存储时间戳。然后创建了一个for循环向通道中写入3个当前的时间戳。
接着，通过使用匿名goroutine实现了每200ms向bus通道中写入当前时间戳的过程，以控制每200毫秒执行一次请求。
goroutine中的代码使用go func函数来定义一个新的匿名函数，然后在其中对time包提供的Tick函数进行for循环，每200ms向bus通道中写入一个时间戳。
在循环中，我们使用range语句读取了time.Tick函数产生的时间信号，将其写入bus通道。
在写入3个时间戳之后，busr通道中开始写入请求。具体来说，我们创建了另一个带缓冲大小为5的通道busr，并使用for循环向其中写入数字1到5。然后，我们遍历busr通道的请求编号，
并使用<-bus等待bus通道的下一个时间戳，从而控制请求的速率。每当读取到一个时间戳时，我们都会输出一条消息，说明当前的请求是第几个和对应的时间戳。

*/

```

#### 原子计数器

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

// 全局计数器，初始值为0
var n uint64

// 使用WaitGroup等待所有协程完成工作
func Main_wait() {
	var wg sync.WaitGroup

	// 启动50个协程
	for i := 0; i < 50; i++ {
		wg.Add(1)

		// 每个协程将计数器递增1000次
		go func() {
			for i := 0; i < 1000; i++ {
				atomic.AddUint64(&n, 1) //原子操作，将计数器加1
			}
			wg.Done()
		}()
	}

	wg.Wait()
	fmt.Println("ops: ", n)
}

// 主函数
func main() {
	// 启动10个协程，使用WaitGroup等待所有协程完成任务
	for i := 0; i < 10; i++ {
		go Wait_Main(i)
		wp.Add(1)
	}
	wp.Wait() // 等待所有协程执行完成
	fmt.Println("end........")
}

// 定义Wait_Main函数，接收一个参数i，模拟协程中执行的任务
var wp sync.WaitGroup

func Wait_Main(i int) {
	defer wp.Done()
	fmt.Printf("i:%v\n", i)
}
```

#### 并发编程之runtime包

##### 实例1

```go
package main

import (
	"fmt"
	"runtime"
)

func show(s string) {
	for i := 0; i < 5; i++ {
		fmt.Println(s)
	}
}

func main() {
	//runtime.Gosched()
	//让出cpu时间，重新安排任务
	go show("java")
	//主协程
	for i := 0; i < 5; i++ {
		//当要执行主协程时，会跳过，让前面的先执行完
		runtime.Gosched()
		fmt.Println("Golang")
	}
}

```

##### 实例2

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func shows() {
	for i := 0; i < 15; i++ {
		if i >= 5 {
			runtime.Goexit()
		}
		fmt.Println(i)
	}
}

func main() {
	//runtime.Goexit()退出协程
	go shows()
	time.Sleep(time.Second)
}

```

##### 实例3

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

// 定义函数a，输出0-9的数字
func a() {
	for i := 0; i < 10; i++ {
		fmt.Println("a: ", i)
	}
}

// 定义函数b，输出0-9的数字
func b() {
	for i := 0; i < 10; i++ {
		fmt.Println("b: ", i)
	}
}

// 主函数
func main() {
	// 获取cpu核心数量并输出
	fmt.Printf("Cpu Number :%v\n", runtime.NumCPU())

	// 设置最大的可同时使用的cpu核心数为8
	runtime.GOMAXPROCS(8)

	// 启动两个goroutine
	go a()
	go b()

	// 主函数休眠1秒，等待两个goroutine执行完成
	time.Sleep(time.Second)
}
```

### 互斥锁实现同步

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// 定义全局变量i，并设置初值为10000
var i = 10000

// 定义WaitGroup用于等待所有goroutine执行完成
var wg sync.WaitGroup

// 定义互斥锁lk，保证i的操作过程是线程安全的
var lk sync.Mutex

// 定义add函数，将i加1
func add() {
	defer wg.Done() // 当前goroutine执行结束，WaitGroup计数器减1

	// 对共享变量i操作之前，先进行加锁操作
	lk.Lock()
	i += 1 // 将i加1
	time.Sleep(time.Millisecond * 10) // 休眠10毫秒，模拟耗时操作
	lk.Unlock() // 对共享变量i的操作完成，解锁
}

// 定义sub函数，将i减1
func sub() {
	defer wg.Done() // 当前goroutine执行结束，WaitGroup计数器减1

	// 对共享变量i操作之前，先进行加锁操作
	lk.Lock()
	i -= 1 // 将i减1
	time.Sleep(time.Millisecond * 2) // 休眠2毫秒，模拟耗时操作
	lk.Unlock() // 对共享变量i的操作完成，解锁
}

func main() {
	// 循环启动100次加1和减1操作的goroutine
	for x := 0; x < 100; x++ {
		wg.Add(1)
		go add()
		wg.Add(1)
		go sub()
	}

	// 等待所有goroutine执行完成
	wg.Wait()

	// 输出最终结果
	fmt.Println("end.... i: ", i)
}

```





### Go Modules和代理

> Go Modules是Go语言依赖管理解决方案。
>
> Go.mod是Golang1.11版本新引入的官方包管理工具用于解决之前没有地方记录依赖包具体版本的问题，方便依赖包的管理。
>
> Go.mod其实就是一个Modules，Modules是相关Go包的集合，是源代码交换和版本控制的单元。
>
> 一个源代码目录甚至是空目录都可以作为Modules，只要其中包含有go.mod文件
>
> Go Modoules的目的之一就是淘汰GOPATH，在使用 GOPATH 模式下，我们需要将应用代码存放在固定的`$GOPATH/src`目录下，并且如果执行`go get`来拉取外部依赖会自动下载并安装到`$GOPATH`目录下。
>
> 用Go Modules的方式创建一个项目, 建议为了与GOPATH分开,不要将项目创建在`GOPATH/src`下.

#### mod文件字段内容

go.mod是启用了Go moduels的项目所必需的最重要的文件，它描述了当前项目（当前模块）的元信息，每一行都以一个动词开头。目前有以下5个动词

> 1. *module*：用于定义当前项目的模块路径
> 2. *go*：用于设置预期的Go版本。
> 3. *requir*e：用于设置一个特定的模块版本。
> 4. *exclud*e：用于从使用中排除一个特定的模块版本。
> 5. *replac*e：用于将一个模块版本替换为另外一个模块版本。

#### 生成go.mod

```bash
# 打开Go modules

go env -w GO111MODULE=on

# 设置GOPROXY 镜像源 用国内的

go env -w GOPROXY=https://goproxy.cn,direct
```

在go文件夹创建一个`hello.go`的文件。文件内容如下：

```go
package main

import "fmt"

func main(){
    fmt.Println("Hello World!")
}
```

在项目根目录生成go.mod文件

```bash
$ cd 文件路径
$ go mod init hello(模板名称)
```

#### Go modules常用命令

| 命令               | 作用                             |
| ------------------ | -------------------------------- |
| go mod init        | 生成 go.mod 文件                 |
| go mod download    | 下载 go.mod 文件中指明的所有依赖 |
| go mod tidy        | 整理现有的依赖                   |
| go mod graph       | 查看现有的依赖结构               |
| go mod edit        | 编辑 go.mod 文件                 |
| go mod vendor      | 导出项目所有的依赖到vendor目录   |
| go mod verify      | 校验一个模块是否被篡改过         |
| go mod why         | 查看为什么需要依赖某模块         |
| go get             | 拉取最新版本                     |
| go get 地址+版本号 | 拉取指定版本                     |
| go get -u          | 更新所有依赖                     |



#### go mod环境变量

可以通过 `go env` 命令来进行查看

go env -w GO111MODULE=on	//开启gomod

**GO111MODULE**

Go语言提供了 `GO111MODULE`这个环境变量来作为 Go modules 的开关，其允许设置以下参数：

- auto：只要项目包含了 go.mod 文件的话启用 Go modules，目前在 Go1.11 至 Go1.14 中仍然是默认值。
- on：启用 Go modules，推荐设置，将会是未来版本中的默认值。
- off：禁用 Go modules，不推荐设置。

#### GOPROXY

这个环境变量主要是用于设置 Go 模块代理（Go module proxy）,其作用是用于使 Go 在后续拉取模块版本时直接通过镜像站点来快速拉取。

GOPROXY 的默认值是：`https://proxy.golang.org,direct`

`proxy.golang.org`国内访问不了,需要设置国内的代理.

-  阿里云：https://mirrors.aliyun.com/goproxy/ 
-  七牛云：https://goproxy.cn,direct 

go env -w GOPROXY=https://goproxy.cn,direct	//设置代理

GOPROXY 的值是一个以英文逗号 “,” 分割的 Go 模块代理列表，允许设置多个模块代理，假设你不想使用，也可以将其设置为 “off” ，这将会禁止 Go 在后续操作中使用任何 Go 模块代理。

如：go env -w GOPROXY=https://goproxy.cn,https://mirrors.aliyun.com/goproxy/,direct

**direct**

实际上 “direct” 是一个特殊指示符，用于指示 Go 回源到模块版本的源地址去抓取（比如 GitHub 等），场景如下：当值列表中上一个 Go 模块代理返回 404 或 410 错误时，Go 自动尝试列表中的下一个，遇见 “direct” 时回源，也就是回到源地址去抓取，而遇见 EOF 时终止并抛出类似 “invalid version: unknown revision...” 的错误。

#### GOSUMDB

它的值是一个 Go checksum database，用于在拉取模块版本时（无论是从源站拉取还是通过 Go module proxy 拉取）保证拉取到的模块版本数据未经过篡改，若发现不一致，也就是可能存在篡改，将会立即中止。

GOSUMDB 的默认值为：`sum.golang.org`，在国内也是无法访问的，但是 GOSUMDB 可以被 Go 模块代理所代理（详见：Proxying a Checksum Database）。

因此我们可以通过设置 GOPROXY 来解决，而先前我们所设置的模块代理 `goproxy.cn` 就能支持代理 `sum.golang.org`，所以这一个问题在设置 GOPROXY 后，你可以不需要过度关心。

另外若对 GOSUMDB 的值有自定义需求，其支持如下格式：

- 格式 1：`<SUMDB_NAME>+<PUBLIC_KEY>`。
- 格式 2：`<SUMDB_NAME>+<PUBLIC_KEY> <SUMDB_URL>`。

也可以将其设置为“off”，也就是禁止 Go 在后续操作中校验模块版本。

**GONOPROXY/GONOSUMDB/GOPRIVATE**

这三个环境变量都是用在当前项目依赖了私有模块，例如像是你公司的私有 git 仓库，又或是 github 中的私有库，都是属于私有模块，都是要进行设置的，否则会拉取失败。

更细致来讲，就是依赖了由 GOPROXY 指定的 Go 模块代理或由 GOSUMDB 指定 Go checksum database 都无法访问到的模块时的场景。

而一般**建议直接设置 GOPRIVATE，它的值将作为 GONOPROXY 和 GONOSUMDB 的默认值，所以建议的最佳姿势是直接使用 GOPRIVATE**。

并且它们的值都是一个以英文逗号 “,” 分割的模块路径前缀，也就是可以设置多个，例如：

```
$ go env -w GOPRIVATE="git.example.com,github.com/eddycjy/mquote"
```

设置后，前缀为 git.xxx.com 和 github.com/eddycjy/mquote 的模块都会被认为是私有模块。

如果不想每次都重新设置，我们也可以利用通配符，例如：

```
$ go env -w GOPRIVATE="*.example.com"
```

这样子设置的话，所有模块路径为 example.com 的子域名（例如：git.example.com）都将不经过 Go module proxy 和 Go checksum database，**需要注意的是不包括 example.com 本身**。



#### 使用Go Modules初始化项目

##### (1) 开启Go Modules

```
 $ go env -w GO111MODULE=on
 
 又或是可以通过直接设置系统环境变量（写入对应的~/.bash_profile 文件亦可）来实现这个目的：
 $ export GO111MODULE=on
```

##### (2) 初始化项目

```
创建项目目录
$ mkdir -p $HOME/aceld/modules_test
$ cd $HOME/aceld/modules_test

执行Go modules 初始化
$ go mod init github.com/aceld/modules_test
go: creating new go.mod: module github.com/aceld/modules_test

在执行 go mod init 命令时，我们指定了模块导入路径为 github.com/aceld/modules_test。接下来我们在该项目根目录下创建 main.go 文件，如下：
package main
import (
    "fmt"
    "github.com/aceld/zinx/znet"
    "github.com/aceld/zinx/ziface"
)

//ping test 自定义路由
type PingRouter struct {
    znet.BaseRouter
}

//Ping Handle
func (this *PingRouter) Handle(request ziface.IRequest) {
    //先读取客户端的数据
    fmt.Println("recv from client : msgId=", request.GetMsgID(), 
              ", data=", string(request.GetData()))

    //再回写ping...ping...ping
    err := request.GetConnection().SendBuffMsg(0, []byte("ping...ping...ping"))
    if err != nil {
      fmt.Println(err)
    }
}

func main() {
    //1 创建一个server句柄
    s := znet.NewServer()

    //2 配置路由
    s.AddRouter(0, &PingRouter{})

    //3 开启服务
    s.Serve()
}
OK, 我们先不要关注代码本身,我们看当前的main.go也就是我们的aceld/modules_test项目,是依赖一个叫github.com/aceld/zinx库的. znet和ziface只是zinx的两个模块.

接下来我们在$HOME/aceld/modules_test,本项目的根目录执行
$ go get github.com/aceld/zinx/znet
go: downloading github.com/aceld/zinx v0.0.0-20200221135252-8a8954e75100
go: found github.com/aceld/zinx/znet in github.com/aceld/zinx v0.0.0-20200221135252-8a8954e75100
我们会看到 我们的go.mod被修改,同时多了一个go.sum文件.
```

##### (3) 查看go.mod文件

```
内容如下：
module github.com/aceld/modules_test
go 1.14
require github.com/aceld/zinx v0.0.0-20200221135252-8a8954e75100 // indirect

解释：
module: 用于定义当前项目的模块路径
go:标识当前Go版本.即初始化版本
require: 当前项目依赖的一个特定的必须版本
indirect: 示该模块为间接依赖，也就是在当前应用程序中的 import 语句中，并没有发现这个模块的明确引用，有可能是你先手动 go get 拉取下来的，也有可能是你所依赖的模块所依赖的.我们的代码很明显是依赖的"github.com/aceld/zinx/znet"和"github.com/aceld/zinx/ziface",所以就间接的依赖了github.com/aceld/zinx
```

##### (4) 查看go.sum文件

在第一次拉取模块依赖后，会发现多出了一个 go.sum 文件，其详细罗列了当前项目直接或间接依赖的所有模块版本，并写明了那些模块版本的 SHA-256 哈希值以备 Go 在今后的操作中保证项目所依赖的那些模块版本不会被篡改。







## 四、数据结构与算法

### 排序算法

#### 4.1.1 冒泡排序

> 时间复杂度 O(n<sup>2</sup>)

```go
func Bubble_Sorting(c []int) []int {
	for i := 0; i < len(c); i++ {
		for j := i; j < len(c); j++ {
			if c[i] > c[j] {
				c[i], c[j] = c[j], c[i]
			}
		}
	}

	return c
}
```

#### 4.1.2 快速排序

> 时间复杂度 O(n)

```go
func Quick_sort(arr []int) []int {
	if len(arr) <= 1 {
		return arr
	}
	pivot := arr[0]
	left, right := 1, len(arr)-1
	for left <= right {
		if arr[left] <= pivot {
			left++
		} else {
			arr[left], arr[right] = arr[right], arr[left]
			right--
		}
	}
	arr[0], arr[right] = arr[right], arr[0]
	leftArr := Quick_sort(arr[:right])
	rightArr := Quick_sort(arr[right+1:])
	result := append(leftArr, arr[right])
	result = append(result, rightArr...)
	return result
}

```

#### 4.1.3 归并排序

> 时间复杂度 `O(nlogn)`

```go
// 归并排序  O(n log n)
func Merge_Sort(a []int) []int {
	// 合并已排序的两个数组
	merge := func(arr1, arr2 []int) []int {
		arr := make([]int, len(arr1)+len(arr2))
		i, j, k := 0, 0, 0
		for i < len(arr1) && j < len(arr2) {
			if arr1[i] < arr2[j] {
				arr[k] = arr1[i]
				i++
			} else {
				arr[k] = arr2[j]
				j++
			}
			k++
		}
		for i < len(arr1) {
			arr[k] = arr1[i]
			i++
			k++
		}
		for j < len(arr2) {
			arr[k] = arr2[j]
			j++
			k++
		}
		return arr
	}
	n := len(a)
	if n <= 1 {
		return a
	}

	// 分解成两个子问题
	mid := n / 2
	left := Merge_Sort(a[:mid])
	right := Merge_Sort(a[mid:])

	// 合并已排序的两个子数组
	return merge(left, right)
}
```

#### 4.1.4 插入排序

> 时间复杂度O(n<sup>2</sup>)

```go
func Inser_sort(c []int) []int {
	for i := 1; i < len(c); i++ {
		for j := i; j > 0; j-- {
			if c[j-1] > c[j] {
				c[j], c[j-1] = c[j-1], c[j]
			}
		}
	}
	return c
}
```

#### 4.1.3 选择排序

> 时间复杂度O(n<sup>2</sup>)

```go
func Select_Sort(c []int) []int {
	for i := 0; i < len(c); i++ {
		for j := i + 1; j < len(c); j++ {
			if c[i] > c[j] {
				c[i], c[j] = c[j], c[i]
			}
		}
	}
	return c

}
```

### 查找算法

#### 二分查找

> 时间复杂度 O(n)

```go
// 二分查找
func Binary_search(a []int, b int) int {
	left, right := 0, len(a)-1
	for left <= right {
		mid := (left + right) / 2
		if a[mid] == b {
			return mid
		} else if a[mid] > b {
			right = mid - 1
		} else {
			right = mid + 1
		}
	}
	return -1
}
```

#### 4.2.2线性查找

> 时间复杂度 O(n)

```go
func Line_search(a []int, b int) int {
	for i := 0; i < len(a); i++ {
		if a[i] == b {
			return i
		}
	}
	return -1
}
```







## 五、Gin框架

`Gin`是一个用`Go语言`编写的`web框架`。它是一个类似于`martini`但拥有更好性能的API框架, 由于使用了`httprouter`，速度提高了近40倍。 

### 5.1 Gin框架的安装

```bash
$ go get -u github.com/gin-gonic/gin
```

### 5.2 第一个Gin实例

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	// 创建一个默认的路由引擎
	r := gin.Default()
	// GET：请求方式；/hello：请求的路径
	// 当客户端以GET方法请求/hello路径时，会执行后面的匿名函数
	r.GET("/hello", func(c *gin.Context) {
		// c.JSON：返回JSON格式的数据
		c.JSON(200, gin.H{
			"message": "Hello world!",
		})
	})
	// 启动HTTP服务，默认在0.0.0.0:8080启动服务
	r.Run()
}
```

### 5.3 Restful API

简单来说，REST的含义就是客户端与Web服务器之间进行交互的时候，使用HTTP协议中的4个请求方法代表不同的动作。

1. `GET`用来获取资源
2. `POST`用来新建资源
3. `PUT`用来更新资源
4. `DELETE`用来删除资源。

**按照Restful API设计**

| 请求方法 |     URL     |        含义        |
| :------: | :---------: | :----------------: |
|   GET    |   /users    |  获取所有用户数据  |
|   GET    | /users/{id} | 获取特定用户的数据 |
|   POST   |   /users    |   创建一个新用户   |
|   PUT    | /users/{id} | 更新特定用户的数据 |
|  DELETE  | /users/{id} | 删除特定用户的数据 |

**Gin框架支持restful API设计方式**

```go
package main

import "github.com/gin-gonic/gin"

type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
    Age  int    `json:"age"`
}

var users []*User

func main() {
    r := gin.Default()

    // 获取所有用户数据
    r.GET("/users", func(c *gin.Context) {
        c.JSON(200, users)
    })

    // 获取特定用户数据
    r.GET("/users/:id", func(c *gin.Context) {
        id := c.Param("id")
        for _, u := range users {
            if strconv.Itoa(u.ID) == id {
                c.JSON(200, u)
                return
            }
        }
        c.JSON(404, gin.H{"message": "User not found"})
    })

    // 创建一个新用户
    r.POST("/users", func(c *gin.Context) {
        var u User
        if err := c.BindJSON(&u); err != nil {
            c.JSON(400, gin.H{"message": "Invalid request"})
            return
        }
        u.ID = len(users) + 1
        users = append(users, &u)
        c.JSON(201, gin.H{"message": "User created", "id": u.ID})
    })

    // 更新特定用户的数据
    r.PUT("/users/:id", func(c *gin.Context) {
        id := c.Param("id")
        for _, u := range users {
            if strconv.Itoa(u.ID) == id {
                var update struct {
                    Name string `json:"name"`
                    Age  int    `json:"age"`
                }
                if err := c.BindJSON(&update); err != nil {
                    c.JSON(400, gin.H{"message": "Invalid request"})
                    return
                }
                u.Name = update.Name
                u.Age = update.Age
                c.JSON(200, gin.H{"message": "User updated", "id": u.ID})
                return
            }
        }
        c.JSON(404, gin.H{"message": "User not found"})
    })

    // 删除特定用户的数据
    r.DELETE("/users/:id", func(c *gin.Context) {
        id := c.Param("id")
        for i, u := range users {
            if strconv.Itoa(u.ID) == id {
                users = append(users[:i], users[i+1:]...)
                c.JSON(200, gin.H{"message": "User deleted", "id": id})
                return
            }
        }
        c.JSON(404, gin.H{"message": "User not found"})
    })

    // 启动服务器
    r.Run()
}
```

### 5.4 Gin渲染

> 现在前后分离，没咋学

#### 5.4.1 HTML渲染

我们首先定义一个存放模板文件的`templates`文件夹，然后在其内部按照业务分别定义一个`posts`文件夹和一个`users`文件夹。 

`posts/index.html`文件内容如下：

```html
{{define "posts/index.html"}}
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>posts/index</title>
</head>
<body>
    {{.title}}
</body>
</html>
{{end}}
```

`users/index.html`文件内容如下：

```html
{{define "users/index.html"}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>users/index</title>
</head>
<body>
    {{.title}}
</body>
</html>
{{end}}
```

`Gin框架`中使用`LoadHTMLGlob()`或者`LoadHTMLFiles()`方法进行`HTML`渲染

```go
func main() {
	r := gin.Default()
	r.LoadHTMLGlob("templates/**/*")
	//r.LoadHTMLFiles("templates/posts/index.html", "templates/users/index.html")
	r.GET("/posts/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "posts/index.html", gin.H{
			"title": "posts/index",
		})
	})

	r.GET("users/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "users/index.html", gin.H{
			"title": "users/index",
		})
	})

	r.Run(":8080")
}

```

#### 5.4.2 自定义模板函数

> 定义一个不转义相应内容`safe`模板函数

```go
func main() {
	router := gin.Default()
	router.SetFuncMap(template.FuncMap{
		"safe": func(str string) template.HTML{
			return template.HTML(str)
		},
	})
	router.LoadHTMLFiles("./index.tmpl")
	router.GET("/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.tmpl", "<a href='https://www.baidu.com'>百度</a>")
	})
	
	router.Run(":8080")
}
```

> 在`index.tmpl`中使用定义好的`safe`函数

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>修改模板引擎的标识符</title>
</head>
<body>
<div>{{ . | safe }}</div>
</body>
</html>
```

#### 5.4.3 静态文件处理

> 当我们渲染的`HTML`文件中引用了静态文件时，我们只需要按照以下方式在渲染页面前调用`gin.Static`方法即可

```go
func main() {
	r := gin.Default()
    // 在
	r.Static("/static", "./static")
	r.LoadHTMLGlob("templates/**/*")
   // ...
	r.Run(":8080")
}
```

**在html文件中这样导入静态文件**

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <title>静态文件导入</title>
        <link src="http://locahost:8080/static/xxx.css">
    </head>
    
    <body>
        
    </body>
</html>

```

#### 5.4.4 使用模板继承

`Gin`框架默认都是使用单模板，如果需要使用`block template`功能，可以通过`"github.com/gin-contrib/multitemplate"`库实现，具体示例如下：

首先，假设我们项目目录下的`emplates`文件夹下有以下模板文件，其中`home.tmpl`和`index.tmpl`继承了`base.tmpl`：

```
templates
├── includes
│   ├── home.tmpl
│   └── index.tmpl
├── layouts
│   └── base.tmpl
└── scripts.tmpl
```

然后我们定义一个`loadTemplates函数`如下：

```go
func loadTemplates(templatesDir string) multitemplate.Renderer {
	r := multitemplate.NewRenderer()
	layouts, err := filepath.Glob(templatesDir + "/layouts/*.tmpl")
	if err != nil {
		panic(err.Error())
	}
	includes, err := filepath.Glob(templatesDir + "/includes/*.tmpl")
	if err != nil {
		panic(err.Error())
	}
	// 为layouts/和includes/目录生成 templates map
	for _, include := range includes {
		layoutCopy := make([]string, len(layouts))
		copy(layoutCopy, layouts)
		files := append(layoutCopy, include)
		r.AddFromFiles(filepath.Base(include), files...)
	}
	return r
}
```

我们在`main函数`中

```go
func indexFunc(c *gin.Context){
	c.HTML(http.StatusOK, "index.tmpl", nil)
}

func homeFunc(c *gin.Context){
	c.HTML(http.StatusOK, "home.tmpl", nil)
}

func main(){
	r := gin.Default()
	r.HTMLRender = loadTemplates("./templates")
	r.GET("/index", indexFunc)
	r.GET("/home", homeFunc)
	r.Run()
}
```

#### 5.4.5 补充文件路径处理

**关于`模板文件`和`静态文件`的路径，我们需要根据`公司/项目`的要求进行设置。可以使用下面的函数获取当前执行程序的路径。**

```go
func getCurrentPath() string {
	if ex, err := os.Executable(); err == nil {
		return filepath.Dir(ex)
	}
	return "./"
}
```

#### 5.4.6 JSON渲染

```go
func main() {
	r := gin.Default()

	// gin.H 是map[string]interface{}的缩写
	r.GET("/someJSON", func(c *gin.Context) {
		// 方式一：自己拼接JSON
		c.JSON(http.StatusOK, gin.H{"message": "Hello world!"})
	})
	r.GET("/moreJSON", func(c *gin.Context) {
		// 方法二：使用结构体
		var msg struct {
			Name    string `json:"user"`
			Message string
			Age     int
		}
		msg.Name = "小王子"
		msg.Message = "Hello world!"
		msg.Age = 18
		c.JSON(http.StatusOK, msg)
	})
	r.Run(":8080")
}
```

#### 5.4.7 XML渲染

```go
func main() {
	r := gin.Default()
	// gin.H 是map[string]interface{}的缩写
	r.GET("/someXML", func(c *gin.Context) {
		// 方式一：自己拼接JSON
		c.XML(http.StatusOK, gin.H{"message": "Hello world!"})
	})
	r.GET("/moreXML", func(c *gin.Context) {
		// 方法二：使用结构体
		type MessageRecord struct {
			Name    string
			Message string
			Age     int
		}
		var msg MessageRecord
		msg.Name = "小王子"
		msg.Message = "Hello world!"
		msg.Age = 18
		c.XML(http.StatusOK, msg)
	})
	r.Run(":8080")
}

```

#### 5.4.8 YMAL渲染

```go
r.GET("/someYAML", func(c *gin.Context) {
	c.YAML(http.StatusOK, gin.H{"message": "ok", "status": http.StatusOK})
})
```

#### 5.4.9 protobuf渲染

```go
r.GET("/someProtoBuf", func(c *gin.Context) {
	reps := []int64{int64(1), int64(2)}
	label := "test"
	// protobuf 的具体定义写在 testdata/protoexample 文件中。
	data := &protoexample.Test{
		Label: &label,
		Reps:  reps,
	}
	// 请注意，数据在响应中变为二进制数据
	// 将输出被 protoexample.Test protobuf 序列化了的数据
	c.ProtoBuf(http.StatusOK, data)
})

```

### 5.5 获取参数

#### 5.5.1 获取querystring参数

> `querystring`指的是URL中?后面携带的参数，例如：`/user/search?username=小王子&address=沙河`。 获取请求的`querystring`参数的方法如下：

```go
func main() {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.GET("/user/search", func(c *gin.Context) {
		username := c.DefaultQuery("username", "小王子")
		//username := c.Query("username")
		address := c.Query("address")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"address":  address,
		})
	})
	r.Run()
}
```

#### 5.5.2 获取form数据

> 当前端请求的数据通过`form`表单提交时，例如向`/user/search`发送一个POST请求，获取请求数据的方式如下

```go
func main() {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.POST("/user/search", func(c *gin.Context) {
		// DefaultPostForm取不到值时会返回指定的默认值
		//username := c.DefaultPostForm("username", "小王子")
		username := c.PostForm("username")
		address := c.PostForm("address")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"address":  address,
		})
	})
	r.Run(":8080")
}
```

#### 5.5.3 获取JSON数据

> 当前端请求的数据通过`JSON`提交时，例如向`/json`发送一个POST请求，则获取请求参数的方式如下：

```go
r.POST("/json", func(c *gin.Context) {
	// 注意：下面为了举例子方便，暂时忽略了错误处理
	b, _ := c.GetRawData()  // 从c.Request.Body读取请求数据
	// 定义map或结构体
	var m map[string]interface{}
	// 反序列化
	_ = json.Unmarshal(b, &m)

	c.JSON(http.StatusOK, m)
})
```

具体详细在下面**<font color="red">[参数绑定](#555-参数绑定)</font>**

#### 5.5.4 获取path 参数

>  请求的参数通过URL路径传递，例如：`/user/search/小王子/沙河`。 获取请求`URL`路径中的参数的方式如下。

```go
func main() {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.GET("/user/search/:username/:address", func(c *gin.Context) {
		username := c.Param("username")
		address := c.Param("address")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"address":  address,
		})
	})

	r.Run(":8080")
}
```

#### 5.5.5 参数绑定

> 为了能够更方便的获取请求相关参数，提高开发效率，我们可以基于请求的`Content-Type`识别请求数据类型并利用反射机制自动提取请求中`QueryString`、`form表单`、`JSON`、`XML`等参数到结构体中。 下面的示例代码演示了`.ShouldBind()`强大的功能，它能够基于请求自动提取`JSON、form表单和QueryString类型`的数据，并把值绑定到指定的结构体对象。

```go
// Binding from JSON
type Login struct {
	User     string `form:"user" json:"user" binding:"required"`
	Password string `form:"password" json:"password" binding:"required"`
}

func main() {
	router := gin.Default()

	// 绑定JSON的示例 ({"user": "q1mi", "password": "123456"})
	router.POST("/loginJSON", func(c *gin.Context) {
		var login Login

		if err := c.ShouldBind(&login); err == nil {
			fmt.Printf("login info:%#v\n", login)
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	// 绑定form表单示例 (user=q1mi&password=123456)
	router.POST("/loginForm", func(c *gin.Context) {
		var login Login
		// ShouldBind()会根据请求的Content-Type自行选择绑定器
		if err := c.ShouldBind(&login); err == nil {
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	// 绑定QueryString示例 (/loginQuery?user=q1mi&password=123456)
	router.GET("/loginForm", func(c *gin.Context) {
		var login Login
		// ShouldBind()会根据请求的Content-Type自行选择绑定器
		if err := c.ShouldBind(&login); err == nil {
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	// Listen and serve on 0.0.0.0:8080
	router.Run(":8080")
}


```

`ShouldBind`会按照下面的顺序解析请求中的数据完成绑定：

    如果是 GET 请求，只使用 Form 绑定引擎（query）。
    如果是 POST 请求，首先检查 content-type 是否为 JSON 或 XML，然后再使用 Form（form-data）。

### 5.6 文件上传

#### 5.6.1 单文件上传

`demo.html`内容:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>文件上传</title>
</head>
<body>
<form action="http://localhost:8080/value" method="post" enctype="multipart/form-data">
  <input type="file" name="file" id="file">文件上传
  <input type="submit" value="上传">
</form>
</body>
</html>
```

`main.go`内容:

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()
	r.LoadHTMLFiles("gin/learn_Gin/文件上传/demo.html")
	r.GET("/value", txtUp)
	r.POST("/value", func(c *gin.Context) {
		t, err := c.FormFile("file")
		if err != nil {
			c.JSON(200, gin.H{
				"message": err,
			})
			return
		}
		log.Println(t.Filename)
		dst := "/usr/local/go/Lear_Project/gin/learn_Gin/文件上传/" + t.Filename
		c.SaveUploadedFile(t, dst)
		c.JSON(200, gin.H{
			"message": fmt.Sprintf("%s,保存成功!!", t.Filename),
		})
		return
	})
	r.Run(":8080")
}

func txtUp(c *gin.Context) {
	c.HTML(200, "demo.html", nil)
}

```

#### 5.6.2 多文件上传

`demo.html`文件内容:

```go
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>多文件上传</title>
  <style>
    body {
      font-family: Arial;
      background-color: #f3f3f3;
      padding: 20px;
    }
    .container {
      background-color: #fff;
      padding: 20px;
      border-radius: 5px;
      box-shadow: 0 0 5px rgba(0,0,0,0.3);
    }
    .form {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    .form label {
      margin-bottom: 10px;
      font-weight: bold;
    }
    .form input[type="file"] {
      margin-bottom: 20px;
    }
    .form button {
      background-color: #4CAF50;
      color: #fff;
      padding: 10px 20px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      transition: background-color 0.3s;
    }
    .form button:hover {
      background-color: #3e8e41;
    }
    .form button:active {
      background-color: #296626;
    }
  </style>
</head>
<body>
<div class="container">
  <form class="form" action="http://localhost:8080/value" method="post" enctype="multipart/form-data">
    <input type="file" id="file" name="file" multiple>
    <input type="submit">上传文件</input>
  </form>
</div>
</body>
</html>

```

`mian.go`文件内容:

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"log"
	"net/http"
	"strconv"
)

func main() {
	//多文件上传
	r := gin.Default()
	r.LoadHTMLFiles("/usr/local/go/Lear_Project/gin/learn_Gin/多文件上传/demo.html")

	r.Any("/value", func(c *gin.Context) {
		switch c.Request.Method {
		case http.MethodGet:
			c.HTML(200, "demo.html", nil)
		case http.MethodPost:
			forms, err := c.MultipartForm()
			if err != nil {
				c.JSON(http.StatusInternalServerError, gin.H{
					"message": "上传失败",
				})
				return
			}
			form := forms.File["file"]
			for index, files := range form {
				log.Println(files.Filename)
				sindex := strconv.Itoa(index)
				dst := "/usr/local/go/Lear_Project/gin/learn_Gin/多文件上传/" + files.Filename + sindex
				c.SaveUploadedFile(files, dst)
			}
			c.JSON(200, gin.H{
				"message": fmt.Sprintf("%d 个文件上传成功！！！", len(form)),
			})
			return
		default:
			c.String(200, fmt.Sprintf("请求方式错误%s", c.Request.Method))
		}
	})
	r.Run(":8080")
}
```

### 5.7 重定向

#### 5.7.1 HTTP重定向

>  `HTTP` 重定向很容易。 内部、外部重定向均支持。

```go
r.GET("/test", func(c *gin.Context) {
	c.Redirect(http.StatusMovedPermanently, "http://www.sogo.com/")
})
```

#### 5.7.2 路由重定向

>  路由重定向，使用`HandleContext`：

```go
r.GET("/test", func(c *gin.Context) {
    // 指定重定向的URL
    c.Request.URL.Path = "/test2"
    r.HandleContext(c)
})
r.GET("/test2", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{"hello": "world"})
})
```

> 使用函数实现路由重定向

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()

	//	路由重定向
	r.GET("/url", func(c *gin.Context) {
		c.Request.URL.Path = "/school"
		r.HandleContext(c)
	})
    r.GET("/abc",func(c *gin.Context) {
        c.JSON(200,gin.H{
            "message":"重定向成功"
        })
    })
	//用函数操作 重定向
	r.GET("/urls", Render)
	//---------------------------------------

	//大概是上面这个意思
	r.Run(":8080")
}

func Render(c *gin.Context) {
	c.Redirect(http.StatusMovedPermanently, "/abc")
}

```

### 5.8 Gin路由

#### 5.8.1 普通路由

```go
r.GET("/index", func(c *gin.Context) {...})
r.GET("/login", func(c *gin.Context) {...})
r.POST("/login", func(c *gin.Context) {...})

```

此外，还有一个可以匹配所有请求方法的`Any`方法如下：

```go
r.Any("/test", func(c *gin.Context) {...})
```

> 顺便写个判断any的

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()
	r.Any("/book", AnyPs)
	err := r.Run(":8080")
	if err != nil {
		panic(err)
	}
}

func AnyPs(c *gin.Context) {
	switch c.Request.Method {
        //如果是GET
	case http.MethodGet:
		c.JSON(http.StatusOK, gin.H{
			"Method": http.MethodGet,
		})
        //如果是delete
	case http.MethodDelete:
		c.JSON(http.StatusOK, gin.H{
			"Method": http.MethodDelete,
		})
	case http.MethodPost:
		c.JSON(http.StatusOK, gin.H{
			"Method": http.MethodPost,
		})
	default:
		c.JSON(http.StatusOK, gin.H{
			"Method": "Hello",
		})
	}
}

```

为没有配置处理函数的路由添加处理程序，默认情况下它返回404代码，下面的代码为没有匹配到路由的请求都返回`404.html`页面。

```go
r.NoRoute(func(c *gin.Context) {
		c.HTML(http.StatusNotFound, "404.html", nil)
})
```

#### 5.8.2 路由组

我们可以将拥有共同URL前缀的路由划分为一个路由组。习惯性一对`{}`包裹同组的路由，这只是为了看着清晰，你用不用`{}`包裹功能上没什么区别。

```go
func main() {
	r := gin.Default()
	userGroup := r.Group("/user")
	{
		userGroup.GET("/index", func(c *gin.Context) {...})
		userGroup.GET("/login", func(c *gin.Context) {...})
		userGroup.POST("/login", func(c *gin.Context) {...})
        }
	shopGroup := r.Group("/shop")
	{
	shopGroup.GET("/index", func(c *gin.Context) {...})
	shopGroup.GET("/cart", func(c *gin.Context) {...})
	shopGroup.POST("/checkout", func(c *gin.Context) {...})
	}
	r.Run()
 	}
```

路由组也是支持嵌套的，例如：

```go
shopGroup := r.Group("/shop")
	{
		shopGroup.GET("/index", func(c *gin.Context) {...})
		shopGroup.GET("/cart", func(c *gin.Context) {...})
		shopGroup.POST("/checkout", func(c *gin.Context) {...})
		// 嵌套路由组
		xx := shopGroup.Group("xx")
		xx.GET("/oo", func(c *gin.Context) {...})
	}


```

#### 5.8.3 路由原理

Gin框架中的路由使用的是[httprouter](https://github.com/julienschmidt/httprouter)这个库。

其基本原理就是构造一个路由地址的前缀树。

### 5.9 中间件

> Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证、权限校验、数据分页、记录日志、耗时统计等。
> 定义中间件

Gin中的中间件必须是一个`gin.HandlerFunc`类型。

#### 5.9.1记录接口耗时的中间件

例如我们像下面的代码一样定义一个统计请求耗时的中间件。

```go
// StatCost 是一个统计耗时请求耗时的中间件
func StatCost() gin.HandlerFunc {
	return func(c *gin.Context) {
		start := time.Now()
		c.Set("name", "小王子") // 可以通过c.Set在请求上下文中设置值，后续的处理函数能够取到该值
		// 调用该请求的剩余处理程序
		c.Next()
		// 不调用该请求的剩余处理程序
		// c.Abort()
		// 计算耗时
		cost := time.Since(start)
		log.Println(cost)
	}
}
```

#### 5.9.2记录响应体的中间件

我们有时候可能会想要记录下某些情况下返回给客户端的响应数据，这个时候就可以编写一个中间件来搞定。

```go
type bodyLogWriter struct {
	gin.ResponseWriter               // 嵌入gin框架ResponseWriter
	body               *bytes.Buffer // 我们记录用的response
}

// Write 写入响应体数据
func (w bodyLogWriter) Write(b []byte) (int, error) {
	w.body.Write(b)                  // 我们记录一份
	return w.ResponseWriter.Write(b) // 真正写入响应
}

// ginBodyLogMiddleware 一个记录返回给客户端响应体的中间件
// https://stackoverflow.com/questions/38501325/how-to-log-response-body-in-gin
func ginBodyLogMiddleware(c *gin.Context) {
	blw := &bodyLogWriter{body: bytes.NewBuffer([]byte{}), ResponseWriter: c.Writer}
	c.Writer = blw // 使用我们自定义的类型替换默认的

	c.Next() // 执行业务逻辑
	
	fmt.Println("Response body: " + blw.body.String()) // 事后按需记录返回的响应

}
```

#### 5.9.3跨域中间件cors

推荐使用社区的[https://github.com/gin-contrib/cors](https://github.com/gin-contrib/cors) 库，一行代码解决前后端分离架构下的跨域问题。

注意： 该中间件需要注册在业务处理函数前面。

这个库支持各种常用的配置项，具体使用方法如下。

```go
package main

import (
  "time"

  "github.com/gin-contrib/cors"
  "github.com/gin-gonic/gin"
)

func main() {
  router := gin.Default()
  // CORS for https://foo.com and https://github.com origins, allowing:
  // - PUT and PATCH methods
  // - Origin header
  // - Credentials share
  // - Preflight requests cached for 12 hours
  router.Use(cors.New(cors.Config{
    AllowOrigins:     []string{"https://foo.com"},  // 允许跨域发来请求的网站
    AllowMethods:     []string{"GET", "POST", "PUT", "DELETE",  "OPTIONS"},  // 允许的请求方法
    AllowHeaders:     []string{"Origin", "Authorization", "Content-Type"},
    ExposeHeaders:    []string{"Content-Length"},
    AllowCredentials: true,
    AllowOriginFunc: func(origin string) bool {  // 自定义过滤源站的方法
      return origin == "https://github.com"
    },
    MaxAge: 12 * time.Hour,
  }))
  router.Run()
}
```

当然你可以简单的像下面的示例代码那样使用默认配置，允许所有的跨域请求。

```go
func main() {
  router := gin.Default()
  // same as
  // config := cors.DefaultConfig()
  // config.AllowAllOrigins = true
  // router.Use(cors.New(config))
  router.Use(cors.Default())
  router.Run()
}
```



#### 5.9.4注册中间件

在gin框架中，我们可以为每个路由添加任意数量的中间件。
为全局路由注册

```go
func main() {
	// 新建一个没有任何默认中间件的路由
	r := gin.New()
	// 注册一个全局中间件
	r.Use(StatCost())
	

	r.GET("/test", func(c *gin.Context) {
		name := c.MustGet("name").(string) // 从上下文取值
		log.Println(name)
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello world!",
		})
	})
	r.Run()

}
```



#### 5.9.5 为某个路由单独注册

```go
// 给/test2路由单独注册中间件（可注册多个）
	r.GET("/test2", StatCost(), func(c *gin.Context) {
		name := c.MustGet("name").(string) // 从上下文取值
		log.Println(name)
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello world!",
		})
	})
```

#### 5.9.6 为路由组注册中间件

为路由组注册中间件有以下两种写法。

写法1：

```go
shopGroup := r.Group("/shop", StatCost())
{
    shopGroup.GET("/index", func(c *gin.Context) {...})
    ...
}
```

写法2：

```go
shopGroup := r.Group("/shop")
shopGroup.Use(StatCost())
{
    shopGroup.GET("/index", func(c *gin.Context) {...})
    ...
}
```

#### 5.9.7 中间件注意事项

`gin`默认中间件

`gin.Default()`默认使用了`Logger`和`Recovery`中间件，其中：

    Logger中间件将日志写入gin.DefaultWriter，即使配置了GIN_MODE=release。
    Recovery中间件会recover任何panic。如果有panic的话，会写入500响应码。

如果不想使用上面两个默认的中间件，可以使用gin.New()新建一个没有任何默认中间件的路由。
`gin`中间件中使用`goroutine`

当在中间件或`handler`中启动新的`goroutine`时，不能使用原始的上下文`（c *gin.Context）`，必须使用其只读副本`（c.Copy()）`。

### 5.10 运行多个服务

我们可以在多个端口启动服务，例如：

```go
package main

import (
	"log"
	"net/http"
	"time"

	"github.com/gin-gonic/gin"
	"golang.org/x/sync/errgroup"
)

var (
	g errgroup.Group
)

func router01() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 01",
			},
		)
	})

	return e
}

func router02() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 02",
			},
		)
	})

	return e
}

func main() {
	server01 := &http.Server{
		Addr:         ":8080",
		Handler:      router01(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}

	server02 := &http.Server{
		Addr:         ":8081",
		Handler:      router02(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}
   // 借助errgroup.Group或者自行开启两个goroutine分别启动两个服务
	g.Go(func() error {
		return server01.ListenAndServe()
	})

	g.Go(func() error {
		return server02.ListenAndServe()
	})

	if err := g.Wait(); err != nil {
		log.Fatal(err)
	}
}

```

## 六、Gorm 入门

> [Gorm官方中文文档](https://gorm.io/zh_CN/docs/)

### Gorm安装

```bash
go get -u github.com/jinzhu/gorm
```

### 数据库连接

连接不同的数据库都需要导入对应数据的驱动程序，GORM已经贴心的为我们包装了一些驱动程序，只需要按如下方式导入需要的数据库驱动即可：

```go
import _ "github.com/jinzhu/gorm/dialects/mysql"
// import _ "github.com/jinzhu/gorm/dialects/postgres"
// import _ "github.com/jinzhu/gorm/dialects/sqlite"
// import _ "github.com/jinzhu/gorm/dialects/mssql"
```

#### 6.2.1 连接MySQL

```go

import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mysql"
)

func main() {
  db, err := gorm.Open("mysql", "user:password@(localhost)/dbname?charset=utf8mb4&parseTime=True&loc=Local")
  defer db.Close()
}
```



#### 6.2.2 连接PostgreSQL

基本代码同上，注意引入对应postgres驱动并正确指定`gorm.Open()`参数。

```go
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/postgres"
)

func main() {
  db, err := gorm.Open("postgres", "host=myhost port=myport user=gorm dbname=gorm password=mypassword")
  defer db.Close()
}
```

#### 6.2.3 连接Sqlite3

基本代码同上，注意引入对应`sqlite`驱动并正确指定`gorm.Open()`参数。

```go
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/sqlite"
)

func main() {
  db, err := gorm.Open("sqlite3", "/tmp/gorm.db")
  defer db.Close()
}
```

#### 6.2.4 连接SQL Server

基本代码同上，注意引入对应mssql驱动并正确指定`gorm.Open()`参数。

```go
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mssql"
)

func main() {
  db, err := gorm.Open("mssql", "sqlserver://username:password@localhost:1433?database=dbname")
  defer db.Close()
}
```

GORM基本示例

注意:

    本文以MySQL数据库为例，讲解GORM各项功能的主要使用方法。
    往下阅读本文前，你需要有一个能够成功连接上的MySQL数据库实例。

### 6.3 操作数据库

#### 6.3.1 Docker快速创建MySQL实例

如果不会安装MySQL或者懒得安装MySQL，可以使用一下命令快速运行一个MySQL8.0.19实例，当然前提是你要有docker环境…

在本地的13306端口运行一个名为mysql8019，root用户名密码为root1234的MySQL容器环境:

```dockerfile
docker run --name mysql8019 -p 13306:3306 -e MYSQL_ROOT_PASSWORD=root1234 -d mysql:8.0.19
```

在另外启动一个MySQL Client连接上面的MySQL环境，密码为上一步指定的密码root1234:

```dockerfile
docker run -it --network host --rm mysql mysql -h127.0.0.1 -P13306 --default-character-set=utf8mb4 -uroot -p
```

#### 6.3.2创建数据库

在使用GORM前手动创建数据库db1：

```mysql
CREATE DATABASE db1;
```

#### 6.3.3GORM操作MySQL

使用GORM连接上面的db1进行**创建**、**查询**、**更新**、**删除**操作。

```go
package main

import (
	"fmt"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

// UserInfo 用户信息
type UserInfo struct {
	ID uint
	Name string
	Gender string
	Hobby string
}


func main() {
	db, err := gorm.Open("mysql", "root:root1234@(127.0.0.1:13306)/db1?charset=utf8mb4&parseTime=True&loc=Local")
	if err!= nil{
		panic(err)
	}
	defer db.Close()

	// 自动迁移
	db.AutoMigrate(&UserInfo{})
	
	u1 := UserInfo{1, "七米", "男", "篮球"}
	u2 := UserInfo{2, "沙河娜扎", "女", "足球"}
	// 创建记录
	db.Create(&u1)
	db.Create(&u2)
	// 查询
	var u = new(UserInfo)
	db.First(u)
	fmt.Printf("%#v\n", u)
	
	var uu UserInfo
	db.Find(&uu, "hobby=?", "足球")
	fmt.Printf("%#v\n", uu)
	
	// 更新
	db.Model(&u).Update("hobby", "双色球")
	// 删除
	db.Delete(&u)

}
```

#### 6.3.4 GORM Model定义

在使用ORM工具时，通常我们需要在代码中定义模型`（Models`）与数据库中的数据表进行`映射`，在`GORM`中模型`（Models）`通常是正常定义的结构体、基本的`go类型`或它们的`指针`。 同时也支持`sql.Scanner`及`driver.Valuer`接口`（interfaces）`。
`gorm.Model`

为了方便模型定义，`GORM`内置了一个`gorm.Model`结构体。`gorm.Model`是一个包含了`ID, CreatedAt, UpdatedAt, DeletedA`四个字段的`Golang`结构体。

```go
// gorm.Model 定义
type Model struct {
  ID        uint `gorm:"primary_key"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt *time.Time
}

```

可以将它嵌入模型中：

```go
// 将 `ID`, `CreatedAt`, `UpdatedAt`, `DeletedAt`字段注入到`User`模型中
type User struct {
  gorm.Model
  Name string
}
```

也可以完全自己定义模型：

```go
// 不使用gorm.Model，自行定义模型
type User struct {
  ID   int
  Name string
}
```

模型定义示例

```go
type User struct {
  gorm.Model
  Name         string
  Age          sql.NullInt64
  Birthday     *time.Time
  Email        string  `gorm:"type:varchar(100);unique_index"`
  Role         string  `gorm:"size:255"` // 设置字段大小为255
  MemberNumber *string `gorm:"unique;not null"` // 设置会员号（member number）唯一并且不为空
  Num          int     `gorm:"AUTO_INCREMENT"` // 设置 num 为自增类型
  Address      string  `gorm:"index:addr"` // 给address字段创建名为addr的索引
  IgnoreMe     int     `gorm:"-"` // 忽略本字段
}
```

#### 6.3.5 结构体标记（tags）

使用结构体声明模型时，`标记（tags）`是可选项。gorm支持以下标记:
支持的结构体标记（`Struct tags`）

##### 6.3.5.1 支持的结构体标记（Struct tags）

|      结构体标记      |                    作用                     |
| :------------------: | :-----------------------------------------: |
|        `type`        |               定义结构体类型                |
|       `table`        |            定义映射的数据库表名             |
|       `column`       |            定义映射的数据库列名             |
|    `primary_key`     |                  定义主键                   |
|   `auto_increment`   |              自动生成递增主键               |
|       `unique`       |                 定义唯一键                  |
|      `not null`      |                定义不能为空                 |
|      `default`       |                 定义默认值                  |
|       `index`        |                  定义索引                   |
|    `unique_index`    |                定义唯一索引                 |
|        `size`        |              定义字段类型长度               |
|     `precision`      |             定义浮点数类型精度              |
|      `embedded`      |                 嵌套结构体                  |
|   `ignored` or `-`   |                 忽略该字段                  |
|      `migrate`       |      定义迁移操作，如创建表、添加列等       |
|      `comment`       |                  定义注释                   |
| `type:varchar(255)`  |           定义字符串类型长度为255           |
| `type:decimal(10,2)` | 定义十进制数类型，整数部分10位，小数部分2位 |
| `type:timestamp(6)`  |          定义时间戳类型精确到毫秒           |
|     `type:text`      |               定义长文本类型                |
|  `type:mediumtext`   |            定义中等长度文本类型             |
|   `type:longtext`    |               定义长文本类型                |
|   `type:datetime`    |              定义日期时间类型               |
|     `type:date`      |                定义日期类型                 |
|     `type:time`      |                定义时间类型                 |

##### 6.3.5.2 关联相关标记

|        结构体标记（Tag）         |                描述                |
| :------------------------------: | :--------------------------------: |
|            MANY2MANY             |             指定连接表             |
|            FOREIGNKEY            |              设置外键              |
|      ASSOCIATION_FOREIGNKEY      |            设置关联外键            |
|           POLYMORPHIC            |            指定多态类型            |
|        POLYMORPHIC_VALUE         |             指定多态值             |
|       JOINTABLE_FOREIGNKEY       |          指定连接表的外键          |
| ASSOCIATION_JOINTABLE_FOREIGNKEY |        指定连接表的关联外键        |
|        SAVE_ASSOCIATIONS         |    是否自动完成 save 的相关操作    |
|      ASSOCIATION_AUTOUPDATE      |   是否自动完成 update 的相关操作   |
|      ASSOCIATION_AUTOCREATE      |   是否自动完成 create 的相关操作   |
|    ASSOCIATION_SAVE_REFERENCE    | 是否自动完成引用的 save 的相关操作 |
|             PRELOAD              |    是否自动完成预加载的相关操作    |

#### 6.3.6 主键、表名、列表的约定

##### 6.3.6.1 主键

`GORM `默认会使用名为ID的字段作为表的主键。

```go
type User struct {
  ID   string // 名为`ID`的字段会默认作为表的主键
  Name string
}

// 使用`AnimalID`作为主键
type Animal struct {
  AnimalID int64 `gorm:"primary_key"`
  Name     string
  Age      int64
}

```



##### 6.3.6.2 表名（Table Name）

表名默认就是结构体名称的复数，例如：

```go
type User struct {} // 默认表名是 `users`

// 将 User 的表名设置为 `profiles`
func (User) TableName() string {
  return "profiles"
}

func (u User) TableName() string {
  if u.Role == "admin" {
    return "admin_users"
  } else {
    return "users"
  }
}

// 禁用默认表名的复数形式，如果置为 true，则 `User` 的默认表名是 `user`
db.SingularTable(true)
```

###### 6.3.6.2.1 也可以通过`Table()`指定表名：

```go
// 使用User结构体创建名为`deleted_users`的表
db.Table("deleted_users").CreateTable(&User{})

var deleted_users []User
db.Table("deleted_users").Find(&deleted_users)
//// SELECT * FROM deleted_users;

db.Table("deleted_users").Where("name = ?", "jinzhu").Delete()
//// DELETE FROM deleted_users WHERE name = 'jinzhu';
```

GORM还支持更改默认表名称规则：

```go
gorm.DefaultTableNameHandler = func (db *gorm.DB, defaultTableName string) string  {
  return "prefix_" + defaultTableName;
}
```

##### 6.3.6.3 列名（Column Name）

列名由字段名称进行下划线分割来生成

```go
type User struct {
  ID        uint      // column name is `id`
  Name      string    // column name is `name`
  Birthday  time.Time // column name is `birthday`
  CreatedAt time.Time // column name is `created_at`
}

```

###### 6.3.6.3.1可以使用结构体tag指定列名：

```go
type Animal struct {
  AnimalId    int64     `gorm:"column:beast_id"`         // set column name to `beast_id`
  Birthday    time.Time `gorm:"column:day_of_the_beast"` // set column name to `day_of_the_beast`
  Age         int64     `gorm:"column:age_of_the_beast"` // set column name to `age_of_the_beast`
}
```

##### 6.3.6.4时间戳跟踪

###### CreatedAt

如果模型有 CreatedAt字段，该字段的值将会是初次创建记录的时间。

```go
db.Create(&user) // `CreatedAt`将会是当前时间

// 可以使用`Update`方法来改变`CreateAt`的值
db.Model(&user).Update("CreatedAt", time.Now())

```

###### UpdatedAt

如果模型有UpdatedAt字段，该字段的值将会是每次更新记录的时间。

```go
db.Save(&user) // `UpdatedAt`将会是当前时间
db.Model(&user).Update("name", "jinzhu") // `UpdatedAt`将会是当前时间
```

###### DeletedAt

>  **如果模型有DeletedAt字段，调用Delete删除该记录时，将会设置DeletedAt字段为当前时间，而不是直接将记录从数据库中删除。**

## 七、jwt库

> 这个库是用来进行token生成的

[官方文档](https://pkg.go.dev/github.com/dgrijalva/jwt-go)---->[github文档](https://github.com/dgrijalva/jwt-go)

导入这个包

```shell
go get -u github.com/dgrijalva/jwt-go
```

> 这个也没找到教程  只有我之前写的项目用过

### 7.1生成token

```go
package common

import (
	"Appitems/models"
	"crypto/ecdsa"
	"crypto/elliptic"
	"crypto/rand"
	"github.com/dgrijalva/jwt-go"
	"time"
)

// 密钥
var (
	jwtPrivateKey, _ = ecdsa.GenerateKey(elliptic.P256(), rand.Reader)
	jwtPublicKey     = &jwtPrivateKey.PublicKey
	jwtKey           = []byte("a_secret_crect")
)

// 生成 token
func ReleaseToken(user models.UserDatabase) (string, error) {
	// Token 过期时间
	expirationTime := time.Now().Add(Week)

	// Token 中的用户信息
	claims := &models.Claims{
		UserId: user.ID,
		StandardClaims: jwt.StandardClaims{
			ExpiresAt: expirationTime.Unix(),
			IssuedAt:  time.Now().Unix(),
			Issuer:    "oceanlearn.tech",
			Subject:   "user access token",
		},
	}

	// 使用 ES256 算法创建 Token
	token := jwt.NewWithClaims(jwt.SigningMethodES256, claims)

	// 使用私钥签名 Token
	tokenString, err := token.SignedString(jwtPrivateKey)
	if err != nil {
		return "", err
	}

	return tokenString, nil
}

// 对token解密看一下
/*
eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJVc2VySWQiOjEsImV4cCI6MTY4NzE0NTg0MSwiaWF0IjoxNjg2NTQxMDQxLCJpc3MiOiJvY2VhbmxlYXJuLnRlY2giLCJzdWIiOiJ1c2VyIGFjY2VzcyB0b2tlbiJ9.E--woGubSsKh0aHdm4KTLKWuTA6XuH9P-_zQN6sRhCi3zCMAmOpxywbdZgsIDMLP2_50OdJzYSCwDg01hfYlzg
第一部分是请求头，第二部分是封装的东西 第三是加密算法
*/

// 第二种生成token算法加密

func ReleaseTokenUser(user models.UserDatabase) (string, error) {
	// Token 过期时间
	expirationTime := time.Now().Add(Week)
	claims := &models.Claims{
		UserId: user.ID,
		StandardClaims: jwt.StandardClaims{
			ExpiresAt: expirationTime.Unix(),
			IssuedAt:  time.Now().Unix(),
			Issuer:    "oceanlearn.tech",
			Subject:   "user access token",
		},
	}
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	Tokenstring, err := token.SignedString(jwtKey)
	if err != nil {
		return "", err
	}
	return Tokenstring, nil

}

// 解析token  用第二种方式加密的
func ParseToken(tokenString string) (*jwt.Token, *models.Claims, error) {
	Cliams := &models.Claims{}
	token, err := jwt.ParseWithClaims(tokenString, Cliams, func(t *jwt.Token) (interface{}, error) {
		return jwtKey, nil
	})
	return token, Cliams, err
}
```

### 7.2 **对应的model**

```go
// Token Claims

type Claims struct {
	UserId uint
	jwt.StandardClaims
}
```



## 八、Cors库

> 这是我用来解决跨域问题的库

安装这个库

```shell
go get -u github.com/gin-contrib/cors
```

```go
config := cors.DefaultConfig()
	config.AllowHeaders = []string{
		"Authorization",
		"Access-Control-Allow-Headers",
		"Access-Control-Allow-Origin",
		"Access-Control-Allow-Methods",
		"Content-Type",
		"Content-Length",
		"Accept-Encoding",
		"X-CSRF-Token",
		"Accept-Language",
		"Accept-Encoding",
		"Cookie",
	} // 允许的请求头
	config.AllowOrigins = []string{"http://127.0.0.1:8848", "http://localhost:63343", "http://localhost:63342"} // 允许的域名或 IP 地址
	config.AllowCredentials = true
	config.AddAllowMethods("OPTIONS")// 同意欲请求
	r.Use(cors.New(config))
```





## 九、Viper库

> 用于读取配置文件

安装这个库

```shell
go get -u github.com/spf13/viper
```

**先创建配置文件**--->我用的`yml`配置文件

> 这配置的mysql的相关信息

```yaml
server:
  prot: 8080
datasource:
  driverName : mysql
  host : 127.0.0.1
  prot: 3306
  database: learn_gorm
  username : root
  password : 17078151675abc
  charset: utf8
```

**读取配置文件内容**

```go
// 读取配置文件

func InitConfig() {
	workDir, err := os.Getwd()
	if err != nil {
		panic(err)
	}
	viper.SetConfigName("application")
	viper.SetConfigType("yml")
	viper.AddConfigPath(workDir + "/config")
	err = viper.ReadInConfig()
	if err != nil {

	}
}
```

**其他文件怎么使用**

```go
// 连接数据库
func DatabaseConnection(DB *gorm.DB) *gorm.DB {
	driverName := viper.GetString("datasource.driverName") // 数据库类型
	username := viper.GetString("datasource.username")     // 账号
	password := viper.GetString("datasource.password")     // 密码
	host := viper.GetString("datasource.host")
	prot := viper.GetString("datasource.prot")       // ip和端口
	dbname := viper.GetString("datasource.database") // 数据库名称
	charset := viper.GetString("datasource.charset")
	aigis := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=%smb4&parseTime=True&loc=Local",
		username,
		password,
		host,
		prot,
		dbname,
		charset,
	)
	var err error
	DB, err = gorm.Open(driverName, aigis)
	if err != nil {
		panic(err)
	}
	// 自定义表名
	//DB.Table("userdatabase").CreateTable(&models.UserDatabase{})
	DB.AutoMigrate(
		&models.UserDatabase{},
		&models.UserDto{},
		&models.IpRecord{},
		&models.BlogComment{},
		&models.BlogArticle{},
		&models.Claims{},
	)
	return DB
}

func GetDB() *gorm.DB {
	return db
}
```

