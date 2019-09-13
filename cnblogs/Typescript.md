* 微软出品，最终编译成JavaScript
* [TypeScript in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
* 安装
    * 单独安装：npm install -g typescript
    * 在Angular的package.json中定义，如在devDependencies中（以为运行时已经编译成了JavaScript，不再需要引用？）定义"typescript": "~2.5.3"
* 编译
    * 不一定用于Angular，可以单独编写ts文件，编译成js文件，然后直接使用这个js文件
    * 用tsc编译命令：tsc xxx.ts
    * 即使编译有错误，也能生成js文件，只不过有warning说可能代码行为可能会和预期不符
* 语法
    * 会做静态语法检查，可以检查出大部分错误，如参数数量、类型检查
    * 变量
        * var
            * 全局声明
                * var声明在函数体外，所声明的变量为全局变量。
                * var所声明的全局变量会作为window的一个属性，可以使用"."来引用
            * 作用域
                * var变量声明的最大特点是它的作用域为声明语句所在的最近函数体内。
                * var声明变量的作用域为函数体的全部，隐含着两个主要问题：变量提升和循环内变量共享。**因此不推荐再使用var来定义变量。**
                * 变量提升
                    * JavaScript会把函数内的变量声明提升到函数的最顶部。
                    * 变量提升有它的优势，但也常常给我们带来一些难以发现的bug。
                * 循环内变量共享
                * 重复声明
        * let
            * let是ES6新增的特性，也是为了解决var变量声明所存在的一些问题，可以说let是更完美的var。
            * 注意：如果let变量声明在全局，它并不会像var声明的变量一样成为window的一个属性。
            * 可以和JavaScript一样用let定义变量
            * 定义数组：let user = [0, 1, 2];
            * 作用域
                * let变量声明和var最大的不同点就是变量的作用域不一样。var为函数作用域，而let变量声明的为块作用域（block-scoping）。
                * 块作用域会把声明的变量限定在代码块（如使用{}括起来的代码库）或者for循环内，而不是整个函数体。
                * let声明的变量不允许在声明前使用，这样解决了var变量提升引起的问题。
                * 对于循环内的变量，每次循环都会是捕获值的副本作为运算，而不是共享同一个值，解决了var循环内共享变量的问题。
                * let是不允许在同一作用域内重复声明，重复声明会报error: can't re-declare 'x' in the same scope。
        * const
            * const变量声明和let类似，但如它的名字所寓意，它定义的是常量，包含了两层意思：
                * 声明的的变量不能被重复赋值
                * const声明变量是必须立刻赋值
            * 对于const声明的对象，对象本身是不能被赋值覆盖，但是对象的可修改属性是允许被修改值的
    * 函数
        * 参数中需要指定类型，如function greeter(person: string)
    * 接口
        * TypeScript中只要两个类型只要内部结构兼容，那么这两个类型就是兼容的。因此我们可以不用显式的implements语句就可以实现一个接口。如下面例子中的user1直接就可以当做一个Person类型。
    * 类
        * TypeScript支持JavaScript的新feature，如基于类的面向对象编程
        * 类中并不一定要一个个单独定义成员变量，只要在构造函数的参数列表中使用public关键字修饰参数，那么这些参数就会自动被当做同名的成员变量。如下面例子中的constructor(public firstName: string, public middleInitial: string, public lastName: string)。

```
class Student {
    fullName: string;
    constructor(public firstName: string, public middleInitial: string, public lastName: string) {
        this.fullName = firstName + " " + middleInitial + " " + lastName;
    }
}

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user1 = { firstName: "Jane", lastName: "User" };
let user2 = new Student("Jane", "M.", "User");

document.body.textContent = greeter(user1);
document.body.textContent = greeter(user2);
```

* 其他
    * Typescript中也可以放JavaScript代码

```
import { Component } from '@angular/core';
import * as html2canvas from 'html2canvas';

@Component({
  selector: 'xxx-screenshot',
  templateUrl: './screenshot.component.html',
  styleUrls: ['./screenshot.component.scss']
})
export class ScreenshotComponent {

  constructor() { }

  /*********************************************************************************/
  // Function that gets called to make a Screenshot, using html2canvas
  // @param _target = the _target parameter might be NULL, but if not it contains
  //                  the element id that will be used as highest DOM element to take
  //                  the screenshot on.

  makeScreenshot(_target) {
    // check if target element id is defined otherwise, use default (should be sufficient)
    if (_target == null || _target === 'undefined') {
      _target = '_dom_id;
    }

    // html2canvas only copies visible elements to the screenshot canvas. Therefore we set
    // everything below our target element visible at first...
    document.getElementById(_target).style.overflow = 'visible';
    if (document.getElementById('scroll-div-showall-screenshot')) {
      document.getElementById('scroll-div-showall-screenshot').classList.remove('scroll-div');
    }
    // ... take the screenshot...
    html2canvas(document.getElementById(_target)).then(canvas => {
      // ... make it write out to our
      window.open().document.write(`<img src=${canvas.toDataURL()} />`);
      // change the overflow style back to default (= auto) so it doesn't mess up the template
      document.getElementById(_target).style.overflow = 'auto';
      if (document.getElementById('scroll-div-showall-screenshot')) {
        document.getElementById('scroll-div-showall-screenshot').classList.add('scroll-div');
      }
      // unselect screenshot button
      if (document.getElementById('screenshot-button')) {
        document.getElementById('screenshot-button').blur();
      }
    });
  }
}
```