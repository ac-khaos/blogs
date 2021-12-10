# 元编程（MP）

狭义上，<span style="color: #0098e1; font-weight: 600;">Meta Programming</span> 指编写能改变语言语法特性或者运行时特性的程序。

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


#### 代理对象 Proxy

元编程特性之一，可以操作对象的基础行为。

通过代理的方式可以很方便的控制对象的操作行为，vue3中监听器的原理就是使用Proxy。

##### New Proxy(target, handler)

创建一个可代理实例

```javascript
// 代理对象
const obj = { x: 1, y: 2 };

const proxyObj = new Proxy(obj, {
  get(target, property) {
    console.log("proxy getter!");
    return target[property];
  },
  set(target, property, value) {
    console.log("proxy setter!");
    return (target[property] = value);
  },
  deleteProperty(target, property) {
    console.log("proxy deleteProperty!");
    return Reflect.deleteProperty(target, property);
  },
});

proxyObj.y = 3; // proxy setter!
proxyObj.y; // proxy getter!
Reflect.deleteProperty(proxyObj, "y"); // proxy deleteProperty!
console.log(obj, proxyObj); // { x: 1 } { x: 1 }

// 代理class
class Person {
  name = "";
  age = 0;
  sex = 0;

  constructor(p) {
    this.name = p.name;
    this.age = p.age;
    this.sex = p.sex;
  }
}

const proxyPerson = new Proxy(Person, {
  construct(target, args) {
    console.log("proxy constructor!");
    return new target(...args);
  },
});

const person = new proxyPerson({ name: "xiaojun", age: 28, sex: 1 }); // proxy constructor!
console.log(person); // Person { name: 'xiaojun', age: 28, sex: 1 }
```

##### Proxy.revocable

创建一个可撤销代理实例

<span style="color: #0098e1;">当使用一些不信任的第三方库时可以通过 revoke 方法撤销当前代理</span>

```javascript
// 通过 revoke 可随时撤销代理
const func = function () {
  return 100;
};

const revocableFunc = Proxy.revocable(func, {});

function librarySomeFunction(someFunc) {
  console.log(someFunc());
}

librarySomeFunction(revocableFunc.proxy); // 100
revocableFunc.revoke(); // 撤销代理后 revocableFunc.proxy 将不再能使用
librarySomeFunction(revocableFunc.proxy); // TypeError
```

##### Proxy  Handler 可代理范围

| 操作                     | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| get                      | 在读取代理对象的某个属性时触发该操作，比如在执行 proxy.foo 时 |
| set                      | 在给代理对象的某个属性赋值时触发该操作，比如在执行 proxy.foo = 1 时 |
| getPrototypeOf           | 在读取代理对象的原型时触发该操作，比如在执行 Object.getPrototypeOf(proxy) 时 |
| setPrototypeOf           | 在设置代理对象的原型时触发该操作，比如在执行 Object.setPrototypeOf(proxy, null) 时 |
| isExtensible             | 在判断一个代理对象是否是可扩展时触发该操作，比如在执行 Object.isExtensible(proxy) 时 |
| preventExtensions        | 在让一个代理对象不可扩展时触发该操作，比如在执行 Object.preventExtensions(proxy) 时 |
| getOwnPropertyDescriptor | 在获取代理对象某个属性的属性描述时触发该操作，比如在执行 Object.getOwnPropertyDescriptor(proxy, "foo") 时 |
| defineProperty           | 在定义代理对象某个属性时的属性描述时触发该操作，比如在执行 Object.defineProperty(proxy, "foo", {}) 时 |
| has                      | 在判断代理对象是否拥有某个属性时触发该操作，比如在执行 "foo" in proxy 时 |
| deleteProperty           | 在删除代理对象的某个属性时触发该操作，比如在执行 delete proxy.foo 时 |
| ownKeys                  | 在获取代理对象的所有属性键时触发该操作，比如在执行 Object.getOwnPropertyNames(proxy) 时 |
| apply                    | 在调用一个目标对象为函数的代理对象时触发该操作，比如在执行 proxy() 时 |
| construct                | 在给一个目标对象为构造函数的代理对象构造实例时触发该操作，比如在执行new proxy() 时 |
