# 高级编程

### 元编程（MP）

狭义上，<span style="color: #0098e1; font-weight: 600;">MetaProgramming</span> 指编写能改变语言语法特性或者运行时特性的程序。

换言之，一种语言本来做不到的事情，通过你编程来修改它，扩展程序自身的能力。

元编程的本质是代码“书写”代码的能力。

```javascript
// eval就是一个典型的元编程api
const word = "word";
const reg = eval(`/${word}/g`); // regexp(/word/g)
reg.test("123word123"); // true
```

##### 相关拓展

- __宏__

计算机科学里的宏（Macro)，是一种批量处理的称谓。一般说来，宏是一种规则或模式，或称语法替换 ，用于说明某一特定输入（通常是字符串）如何根据预定义的规则转换成对应的输出（通常也是字符串)。这种替换在预编译时进行，称作宏展开。

Javascript中是不支持宏的，不过可以通过自定义转换器或者第三方插件进行相关宏的编写，比如 __babel-plugin-macro__

```javascript
import eval from 'eval.macro'
const val = eval`7 * 6`
// or ...
const val = eval("7 * 6");
```

- __DSL__

```
DSL 即 Domain Specific Language，React、Vue 等都算是DSL的体现。
```

#### 对象属性

Javascript允许对对象属性的控制

1. __数据属性__

   - _value_ 值

   - _writable_ 可写

   - _enumerable_ 可枚举

     ```javascript
     // 读取所有可枚举属性
     Object.keys(obj);
     // 读取所有属性(包含不可枚举)
     Reflect.ownKeys(obj);
     ```

   - _configurable_ 可配置

   ```javascript
   // 使用 defineProperties 统一设置
   const a = {};
   Object.defineProperties(a, {
     b: {
       writable: false,
       enumerable: true,
       configurable: true,
       value: 1,
     },
   });
   a.b = 2; // 因为 writable 为 false 所以修改不会生效，严格模式下会报错
   a.b; // 1
   
   // 也可以使用 defineProperty 单独设置
   Object.defineProperty(a, "b", {
     value: 2,
     writable: false,
     enumerable: false,
     configurable: false,
   });
   
   a.b; // 2
   Object.keys(a); // []
   
   // 再次定义 descriptor 时会报错，因为 configurable 已被设置为 false
   Object.defineProperty(a, "b", {
     value: 3,
     writable: true,
     enumerable: true,
     configurable: true,
   });
   
   ```

   

2. __访问器属性__

   - _get_ 读取操作
   - _set_ 写入操作
   - _enumerable_ 可枚举
   - _configurable_ 可配置

   ```javascript
   const a = {};
   Object.defineProperty(a, "b", {
     get() {
       console.log("defineProperty's get!");
       return 1;
     },
     set(value) {
       console.log("defineProperty's set!", value);
       return value;
     },
     enumerable: true,
     configurable: true,
   });
   
   a.b = 2; // defineProperty's set! 2
   a.b; // 1 defineProperty's get!
   ```

3. __可扩展性__

   - *Reflect.preventExtensions()* 不可扩展

     只是简单让对象不可扩展

     ```javascript
     const o = { a: 1, b: { c: "abc" } };
     
     Object.preventExtensions(o);
     o.a = 2; // 有效
     o.b.c = "cba"; // 有效
     o.d = "1234"; // 无效
     console.log(o); // { a: 2, b: { c: 'cba' } }
     Reflect.deleteProperty(o, "a"); // 有效
     Reflect.deleteProperty(o.b, "c"); // 有效
     console.log(o); // { b: {} }
     ```

     

   - *Object.seal()* 封存

     除了让对象不可扩展，同时也会让对象自有属性也不可扩展

     相当于在 preventExtensions 上添加 configurable: false

     ```javascript
     const o = { a: 1, b: { c: "abc" } };
     
     Object.seal(o);
     o.a = 2; // 有效
     o.b.c = "cba"; // 有效
     o.d = "1234"; // 无效
     console.log(o); // { a: 2, b: { c: 'cba' } }
     Reflect.deleteProperty(o, "a"); // 无效
     Reflect.deleteProperty(o.b, "c"); // 有效
     console.log(o); // { a: 2, b: {} }
     ```

   - *Object.freeze()* 冻结

     除了让对象及对象自有属性也不可扩展，同时也会把所以自有对象属性变为只读

     相当于在 seal上添加 writable: false

     ```javascript
     const o = { a: 1, b: { c: "abc" } };
     
     Object.freeze(o);
     o.a = 2; // 无效
     o.b.c = "cba"; // 有效
     o.d = "1234"; // 无效
     console.log(o); // { a: 1, b: { c: 'cba' } }
     Reflect.deleteProperty(o, "a"); // 无效
     Reflect.deleteProperty(o.b, "c"); // 有效
     console.log(o); // { a: 1, b: {} }
     ```

   ```
   总结:
   1. 对于扩展性的操作是不可逆的
   2. 对于子对象的属性(即有访问器属性)是没有任何影响的，即影响范围为自有属性
   3. 都有返回值，Object.freeze(Object.seal(Object.preventExtensions(obj)))
   ```

   

   

### 函数式编程（FP）

函数式编程（Function Programming）属于编程范式的一种。