# 知识点杂记

#### 函数和类也是对象

```javascript
function func() {};
func.a = 100;
```

*\*在react组件中可利用这点*

```javascript
function Layout() {
    return <div>Layout</div>;
}

Layout.Header = function() {
    return <div>Header</div>;
}

Layout.Sidebar = function() {
    return <div>Siderbar</div>;
}

Layout.Content = function() {
    return <div>Content</div>;
}
```

#### number

- **e(n)代表10^n**

```
1e2 = 10^2
2e3 = 2*(10^3)
```

- **可用 _ 进行分割**

```javascript
const num = 12_34; // 1234
```

- **非0数除以0不是错误，而是为Infinity**

```javascript
(10 / 0) === Infinity; // true
Number.isNaN(0 / 0); // true
```

- **0等于-0，但Infinity不等于-Infinity**

```javascript
0 === -0; // true
1 / 0 === 1 / -0 // false
Infinity === -Infinity // false
```

- **浮点数精度丢失问题**

本质原因是二进制表示浮点数问题

参考文档: <https://zhuanlan.zhihu.com/p/269619376>

#### Symbol

```javascript
const a1 = Symbol();
const b1 = Symbol();
a1 === b1; // false

const a2 = Symbol.for("example");
const b2 = Symbol.for("example");
a2 === b2; // true
Symbol.keyFor(a2); // "example"
```

#### 解构赋值

```javascript
const [x, ...rest] = [1, 2, 3, 4]; // rest = [2, 3, 4]

const [value, setValue] = useState();
```

#### 条件式属性访问及条件式调用

```javascript
const a = {};
a?.b; // => undefined
a?.b.c.d; // => undefined

const a1 = { b: {} };
a1.b?.c?.d; // => undefined

// 条件式调用
function test(func) {
    let x = 1;
    func?.(x++); // func && func(x++)
    return x; // 因为短路问题x++不会执行 x=0
}
```

#### 先定义(??)

```javascript
const a = b || 100; // 这种写法的问题在于0、false、""都为假值
// 改写为
const a = b ?? 100;
```

#### void操作符
```javascript
// void先求值自己的操作数，后丢弃自己的操作数
const voidTest = (count) => void count++;
voidTest(1); // undefined
```

#### 数组

1. Array()

```
当使用 new Array() 创建数组时，一个参数表达数组长度，多个参数表示具体元素，故这种方式无法创建一个元素的数组
```

2. Array.of()

```javascript
// 以参数作为数组元素，解决上面问题
Array.of(10); // [10]
```

3. Array.from()
