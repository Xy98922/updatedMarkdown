# react笔记

## 为什么要从class组件转向function组件（hooks出现的意义）

<a href='https://zh-hans.reactjs.org/docs/hooks-intro.html'>官方解释：</a>
1、在组件之间<a href='https://blog.csdn.net/qq_39207948/article/details/113819803'>复用状态逻辑</a>很难
2、复杂组件变得难以理解（生命周期处理）

>> <a href='https://www.zhihu.com/question/343314784/answer/970219202'>函数式组件的心智模型更加“声明式”</a>，hooks（主要是useEffect）取代了生命周期的概念（减少API），让开发者的代码更加“声明化”：
旧的思维：“我在这个生命周期要检查props.A和state.B（props和state），如果改变的话就触发xxx副作用”。这种思维在后续修改逻辑的时候很容易漏掉检查项，造成bug。
新的思维：“我的组件有xxx这个副作用，这个副作用依赖的数据是props.A和state.B”。从过去的命令式转变成了声明式编程。
其实仔细想一想，人们过去使用生命周期不就是为了判断执行副作用的时机吗？现在hooks直接给你一个声明副作用的API，使得生命周期变成了一个“底层概念”，无需开发者考虑。开发者工作在更高的抽象层次上了。
类似的道理，除了声明副作用的API，react还提供了声明“密集计算”的API（useMemo），取代了过去“在生命周期做dirty检查，将计算结果缓存在state里”的做法。React内核帮你维护缓存，你只需要声明数据的计算逻辑以及数据的依赖。

3、难以理解的 class（学习成本+this）
4、Hook避免了class需要的额外开支，像是创建类实例和在构造函数中绑定事件处理器的成本。

## 为什么要用虚拟DOM

>>虚拟dom:就是一个普通的js对象

<a href='https://www.csdn.net/tags/NtTakgwsOTQxMDYtYmxvZwO0O0OO0O0O.html#DOM_47'>详解</a>
1、真实的DOM运行是很慢的，其元素非常庞大，页面的性能问题，大部分都是由DOM操作引起的，**真实的DOM节点，哪怕一个最简单的div也包含着很多属性，由此可见，操作真实DOM的代价仍旧是昂贵的**，频繁操作还是会出现页面卡顿，影响用户的体验

2、虚拟 dom 是相对于浏览器所渲染出来的真实 dom而言的，在react，vue等技术出现之前，我们要改变页面展示的内容**只能通过遍历查询 dom 树的方式找到需要修改的 dom 然后修改样式行为或者结构**，来达到更新 ui 的目的，这种方式相当消耗计算资源，因为每次查询 dom 几乎都需要遍历整颗 dom 树，如果建立一个与 dom 树对应的虚拟 dom 对象（ js 对象），**以对象嵌套的方式来表示 dom 树及其层级结构，那么每次 dom 的更改就变成了对 js 对象的属性的增删改查，这样一来查找 js 对象的属性变化要比查询 dom 树的性能开销小**

3、通过diff算法对比新旧vdom之间的差异，可以批量的、最小化的执行 dom操作，从而提高性能。