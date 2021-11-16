# 前端基础知识

**函数和类也是对象**

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

#### 关于number

1. **e(n)代表10^n**

```
1e2 = 10^2
2e3 = 2*(10^3)
```

2. **可用_进行分割**

```javascript
const num = 12_34; // 1234
```

3. **非0数除以0不是错误，而是为Infinity**

```javascript
(10 / 0) === Infinity; // true
Number.isNaN(0 / 0); // true
```

4. **0等于-0，但Infinity不等于-Infinity**

```javascript
0 === -0; // true
1 / 0 === 1 / -0 // false
Infinity === -Infinity // false
```
