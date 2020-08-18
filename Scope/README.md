# JavaScript的作用域

1. 什么是作用域？  
Scope is the accessibility of variables, functions, and objects in some particular part of your code during runtime. In other words, scope determines the visibility of variables and other resources in areas of your code.

2. 作用域的目的？  
规避变量名称冲突 - JavaScript查找规则，逐级向上，匹配则停，内曾找到，不再向外层查找  
隐藏内部实现 - 隐藏内部实现的模块模式主要依赖闭包

3. 作用域的生成？  
作用域的生成主要依靠词法定义。词法定义就是书写规则，编译器会按照所书写的代码确定出作用域范围。许多语言中有函数作用域和块级作用域，JavaScript 主要使用的是函数作用域

4. 词法作用域(又称静态作用域)  
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
    Output:
    ```js
    aa bb
    Uncaught ReferenceError: aa is not defined
    ```
    注：**不关心函数调用在哪里，只关心函数定义在哪里**

5. 函数作用域
    ```js
    function c () {
        var cc = 'cc'
        console.log(cc)
    }
    function a () {
        var aa = 'aa'
        console.log(aa)
        b();
    }
    function b () {
        var bb = 'bb'
        console.log(aa, bb)
    }
    a();
    c();
    ```
    Output:
    ```js
    aa
    Uncaught ReferenceError: aa is not defined
    ```
    注：JavaScript是动态编程语言，它的动指的是执行时才编译，而不像一般编程语言先编译，后执行。  
    注：而它的静指的是运行前在编译阶段，编译器收集了嵌套层次，根据词法规则，形成了变量查找规则依赖的作用域链。  
    注：运行时，调用栈会根据JavaScript**先进后出的**同步执行机制，使函数像栈的数据结构排成队列先后运行。  
    注：所以上述函数，a的调用出现在c之前，运行时a会先被调用，全局作用域之后会先后进入a的作用域->b的作用域->c的作用域，b内报错了，c也就不会编译执行了。
    
6. 垃圾作用域
    ```
    if (a === 'a') {
        let a = 'a'
        console.log(a)
    } else {
        console.log('a is not defined！')
    }
    ```
    Output:
    ```
    Uncaught ReferenceError: a is not defined
    ```
    ```
    if (a === 'a') {
        var a = 'a'
        console.log(a)
    } else {
        console.log('a is not defined！')
    }
    ```
    Output:
    ```
    a is not defined！
    ```
