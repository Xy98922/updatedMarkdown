# HTML CSS笔记

[TOC]

## flex布局

- 优点：操作方便，布局极为简单，移动端应用广泛
  
- 原理：通过给**父盒子**添加display ：flex属性来控制**子盒子**的位置和排列方式
  
- flex父盒子属性
  
>>- 1、flex-direction: row | row-reverse | column | column-reverse;
     设置主轴的方向，元素跟着主轴走

----

>>- 2、justify-content：flex-start | flex-end | center | space-between | space-around;
    设置主轴上子元素的排列方式
   ![image.png](https://img-blog.csdnimg.cn/20200910110849844.png#pic_center)

   ----

>>- 3、align-content: stretch | flex-start | flex-end | center | space-between | space-around;
设置侧轴上的元素对齐方式(多行)，若为单行则该属性不起作用。

----

>>- 4、align-items: flex-start | flex-end | center | baseline | stretch;
设置侧轴上的元素对齐方式(单行)，若为单行则该属性不起作用。

----

>>- 5、flex-wrap: nowrap | wrap | wrap-reverse;
设置子元素是否换行

   ----

>>- 6、flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

- flex子盒子属性
  
>>- 1、flex：\<number>,flex属性定义子项分配剩余空间(剩余空间是flex容器的大小减去所有flex项的大小加起来的大小)，用flex来表示占多少份，项目flex属性若不为0，则项目宽度无效
*实际上：flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选*

----

>>- 2、align-self(了解)：属性允许单个项目有其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch

----

>>- 3、order(了解): 定义项目的排列前后顺序(值越小越靠前)

## 阻止事件冒泡/默认事件的方法

>> 注意：
像**onclick这样的元素**为non-capture事件侦听器，他们总**是在冒泡阶段倾听，从不在捕捉阶段触发**
相比之下，**addEventListener**添加了一个事件侦听器，**可以是捕获触发，也可以冒泡触发**

- 1、event.stopPropagation：这是**阻止事件的冒泡**方法，但是默认事件任然会执行，当你调用这个方法的时候，如果点击一个链接\<a>，这个链接仍然会被打开
  (*该语句无论在回调函数中的哪个位置都能产生效果，且不影响回调函数其他代码正常执行*)
  
  ````js {.font}
  box3.addEventListener('click',()=>{
     console.log('第三层被点击');
     event.stopPropagation();
   });

  box3.addEventListener('click',()=>{
     console.log('第三层被点击');
   },true);  //第三个参数默认为false，触发=>冒泡
             //当设置为true时，捕获=>触发

  ````

- 2、event.preventDefault()：这是**阻止默认事件**的方法，调用此方法时，\<a>标签链接不会被打开，**但是会发生冒泡**
  (*该语句无论在回调函数中的哪个位置都能产生效果，且不影响回调函数其他代码正常执行*)
  
   ````js {.font}
  a.addEventListener('click',()=>{
     console.log('a tag 被点击');  //会打印出来，也会冒泡
     event.preventDefault();      //不会跳转链接
   });
  ````

- 3、event.returnValue = false(*将被废弃，不推荐使用*)：属性表示该事件的默认操作是否已被阻止。默认情况下，它被**设置为 true，即允许进行默认操作**。将该**属性设置为 false 即可阻止默认操作。**
  
## 选择器与优先级

 <table class="standard-table">
 <thead>
  <tr>
   <th scope="col">选择器</th>
   <th scope="col">示例</th>
   <th scope="col">学习CSS的教程</th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="https://blog.csdn.net/sinat_38021004/article/details/122977783">类型选择器||标签选择器||元素选择器</a></td>
   <td><code>h1 {&nbsp; }</code></td>
   <td><a href="/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#Type_selectors" class="page-not-created" title="This is a link to an unwritten page">类型选择器</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Universal_selectors">通配选择器</a></td>
   <td><code>* {&nbsp; }</code></td>
   <td><a href="/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#The_universal_selector" class="page-not-created" title="This is a link to an unwritten page">通配选择器</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Class_selectors">类选择器</a></td>
   <td><code>.box {&nbsp; }</code></td>
   <td><a href="/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#Class_selectors">类选择器</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/ID_selectors">ID选择器</a></td>
   <td><code>#unique { }</code></td>
   <td><a href="/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#ID_Selectors">ID选择器</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Attribute_selectors">属性选择器</a></td>
   <td><code>[title='xxx'] {&nbsp; }</code></td>
   <td><a href="/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Attribute_selectors">属性选择器</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Pseudo-classes">伪类选择器</a></td>
   <td><code>p:first-child { }</code></td>
   <td><a href="/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Pseuso-classes_and_Pseudo-elements#What_is_a_pseudo-class">伪类</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Pseudo-elements">伪元素选择器</a></td>
   <td><code>p::first-line { }</code></td>
   <td><a href="/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Pseuso-classes_and_Pseudo-elements#What_is_a_pseudo-element">伪元素</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Descendant_combinator">后代选择器</a></td>
   <td><code>article p</code></td>
   <td><a href="/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#Descendant_Selector">后代运算符</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Child_combinator">子代选择器</a></td>
   <td><code>article &gt; p</code></td>
   <td><a href="/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#Child_combinator">子代选择器</a></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator">相邻兄弟选择器</a></td>
   <td><code>h1 + p</code></td>
   <td><a href="/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#Adjacent_sibling">相邻兄弟</a></td>
   <td>h1标题后面紧跟着的段落将被选中</td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/CSS/General_sibling_combinator">通用兄弟选择器</a></td>
   <td><code>h1 ~ p</code></td>
   <td><a href="/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#General_sibling">通用兄弟</a></td>
   <td>选择h1元素之后所有同层级p元素</td>
  </tr>
 </tbody>
 <tfoot><tr>
 <td>我是tfoot元素</td>
 <td>我是tfoot元素</td>
 <td>我是tfoot元素</td>
 </tr></tfoot>
</table>

- 优先级
  >>!important>行内样式>ID选择器>类选择器=伪类=属性>标签>通配符(* + > ~ )>继承>浏览器默认属性
  ***注意***：**a:hover为标签+伪类；input\[type='password']为标签+属性**
  
- 计算
  >>具体到计算层面，优先级是由ABCD的值来决定的，计算规则如下：
  - A=是否存在内联样式？1：0
  - B=ID选择器出现的次数
  - C=类选择器+属性选择器+伪类出现的总次数
  - D=标签选择器+伪元素出现的总次数
***注意：通配符选择器不影响优先级***

## CSS中的表

- **\<table>**
  >>*搭配标签\<thead>，\<tbody>，\<tfoot>，\<tr>，\<td>使用，使用方法如上表*

- **\<ul>/\<ol>无序列表与有序列表** 搭配\<li>标签使用
  >><ul>
  >><li>无序一号</li>
  >><li>无序二号</li>
  >><li>无序三号</li>
  >></ul>
  >><ol>
  >><li>有序一号</li>
  >><li>有序二号</li>
  >><li>有序三号</li>
  >></ol>

- **\<dl>(definition list)**，*搭配标签\<dt>(Definition Term )与\<dd>(Definition Description)*
  定义列表:是一个包含术语定义以及描述的列表
  >><dl>
  >><dt>Firefox</dt>
  >><dd>A free, open source, cross-platform, graphical web browser
  >>developed by the Mozilla Corporation and hundreds of volunteers.
  >></dd>
  >></dl>

- **form**:
  此区域包含交互控件，用于向 Web 服务器提交信息
  搭配标签\<input>,\<label>标签使用

  >><form action="https://www.baidu.com" method="post" class="form-example">
  >><div class="form-example">
  >>  <label for="name">Enter your name: </label>
  >>  <input type="text" name="name" id="name" required>
  >></div>
  >><div class="form-example">
  >>  <label for="email">Enter your email: </label>
  >>  <input type="email" name="email" id="email" required>
  >></div>
  >><div class="form-example">
  >>  <input type="submit" value="Subscribe!">
  >></div>
  >></form>
  
  form表单向服务器提交的数据是一个字符串，其形式为`'name1=input1输入信息&name2=input1输入信息···'`

## 浮动

>>最初，引入 float 属性是为了能让 web 开发人员实现简单的布局，包括在一列文本中浮动的图像，文字环绕在它的左边或右边;或者用浮动创建一个有趣的drop-cap(首字下沉)效果

<img style='float:left; margin-right: 30px;' src="https://mdn.mozillademos.org/files/13340/butterfly.jpg" alt="A pretty butterfly with red, white, and brown coloring, sitting on a large leaf">
<p> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla luctus aliquam dolor, eu lacinia lorem placerat vulputate. Duis felis orci, pulvinar id metus ut, rutrum luctus orci. Cras porttitor imperdiet nunc, at ultricies tellus laoreet sit amet. Sed auctor cursus massa at porta. Integer ligula ipsum....</p>

<h1 style='float:left;'>l</h1><p style='vertical-align:bottom;line-height:130px'>earing</p>

- **清除浮动**
  >> 为什么清除浮动：因为浮动元素是**脱离标准流**的，所以会影响父元素与后面的元素布局，产生的具体副作用有：父元素高度塌陷；影响下方元素

  1、在**最后**一个**子元素新添加最后一个冗余元素**，然后将其设置clear:both,这样就可以清除浮动。这里强调一点，即在父级元素末尾添加的元素必须是一个块级元素，否则无法撑起父级元素高度。
  <style>
  #wrap{
  border: 1px solid;
  }
  #inner{
       float: left;
       width: 200px;
       height: 200px;
       background: pink;
       color:black;
  }
  </style>
  <div id="wrap">父元素
  <div id="inner">浮动元素</div>
  <div style="clear: both;"></div>
  </div>

  ----

  2、给父元素添加伪元素::after
  <style>
  .clearfix::after {
    content: ' ';
    display: block;
    clear: both;
    height:0;
    line-height:0;
    visibility:hidden;//允许浏览器渲染它，但是不显示出来
   }
   </style>
   <div id="wrap" class="clearfix">父元素
   <div id="inner">浮动元素</div>
   </div>

   ----
  3、给父元素使用overflow:hidden;
     这种方案让父容器形成了BFC(块级格式上下文)，而BFC可以包含浮动，通常用来解决浮动父元素高度坍塌的问题。
   <style>
    #wrap{overflow:hidden}
   </style>
   <div id="wrap">父元素
   <div id="inner">浮动元素</div>
   </div>

   ----
   4、在**最后**一个**子元素新添加br标签**，设置为\<br clear="all" />

## 层叠上下文

<style>
.stack{
  position:relative;
  height:500px;
}
.stack div
{
  width: 200px;
  height:80px;
  position:absolute;
  color:white;
  font-size:18px;
  padding-top:10px;
  padding-left:10px;
  box-sizing:border-box;
}
.div1 {
  background-color: #b475c1;
}
.div2 {
  background-color: #8275c1;
  top:56px;
  left:72px;
}
.div3 {
  background-color: #4e80c4;
  top:122px;
  left:144px;
}
.div4 {
  background-color: #4ec4ac;
  top: 188px;
  left: 216px;
}
.div5 {
  background-color: #a4c443;
  top:254px;
  left:288px;
}
.div6 {
  background-color: #ea9632;
  top:320px;
  left:360px;
}
.div7{
  background-color: #ea3f32;
  top:386px;
  left:432px;
}
</style>
<div class='stack'>
<div class="div1">Background/Borders</div>
<div class="div2">Negative Z-Index</div>
<div class="div3">Block Level Boxes</div>
<div class="div4">Floated Boxes</div>
<div class="div5">Inline Boxes</div>
<div class="div6">Z-Index =0|auto</div>
<div class="div7">Positive Z-Index</div>
</div>

- 概念1：**z-index: 0和z-index: auto的区别**:
  >>
  >>- 当没有指定z-index的时候,所有元素都在会被渲染在默认层(auto)，默认层也就是0层。
  >> - z-index: 0 与没有定义 z-index 也就是z-index: auto在同一层级内没有高低之分，文档流中后出现的会覆盖先出现的。
  >>- z-index: 0 会创建层叠上下文z-index: auto不会创建层叠上下文。
  >><a href='https://www.jb51.net/css/785509.html'>详细分析</a>
**注意：当一个元素产生了层叠上下文，其子级层叠上下文的z-index值只在父级中才有意义，没有创建层叠上下文的元素同其父级处于一个层叠上下文**

## Position

- 1、static
  >>该关键字指定元素使用正常的布局行为，即元素在文档常规流中当前的布局位置。此时 top, right, bottom, left 和 z-index 属性无效。
  ----

- 2、relative
  >>元素先放置在未添加定位时的位置，再在不改变页面布局的前提下调整元素位置(因此会在此元素未添加定位时所在位置留下空白)
  <style>
    .box {
      display: inline-block;
      width: 100px;
      height: 100px;
      background: red;
      color: white;
    }
  #two {
    position: relative;
    top: 20px;
    left: 20px;
    background: blue;
  }

    </style>
    <div class="box" id="one">One</div>
    <div class="box" id="two">Two</div>
    <div class="box" id="three">Three</div>
    <div class="box" id="four">Four</div>
  
    ----
- 3、absolute
  >>**相对定位的元素并未脱离文档流**，而**绝对定位的元素则脱离了文档流**
  在**布置文档流中其它元素**时，**绝对定位元素不占据空间**。绝对定位元素**相对于最近的非 static 祖先元素定位**。当这样的祖先元素不存在时，则相对于ICB(inital container block, **初始包含块---以浏览器视窗内渲染HTML的空间为大小的矩形，也就是页面的第一屏**)

----

- 4、fixed
  >>**固定定位与绝对定位相似，但元素的包含块为viewport视口**。该定位方式常用于创建在滚动屏幕时仍固定在相同位置的元素。**fixed定位脱离文档流**

- 5、sticky
  >>粘性定位可以被认为是相对定位和固定定位的混合。**元素在跨越特定阈值前为相对定位**，**之后为固定定位**---MDN(感觉解释不太好)
  &nbsp;
  **粘性定位：可以说是static(没有定位) 和 固定定位fixed 的结合；它主要用在对 scroll 事件的监听上；简单来说，在滑动过程中，某个元素距离其父元素的距离达到 sticky 粘性定位的要求时(比如top：100px)；position:sticky这时的效果相当于fixed定位，固定到适当位置**
  &nbsp;
  - 使用：
  1、父元素不能overflow:hidden属性
  2、必须指定top、bottom、left、right4个值之一，否则只会处于相对定位
  3、父元素的高度不能低于sticky元素的高度
  4、sticky元素仅在其父元素内生效
  5、使用额外添加position：-webkit-sticky保证在ios设备上的兼容性
  <style>
    .sticky{
        box-sizing: border-box;
        height:200px;
        overflow:scroll;
      }
     .sticky dl {
        margin: 0;
        padding: 24px 0 0 0;
      }
     .sticky dt {
        background: #B8C1C8;
        border-bottom: 1px solid #989EA4;
        border-top: 1px solid #717D85;
        color: #FFF;
        font: bold 18px/21px Helvetica, Arial, sans-serif;
        margin: 0;
        padding: 2px 0 0 12px;
        position: -webkit-sticky;
        position: sticky;
        top: -1px;
      }
     .sticky dd {
        font: bold 20px/45px Helvetica, Arial, sans-serif;
        margin: 0;
        padding: 0 0 0 12px;
        white-space: nowrap;
      }
      .sticky dd + dd {
        border-top: 1px solid #CCC
      }

  </style>
  <div class="sticky">
  <dl>
    <dt>A</dt>
    <dd>Andrew W.K.</dd>
    <dd>Apparat</dd>
    <dd>Arcade Fire</dd>
    <dd>At The Drive-In</dd>
    <dd>Aziz Ansari</dd>
  </dl>
  <dl>
    <dt>C</dt>
    <dd>Chromeo</dd>
    <dd>Common</dd>
    <dd>Converge</dd>
    <dd>Crystal Castles</dd>
    <dd>Cursive</dd>
  </dl>
  <dl>
    <dt>E</dt>
    <dd>Explosions In The Sky</dd>
  </dl>
  <dl>
    <dt>T</dt>
    <dd>Ted Leo & The Pharmacists</dd>
    <dd>T-Pain</dd>
    <dd>Thrice</dd>
    <dd>TV On The Radio</dd>
    <dd>Two Gallants</dd>
  </dl>

</div>

## BFC

>>块级格式化上下文,把BFC理解成一块独立的渲染区域，BFC看成是元素的一种属性， 当元素拥有了BFC属性后，这个元素就可以看做成隔离了的独立容器。容器内的元素不会影响容器外的元素。

- 实现BFC的方法
  
  >>1. float不为none
  >>2. position为fixed或absolute
  >>3. display 为 inline-block 、table-cell、table-caption、table、table-row、table-row-group、table-header-grou>>table-footer-group、inline-table、flow-root、flex、inline-flex、grid或 inline-grid
  >>4. overflow 除了 visible 以外的值(hidden，auto，scroll)
  >>5. 根元素html 就是一个 BFC

- 应用
  1、避免外边距重叠(防止margin塌陷)
  >>通过**给其中一个div包裹一个父div，设置BFC属性**，来解决margin塌陷的问题

  2、用于清除浮动(防止父元素塌陷)
  >> 通过**给父元素设置BFC属性**，来实现清除浮动

  3、阻止元素被浮动元素覆盖(两栏布局，防止float将文字挤走)
  >>通过给**被影响的兄弟元素设置BFC属性**，来解决被覆盖的情况

## 盒子模型

>>把所有的网页元素都看成一个盒子，它具有： content，padding，border，margin 四个属性，这就是盒子模型

- 分类
  1、W3C标准盒子模型(box-sizing:content-box;)
  >> 一个块的宽度 = width+padding(内边距)+border(边框)+margin(外边距);
  >>
  >>- 即CSS中定义的宽(width)=内容(content)的宽；
      CSS中的高(height)=内容(content)的高

  2、IE盒子模型(box-sizing:border-box;)
  >>一个块的宽度 = width+margin(外边距) (即怪异模式下，width包含了border以及padding)
  >>
  >>- CSS中的宽(width)=内容(content)的宽+(border+padding)*2
CSS中的高(height)=内容(content)的高+(border+padding)*2

- 补充：background-color
  >>**（1）一般div元素的background-color只覆盖到border，而其margin的颜色由外层元素的背景色决定**
 （2）body元素的background-color覆盖了除子元素之外的部分，包括其自身的margin。
 （3）但当html设置background-color，body的background-color就只覆盖到border，与一般div元素相同。而html的背景色覆盖了body的border以外的部分

## cookie sessionStorage localStorage 异同点

相同点：
>>1、都是浏览器存储
&nbsp;
2、都存储在浏览器本地

不同点：
>>1、cookie由**服务器写入**，sessionStorage以及localStorage都是由**前端写入**
&nbsp;
2、cookie的生命周期由**服务器端写入时就设置好**的；localStorage是写入就**一直存在，除非手动清除**，sessionStorage是由**页面关闭时自动清除**
&nbsp;
3、cookie存储空间大小约**4kb**，sessionStorage及localStorage空间比较大，大约**5M**
&nbsp;
4、三者的数据共享都遵循**同源原则**，**sessionStorage**还限制必须是**同一个页面**
&nbsp;
5、前端给后端发送请求时，**自动携带cookie**, session 及 local都不携带
&nbsp;
6、cookie一般存储**登录验证信息或者token**，localStorage常用于**存储不易变动的数据，减轻服务器压力**，**sessionStorage可以用来监测用户是否是刷新进入页面，如音乐播放器恢复进度条功能**

## CSS尺寸单位

<dl><dt>px</dt>
<dd>绝对长度单位，像素大小</dd>
</dl>
<dl><dt>em</dt>
<dd>根据父元素的字体(font-size)大小决定当前元素em单位的长度</dd>
</dl>
<dl><dt>rem</dt>
<dd>根据根元素（页面）的字体大小来决定当前元素rem单位的长度</dd>
</dl>
<dl><dt>vw&vh</dt>
<dd>根据视口大小的百分比</dd>
</dl>
<dl><dt>%</dt>
<dd>根据父元素的宽高的百分比</dd>
</dl>

## <a href='https://tech.meituan.com/2018/09/27/fe-security.html'>XSS攻击</a>

## <a href='https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0#%E6%B7%BB%E5%8A%A0%E6%A0%A1%E9%A9%97token'>CSRF</a>

1.概念：跨域请求伪造
2.原理：诱导用户跳转到新的页面，利用服务器的验证漏洞和用户之前的登入状态，来模拟用户进行操作
3.防范：利用cookie的sameSize属性规定其他网站不能使用本网站的cookie,或者使用token验证，再去验证用户身份

举个栗子：
比如在不安全聊天室或论坛上的一张图片，它实际上是一个给你银行服务器发送提现的请求：\<img src="http://bank.example.com/withdraw?account=bob&amount=1000000&for=mallory">
当你打开含有了这张图片的 HTML 页面时，如果你之前已经登录了你的银行帐号并且 Cookie 仍然有效（还没有其它验证步骤），你银行里的钱很可能会被自动转走。

## JS获取元素的大小（高度和宽度）

<dl>
<dt>.clientWidth</dt>
<dd>获取元素可视部分的宽度，即 CSS 的 width 和 padding 属性值之和，元素边框和滚动条不包括在内，也不包含任何可能的滚动区域</dd>
<dt>.clientHeight</dt>
<dd>获取元素可视部分的高度，即 CSS 的 height 和 padding 属性值之和，元素边框和滚动条不包括在内，也不包含任何可能的滚动区域</dd>
<dt>.offsetWidth</dt>
<dd>元素在页面中占据的宽度总和，包括 width、padding、border 以及滚动条的宽度</dd>
<dt>.offsetHeight</dt>
<dd>元素在页面中占据的高度总和，包括 height、padding、border 以及滚动条的宽度</dd>
<dt>.scrollWidth</dt>
<dd>当元素设置了 overflow:visible 样式属性时，元素的总宽度，也称滚动宽度。在默认状态下，如果该属性值大于 clientWidth 属性值，则元素会显示滚动条，以便能够翻阅被隐藏的区域</dd>
<dt>.scrollHeight</dt>
<dd>当元素设置了 overflow:visible 样式属性时，元素的总高度，也称滚动高度。在默认状态下，如果该属性值大于 clientWidth 属性值，则元素会显示滚动条，以便能够翻阅被隐藏的区域</dd>
<dt>window.getComputedStyle("元素"[, "伪类"])</dt>
<dd></dd>
</dl>

## src与href的区别

***一句话概括：src用于替代这个元素，而href用于建立这个标签与外部资源之间的关系；href会并行下载，src会暂停其他资源的下载与处理***

<dl>
<dt>hred</dt>
<dd>是Hypertext Reference的简写，表示超文本引用，指向外部资源所在位置；常用于a与link标签</dd>
<dt>src</dt>
<dd>是source的简写，目的是要把文件下载到html页面中去；常用于img，script，iframe标签</dd>
<dt>浏览器解析方式</dt>
<dd>当浏览器遇到href会并行下载资源并且不会停止对当前文档的处理；
当浏览器解析到src ，会暂停其他资源的下载和处理，直到将该资源加载和执行完毕。
</dd>
</dl>