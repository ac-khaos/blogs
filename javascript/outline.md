**函数和类也是对象**

```javascript
function func() {};
func.a = 100;
```

*在react组件中可利用这点*

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
