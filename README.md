<!-- TOC -->

- [1. 目录](#1-目录)
    - [1.1 前端必备](#11-前端必备)
        - [1.1.1 this指向总结](#111-this指向总结)
            - [1.1.1.1 预备知识](#1111-预备知识)
                - [1.1.1.1.1 this的绑定方式](#11111-this的绑定方式)
                - [1.1.1.1.2 new三步走](#11112-new三步走)
            - [1.1.1.2 全局作用域this-默认绑定](#1112-全局作用域this-默认绑定)
            - [1.1.1.3 函数中的this](#1113-函数中的this)
                - [1.1.1.3.1 普通函数中的this指向-隐式绑定](#11131-普通函数中的this指向-隐式绑定)
                - [1.1.1.3.2 箭头函数中的this指向](#11132-箭头函数中的this指向)
                - [1.1.1.3.3 构造函数中的this指向](#11133-构造函数中的this指向)
            - [1.1.1.4 改变this指向-显示绑定](#1114-改变this指向-显示绑定)
                - [1.1.1.4.1 call和apply和bind的详解](#11141-call和apply和bind的详解)
                - [1.1.1.4.2 apply和call的区别](#11142-apply和call的区别)
                - [1.1.1.4.3 apply和call的用法](#11143-apply和call的用法)
                - [1.1.1.4.4 call和bind的区别](#11144-call和bind的区别)
        - [1.1.2 原型与原型链](#112-原型与原型链)
            - [1.1.2.1 prototype与proto 与constructor](#1121-prototype与proto与constructor)
            - [1.1.2.2 实例与原型](#1122-实例与原型)
            - [1.1.2.3 继承](#1123-继承)
            - [1.1.2.4 instanceof](#1124-instanceof)
    - [1.2 拓展](#12-拓展)
        - [1.2.1 IIFE](#121-iife)
        - [1.2.2 BFC](#122-bfc)
<!-- /TOC -->

# 1. 目录
## 1.1. 前端必备
### 1.1.1. this指向总结
this是在函数执行的过程中自动创建的一个指向一个对象的内部指针。确切的说，`this并不是一个对象，而是指向一个已经存在的对象的指针`，也可以认为是this就是存储了某个对象的地址。
this的指向不是固定的，会根据调用的不同，而指向不同的地方。
#### 1.1.1.1. 预备知识
##### 1.1.1.1.1. this的绑定方式
>绑定方式：默认绑定、隐式绑定、显式绑定、硬绑定、new绑定
优先级：new绑定>显式绑定>隐式绑定>默认绑定
##### 1.1.1.1.2. new三步走

```
    var funA = function() {
        this.name = 'wjc'
    }
    var obj = new Object();
    obj._proto_ = funA.prototype;
    funA.call(obj);
        
```
#### 1.1.1.2. 全局作用域this-默认绑定
>全局作用域中使用this，也就是说不在任何的函数内部使用this，那么这个时候this就是指的 Window

```
    <script type="text/javascript">
    	//全局作用域中的this
        //向this对象指代的对象中添加一个属性 num， 并让属性的值为100
        this.num = 100;
        // 因为this就是window，所以这时是在修改属性num的值为200
        window.num = 200;
        alert(this === window);  // true         this就是指向的window对象，所以是恒等 
        alert(this.num);    //200   
        alert(window.num);  //200
    </script>
```
#### 1.1.1.3. 函数中的this
##### 1.1.1.3.1. 普通函数中的this指向-隐式绑定
    >非构造函数中this指向的就是 调用这个方法的那个对象
```
<script type="text/javascript">
      function test() {
          alert(this == window);
          this.age = 20;
      }
      test();  //其实是 window.test();  所以这个时候test中的this指向window
  </script>

```
```
 <script type="text/javascript">
      var p = {
          age : 20,
          sex : "男",
          sayAge: function (argument) {           
              alert(this.age);
          }
      }
      p.sayAge(); //调用对象p的方法sayAge()  所以这个时候this指的是 p 这个对象
  </script>
```
```
  <script type="text/javascript">
      var p = {
          age : 20,
          sex : "男",
          sayAge: function (argument) {
              alert(this.age);
              alert(this === p);  //true
          }
      }
      var again = p.sayAge;   //声明一个变量(方法)，把p的方法复制给新的变量
//调用新的方法： 其实是window.again().  
//所以 方法中的this指代的是window对象，这个时候age属性是undefined
// this和p也是不相等的。 
      again();    
  </script>
```
>综上：this的指代和代码出现的位置无关，只和调用这个方法的对象有关。

`注意：在严格模式下（strict mode），全局对象将无法使用默认绑定，即执行会报undefined的错误`
```
    <script type="text/javascript">
        function foo() { 
            "use strict";
        console.log( this.a );
        }

        var a = 2; 
        foo(); // Uncaught TypeError: Cannot read property 'a' of undefined
    </script>
```
##### 1.1.1.3.2. 箭头函数中的this指向
>箭头函数的this绑定只取决于`外层函数或全局`的作用域
```
<script>
var name = 'zhangsan'
var a = {
    name: 'lisi',
    cbk: () => {
        console.log(this.name);
    },
    cbk2() {
        console.log(this.name);
    },
    b: {
        name: 'wangwu',
        cbk: () => {
            console.log(this.name);
        },
        cbk2() {
            console.log(this.name);
        }
    }
}
a.cbk(); // zhangsan
a.cbk2(); // lisi
a.b.cbk(); // zhangsan
a.b.cbk2(); // wangwu
</script>
```
##### 1.1.1.3.3. 构造函数中的this指向
    ```
        <script type="text/javascript"> 
          function Person () {
              this.age = 20;
              return this;  //作为构造函数的时候，这个行代码默认会添加
          }
          var p1 = new Person();  //这个时候 Person中的this就是指的p1
          var p2 = new Person();  //这是时候 Person中的this就是知道p2
      </script>

    ```
>构造方法中的this指代的要未来要创建的那个对象。其实用new调用构造函数的时候，构造函数内部其实有个默认的return this; 这就是为什么this指代那个要创建的对象了。
#### 1.1.1.4. 改变this指向-显示绑定
在JavaScript中，允许更改this的指向。
通过call方法或apply方法
函数A可以成为指定任意对象的方法进行调用 。函数A就是函数对象，每个函数对象中都有一个方法call，`通过call可以让你指定的对象去调用这个函数A`。
ECMAScript 规范给所有函数都定义了call与 apply 两个方法。
call和apply是放在Function的原型对象上的，而不是Object原型对象上！
```
<script type="text/javascript"> 
    var age = 20;
    function showPropertyValue (propertyName) {
        alert(this[propertyName]);
    }
    //使用call的时候，第一个参数表示showPropertyValue中的this的执行，后面的参数为向这个函数传的值。
    //注意一点：如果第一个参数是null，则this仍然是默认的指向。
    showPropertyValue.call(null, "age");
    showPropertyValue.call(this, "age");
    showPropertyValue.call({age:50}, "age")
</script>

```
##### 1.1.1.4.1. call和apply和bind的详解
在this的指向中有第三个方向就是通过call/apply去改变this的指向，这个JavaScript中一个独特的使用形式，其他语言并没有。
##### 1.1.1.4.2. apply和call的区别
ECMAScript 规范给所有函数都定义了 call与apply 两个方法，它们的应用非常广泛，它们的`作用也是一模一样，只是传参的形式有区别而已`。
简单来说，假设有一个函数A，我们调用函数A会直接去A()，那么如果是A()这样直接调用的话，函数体A里面的this就是window了。而我们可以通过call（或apply）去调用，比如：A.call().这样子调用就可以指定A中的this到底是哪个对象。
用call来做比对，里面有两个参数，参数一就是重新指定其中的this是谁，`参数2是属性名`。而事实上，`call与apply也就是参数二的不同这个差异`。
apply
apply 方法传入两个参数：一个是作为函数上下文的对象，简单来说，重新指定函数中的this是谁。另外一个是作为函数参数所组成的数组，`是传入一个数组`。
```
var obj = {
    name : 'ya LV'
}

function func(firstName, lastName){
    console.log(firstName + ' ' + this.name + ' ' + lastName);
}

func.apply(obj, ['A', 'B']);    // A ya LV B

```
call方法第一个参数也是作为函数上下文的对象。与apply没有任何区别。但是后面传入的是一个参数列表，而不是单个数组。
```
var obj = {
    name: 'ya LV'
}

function func(firstName, lastName) {
    console.log(firstName + ' ' + this.name + ' ' + lastName);
}

func.call(obj, 'C', 'D');       // C ya LV D
```
巧用apply
```
let max = Math.max(1, 4, 8, 9, 0)
let arr = [1, 4, 8, 9, 0];
let max = Math.max.apply(null, arr);

```
##### 1.1.1.4.3. apply和call的用法
- 改变this指向
    ```
    var obj = {
      name: 'ya'
  }

  function func() {
      console.log(this.name);
  }

  func.call(obj);       // ya

    ```
- 继承
    ```
     var Person1  = function () {
      this.name = 'wjc';
  }

  var Person2 = function () {
      this.getname = function () {
          console.log(this.name);
      }
      Person1.call(this);
  }
  var person = new Person2();
  person.getname();       // wjc。
  
    ```
- 函数调用
    ```
      function func() {
      console.log('wjc');
  }
  func.call();            // wjc

    ```

##### 1.1.1.4.4. call和bind的区别
- bind 的返回值是函数
```
    var name='HELLO'
    var obj = {
      name: 'wjc'
    }

    function func() {
      console.log(this.name);
    }
    
```
将func的代码拷贝一份，并且永远改变其拷贝出来的函数中的this，为bind第一个参数所指向的对象。把这     份永远改变着this指向的函数返回给func1.
```  var func1 = func.bind(obj);```
   `bind方法不会立即执行，是返回一个改变上下文this的函数，要对这个函数调用才会执行`。
```    func1();  //wjc```
   可以看到，现在这份改变this之后拷贝过来的函数，this的指向永远是bind（）绑定的那个，`不管之后去call重新指向对象，func1都不会改变this的指向。永远！可知，bind比call优先级还高。`
```func1.call({name:'CALL'});   //wjc```

又从func重新拷贝一份永远改变this指向对象为{name:'LI SI'}这个对象的函数，返回给func2.
```    
var func2 = func.bind({name:'LI SI'});
func2();   //LI SI
```
注意，`这里是拷贝一份func2(而不是func)的代码`，而func2之前已经绑定过去永远改变this的指向了，所以这里并不去改变！`还是会输出原来的最先bind的this指向对象。`
```    
var func3 = func2.bind({name:'ZHANG SAN'});
func3();   //LI SI
```

上面对func最初的函数进行了多次绑定，绑定后原函数 func 中的 this 并没有被改变，`依旧指向全局对象window。`因为绑定bind的过程是拷贝代码的一个过程，而不是在其自身上修改。
``` 
window.name = HELLO
func();   //HELLO

```
bind 方法不会立即执行，而是返回一个改变了上下文this后的函数。而原函数func中的 this并没有被改变，依旧指向全局对象 window。

- 参数的使用
```
 function func(a, b, c) {
      console.log(a, b, c);
  }
  var func1 = func.bind(null,'wjc');

  func('A', 'B', 'C');            // A B C
  func1('A', 'B', 'C');           // wjc A B
  func1('B', 'C');                // wjc B C
  func.call(null, 'wjc');       // wjc undefined undefined

```
call 是把第二个及以后的参数作为 func方法的实参传进去，而 func1方法的实参实则是在`bind中参数的基础上再往后排`。也就是说，var func1 = func.bind(null,'yaLV');  bind现有两个参数，第一个是指向，`第二个实参是'yaLV'`，那么就是先让func中的a='yaLV',然后没排满就是让func1('A', 'B', 'C'); 这个参数依次排，现在b='A'，c='B' , 形参已经排完了。也就是输出yaLV A B。。

- 实现原理
```
if (!Function.prototype.bind) {
   Function.prototype.bind = function () {
     var self = this,                        // 保存原函数
     context = [].shift.call(arguments),//保存需要绑定的this上下文
     args = [].slice.call(arguments);    // 剩余的参数转为数组
     return function () {                    // 返回一个新函数
     self.apply(context[].concat.call(args[].slice.call(arguments));
     }
    }
}

```
### 1.1.2. 原型与原型链
#### 1.1.2.1. prototype与proto与constructor
- prototype
任何的函数都有一个属性prototype，这个属性的值是一个对象，这个对象就称为这个函数的原型对象。
```
function Person() {

}
// prototype是函数才有的属性
Person.prototype.name = 'wjc';

var person1 = new Person();  //person1是Person构造函数的实例
var person2 = new Person();  //person2是Person构造函数的实例
console.log(person1.name) // wjc
console.log(person2.name) // wjc

```
>我们创建的每一个对象都会继承另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型”继承”属性。

- __proto__
当使用构造函数创建对象的时候, 新创建的对象会有一个属性__proto__,这个属性会指向该对象的原型。
```
function Person() {
    
}
var person1 = new Person();// 具体可查看本章new三步走文章
console.log(person1.__proto__ === Person.prototype); //true

```
>我们在开发时创建的对象都是通过 new Object()来创建的
```
var obj = {} // 其实是执行了 new Object();
var arr = [] // 其实是执行了 new Array(),Array的原型又继承了Object
```
- constructor
每个原型都有一个constructor属性指向关联的构造函数。注意是构造函数本身，而不是实例。
```
function Person() {

}
console.log(Person === Person.prototype.constructor); //true

```
![关系](https://user-gold-cdn.xitu.io/2019/2/22/1691328a8c90abf7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
综上可以得出
```
function Person() {

}
var person1 = new Person();
//对象的__proto__属性: 创建对象时自动添加的, 默认值为构造函数的prototype属性值（很重要）
console.log(person1.__proto__ == Person.prototype) // true
console.log(Person.prototype.constructor == Person) // true

// 顺便学习一个ES5的方法,可以获得对象的原型
console.log(Object.getPrototypeOf(person1) === Person.prototype) //true

```
#### 1.1.2.2. 实例与原型
当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层为止。
例：
```
function Person() {

}
//往Person对象原型中添加一个属性
Person.prototype.name = 'name';
//创建一个person1实例对象
var person1 = new Person();
//给创建的实例对象person1添加一个属性
person1.name = 'name of this person1';
//查找person1.name，因为本身实例对象有，那么就找到了自身实例对象上的属性和属性值
console.log(person1.name) // name of this person1
//删除实例对象的属性和属性值
delete person1.name;
//查找属性name，在实例对象自身上找不到，通过proto指向往原型链上找，在原型对象中找到
console.log(person1.name) // name

```
>实例对象的proto指向构造函数的prototype。也就是说，Person.prototype这个原型对象（实例原型）是通过Object这个构造函数new出来的，也就是Person.prototype这个原型对象是Object的实例，所以这个实例会有proto属性指向Object构造函数的原型对象Object.prototype。

![关系](https://user-gold-cdn.xitu.io/2019/2/22/1691328a8ca680e6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

`那Object.prototype的原型呢？null，嗯，就是null。所以查到Object.prototype就可以停止查找了。`

- 原型链
![关系](https://user-gold-cdn.xitu.io/2019/2/22/1691328aac644da7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
>图中由相互关联的原型组成的链状结构就是原型链，也就是蓝色的这条线，都是通过proto属性进行查找的，因为proto指向构造函数本身。
#### 1.1.2.3. 继承
例：
```
// 创建一个基类
function Animal(name) {
    this.name = name;
}
Animal.prototype.eat = function(food) {
    console.log(this.name + '正在吃：' + food);
}
//创建一个派生类
function cat(name) {
    Animal.call(this, name); //利用call实现私有属性继承
}
cat.prototype = Object.create(Animal.prototype); // Object.create() 浅拷贝 可以防止基类方法被重写
cat.prototype.constructor = cat; //constructor指向
cat.prototype.eat = function() {
    console.log(2222)
}
let test = new cat('猫');
test.eat('鱼')
```
#### 1.1.2.4. instanceof
例如：A instanceof B
>如果A实例原型链上有构造函数B的原型则返回true
例1:
```
  //一个构造函数Foo
  function Foo() {  }
  //一个f1实例对象
  var f1 = new Foo()
  //翻译:f1是Foo的实例对象吗？
  //还记得我说过，一个实例对象通过proto指向其构造函数的原型对象上。
  //深入翻译：f1这个实例对象通过proto指向是否可以找到Foo.prototype上呢？
  console.log(f1 instanceof Foo) // true
  //这行代码可以得出，沿着proto只找了一层就找到了。
  console.log(f1.__proto__ === Foo.prototype);   // true
  
  //翻译：f1是Object的实例对象吗？
  //深入翻译：f1这个实例对象通过proto指向是否可以找到Object.prototype上呢？
  console.log(f1 instanceof Object) // true
  //这两行代码可以得出，沿着proto找了两层才找到。事实上，f1.__proto__找到了Foo.prototype（Foo构造函数原型上），再次去.__proto__，找到了Object的原型对象上。见下图。
  console.log(f1.__proto__ === Object.prototype);  // false
  console.log(f1.__proto__.__proto__ === Object.prototype);  // true

```
例2:
```
//这个案例的实质还是那句话：一个实例对象通过proto属性指向其构造函数的原型对象上。
//翻译：实例对象Object是否可以通过proto属性（沿着原型链）找到Function.prototype（Function的原型对象）
  console.log(Object instanceof Function) // true
//以上结果的输出可以看到下图，Object.__proto__直接找到一层就是Function.prototype.（Object created by Function）可知Object构造函数是由Function创建出来的，也就是说，Object这个实例是new Function出来的。

  console.log(Object instanceof Object) // true
//很有意思。上面我们已经知道Object这个实例是new Function出来的。也就是Object.proto指向Function.prototype。有意思的是，Function的原型对象又是Object原型对象的一个实例，也就是Function.prototype.proto 指向  Object.prototype .很有意思吧，见下图很更清楚这个“走向”。


  console.log(Function instanceof Function) // true
//由这个可知，可以验证我们的结论：Function是通过new自己产生的实例。                                   	 Function.proto===Function.prototype

  console.log(Function instanceof Object) // true
//Function.proto.proto===Object.prototype (找了两层)

  //定义了一个Foo构造函数。由下图可知，Foo.proto.proto.proto===null  
  function Foo() {}
  console.log(Object instanceof  Foo) // false
//这条语句要验证的是，Object是否可以通过其原型链找到Foo.prototype。
// Object.proto.proto.proto=null  并不会找到Foo.prototype。所以，返回FALSE。

```

- 所有函数都有一个特别的属性:
    - prototype : 显式原型属性
- 所有实例对象都有一个特别的属性:
    - __proto__ : 隐式原型属性
- 显式原型与隐式原型的关系
    - 函数的prototype: 定义函数时被自动赋值, 值默认为{},                    即用为原型对象
    - 实例对象的__proto__: 在创建实例对象时被自动添加,                    并赋值为构造函数的prototype值
    - 原型对象即为当前实例对象的父对象
- 原型链
    - 所有的实例对象都有__proto__属性, 它指向的就是原型对象
    - 这样通过__proto__属性就形成了一个链的结构---->原型链
    - 当查找对象内部的属性/方法时, js引擎自动沿着这个原型链查找
    - 当给对象属性赋值时不会使用原型链, 而只是在当前对象中进行操作
## 1.2. 拓展
### 1.2.1. IIFE
### 1.2.2. BFC
