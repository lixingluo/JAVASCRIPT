# JavaScript的作用域

1. 什么是作用域？  
Scope is the accessibility of variables, functions, and objects in some particular part of your code during runtime. In other words, scope determines the visibility of variables and other resources in areas of your code.

2. 作用域的目的？  
规避变量名称冲突 - JavaScript查找规则，逐级向上，匹配则停，内曾找到，不再向外层查找  
隐藏内部实现 - 隐藏内部实现的模块模式主要依赖闭包

3. 作用域的生成？  
作用域的生成主要依靠词法定义。词法定义就是书写规则，编译器会按照所书写的代码确定出作用域范围。许多语言中有函数作用域和块级作用域，JavaScript 主要使用的是函数作用域

4. 词法作用域  
    ```js
    function a () {
        var aa = 'aa';
        function b () {
            var bb = 'bb'
            console.log(aa, bb)
            c();
        }
        b();
    }

    function c () {
        var cc = 'cc'
        console.log(aa, bb, cc)
    }
    a();
    ```
