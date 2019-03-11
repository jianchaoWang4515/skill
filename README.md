<!-- TOC -->

- [1. 目录](#1-目录)
    - [1.1 前端必备](#11-前端必备)
        - [1.2 this指向总结](#12-this指向总结)
            - [1.2.1 预备知识](#121-预备知识)
                - [1.2.1.1 this的绑定方式](#1211-this的绑定方式)
                - [1.2.1.2 new三步走](#1212-new三步走)
            - [1.2.2 全局作用域this-默认绑定](#122-全局作用域this-默认绑定)
            - [1.2.3 函数中的this](#123-函数中的this)
                - [1.2.3.1 普通函数中的this指向-隐式绑定](#1231-普通函数中的this指向-隐式绑定)
                - [1.2.3.2 箭头函数中的this指向](#1232-箭头函数中的this指向)
                - [1.2.3.3 构造函数中的this指向](#1233-构造函数中的this指向)
            - [1.2.4 改变this指向-显示绑定](#124-改变this指向-显示绑定)
                - [1.2.4.1 call和apply和bind的详解](#1241-call和apply和bind的详解)
                - [1.2.4.2 apply和call的区别](#1242-apply和call的区别)
                - [1.2.4.3 apply和call的用法](#1243-apply和call的用法)
                - [1.2.4.4 call和bind的区别](#1244-call和bind的区别)
<!-- /TOC -->

# 1. 目录
## 1.1. 前端必备
### 1.2. this指向总结
this是在函数执行的过程中自动创建的一个指向一个对象的内部指针。确切的说，this并不是一个对象，而是指向一个已经存在的对象的指针，也可以认为是this就是存储了某个对象的地址。
this的指向不是固定的，会根据调用的不同，而指向不同的地方。
#### 1.2.1. 预备知识
##### 1.2.1.1. this的绑定方式
>绑定方式：默认绑定、隐式绑定、显式绑定、硬绑定、new绑定
优先级：new绑定>显式绑定>隐式绑定>默认绑定
##### 1.2.1.2. new三步走

```
    var funA = function() {
        this.name = 'wjc'
    }
    var obj = new Object();
    obj._proto_ = funA.prototype;
    funA.call(obj);
        
```
#### 1.2.2. 全局作用域this-默认绑定
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
#### 1.2.3. 函数中的this
##### 1.2.3.1. 普通函数中的this指向-隐式绑定
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
##### 1.2.3.2. 箭头函数中的this指向
>箭头函数的this绑定$\color{red}{只取决于外层（函数或全局）}$的作用域
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
##### 1.2.3.3. 构造函数中的this指向
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
#### 1.2.4. 改变this指向-显示绑定
在JavaScript中，允许更改this的指向。
通过call方法或apply方法
函数A可以成为指定任意对象的方法进行调用 。函数A就是函数对象，每个函数对象中都有一个方法call，通过call可以让你指定的对象去调用这个函数A。
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
##### 1.2.4.1. call和apply和bind的详解
在this的指向中有第三个方向就是通过call/apply去改变this的指向，这个JavaScript中一个独特的使用形式，其他语言并没有。那么，我们就在这里顺带讲一下call 、apply 以及bind的用法。
##### 1.2.4.2. apply和call的区别
ECMAScript 规范给所有函数都定义了 call与apply 两个方法，它们的应用非常广泛，它们的作用也是一模一样，只是传参的形式有区别而已。
简单来说，假设有一个函数A，我们调用函数A会直接去A()，那么如果是A()这样直接调用的话，函数体A里面的this就是window了。而我们可以通过call（或apply）去调用，比如：A.call().这样子调用就可以指定A中的this到底是哪个对象。
用call来做比对，里面有两个参数，参数一就是重新指定其中的this是谁，参数2是属性名。而事实上，call与apply也就是参数二的不同这个差异。
apply
apply 方法传入两个参数：一个是作为函数上下文的对象，简单来说，重新指定函数中的this是谁。另外一个是作为函数参数所组成的数组，是传入一个数组。
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
##### 1.2.4.3. apply和call的用法
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

##### 1.2.4.4. call和bind的区别
- bind 的返回值是函数
```
    var name='HELLO'
    var obj = {
      name: 'wjc'
    }

    function func() {
      console.log(this.name);
    }
    
    //将func的代码拷贝一份，并且永远改变其拷贝出来的函数中的this，为bind第一个参数所指向的对象。把这     份永远改变着this指向的函数返回给func1.
    var func1 = func.bind(obj);
   //bind方法不会立即执行，是返回一个改变上下文this的函数，要对这个函数调用才会执行。
    func1();  //wjc
   //可以看到，现在这份改变this之后拷贝过来的函数，this的指向永远是bind（）绑定的那个，不管之后去call    重新指向对象，func1 都不会改变this的指向。永远！可知，bind比call优先级还高。
    func1.call({name:'CALL'});   //wjc

    //又从func重新拷贝一份永远改变this指向对象为{name:'LI SI'}这个对象的函数，返回给func2.
    var func2 = func.bind({name:'LI SI'});
    func2();   //LI SI

   //注意，这里是拷贝一份func2(而不是func)的代码，而func2之前已经绑定过去永远改变this的指向了，所以这   里并不去改变！还是会输出原来的最先bind的this指向对象。
    var func3 = func2.bind({name:'ZHANG SAN'});
    func3();   //LI SI

   //上面对func最初的函数进行了多次绑定，绑定后原函数 func 中的 this 并没有被改变，依旧指向全局对象      window。因为绑定bind的过程是拷贝代码的一个过程，而不是在其自身上修改。window.name = HELLO
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
call 是把第二个及以后的参数作为 func方法的实参传进去，而 func1方法的实参实则是在bind中参数的基础上再往后排。也就是说，var func1 = func.bind(null,'yaLV');  bind现有两个参数，第一个是指向，第二个实参是'yaLV'，那么就是先让func中的a='yaLV',然后没排满就是让func1('A', 'B', 'C'); 这个参数依次排，现在b='A'，c='B' , 形参已经排完了。也就是输出yaLV A B。。

- 实现原理
```
if (!Function.prototype.bind) {
   Function.prototype.bind = function () {
       var self = this,                        // 保存原函数
       context = [].shift.call(arguments), // 保存需要绑定的this上下文
       args = [].slice.call(arguments);    // 剩余的参数转为数组
       return function () {                    // 返回一个新函数
       self.apply(context[].concat.call(args[].slice.call(arguments));
              }
          }
      }

```

## 前端进阶
