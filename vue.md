# Vue 笔记

[TOC]

## Vue 响应式原理

| 实现原理 | `defineProperty`                               | `Proxy`                                     | `getter` `setter`   |
| -------- | ---------------------------------------------- | ------------------------------------------- | ------------------- |
| 实际场景 | Vue 2 响应式                                   | Vue 3 `reactive`                            | Vue 3 `ref`         |
| 优势     | 兼容性                                         | 基于 Proxy 实现真正的拦截，自动代理嵌套对象 | 实现简单            |
| 劣势     | 数组和属性删除等监听不了，深度嵌套对象需要递归 | 兼容不了 IE11                               | 只拦截了 value 属性 |
| 实际应用 | Vue 2                                          | Vue 3 复杂数据结构                          | Vue 3 简单数据结构  |

### Vue2 defineProperty

```JS
const getDouble = (n) => n*2
const obj = {}
let count = 1
let double = getDouble(count)

Object.defineProperty(obj,'count',{
    get(){
        return count
    },
    set(val){
        count = val
        double = getDouble(val)
    }
})
console.log(double)  // 打印2
obj.count = 2
console.log(double) // 打印4  有种自动变化的感觉
delete obj.count
console.log(double) // doube还是4
```

### Vue3 reactive

```js
const reactiveMap = new WeakMap();

function reactive(target) {
  // 如果 target 不是对象，直接返回
  if (typeof target !== "object" || target === null) {
    return target;
  }

  // 如果 target 已经被代理，直接返回已有的代理对象
  const existingProxy = reactiveMap.get(target);
  if (existingProxy) {
    return existingProxy;
  }

  const proxy = new Proxy(target, {
    get(target, key, receiver) {
      // 收集依赖：调用 track() 函数
      track(target, key);

      // 递归处理嵌套对象，返回 reactive 代理对象
      const result = Reflect.get(target, key, receiver);
      return typeof result === "object" && result !== null
        ? reactive(result)
        : result;
    },
    set(target, key, value, receiver) {
      const oldValue = target[key];
      const result = Reflect.set(target, key, value, receiver);

      // 如果值有变化，触发依赖更新
      if (oldValue !== value) {
        trigger(target, key);
      }
      return result;
    },
    deleteProperty(target, key) {
      const result = Reflect.deleteProperty(target, key);
      trigger(target, key);
      return result;
    },
  });

  // 缓存该代理对象
  reactiveMap.set(target, proxy);
  return proxy;
}
```

### Vue3 ref

```js
// 判断值是否发生了变化（简单版）
function hasChanged(newVal, oldVal) {
  return newVal !== oldVal;
}

// 将值转换为响应式对象（如果是对象的话）
function convert(value) {
  return typeof value === "object" && value !== null ? reactive(value) : value;
}

// 模拟依赖收集与触发更新的函数（实际实现会更复杂）
function trackRefValue(ref) {
  // 假设 activeEffect 为当前正在执行的副作用函数
  if (activeEffect) {
    ref.dep.add(activeEffect);
  }
}

function triggerRefValue(ref) {
  ref.dep.forEach((effect) => effect());
}

// Ref 的实现类
class RefImpl {
  constructor(value) {
    this._rawValue = value;
    this._value = convert(value);
    this.dep = new Set(); // 存储依赖（副作用函数）
    this.__v_isRef = true; // 标记为 ref
  }

  get value() {
    // 读取时收集依赖
    trackRefValue(this);
    return this._value;
  }

  set value(newVal) {
    if (!hasChanged(newVal, this._rawValue)) {
      return;
    }
    this._rawValue = newVal;
    this._value = convert(newVal);
    // 触发依赖更新
    triggerRefValue(this);
  }
}

// ref 函数返回一个 RefImpl 的实例
function ref(value) {
  return new RefImpl(value);
}
```

## 深入响应式原理

### 目标

```js
// 当 A0，A1改变时，A2也需同步更新
A0 = 1;
A1 = 2;
A2 = A0 + A1;
```

为了能重新运行计算的代码来更新 A2，我们需要将其包装为一个函数：

```js
function update() {
  A2 = A0 + A1;
}
```

### 定义术语

1. **副作用**：因为 `update` 会更改程序里面的 `A2` 的状态，我们称之函数会产生一个副作用，简称作用(effect)
2. **依赖**：因为执行 `update` 需要依赖`A0`,`A1`，所以 `A0` 和 `A1` 被视为这个作用的依赖 (dependency)
3. **订阅者**：作用也可以被称作它的依赖的一个订阅者

### 实现

```js
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key) {
      track(target, key);
      return target[key];
    },
    set(target, key, value) {
      target[key] = value;
      trigger(target, key);
    },
  });
}

function ref(value) {
  const refObject = {
    get value() {
      track(refObject, "value");
      return value;
    },
    set value(newValue) {
      value = newValue;
      trigger(refObject, "value");
    },
  };
  return refObject;
}
```

可以看到 vue3 在创建响应式对象的时候，分别在 `get` 阶段进行依赖收集 track、在 `set` 阶段执行副作用 trigger

#### track()

在 `track() `内部，我们会检查当前是否有正在运行的副作用。如果有，我们会查找到一个存储了所有追踪了该属性的订阅者的 `Set`，然后将当前这个副作用作为新订阅者添加到该 `Set` 中。

```js
// 这会在一个副作用就要运行之前被设置
// 我们会在后面处理它
let activeEffect;

function track(target, key) {
  if (activeEffect) {
    const effects = getSubscribersForProperty(target, key);
    effects.add(activeEffect);
  }
}
```

副作用订阅将被存储在一个全局的 `WeakMap<target, Map<key, Set<effect>>>` 数据结构中

#### trigger

在 `trigger()`之中，我们会再查找到该属性的所有订阅副作用。但这一次我们需要执行它们：

```js
function trigger(target, key) {
  const effects = getSubscribersForProperty(target, key);
  effects.forEach((effect) => effect());
}
```

#### whenDepsChange(updateFn) 函数 也可以视为 watchEffect(updateFn),computed(updateFn)

```js
function whenDepsChange(update) {
  const effect = () => {
    activeEffect = effect;
    update();
    activeEffect = null;
  };
  effect();
}
```

当`whenDepsChange`首次执行时，`update`函数语句`A2 = A0 + A1;`
读取`A0`与`A1`时，触发了 `track`，收集了依赖，给 A2 赋值的时候触发了`trigger`,后续修改`A0`与`A1`时，A2 便会自动更新。

#### 为什么是 WeakMap

使用 WeakMap，当目标对象（例如某个 reactive 对象）在其它地方不再被引用时，它在 WeakMap 中的对应映射也会自动失效，从而避免内存泄漏。

> 内存泄漏是指程序中已经不再使用的数据或对象没有被释放，导致这些内存空间无法被回收利用，从而在程序运行过程中逐渐占用越来越多的内存资源。长时间运行后，这种“未释放”的内存会积累，可能会导致程序性能下降、内存耗尽，甚至引发程序崩溃。

## template VS. JSX return()

### JSX

虚拟 DOM 在 React 和大多数其他实现中都是**纯运行时**的：更新算法无法预知新的虚拟 DOM 树会是怎样，因此它**总是需要遍历整棵树**

### template

Vue 框架同时控制着**编译器**和**运行时**，这使得我们可以为紧密耦合的模板渲染器应用许多**编译时优化**。包括：

1. 缓存静态内容

2. 更新类型标记

   ```HTML
   <!-- 仅含 class 绑定 -->
   <div :class="{ active }"></div>

   <!-- 仅含 id 和 value 绑定 -->
   <input :id="id" :value="value" />

   <!-- 仅含文本子节点 -->
   <div>{{ dynamic }}</div>
   ```

3. 树结构打平

## 渲染机制

Vue 组件挂载时会发生如下几件事：

1. **编译**：Vue 模板被编译为渲染函数：即用来返回虚拟 DOM 树的函数。这一步骤可以通过构建步骤提前完成，也可以通过使用运行时编译器即时完成。

2. **挂载**：运行时渲染器调用渲染函数，遍历返回的虚拟 DOM 树，并基于它创建实际的 DOM 节点。这一步会作为响应式副作用执行，因此它会追踪其中所用到的所有响应式依赖。

3. **更新**：当一个依赖发生变化后，副作用会重新运行，这时候会创建一个更新后的虚拟 DOM 树。运行时渲染器遍历这棵新树，将它与旧树进行比较，然后将必要的更新应用到真实 DOM 上去。

![alt text](./images/vue渲染机制.png)

## 侦听器与响应式解构

### 用 watchEffect 与 watch 来理解响应式

- watchEffect: ==立即==运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行。

  - 侦听 ref

  ```js
  const count = ref(0);

  watchEffect(() => console.log(count.value));
  // -> 输出 0

  count.value++;
  // -> 输出 1
  ```

  这里的时候追踪依赖的时候触发了 count 的 `get value(){}`，所以后续 `set value(){}` 的时候，能触发其副作用

  举个反例

  ```js
  let count = ref(0);

  watchEffect(() => console.log(count));
  // -> 输出 count这个ref

  count.value++;
  count = ref(100);
  // -> 都不会输出，因为读取count本身没有get 或者 set 所以不会有副作用的收集与触发
  ```

  - 侦听 reactive
    侦听器会自动启用深层模式，会侦听响应式对象的所有属性，包括嵌套属性

- watch：侦听一个或多个响应式数据源，并在数据源变化时调用所给的回调函数。默认是**懒侦听**的，即**仅在侦听源发生变化时**才执行回调函数。

  - 侦听 ref

  ```js
  const count = ref(0);

  watch(count, (count) => console.log(count));
  // -> 不输出

  count.value++;
  // -> 输出 1
  ```

  举个反例

  ```js
  const count = ref(0);

  watch(count.value, (count) => console.log(count));
  //[Vue warn]: Invalid watch source:  0 A watch source can only be a getter/effect function, a ref, a reactive object, or an array of these types.
  ```

  - 侦听 reactive
    侦听器会自动启用深层模式，会侦听响应式对象的所有属性，包括嵌套属性

- 总结：

  1. watchEffect：对于 ref ，需要使用`.value`，对于 reactive，如果没有指定侦听的属性，那么则侦听其所有属性，包括嵌套属性。

  2. watch：因为限定了第一个入参的条件：

  - 一个函数，返回一个值
  - 一个 ref
  - 一个响应式对象
  - ...或是由以上类型的值组成的数组
    所以会自行收集对 ref 的 value 依赖，对于 reactive 表现与 watchEffect 一致

### props 解构

Vue 对 props 做了特殊处理：

props 会被包裹在一个 只读的 Proxy 对象 中（通过 shallowReadonly 实现）。

props 的响应式是**浅层（Shallow）**的：只有直接访问 props 的顶层属性会触发响应式，嵌套对象的属性默认不会自动追踪。

**为什么解构后会失去响应式？**

- 在 3.4 及以下版本，name 是一个实际 string 的常量，永远不会改变。

```js
const { name } = defineProps<name:string>();
watchEffect(() => {
  // 在 3.5 之前只运行一次
  // 在 3.5+ 中在 "foo" prop 变化时重新执行
  console.log(foo);
});
watch(()=>foo,(foo)=>console.log(foo)) //在3.5之前，foo改变不会执行，但是3.5之后会
watch(foo,(foo)=>console.log(foo))  //无论3.5之前或者之后都会报错
```

- 在 3.5 及以上版本，当在同一个 `<script setup>`代码块中访问由 defineProps 解构的变量时，Vue 编译器会自动在前面添加 props.

  因此，上面的代码等同于以下代码：

  ```js
  const props = defineProps(["foo"]);

  watchEffect(() => {
    // `foo` 由编译器转换为 `props.foo`
    console.log(props.foo); //可以进行依赖收集
  });
  watch(
    () => props.foo,
    (foo) => console.log(foo)
  );
  ```
