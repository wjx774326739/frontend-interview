# 记录遇到的一些前端面试题
## css
### 一个div设置为“标准模型”，同时设置它的背景颜色为红色。那么背景色会覆盖哪些区域呢？
- 会覆盖content、padding区域。这边其实跟是“标准模型”还是“IE模型”没有关系。

### css中选择器的优先级
- 标签选择器(p) < 类选择器(.example)、属性选择器([type='radio']) < ID选择器(#example);
- 关系选择符(~,+,>)、通配符(*)对优先级没有影响；
- 越具体的规则，优先级越高。比如p span{}会比p{}和span{}的优先级更高；
- !important可以将优先级提到最高，但其实不太建议用它。建议通过样式规则来调整最终的效果；

# js
### this指针
- 一般情况下，this指向最终调用的对象；
- 箭头函数()=>{}中的this指针是在定义时确定，执行定义时的上下文；
- bind、call、apply等可以改变this指针的指向；
- 严格模式下，全局对象是undefined；
```js
var obj1 = {
    age: 10,
    getAge: function () {
        console.log(this.age);
    }
};

var obj2 = {
    age: 20,
};

var getAge = obj1.getAge;
obj2.getAge = obj1.getAge;

// 此时this指向全局对象window。
// 因为window下没有定义age，所以会打印出undefined.
// 如果是严格模式下，全局对象是undefined，
// 所以会报错，而不是打印出undefined
getAge();

// 此处是将obj1.getAge函数传入到setTimeout中，
// 具体调用还是window，即window.setTimeout。
// 所以此时this是指向全局对象window.
// 因为window下没有定义age，所以会打印出undefined
setTimeout(obj1.getAge, 100);

// 因为上文中已经在obj2中新增了getAge函数。
// 此时调用getAge函数的是obj2。所以此时this指向obj2
// 所以打印的值为20.
obj2.getAge();
```