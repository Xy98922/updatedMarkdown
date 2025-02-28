# 前端笔试题

[TOC]

## 请写出下列代码的打印顺序

```js {.font}
var name = "global";
var obj = {
  name: "local",
  foo: function () {
    this.name = "foo";
  }.bind(window),
};
var bar = new obj.foo();
setTimeout(function () {
  console.log(window.name);
}, 0);
console.log(bar.name);
var bar3 = (bar2 = bar);
bar2.name = "foo2";
console.log(bar3.name);
```

> 解析：
> 1、输出顺序主要考察：Event Loop；
> 2、第一个和第三个输出考察：this 指针；
> 3、第二个输出考察：引用类型赋值

```js {.font}
var name = "global";
var obj = {
  name: "local",
  foo: function () {
    console.log(this);
    this.name = "foo";
  }.bind(window),
};
console.log(obj.foo()); // 此时调用的this是window
// 由于new绑定的优先级大于bind绑定，所以函数内部this还是obj {}
var bar = new obj.foo();
console.log(bar); //{name：'foo'}
console.log(window.name); //global

// 定时器任务，在最后放入任务队列，window对象没有被改变，所以输出 'global'
setTimeout(function () {
  console.log(window.name);
}, 0);
// 此时bar.name =foo,因为被赋值了
console.log(bar.name);

// 此时执行顺序是var bar3,bar2=bar,bar3=bar2, 所以bar3/bar2/bar都是指向同一个对象
var bar3 = (bar2 = bar);
bar2.name = "foo2";
// 所以bar2修改属性，bar3的也改变了，此时输出为'foo2'
console.log(bar3.name);
```

## 请写出下面 ES6 代码编译后所生成的 ES5 代码

```js {.font}
class Person {
  constructor(name) {
    this.name = name;
  }
  greet() {
    console.log(`Hi, my name is ${this.name}`);
  }
  greetDelay(time) {
    setTimeout(() => {
      console.log(`Hi, my name is ${this.name}`);
    }, time);
  }
}
```

> > 解析：
> > 1、class 语法糖
> > 2、模板字符串
> > 3、setTimeout 中的 this 问题
> > 4、箭头函数中的 this 问题

```js {.font}
 fucntion Person(name){
     this.name=name;
 }
 Person.prototype.greet=function(){
    console.log('Hi, my name is'+this.name);
 }
 Person.prototype.greetDelay=function(time){
     var _this=this;
     setTimeout(
     fucntion(){
         console.log('Hi, my name is' _this.name);
     },time);
 }
```

## 排序算法

## JS 中创建节点的方式有哪些？

- Document.createElement()
- var dupNode = node.cloneNode(deep);
  > >
   <dl>
   <dt id="node"><code>node</code></dt>
   <dd>将要被克隆的节点</dd>
   <dt id="dupnode"><code>dupNode</code></dt>
   <dd>克隆生成的副本节点</dd>
   <dt id="deep"><code>deep</code> 可选</span></dt>
   <dd>是否采用深度克隆，如果为 <code>true</code>，则该节点的所有后代节点也都会被克隆，如果为 <code>false</code>，则只克隆该节点本身。</dd>
  </dl>

## redux 三大原则

<ul><li>
单一数据源
    <ul><li>整个应用程序的<span style='color:#fe2c24;'>state</span>只存储在<span style='color:#fe2c24;'>一个&nbsp;store</span>&nbsp;中</li><li>Redux并没有强制让我们不能创建多个Store，但是那样做并不利于数据的维护</li><li>单一的数据源可以让整个应用程序的state变得方便维护、追踪、修改</li></ul></li><li>
State是只读的
    <ul><li>唯一<span style='color:#fe2c24;'>修改State</span>的方法一定是<span style='color:#fe2c24;'>触发action</span>，不要试图在其他地方通过任何的方式来修改State</li><li>这样就确保了View或网络请求都不能直接修改state，它们只能通过action来描述自己想要如何修改stat；</li><li>这样可以保证所有的修改都被集中化处理，并且按照严格的顺序来执行，所以不需要担心race&nbsp;condition（竟态）的问题；</li></ul></li><li>
使用
<span style='color:#fe2c24;'>纯函数(返回结果只依赖于它的参数，并且在执行过程里面没有副作用)</span>来执行修改
    <ul><li>通过reducer将&nbsp;旧state和&nbsp;action联系在一起，并且返回一个新的State：</li><li>随着应用程序的复杂度增加，我们可以将reducer拆分成多个小的reducers，分别操作不同state&nbsp;tree的一部分</li><li>但是所有的reducer都应该是纯函数，不能产生任何的副作用</li></ul></li></ul>

## 下段代码打印结果为

```js {.font}
const Person = (name = "wang", age = 10) => {
  this.name = name;
  this.age = age;
  return this.name + " is " + this.age + "years old";
};
let result = new Person("zhang", 11);
console.log(result);
```

正解：**箭头函数不能作为构造函数使用**，所以函数执行会报错

## JavaScript 中，字符串与数组有许多相同的方法，以下不属于的是

- slice
- indexOf
- reverse
- concat

正解：字符串没有内置的 reverse 方法，可用`str.split("").reverse().join("")` 代替

## 以下哪些 ul 的 height 不是 0

```js {.font}
A：  <ul style="overflow: hidden;">
      <li style="float: left">1</li>
     </ul>

B：  <ul style="float: left;">
      <li style="float:left;">1</li>
     </ul>

C：  <ul style="clear: both;">
      <li style="float: left">1</li>
     </ul>
```

正解:AB

- A 选项：overflow：hidden 产生 BFC，可以防止元素塌陷
- B 选项： float：left 也产生 BFC，可以防止元素塌陷
- C 选项：clear：both，属性应该加载父级元素的最后一个子元素上，而不是加载父元素上

## 以下表达式，正确的是

正确答案: D 你的答案: B (错误)

A &nbsp;&nbsp;Number('a') == Number('a')
B &nbsp;&nbsp;-1 == true
C &nbsp;&nbsp;3 + '2' === 5
D &nbsp;&nbsp;![] == ''

js 在**转换布尔值**时候将：0、null、false、NaN、undefined、'' 转为 false，其他所有数据都转换为 true

注意：会将参数转换为布尔值的情况有：！、if()、A?B:C\*；**_在==类型转换时，并不会将数据转为布尔值_**

思考：

```js {.font}
判断：![]==[];
      null==false;
      undefined==false;
      []==false;
      NaN==false;
```

## 下列创建的对象中，打印的值分别为多少？

```js {.font}
var a = {},
  b = "123",
  c = 123;
a[b] = "b";
a[c] = "c";
console.log(a[b]); //==>c

var a = {},
  b = Symbol("123"),
  c = Symbol("123");
a[b] = "b";
a[c] = "c";
console.log(a[b]); //==>b

var a = {},
  b = { key: "123" },
  c = { key: "456" };
a[b] = "b";
a[c] = "c";
console.log(a[b]); //==>c
```

**属性访问器**提供了两种方式用于访问一个对象的属性，它们分别是**点号和方括号**，对象的**属性名**可以是任意的**字符串**或**Symbol**。其它类型会被\*\*自动转换成字符串

> > 转换为字符串规则：
> > 1、对象：**进行\[Symbol.toPrimitive]()\==>valuOf()\==>toString()**
> > 2、非对象：**String()**

1、(.) 点操作符: 静态的。右侧必须是一个以**属性名称命名的简单标识符**
2、([]) 中括号操作符: 动态的；方括号里必须是一个计算结果为字符串的表达式，可以是变量、字符串、数字、表达式(或 Symbol 值·了解)

## 关于 transition

正解：若**过渡起始值为 auto**，**不发生过渡效果**。所以要过渡某些属性，首先需要将其重置成具体数字值

## 关于\<input type="text" /> change 事件和 input 事件描述最准确的是？

正解：用户键入内容改变时，触发 input 事件，且在当标签失焦后，触发 change 事件

## CSS 中，top 属性是那两个部分的距离？

正解：top 样式属性定义了定位元素的**上外边距边界**与其包含块上边界(border-top)之间的偏移

## 5 % 6===5

## 通常情况下，一个 URL 的格式是

协议：//主机：端口/路径名称?搜索条件#哈希标识

## CSS 权重顺序正确的是

- 优先级
  > > !important>行内样式>ID 选择器>类选择器=伪类=属性>标签=伪元素>通配符(\* + > ~ )>继承>浏览器默认属性
  > > **_注意_**：**a:hover 为标签+伪类；input\[type='password']为标签+属性**
- 计算
  > > 具体到计算层面，优先级是由 ABCD 的值来决定的，计算规则如下：
  - A=是否存在内联样式？1：0
  - B=ID 选择器出现的次数
  - C=类选择器+属性选择器+伪类出现的总次数
  - D=标签选择器+伪元素出现的总次数
    **_注意：通配符选择器不影响优先级_**

## js 中,标识符不能以数字开头,必须是字母、下划线“\_”或美元符号“$”

## 怎样完美实现左右定宽，section 自适应尺寸的布局

```js {.font}
方式一: <div>
        <div class="right" />
        <div class="left" />
        <div class="section"/>
        </div>

     .content {
            height: 400px;
            background: #f90;
            width: 100%;
        }
        .left {
            width: 300px;
            height: 400px;
            background: purple;
            float: right;
        }
        .right {
            width: 300px;
            height: 400px;
            background: palevioletred;
            float: left;
        }


方式二:    <div style="display:flex">
            <div style="height: 100px;width: 100px;"></div>
            <div style="flex:1"> </div>
            <div style="height: 100px;width: 100px;"></div>
          </div>
```

## 执行下面的代码，执行后，5S 内点击两下，过了 5S 后，再点击两下，整个过程的输出结果是什么？

```js {.font}
setTimeout(function () {
  for (let i = 0; i < 1000; i++) {}
  console.log("timer a");
}, 0);

for (let j = 0; j < 5; j++) {
  console.log(j);
}

setTimeout(function () {
  console.log("timer b");
}, 0);

function waitFiveSeconds() {
  let now = new Date().getTime();
  while (new Date().getTime() - now < 5000) {}
  console.log("finished waiting");
}

document.addEventListener("click", function () {
  console.log("click");
});

console.log("click begin");
waitFiveSeconds();
```

## 写出下方代码打印顺序

```js {.font}
console.log("start");
setTimeout(() => console.log("s1"), 0);
new Promise((resolve) => {
  console.log("p1");
  resolve();
}).then((v) => {
  console.log("t1");
  setTimeout(() => {
    console.log("s2");
  }, 0);
  new Promise((resolve) => {
    console.log("p2");
    resolve();
  }).then((v) => {
    console.log("t2");
  });
  console.log("t3");
  setTimeout(() => {
    console.log("s3");
  }, 0);
});
console.log("end");
```

为什么学前端\*4

## 实现边长自适应的正方形

- 方法一,灵活运用视窗单位`vw`。当**没给 body 设置 css**时，**body 的 width 为 100vw，height 为 0**，即说明当一个元素以 body 为父元素时，**width：n%与 width：nvw 等价**，**height：n%与 height: nvh 等价且都等于 0**

```css
#square {
  width: 30vw;
  height: 30vw;
  background: red;
}
或者 #square {
  width: 30%;
  height: 30vw;
  background: red;
}
```

- 方法二，设置垂直方向的 padding 撑开容器。如果给子**元素的 padding 属性设置为 %**，无论上下左右方向，则其**基数都取决于父元素的 width 属性。**

```css
#square{
   width:30%;
   padding-bottom: 100%;  或者padding-top：30%
   background:red;
}
```

- 方法三，利用伪元素的 margin(padding)-top 撑开容器（::after 用来创建一个伪元素，作为已选中元素的最后一个**子元素**）

> > 为什么加`overflow：hidden`：在**垂直方向**上，当**子元素的 margin 与父元素的 border 相邻时**，**子元素的 margin 会贯穿父元素，变成父元素的 margin**==>margin 贯穿，是父元素形成一个 BFC 可避免这一现象，或者使用 padding 代替 margin

```css
#square{
    width:30%;
    background:red;
    overflow:hidden;
}
#square:after{
    content: '';
    display: block;
    margin-top:100%;  使用padding-top则不需要触发BFC
}
```

## v-if VS v-show

- v-if 是“真实的”按条件渲染，因为它确保了**在切换时，条件区块内的事件监听器和子组件都会被销毁与重建**

- v-if 也是惰性的：如果在初次渲染时条件值为 false，则不会做任何事。条件区块只有当条件首次变为 true 时才被渲染。

- 相比之下，**v-show 简单许多，元素无论初始条件如何，始终会被渲染，只有 CSS display 属性会被切换**。

- 总的来说，**v-if 有更高的切换开销**，而 v-show 有更高的初始渲染开销。**因此，如果需要频繁切换，则使用 v-show 较好**；如果在运行时绑定条件很少改变，则 v-if 会更合适

## \<style scoped>中的 scoped 的作用是？原理是？加和不加的区别

- 1、什么是 scoped，为什么要用？
  vue-cli 脚手架规范,**VUE 打包只生成一个 js 文件,一个 CSS 文件**
  当一个 style 标签拥有 scoped 属性时，它的 CSS 样式就只能作用于当前的组件，通过该属性，可以使得组件之间的样式不互相污染。

- 2、scoped 的原理（）
  ① 为组件实例生成一个唯一标识，给组件中的每个**标签对应的 dom 元素添加一个标签属性**，data-v-xxxx（也就是说：只要\<style scoped>定义的选择器选中了 DOM 元素，则该 DOM 元素有个标签属性 data-v-xxx，该组件的所有标签属性都一样，都为同一个 data-v-xxx）
  ② 给\<style scoped>中的每个选择器的最后一个选择器添加一个属性选择器，原选择器[data-v-xxxx]，如：原选择器为.container #id div，则更改后选择器为.container #id div[data-v-xxxx]

- 3、样式穿透

```HTML
<style scoped>
    div{
        color: red;
    }
    div h1 span{
      color:green !important;
    }
</style>

页面转换样式如下
<style>
    div[data-v-789]{
        color: red;
    }
    div h1 span[data-v-789]{
      color:green !important;
    }
</style>

DOM元素性质如下
<body>
  <div data-v-789>
    ddsfsf
        <h1 data-v-789>h111
            <span>
                span
            </span>
        </h1>
  </div>
```

问题出现：

> > &nbsp; div 为正在开发组件的元素（父元素），h1 为引入的外部组件（子元素），则在页面中最终会呈现如上代码一样的效果，即**只有子组件的根元素有跟父元素一样的属性标签**（即 scoped 只会作用至子组件根节点），但是**子组件的非根元素样式并没有属性标签**，div h1 span[data-v-789]选择器选择不到 span，若子组件有 css span{color:pink},最终 span 文字颜色为粉红色，若子组件无 css 样式，则 span 颜色为红色（继承父元素）

我们**如何在父组件中的 scope 里，修改子组件的 css 样式**？（既可以在该组件中生效，又不影响其他组件）

解决办法: 样式穿透 :deep()

```HTML
<style scoped>
    div{
        color: red;
    }
    :deep(div h1 span){
      color:green !important;
    }
</style>

页面转换样式如下:
<style>
    div[data-v-789]{
        color: red;
    }
    div[data-v-789] h1 span{
      color:green !important;
    }
</style>

DOM元素性质如下:
<body>
  <div data-v-789>
    ddsfsf
        <h1 data-v-789>h111
            <span>
                span
            </span>
        </h1>
  </div>
```

## js 与 ts 区别

## 前端框架里面的 key 的作用

- **相同的 key react 认为是同一个组件**，这样**后续相同的 key 对应组件都不会被创建**。在 render 函数执行的时候，**新旧两个虚拟 DOM 会进行对比**:
  **如果两个元素有不同的 key**，那么在前后两次渲染中就会被认为是不同的元素，这时候**旧的那个元素会被销毁，新的元素会被创建**
  **如果是相同的 key**，**且元素类型相同**， **若元素属性有所变化，则 React 只更新组件对应的属性，这种情况下，性能开销会相对较小。**

_问题：需要把 keys 设置为全局唯一吗？_
key 是用来进行 diff 算法的时候进行同层比较,**key 只需要在兄弟节点之间唯一**

## 跨域

## 事件委托

## 为什么要从 class 组件转向 function 组件（hooks 出现的意义）

<a href='https://zh-hans.reactjs.org/docs/hooks-intro.html'>官方解释：</a>
1、在组件之间<a href='https://blog.csdn.net/qq_39207948/article/details/113819803'>复用状态逻辑</a>很难
2、复杂组件变得难以理解（生命周期处理）

> > <a href='https://www.zhihu.com/question/343314784/answer/970219202'>函数式组件的心智模型更加“声明式”</a>，hooks（主要是 useEffect）取代了生命周期的概念（减少 API），让开发者的代码更加“声明化”：
> > 旧的思维：“我在这个生命周期要检查 props.A 和 state.B（props 和 state），如果改变的话就触发 xxx 副作用”。这种思维在后续修改逻辑的时候很容易漏掉检查项，造成 bug。
> > 新的思维：“我的组件有 xxx 这个副作用，这个副作用依赖的数据是 props.A 和 state.B”。从过去的命令式转变成了声明式编程。
> > 其实仔细想一想，人们过去使用生命周期不就是为了判断执行副作用的时机吗？现在 hooks 直接给你一个声明副作用的 API，使得生命周期变成了一个“底层概念”，无需开发者考虑。开发者工作在更高的抽象层次上了。
> > 类似的道理，除了声明副作用的 API，react 还提供了声明“密集计算”的 API（useMemo），取代了过去“在生命周期做 dirty 检查，将计算结果缓存在 state 里”的做法。React 内核帮你维护缓存，你只需要声明数据的计算逻辑以及数据的依赖。

3、难以理解的 class（学习成本+this）
4、Hook 避免了 class 需要的额外开支，像是创建类实例和在构造函数中绑定事件处理器的成本。

## 为什么要用虚拟 DOM

> > 虚拟 dom:就是一个普通的 js 对象

<a href='https://www.csdn.net/tags/NtTakgwsOTQxMDYtYmxvZwO0O0OO0O0O.html#DOM_47'>详解</a>
1、真实的 DOM 运行是很慢的，其元素非常庞大，页面的性能问题，大部分都是由 DOM 操作引起的，**真实的 DOM 节点，哪怕一个最简单的 div 也包含着很多属性，由此可见，操作真实 DOM 的代价仍旧是昂贵的**，频繁操作还是会出现页面卡顿，影响用户的体验

2、虚拟 dom 是相对于浏览器所渲染出来的真实 dom 而言的，在 react，vue 等技术出现之前，我们要改变页面展示的内容**只能通过遍历查询 dom 树的方式找到需要修改的 dom 然后修改样式行为或者结构**，来达到更新 ui 的目的，这种方式相当消耗计算资源，因为每次查询 dom 几乎都需要遍历整颗 dom 树，如果建立一个与 dom 树对应的虚拟 dom 对象（ js 对象），**以对象嵌套的方式来表示 dom 树及其层级结构，那么每次 dom 的更改就变成了对 js 对象的属性的增删改查，这样一来查找 js 对象的属性变化要比查询 dom 树的性能开销小**

3、通过 diff 算法对比新旧 vdom 之间的差异，可以批量的、最小化的执行 dom 操作，从而提高性能。

## 虚拟 DOM 是如何渲染到页面的

<a href="https://cn.vuejs.org/guide/extras/rendering-mechanism.html">Vue 的渲染机制</a>

## 箭头函数与普通函数的区别（见 js.md）

## 箭头函数中的 this 问题（见 js.md）

## 如何把 ts jsx vue 转换成浏览器能识别的语言

需要安装一些特殊的依赖库，需要把 typescript、jsx、vue 翻译成 JavaScript 语言浏览器才能运行出效果（如使用 Babel 将 jsx 编译成为浏览器支持的 JavaScript）

## 前端框架的 diffing 算法

<a href="https://zh-hans.reactjs.org/docs/reconciliation.html#the-diffing-algorithm">React diffing</a>

## 通常情况下，一个 URL 的格式是：协议：//主机：端口/路径名称?搜索条件#哈希标识

## 0.2 - 0.1 ==？0.1 、0.3 - 0.2 == ?0.1

## 已知字符串：'电话号码是：123-4567-8901'，如下正则表达式可以匹配到字符串中的电话号码的有

**_正确答案: B C D 你的答案: A C (错误)_**

- A: /\d[3]-\d[4]-\d[4]/
- B: /\d{3}-\d{4}-\d{4}/
- C: /[0-9-]+/
- D: /[0-9\\-]+/

> > 减号用在字符集“[…]”里表示一组字符，
> > 如：“[1-3]” —— 表示 1 到 5 中的任意一个字符，所以“H[1-3]”表示“H1”、“H2”或“H3”
> > 如果不是用在字符集“[…]”里，就是普通的字符，即减号：
> > “1-[1-3]” —— 表示“1-1”、“1-2”或者“1-3”
> > 但是，即使在字符集“[…]”里，却非连续字符之间，也失去了特殊含义：
> > “1[-1]” —— 表示“1-”或者“11”，此时使用转义符\也不会影响其原本的匹配

## 函数返回包含 n 个不重复的大于等于 2 小于 32 的随机数的数组

```js {.font}
function f(n) {
  let arr = new Array(n);
  let obj = {};
  for (let i = 0; i < n; i++) {
    arr[i] = Math.floor(Math.random() * 30) + 2;
    while (obj[arr[i]] !== undefined) {
      arr[i] = Math.floor(Math.random() * 30) + 2;
    }
    obj[arr[i]] = 1;
  }
  return arr;
}

function f(n) {
  let s = new Set();
  while (s.size !== n) {
    s.add(Math.floor(Math.random() * 30) + 2);
  }
  return [...s];
}

function f(n) {
  let arr = new Array(n);
  let obj = {};
  while (arr.length < n) {
    const temp = Math.floor(Math.random() * 30) + 2;
    if (obj[temp] == undefined) {
      arr.push(temp);
      obj[temp] = 1;
    }
  }
  return arr;
}
```

## promise 实现，红色三秒打印一次、绿色两秒打印一次、黄色一秒打印一次

```js
function red() {
  console.log("red");
}
function green() {
  console.log("green");
}
function yellow() {
  console.log("yellow");
}

function light(time, name) {
  return new Promise((resolve) => {
    setTimeout(() => {
      name();
      resolve();
    }, time);
  });
}

function step() {
  Promise.resolve()
    .then(() => {
      return light(3000, red);
    })
    .then(() => {
      return light(2000, green);
    })
    .then(() => {
      return light(1000, yellow);
    })
    .then(() => {
      // 黄色循环完毕再调用一次step，实现不断循环打印
      return step();
    });
}
step();
```

## 大文件上传

```TypeScript
// cutFile.ts
const CHUNK_SIZE = 1024 * 1024 * 10;
const THREAD_COUNT = navigator.hardwareConcurrency || 4;
export async function cutFile(file: File) {
  return new Promise((resolve) => {
    // 总共的分片数量
    const chunkCount = Math.ceil(file.size / CHUNK_SIZE);
    // 每个线程能分配到的分片数量
    const threadChunkCount = Math.ceil(chunkCount / THREAD_COUNT);
    const result: any[] = [];
    let finishCount = 0;
    for (let i = 0; i < THREAD_COUNT; i++) {
      // 开启一个线程任务
      const worker = new Worker(new URL("@/utils/worker.ts", import.meta.url), {
        type: "module",
      });
      let start = i * threadChunkCount;
      let end = Math.min(start + threadChunkCount, chunkCount);
      worker.postMessage({
        file,
        start,
        end,
        CHUNK_SIZE,
      });
      worker.onmessage = (e) => {
        worker.terminate();
        result[i] = e.data;
        finishCount++;
        if (finishCount === THREAD_COUNT) {
          resolve(result.flat());
        }
      };
    }
  });
}
```

```TypeScript
//worker.ts
import SparkMD5 from "spark-md5";
function createChunk(file: File, index: number, chunkSize: number) {
  return new Promise((resolve) => {
    const start = index * chunkSize;
    const end = start + chunkSize;
    const spark = new SparkMD5.ArrayBuffer();
    const fileReader = new FileReader();
    const blob = file.slice(start, end);
    fileReader.onload = (e: any) => {
      spark.append(e.target.result);
      resolve({
        start,
        end,
        index,
        hash: spark.end(),
        blob,
      });
    };
    fileReader.readAsArrayBuffer(blob);
  });
}

onmessage = async (event) => {
  const { file, start, end, CHUNK_SIZE } = event.data;
  const result = [];
  for (let i = start; i < end; i++) {
    const promise = createChunk(file, i, CHUNK_SIZE);
    result.push(promise);
  }
  const chunks = await Promise.all(result);
  postMessage(chunks);
};

```

## 断点续传

- `localStorage`保存

```javascript
{
  key:`${file.name}-${file.size}-${file.lastModified}`,
  value:[1,2,3,...]; //已上传的块index
}
```

## 秒传

用户上传的文件在服务端已经存在时，直接返回成功。通常通过文件哈希来判断文件是否已经上传。

## ES 模块是如何工作的

1. 构建过程（Construction） -- 查找并解析所有的模块文件，生成模块记录。
   模块记录包括以下信息：

   - 导入的模块（imports）
   - 导出的内容（exports）：
   - 求值状态（evaluationStatus）：模块是否已被过。
     ![模块记录](./images/moduleRecord.png)

2. 初始化（Instantiation） -- 为模块导出的变量分配内存（但不求值）。然后将导入和导出都指向这些内存地址，这称为链接。

3. 求值（Evaluation） -- 运行代码，将变量值填充到内存中去。

## Vite (bundleless)

### 原理

在`<script type="module">`中，浏览器遇到内部的 import 引用时，会自动发起 http 请求，去加载对应的模块。
vite 也正是利用了 ES module 这个特性，使用 vite 运行项目时，首先会用 esbuild 进行预构建，将所有模块转换为 es module，不需要对我们整个项目进行编译打包，而是在浏览器需要加载某个模块时，拦截浏览器发出的请求，根据请求进行按需编译，然后返回给浏览器。
这样一来，首次启动项目（冷启动）时，自然也就比 webpack 快很多了，并且项目大小对 vite 启动速度的影响也很小。

1. 解决了服务器启动缓慢的问题

   - 使用 esbuild 预构建依赖

     > 当你首次启动 vite 时，Vite 在本地加载你的站点之前预构建了项目依赖。默认情况下，它是自动且透明地完成的，目的是：
     >
     > - 性能： 为了提高后续页面的加载性能，Vite 将那些具有许多内部模块的 ESM 依赖项转换为单个模块。
     > - CommonJS 和 UMD 兼容性

   - 以原生 ESM 方式提供源码，让浏览器接管打包程序的部分工作
     ![alt text](./images/vite.svg)

2. 解决了更新缓慢的问题
   在 Vite 中，HMR 是在原生 ESM 上执行的。当编辑一个文件时，Vite 只需要精确地使已编辑的模块与其最近的 HMR 边界之间的链失活（大多数时候只是模块本身），使得无论应用大小如何，HMR 始终能保持快速更新。
   > - Vite 的 HMR 以 模块为单位 进行更新
   > - Vite 通过显式 HMR 边界限制更新范围
   >   示例：
   >   假设依赖链为 App → Router → Page → Component，若修改 Component，Vite 仅使 Component → Page 的链路失效（若 Page 是 HMR 边界），而 App 和 Router 不受影响。

## Webpack

1. 工作原理
   当我们使用 webpack 启动项目时，webpack 会根据我们配置文件（webpack.config.js） 中的入口文件（entry），分析出项目项目所有依赖关系，然后打包成一个文件（bundle.js），交给浏览器去加载渲染。
   ![alt text](./images/webpack.svg)

## <a href="https://juejin.cn/post/7283682738497765413?searchId=20250228175028AB78D75C81CADE4E2D7C">Webpack VS Vite</a>
