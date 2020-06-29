# 记录遇到的一些前端面试题
## css
### 一个div设置为“标准模型”，同时设置它的背景颜色为红色。那么背景色会覆盖哪些区域呢？
- 会覆盖content、padding区域。这边其实跟是“标准模型”还是“IE模型”没有关系。

### css中选择器的优先级
- 标签选择器(p) < 类选择器(.example)、属性选择器([type='radio']) < ID选择器(#example);
- 关系选择符(~,+,>)、通配符(*)对优先级没有影响；
- 越具体的规则，优先级越高。比如p span{}会比p{}和span{}的优先级更高；
- !important可以将优先级提到最高，但其实不太建议用它。建议通过样式规则来调整最终的效果；

### position:absolute绝对定位
- 位置是相对于含有`position:relative`、`position:absolute`或者`position:fixed`样式的父节点的位置；
- 如果父节点没有该样式，则逐级往上，直到某个父节点有该样式，或者到body元素；
- 绝对定位时，位置是相对于符合条件的父元素或者body元素的padding的左上角进行定位的。

## js
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

### call，apply和bind的区别：
- 这3个都可以改变函数中this指向的对象；
- call和apply是在改变this指向的对象时，同时执行函数；bind是返回改变this指向的对象后的函数，该函数并没有被执行；
- call和apply的区别是参数的传入方式不同。它们的第一个参数都是this指向的对象，第二个参数开始，call传入参数是一个一个的传如剩余参数，apply是将剩余参数放在一个数组中一次传入；

### 防抖和节流
- 防抖就是前后两次触发的间隔要大于规定的时间。如果小于规定的时间，就以最新的一次触发为准，重新开始计时；
- 节流是规定的时间内只触发一次，同时触发的为最后的值或者事件；

### 浏览器安全
#### xss跨站脚本攻击：在html中插入攻击代码，用户在访问页面的时候攻击代码被执行，从而导致被攻击；
- 永远不相信用户输入的内容，因此对用户的输入进行转义、过滤，避免被插入恶意的js代码；
- 使用https协议；
- 在cookie中设置httpOnly,这样cookie就无法通过js访问获取，提升cookie安全性；

### xsrf跨站请求伪造：在用户登录后，假借用户的身份向服务器发送一些请求，执行一些操作，达到攻击的目的；
- 过程：
1. 用户访问网站A，并成功登录；
2. 用户被诱导访问攻击者服务器B，B返回一段恶意代码给用户；
3. 用户浏览器上接收到这段恶意代码，并悄悄执行，在用户不知道的情况下向网站A发送一些请求；
4. 因为用户已经登录过，所以发送的请求会带上cookie之类的身份验证信息，网站A就会以为这些请求是用户发送的，以用户的身份权限进行操作；
- 防范措施：
1. 使用token，可以因此在请求头中

### js加载
- 不加任何属性，即`<script src="./a.js"></script>`.这种js加载方式，会阻塞页面的渲染，直到它加载并执行完，页面的渲染才会继续；
- 加async属性,即`<script src="./a.js" async></script>`.这种js加载方式，在js加载时不会阻塞页面的渲染。但是当js加载完时，会优先执行js中的内容。在js中的内容执行完后，才会继续页面的渲染；
- 加defer属性,即`<script src="./a.js" defer></script>`.这种js加载方式，不管是在js加载时，还是js加载完成后都不会阻塞页面的渲染。在页面渲染完毕后，才会执行js中的内容；

### http缓存
- 强缓存：符合缓存条件时，会直接使用缓存中的数据，而不会向服务器发起http请求；
1. expired和cache-control;
2. expired是http1.0的产物，是否使用缓存的条件是expired指定的过期时间。当大于过期时间时，会向服务器发起请求，否则直接使用缓存中的数据。但是时间判定时是获取浏览器本地的时间和过期时间做对比，而本地时间容易被调整；
3. cache-control是http1.1的产物，通过max-age指定数据有效时长，单位为s。因为并不是通过本地时间来判断，所以会比expired好。
4. cache-control中还有其它属性。常用的有：private:只能浏览器缓存，代理服务器不能缓存；public为代理服务器也能缓存；no-cache:不使用强缓存，使用协商缓存；no-store:完全不使用缓存