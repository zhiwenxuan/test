# 第三章 基本概念

## 3.1 语法

## 3.2 关键字和保留字

### 关键字

```js
break     do       instanceof    typeof
case      else     new           var
catch     finally  switch        while
debugger  function this          with
default   if       throw
delete    in       try
```

### 保留字

```js
abstract    enum      int          short
boolean     export    interface    static
byte        extends   long         super
char        final     native       synchronized
class       float     package      throws
const       goto      private      transient
debugger    implement protected    volatile
double      import    public
```

## 3.3 变量

## 3.4 数据类型

### typeof 操作符

有六种结果：undefined，string， number， boolean， object， function

1. undefined： 值未定义
2. string： 值为字符串，而且不是通过 String 函数构造出来的
3. number： 值为数字，而且不是通过 Number 函数构造出来的
4. boolean： 值为 bool，而且不是通过 Boolean 函数构造出来的
5. object： 值为对象或者 null
6. function： 值为函数

### Undefined 类型

undefined 类型只有一个值，即 undefined。变量已声明但未初始化。

### Null 类型

Null 也是只有一个值的类型，表示空指针对象。

### Boolean 类型

Boolean 类型有两个值： true or false 。对于任何类型使用 Boolean 函数都会返回来 Boolean 类型值。转化规则如下：

```
        数据类型                 转换为true的值                  转换为false的值

        Boolean                    true                           false
        String                  任何非空字符串                      "" (空字符串)
        Number                    任何非0数字                      0 和 NaN
        Undefined                    无                           undefined
        Object                  任何对象（除null）                      null
        Function                 任何值                                无
```

使用 if 语句时，自动按照上面的规则进行转换

### Number 类型

Number 类型使用 IEEE754 标准来表示整数和浮点数

#### 整数

**八进制：**

第一位必须是 0，后面是 0 ～ 7，如果后面的数字大于 7，则八进制失效，变成十进制。另外，严格模式下，八进制会无效且报错。

```js
012; //代表十进制的10
08; //八进制失效，变成十进制的8
```

**十六进制**
前两位是 0x，后面是（0 ～ 9 以及 A ～ F），A ～ F 可以小写

```
0x1A //代表十进制的26
```

#### 浮点数

**语法**：有一个小数点，小数点后面至少有一位数字。

```js
var floatNum1 = 1.1; // 有效
var floatNum2 = 10.0; // 无效，解析成整数10
```

极大或者极小值，可以用 e 表示法（即科学计数法）。

```js
var floatNum = 3.125e7; // 等于 3.125 * 10^7 = 31250000
```

浮点数值的最高精度是 17 位小数, 0.1 + 0.2 = 0.30000000000000004, 这是 IEEE754 的 bug，有舍入误差。所以不要拿浮点数做相等判断，如下：

```js
if (a + b == 0.3) {
  // 不要做这样的测试
  alert("You got 0.3");
}
```

#### 数值范围

最小值保存在 Number.MIN_VALUE, 最大值保存在 Number.MAX_VALUE，对于大多数浏览器，值如下：

```js
Number.MIN_VALUE; // 5e-324
Number.MAX_VALUE; // 1.7976931348623157e+308
```

如果值超过这个范围：正数自动转为 Infinity，负数自动转为-Infinity。Infinity 和 -Infinity 都没法参与计算。使用 isFinite()可以判断值是否是无穷。

#### NaN

表示非数值的值。NaN 与任何值都不相等，包括 NaN 本身。但是可以用 isNaN 来判断值是否是 NaN。isNaN 会尝试将参数转换成数字，如果可以转换则返回 false，否则返回 true。

```js
isNaN(NaN); // true
isNaN(10); // false
isNaN("10"); // false(可以被转换成数值10)
isNaN("blue"); // true(不可以转换成数字)
isNaN(true); //false (可以被转换成数值1)
```

#### 数值转换

##### Number() 函数的转换规则

1. 如果是 Boolean 值，true 和 false 将分别被转换为 1 和 0
2. 如果是数字值，只是简单的传入和返回
3. 如果是 null 值，返回 0
4. 如果是 undefined，返回 NaN
5. 如果是字符串，遵循下列规则：
   - 字符串只包含数字，忽略前导 0，即忽略八进制
   - 字符串包含有效的十六进制格式，如“0xf”，则转换为相同的大小的十进制
   - 字符串为空，转换为 0
   - 字符串包含除上述格式外的字符，转换为 NaN
6. 如果是对象，则调用对象的 valueOf()方法，然后依照前面的规则转换返回的值。如果转换结果是 NaN，则调用对象的 toString()方法，再按照前面的规则转换返回的值。

```js
Number(0xf); // 15
Number(true); // 1
Number(null); // 0
Number(undefined); //  NaN
Number("0011"); // 11
Number("blue"); // NaN

var a = {};
a.__proto__.toString = function() {
  return 10;
};
Number(a); // 首先Number(a.valueOf()) ,值为NaN，接着调Number(a.toString()) ,结果为10
```

##### parseInt() 转换规则

从第一个字符开始解析，如果是非数字或者非负号则停止截取，最后如果得到空字符串则返回 NaN。另外能识别八进制、十进制、十六进制格式。

```js
parseInt("1234blue"); //1234
parseInt(""); //NaN
parseInt("blue1234"); //NaN
parseInt(22.5); //22 有.符号，停止取值
parseInt("070"); // 56：'070'为八进制，转换为十进制的56
parseInt("0xf"); // 15
```

**注意：**

1. 在 ES5 JavaScript 引擎中和严格模式下，parseInt 不能解析八进制值得的值，即 `parseInt('070') //70`。
2. parseInt 的第一个参数要是 String 类型，如果是整型则自动转成 String 类型再处理。
   `parseInt(070) => parseInt(070.toString()) => parseInt('56') => 56`

以上的转换有些混乱，parseInt 可以传入第二个参数来指定要转换的进制模式。

```js
parseInt("10", 2); //2
parseInt("10", 8); //8
parseInt("10", 10); //10
parseInt("0x10", 10); //16
```

##### parseFloat() 转换规则

parseFloat 只解析十进制，没有第二个参数，另外对于'.'符号，第一个有效，其他规则跟 parseInt 一样。

### String 类型

**字符串特点：**字符串一旦创建就不可变

```js
var lang = "Java";
lang = lang + "Script";
```

第二行代码拼接字符串，实现过程：首先创建能容纳 10 个字符的字符串，然后填充“Java” 和 “Script”，最后销毁“Java” 和 “Script”。

#### 转为字符串

##### toString

1. null 和 undefined 没有此方法
2. 数字可以指定要转的进制，默认是十进制。

```js
var num = 10;
num.toString(2); // '1010'
num.toString(8); // '12'
num.toString(10); // '10'
num.toString(16); // 'a'
```

##### String() 函数规则

1. 如果有 toString()方法，则调用改方法
2. 如果是 null，返回‘null’
3. 如果是 undefined，返回‘undefined’

### Object 类型

#### Object 一些属性和方法

1. constructor：保存着用于创建当前对象的函数
2. hasOwnProperty(propertyName): 用于检查给定的属性在当前对象实例中（而不是在实例的原型中）是否存在。
3. isPrototypeOf(object): 用于检查传入的对象是否是传入对象的原型
4. propertyIsEnumerable(propertyName): 用于检查给定的属性是否能够使用 for-in 语句来枚举。
5. toLocalString(): 返回对象的字符串表示，该字符串与执行环境的地区对应。例如：数字的表示
6. toString(): 返回对象的字符串表示
7. valueOf(): 返回对象的字符串、数值或布尔值表示。通常与 toString()方法的返回值相同。

## 3.5 操作符
ECMA-262 描述了一组用于操作数据值得操作符，包括算术操作符、位操作符、关系操作符、相等操作符等。这些操作符适用于很多值，例如字符串，数字值、布尔值，甚至对象。作用于对象时，相应的操作符通常都会调用对象的 `valueOf()` 或者 `toString()` 方法来取值的值。

#### 一元操作符
只能操作一个值得操作符叫做一元操作符

1. 递增/递减操作符
```js
++value(--value) // 前置型, 先执行递增或者递减再和其他操作符进行运算
value++ (value--) // 后置型
```

2. 一元加减操作符
一元加操作符 + ,相当于 Number() 函数对于该值进行转换。
```js
var num;
// +num => Number(num)
```

一元减操作符 - ,相当于 Number() 函数对于该值进行转换，最后再转负。
```js
var num;
// -num => -Number(num)
```

#### 位操作符

1. 按位非（NOT） ~
2. 按位与（AND） &
3. 按位或（OR) |
4. 按位异或（XOR) ^
5. 左移 <<
6. 有符号的右移 >> , 数值向右移动，以0填充空白位置，但保留符号位，正负不变
```js
64 >> 5 // 2
-64 >> 5 // -2
```
7. 无符号右移 >>>，首先会把符号位看做数值位，不保留符号位，都是正数。数值向右移动，以0填充空白位置，如果是负数，会变成正数。
```js
64 >> 5 // 2
-64 >> 5 // 134217726
// -64 会看做正数 11111111111111111111111111000000 ，然后 移动5位 00000111111111111111111111111110
```

#### 布尔操作符

1. 逻辑非 !
2. 逻辑与 &&
3. 逻辑或 ||

#### 乘性操作符

1. 乘法 *
2. 除法 /
3. 求模(余数) %

#### 加性操作符

1. 加法 +
2. 减法 -

#### 关系操作符

1. 小于 （<）
2. 大于 （>）
3. 小于等于 (<=)
4. 大于等于 (>=)

#### 相等操作符

1. 相等和不相等
先转换操作数，再做比较它们的相等性

2. 全等和不全等
直接比较, 不做类型转换。只有NaN 比较特殊，所以建议相等操作符都使用全等和不全等

```js
NaN === NaN // false

// 判断两个数是否相等
cosnt isEqual = function (a, b) {
  // NaN 特殊处理
  return a === b || (a !== NaN && b !== NaN);
}
```

#### 条件操作符
```js
variable = boolean_expression ? true_value : false_value;
```

#### 赋值操作符

1. 等 =
2. 乘/赋值 (*=)
3. 除/赋值 (/=)
4. 模/赋值 (%=)
5. 加/赋值 (+=)
6. 减/赋值 (-=)
7. 左移/赋值 (<<=)
8. 有符号右移/赋值 (>>=)
9. 无符号右移/赋值 (>>>=)

#### 逗号操作符

```js
var num1=1, num2=2, num3=3;
```

## 3.6 语句

#### if 语句
#### do-while 语句
#### while 语句
#### for 语句
#### for-in 语句
```js
for (property in expression) statement

for (var propName in window) {
    console.log(propName);
}
```

#### label 语句
#### break 和 continue 语句
#### with 语句
with 语句的作用是将代码的作用域设置到一个特定的对象中。
```js
with (expression) statement;

// example
var qs = location.search.substring(1);
var hostName = location.hostname;
var url = location.href;

// 优化 =>

with (location) {
  var qs = search.substring(1);
  var hostName = hostname;
  var url = href;
}
```
#### switch 语句

## 3.7 函数

#### 理解参数
传入参数可以和定义的参数不一致，可以通过 `arguments`来获取参数数组。arguments 不是数组，是类数组对象

#### 没有重载
ECMAScript 函数不能像传统意义上那样实现重载，像Java 中，可以为一个函数编写两个定义，只要这个定义的签名（接收参数的类型和数量）不同即可。ECMAScript 函数没有签名，因为其参数是有包含零或者多个值组成来表示。 
