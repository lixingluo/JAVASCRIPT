# JavaScript的作用域

1. 什么是作用域？  
Scope is the accessibility of variables, functions, and objects in some particular part of your code during runtime. In other words, scope determines the visibility of variables and other resources in areas of your code.

2. 作用域的目的？  
规避变量名称冲突 - JavaScript查找规则，逐级向上，匹配则停，内曾找到，不再向外层查找  
隐藏内部实现 - 隐藏内部实现的模块模式主要依赖闭包

3. 作用域的生成？  
作用域的生成主要依靠词法定义。词法定义就是书写规则，编译器会按照所书写的代码确定出作用域范围。许多语言中有函数作用域和块级作用域，JavaScript主要使用的是函数作用域

4. 词法作用域(又称静态作用域): **Lexical Scope**
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

5. 函数作用域: **Function Scope**
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
    
6. 块级作用域: **Block Scope**
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
    注意：  
    导致不同输出结果的原因是因为暂时性死区，简写是TDZ。  
    解释：  
    When a JavaScript engine looks through an upcoming block and finds a variable declaration, it either hoists the declaration to the top of the function or global scope (for **var**) or places the declaration in the TDZ (for **let** and **const**). Any attempt to access a variable in the TDZ results in a runtime error. That variable is only removed from the TDZ, and therefore safe to use, once execution flows to the variable declaration.  
    翻译：  
    当JavaScript引擎浏览即将出现的代码块并查找变量声明时，它既把声明提升到了函数的顶部或全局作用域（对于**var**），也将声明放入暂时性死区（对于**let**和**const**）。任何想要访问暂时性死区中变量的尝试都会导致运行时错误。只有当执行流到达变量声明的语句时，该变量才会从暂时性死区中移除，可以安全访问。  
    记住：  
    let和var声明提前引用导致的结果的区别仅仅是因为在编译器在词法分析阶段，将块级作用域变量做了特殊处理，用暂时性死区把它们包裹住，保持块级作用域的特性。
    
7. 全局作用域: **Global Scope**  
    **全局作用域就是运行时的顶级作用域，在所有函数之外定义的变量都归属于全局作用域**。浏览器中的全局作用域就是windows，Node.js里就是global对象。全局作用域运行时会由引擎实现，跟开发者无关。  
    全局作用域里的变量越少越好，具体参考**全局污染**。
    
8. 局部作用域: **Local Scope**  
    **局部作用域就是在全局作用域之下创建的任何内层作用域**，函数和块级作用域都是局部作用域。
    
9. 公有作用域: **Public Scope**  
    **公有作用域存在于模块中，它是提供项目中所有其他模块都可以访问的变量和方法的范围或命名空间**。
    ```
    function a () {
        var aa = 'aa'
        function b () {
            var bb = 'bb'
        }
        b()
        console.log(bb)
    }
    a()
    ```
    Output:
    ```
    Uncaught ReferenceError: bb is not defined
    ```
    因为函数b在运行完后就从执行栈里出栈了，其内存引用(变量bb)也被内存回收机制(GC)清理掉了
    ```
    function a () {
        var aa = 'aa'
        function b () {
            var bb = 'bb'
            return function c () {
                return bb;
            }
        }
        var c = b()
        console.log(c());
    }
    a()
    ```
    Output:
    ```
    bb
    ```
    - 变量c保留了对函数b中返回的函数c的引用，函数c又根据词法作用域法则，能够进入函数b的作用域查找变量，这个引用形成的闭包就被保存在函数a中变量c的值中。
    - 函数a可以在任何想要的时候调用这个闭包来获取函数b里的数据，此时这个被返回的变量bb就成为了暴露在函数a的作用域范围内，定义在函数b里的公有作用域变量。
    ```
    var a = (function a () {
    var aa = 'aa'
    function b () {
        var bb = 'bb'
        console.log(bb)
    }
    return {
        aa: aa,
        b: b
    }
    })()
    console.log(a.aa)
    a.b()
    ```
    - 使用闭包实现了一个单例模块，输出了共有变量 a.aa 和 共有方法也称 API 的 a.b。
  
10. 私有作用域:  **Private Scope**  
    **私有作用域是存在于模块中，只能提供由定义模块直接访问的变量和方法的范围或命名空间**。说白了就是不能直接改动作用域里面定义的变量
    ```
    var a = (function a () {
        var bb = 'bb'
        function b () {
            console.log(bb)
        }
        function c () {
            bb = 'dd'
            console.log(bb)
        }
        return {
            b: b,
            c: c
        }
    })()
    a.b()
    a.c()
    a.b()
    ```
    Output:
    ```
    bb
    dd
    dd
    ```
    注：模块暴露的两个方法b和c，分别实现了一个查找操作和修改操作，间接控制模块中上述两个私有作用域变量。
    
12. 作用域与this：**Scope vs This **  
    作用域不是this对象，证据1，函数调用时，函数内部的this指向的不是函数本身，而是全局对象(最坑无比)。证据2，this是在运行时动态绑定到函数作用域上的。
