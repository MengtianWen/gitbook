# 前端代码规则指南

部分内容参考 [AirBnB's style guide](https://github.com/airbnb/javascript).

_使我们的代码更加简洁优雅_

## 内容导航

1. [变量声明](broken-reference)
2. [命名约定](broken-reference)
3. [编码风格](broken-reference)
4. [String](broken-reference)
5. [Object](broken-reference)
6. [Array](broken-reference)
7. [函数](broken-reference)
8. [模块](broken-reference)
9. [操作符](broken-reference)

## 变量声明

*   使用const/let（如果需要重新赋值引用）而不是var

    ```javascript
    // bad
    var a = 'hello';
    var b = 2;
    a = 'world';

    // good
    let a = 'hello';
    const b = 2;
    a = 'world';
    ```
*   变量应先声明再使用，禁止引用任何未声明的变量，除非你明确知道引用的变量存在于当前作用域链上。禁止不带任何关键词定义变量，这样做将会创建一个全局变量，污染全局命名空间，造成程序意料之外的错误

    ```javascript
    // bad, 这会创建一个全局变量
    superPower = new SuperPower();

    // good
    const superPower = new SuperPower();

    // bad, 容易污染外部变量
    let superPower = 'a';
    (function() {
      superPower = 'b';
    })();
    console.log(superPower);

    // good
    let superPower = 'a';
    (function() {
      let superPower = 'b';
    })();
    console.log(superPower);

    // bad, 更常见的情况是这样的，在 for 循环里的 i 将会污染外部的变量 i
    let i = 1;
    (function() {
      for (i = 0; i < 10; i++) {
        console.log('inside', i);
      }

      console.log('outside', i)
    })();
    console.log('global', i);

    // good
    let i = 1;
    (function() {
      // i 的作用域在 for 循环内
      for (let i = 0; i < 10; i++) {
        console.log('inside i', i);
      }

      // 如果真的需要在 for 循环外使用循环变量，应该先定义在外部
      let j;

      for (j = 0; j < 10; j++) {
        console.log('inside j:', j);
      }

      console.log('outside j', j);
    })();
    console.log('global', i);
    ```

[**⬆ back to top**](broken-reference)

## 命名约定

*   使用语义化的命名而不是单个字符

    ```javascript
    // bad
    function t() {
      // do something...
    }

    // good
    function test() {
      // do something...
    }
    ```
*   变量命名规范： 1、普通变量 2、布尔类型：需要一个标识变量含义的前缀，如has、is、wether、can、should等像isVisible 3、数组/集合等复数形式：最后以s或者list等能够标识复数形式的后缀结尾，标识当前变量是复数形式，提高可读性 4、常量全部大写，且用下划线来分隔单词，eg:MAX\_LENGTH=1

    ```javascript
    // bad
    const OBject = {};
    const heres_an_object = {};
    function c() {}
    const u = new user({
      name: 'Evernote';
    });

    // good
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
    const user = new User({
      name: 'Evernote';
    });
    ```
*   函数命名规则： 1、命名方式 : 小驼峰方式 lowerCamelCase ( 构造函数使用大驼峰命名法 ) 2、命名规则 : 前缀为动词，动词 eg：add / update / delete / detail / get

    ```javascript
    // 更新数据
    function updateData(){
        return {};
    }

    // 获取用户信息
    function getUserInfo{
        return {}
    }
    ```
*   css命名： 样式类名使用小写字母，以半角连接符(-)分割 id 采用驼峰式命名 scss / less 中的变量、函数、混合、placeholder 采用驼峰式命名

    ```css
    .heavy{
      font-weight: bold;
    }
    #userModal{
      position: absolute;
    }
    ```
* 文件资源命名： 1、文件名不得含有空格 2、文件名建议只使用小写字母，不适用大写字母 3、名称较长时此阿勇半角连接符(-)分隔 如：src/view/user-manage.vue

[**⬆ back to top**](broken-reference)

## 编码风格

*   不要在开头使用逗号.

    ```javascript
    // bad
    let evernote
      , premium
      , business;

    // good
    let evernote,
        premium,
        business;

    // bad
    const product = {
        name: 'Evernote'
      , type: 'App'
      , category: 'Productivity'
    };

    // good
    const product = {
      name: 'Evernote',
      type: 'App',
      category: 'Productivity'
    };
    ```
*   当你访问一个基本类型时，直接操作它的值。 当你访问一个复杂类型时，直接操作其值的引用

    ```javascript
    let name = '张三';
    let obj = {
      a: 1,
      b: 2
    }
    name = '李四';

    // bad
    obj.a = 4

    // good（不会修改原来的obj的值）
    let obj2 = {...obj}
    obj2.a = 4

    ```
* 使用 rest 语法 ... 代替 arguments
*   默认参数放在最后

    ```javascript
    // bad
    function handleThings(opts = {}, name) {
      // ...
    }

    // good
    function handleThings(name, opts = {}) {
      // ...
    }
    ```

[**⬆ back to top**](broken-reference)

## String

*   字符串使用单引号 `''` 而不是双引号 `""`

    ```javascript
    // bad
    const name = "Eva Note";

    const fullName = "Eva " + this.lastName;

    // good
    const name = 'Eva Note';

    const fullName = 'Eva ' + this.lastName;
    ```

[**⬆ back to top**](broken-reference)

## Object

*   使用字面量的方式创建对象

    ```javascript
    // bad
    const item = new Object();

    // good
    const item = {};
    ```
*   声明对象时，将简写的属性放在前面

    ```javascript
    const anakinSkywalker = 'Anakin Skywalker';
    const lukeSkywalker = 'Luke Skywalker';

    // bad
    const obj = {
      lukeSkywalker,
      episodeThree: 3,
      mayTheFourth: 4,
      anakinSkywalker,
    };

    // good
    const obj = {
      lukeSkywalker,
      anakinSkywalker,
      episodeThree: 3,
      mayTheFourth: 4,
    };
    ```
*   使用对象扩展操作符浅拷贝对象，而不是用Object.assign方法

    ```javascript
    // very bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign(original, { c: 3 }); // 变异的 `original` ಠ_ಠ
    delete copy.a; // 这....

    // bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

    // good
    const original = { a: 1, b: 2 };
    const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

    const { a, ...noA } = copy; // noA => { b: 2, c: 3 }

    //针对数组的拷贝，使用扩展运算符
    // bad
    const len = items.length;
    const itemsCopy = [];
    let i;

    for (i = 0; i < len; i += 1) {
      itemsCopy[i] = items[i];
    }

    // good
    const itemsCopy = [...items];
    ```
*   属性值、对象方法简写

    ```javascript
    // bad
    const value = 1;
    const atom = {
      value: value,
      addValue: function (newVal){
        return atom.value + newVal;
      }
    }

    //good
    const value = 1;
    const atom = {
      value,
      addValue(newVal) {
        return atom.value + newVal;
      }
    }
    ```
*   不要使用 [保留字](http://es5.github.io/#x7.6.1) 作为 keys(对象的键值). 他在IE8中会带来麻烦

    ```javascript
    // bad
    var superman = {
      default: { clark: 'kent' }, //default是保留字
      private: true
    };

    // good
    var superman = {
      defaults: { clark: 'kent' },
      hidden: true
    };
    ```
*   使用可读的同义词代替保留字

    ```javascript
    // bad
    const superman = {
      class: 'alien'
    };

    // bad
    const superman = {
      klass: 'alien'
    };

    // good
    const superman = {
      type: 'alien'
    };
    ```

[**⬆ back to top**](broken-reference)

## Array

*   使用字面量的方式来创建数组

    ```javascript
    // bad
    const items = new Array();

    // good
    const items = [];
    ```
*   使用 Array#push 代替直接赋值来给数组添加项

    ```javascript
    const someStack = [];

    // bad
    someStack[someStack.length] = 'abracadabra';

    // good
    someStack.push('abracadabra');
    ```
*   想要复制数组时用数组的slice方法. [jsPerf](http://jsperf.com/converting-arguments-to-an-array/7)

    ```javascript
    let len = items.length,
        itemsCopy = [],
        i;

    // bad
    for (let i = 0; i < len; i++) {
      itemsCopy[i] = items[i];
    }

    // good
    itemsCopy = items.slice();
    ```
*   使用 Array.from 将一个类数组（array-like）对象转换成一个数组。

    ```javascript
    const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };

    // bad
    const arr = Array.prototype.slice.call(arrLike);

    // good
    const arr = Array.from(arrLike);
    ```
*   使用 Array.from 代替展开符 ... 映射迭代器，因为它避免了创建一个中间数组。

    ```javascript
    // bad
    const baz = [...foo].map(bar);

    // good
    const baz = Array.from(foo, bar);
    ```

[**⬆ back to top**](broken-reference)

## 函数

*   函数声明语句中需要空格

    ```javascript
    // bad
    const f = function(){};
    const g = function (){};
    const h = function() {};

    // good
    const x = function () {};
    const y = function a() {};
    ```
*   箭头函数表达式跨越多个行，用括号将其括起来，以获得更好的可读性

    ```javascript
    // bad
    ['get', 'post', 'put'].map(httpMethod => Object.prototype.hasOwnProperty.call(
        httpMagicObjectWithAVeryLongName,
        httpMethod,
      )
    );

    // good
    ['get', 'post', 'put'].map(httpMethod => (
      Object.prototype.hasOwnProperty.call(
        httpMagicObjectWithAVeryLongName,
        httpMethod,
      )
    ));
    ```
*   如果你的函数只有一个参数并且函数体没有大括号，就删除圆括号。 否则，为了保证清晰和一致性，请给参数加上括号。

    ```javascript
    // bad
    [1, 2, 3].map((x) => x * x);

    // good
    [1, 2, 3].map(x => x * x);

    // good
    [1, 2, 3].map(number => (
      `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
    ));

    // bad
    [1, 2, 3].map(x => {
      const y = x + 1;
      return x * y;
    });

    // good
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });
    ```
*   在箭头函数用隐式 return 时强制将函数体的位置约束在箭头后

    ```javascript
    // bad
    (foo) =>
      bar;

    (foo) =>
      (bar);

    // good
    (foo) => bar;
    (foo) => (bar);
    (foo) => (
        bar
    );
    ```

[**⬆ back to top**](broken-reference)

## 模块

*   使用es6新规范 export和import 导出导入模块的方式进行

    ```javascript
    // good
    import React, { Component } from 'react';
    export default { Component };
    ```
*   不要将导入的内容直接export导出

    ```javascript
    // bad
    export { Component } from 'React';

    // good
    import { Component } from 'React';
    export default Component;
    ```
*   一个模块内只有一个导出项时，请使用export default

    ```javascript
    // bad 
    export a;

    // good
    export default a;
    ```
*   多行引入应该像多行数组和对象字面量一样缩进

    ```javascript
    // bad
    import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';

    // good
    import {
      longNameA,
      longNameB,
      longNameC,
      longNameD,
      longNameE,
    } from 'path';
    ```

[**⬆ back to top**](broken-reference)

## 操作符

* 使用 === 和 !== 而不是 == 和 !=
* 不要使用一元自增自减运算++ --，for循环除外
*   避免三元表达式嵌套, 会降低代码的阅读性

    ```javascript
    // bad
    foo ? baz === qux ? quxx() : foobar() : bar();
    // good
    if (foo) {
      thing = bar;
    } else if (baz === qux) {
      thing = quxx;
    } else {
      thing = foobar;
    }
    ```
*   避免不必要的三元表达式

    ```javascript
    // Bad
    var isYes = answer === 1 ? true : false;

    // Good
    var isYes = answer === 1;
    ```
* 混合使用多种操作符时，要用小括号分组

[**⬆ back to top**](broken-reference)
