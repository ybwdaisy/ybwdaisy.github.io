### JavaScript 类型转换

首先从一系列让`JavaScript`初学者抓狂的运算说起。

```javascript
1 + {}
{} + 1
[] + {}
{} + []
[] + []
{} + {}
```

能全部答对上面的运算结果，不必浪费时间继续阅读本文了。
如果对某一些的结果还不确定，请慢慢往下看。

上面列的所有运算，需要理清以下两点：
- `+`和`{}`的解析规则；
- `JavaScript`是如何进行类型转换的；

### `+`和`{}`的解析规则

#### `+`符号
- 数字的加法运算，二元运算符
- 字符串的连接运算，二元运算符
- 正号，一元运算符，强制转换其他类型的运算元为数字类型

#### `{}`符号
- 对象的字面量
- 区块语句

#### 加法运算规则
首先，我们来了解，`+`符号作为加号二元运算符的运算规则

1. 使用`ToPrimitive`转换左右运算元为原始数据类型值；
2. 在第`1`步转换后，如果有运算元出现原始数据类型为“字符串”类型时，则另一运算元强制转换为字符串，然后做字符串连接运算；
3. 其他情况时，所有运算元都转换为原始数据类型为“数字”类型，然后做数字相加运算；

#### `ToPrimitive`运算

> ToPrimitive(input, PreferredType?)

`input`代表代入的值，而`PreferredType`可以是数字(`Number`)或字符串(`String`)其中一种，这会代表“优先的”、“首选的”的要进行转换到哪一种原始类型，转换的步骤会依这里的值而有所不同。
但如果没有提供这个值也就是预设情况，则会设置转换的`hint`值为`default`。这个首选的转换原始类型的指示(`hint`值)，是在作内部转换时由`JS`视情况自动加上的，一般情况就是预设值。

##### 当`PreferredType`为数字(`Number`)时

转换步骤为：
1. 如果`input`是原始数据类型，则直接返回`input`。
2. 否则，如果`input`是个对象时，则调用对象的`valueOf()`方法，如果能得到原始数据类型的值，则返回这个值。
3. 否则，如果`input`是个对象时，调用对象的`toString()`方法，如果能得到原始数据类型的值，则返回这个值。
4. 否则，抛出`TypeError`错误。

##### 当`PreferredType`为字符串(`String`)时

上面的步骤2与3对调，转换步骤为：
1. 如果`input`是原始数据类型，则直接返回`input`。
2. 否则，如果`input`是个对象时，则调用对象的`toString`方法，如果能得到原始数据类型的值，则返回这个值。
3. 否则，如果`input`是个对象时，调用对象的`valueOf()`方法，如果能得到原始数据类型的值，则返回这个值。
4. 否则，抛出`TypeError`错误。

有几点值得注意：
1. 数字是预设的`PreferredType`；
2. 在一般情况下，加号运算中的对象要作转型时，都是先调用`valueOf`再调用`toString`;
3. 例外：Date 对象、Symbol 对象
    - Date 对象的预设首选类型是字符串(String)；
    - Symbol 能显示转为字符串 String(Symbol) 和布尔值，不能转为数字；

##### 模拟代码

```javascript
a + b:
    pa = ToPrimitive(a)
    pb = ToPrimitive(b)

    if(pa is string || pb is string)
       return concat(ToString(pa), ToString(pb))
    else
       return add(ToNumber(pa), ToNumber(pb))
```

##### `V8`实现源码

```javascript
// ECMA-262, section 9.1, page 30. Use null/undefined for no hint,
// (1) for number hint, and (2) for string hint.
function ToPrimitive(x, hint) {
    if (!IS_SPEC_OBJECT(x)) return x;
    if (hint == NO_HINT) hint = (IS_DATE(x)) ? STRING_HINT : NUMBER_HINT;
    return (hint == NUMBER_HINT) ? DefaultNumber(x) : DefaultString(x);
}
```
- 可以看出，`Date`类型的对象预设值为字符串(`String`)。

`DefaultNumber`和`DefaultString`方法

```javascript
// ECMA-262, section 8.6.2.6, page 28.
function DefaultString(x) {
   if (!IS_SYMBOL_WRAPPER(x)) {
       if (IS_SYMBOL(x)) throw MakeTypeError(kSymbolToString);
       var toString = x.toString;
       if (IS_SPEC_FUNCTION(toString)) {
           var s = % _CallFunction(x, toString);
           if (IsPrimitive(s)) return s;
       }

       var valueOf = x.valueOf;
       if (IS_SPEC_FUNCTION(valueOf)) {
           var v = % _CallFunction(x, valueOf);
           if (IsPrimitive(v)) return v;
       }
   }
   throw MakeTypeError(kCannotConvertToPrimitive);
}
```

```javascript
// ECMA-262, section 8.6.2.6, page 28.
function DefaultNumber(x) {
    var valueOf = x.valueOf;
    if (IS_SPEC_FUNCTION(valueOf)) {
        var v = % _CallFunction(x, valueOf);
        if (IS_SYMBOL(v)) throw MakeTypeError(kSymbolToNumber);
        if (IS_SIMD_VALUE(x)) throw MakeTypeError(kSimdToNumber);
        if (IsPrimitive(v)) return v;
    }
    var toString = x.toString;
    if (IS_SPEC_FUNCTION(toString)) {
        var s = % _CallFunction(x, toString);
        if (IsPrimitive(s)) return s;
    }
    throw MakeTypeError(kCannotConvertToPrimitive);
}
```
至此，我们弄清楚了`ToPrimitive`内部运算规则。

### 运算例子解析

#### `1 + {}`
解析为数字`1`与空对象`{}`进行加运算，按照上面的规则，空对象`{}`按照`valueOf` -> `toString`顺序，`valueOf`返回对象本身，所以调用`toString`返回`"[object Object]"`为字符串，根据规则，有一个为字符串，则进行字符串连接运算，数字`1`强制转换为字符串`"1"`，最终结果为`"1[object Object]"`。

#### `{} + 1`
这个例子有坑，因为对于浏览器引擎来说，它们会认为以花括号开头`{`的，是一个 **区块语句** 的开头，而不是一个对象字面量的语句，所以会认为略过第一个`{}`。所以这个例子相当于`+1`，加号运算会直接变为一元正号运算，对数字`1`强制转换为数字类型，结果为`1`。

#### `[] + {}`
将`[]`和`{}`分别调用`ToPrimitive`方法，返回`""`和`"[object Object]"`，然后做字符串拼接得到`"[object Object]"`。

#### `{} + []`
开始的`{}`被解析为区块语句而略过，相当于`+[]`，而`[]`转为原始数据类型结果为`""`，强制转换`""`为数字类型，结果为`0`；

#### `[] + []`
将`[]`和`{}`分别调用`ToPrimitive`方法，返回`""`和`""`，然后做字符串拼接得到`""`。

#### `{} + {}`
这个例子有争议，对于`Firefox`和`Edge`浏览器来说，他们一以贯之的将第一个`{}`作为区块语句略过，而对于`V8`引擎系列(`Chrome`、`Node.js`等)则将第一个`{}`解析为对象字面量。
这样导致结果不一致，`Firefox`等解析语句为`+{}`，对空对象`{}`强制转为数字类型，即为`+"[object Object]"`，将非空字符串转换为数字类型，结果为`NaN`。
`Chrome`等解析语句为两个空对象做加运算，结果也比较好理解：`"[object Object][object Object]"`。

### 复杂一点的例子

#### `[] == ![]`
这是一个不严格等于运算，我们来看转换过程。

```javascript
// 第一步，转换右边，根据上述原始数据类型转换规则表，
// 所有对象强制转 Boolean 类型都是 true，所以 ![] 为 false
ToPrimitive(![]) >> ToPrimitive(!ToBoolean([])) >>
ToPrimitive(!true) >> ToPrimitive(false) >> 0
// 第二步，转换左边
ToPrimitive([]) >> ""
// 第三步，判断
"" == 0 >> ToNumber("") == 0 >> 0 == 0 >> return true
```

#### `++[[]][+[]] + [+[]]`
进一步，来看这个例子。
很明显，根据运算符优先级，这个表达式可以用`+`分隔为左右两个部分`++[[]][+[]]`和`[+[]]`。

##### 先看右边`[+[]]`
可以看出这是一个数组里面有一个元素`+[]`，而`+[]`即将`[]`强制转换为数字类型，所以等于`+""`，结果为`0`。
综上，右边表达式转换为`[0]`。


##### 再看左边`++[[]][+[]]`
我们来一步步拆解， 根据对右边表达式的转换，这个表达式可以等同看做`++([[]][0])`，`++`后面又可以看做数组去第`1`个元素，表达式转换为`++[]`。
但是当我们去浏览器执行`++[]`时，报错了：***<font color=red>Uncaught ReferenceError: Invalid left-hand side expression in prefix operation</font>***。
吓得我赶紧去看`++`的语法，原来`++`的运算是一种引用运算，即`++[]`应该转换为：
```javascript
var ref = []
ref = ref + 1
```
所以`++[]`转换的正确姿势为`[] + 1`。

##### 加法运算
左右进行相加得到：`[] + 1 + [0]`。
根据`ToPrimitive`运算规则，`[] + 1 + [0] === "" + 1 + [0] === "1" + [0] === "10"`。
