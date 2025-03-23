# TypeScript 笔记

[toc]

## 接口类型的联合与交叉

### 结构性兼容与子类型兼容

鸭子类型（Duck Typing）是一种动态类型的概念，它的核心思想是：

> “如果它像鸭子，走起来像鸭子，叫起来也像鸭子，那么它就是鸭子。”

在 TypeScript 中，这意味着只要某个对象具备某种结构，即使它没有显式地声明实现某个接口，TypeScript 仍然会认为它符合该接口。

> 决定了不同类型之间是否可以相互赋值或传递

- 结构性兼容：
  > 👉 规则：
  > 目标类型的属性必须是源类型的子集，额外的属性无所谓。
  > 缺少属性时不兼容。
  > `p1 = p2 //p2必须要有p1的所有属性`

```TS
//结构性兼容示例：
interface A {
    foo: number;
}

interface B {
    foo: number;
    bar: string;
}

let a: A = { foo: 1 };
let b: B = { foo: 1, bar: "hello" };

a = b; // ✅ 结构性兼容
b = a; // ❌ 结构不兼容，缺少 bar
```

- 子类型兼容
  > 👉 规则：
  > 子类（Child）可以赋值给父类（Parent）
  > 父类（Parent）不能赋值给子类（Child）

```TS
//子类型兼容示例：
class Base {
    foo: number = 1;
}

class Derived extends Base {
    bar: string = "hello";
}

let base: Base;
let derived: Derived = new Derived();

base = derived; // ✅ 子类型兼容
derived = base; // ❌ 反向不兼容，Base 没有 bar
```

### 结构性兼容性 VS. 变量的额外属性检查

**1. 什么是额外属性检查？**
**额外属性检查**是 TypeScript 在**直接赋值对象字面量**时执行的**额外的静态检查**，用于检测对象是否包含了**目标类型未声明的属性**。这一检查主要用于**防止错误地向对象添加无效的属性**。

**2. 额外属性检查的触发条件**
只有在**直接赋值对象字面量**时，TypeScript 才会进行额外属性检查。例如：

```TS
interface T5 {
  a: number;
}

let e = { a: 1, b: 2 };

let g: T5 = e; // ✅ 变量赋值，不进行额外属性检查
let h: T5 = { a: 1, b: 2 }; // ❌ 直接赋值对象字面量，额外属性检查失败
```

**3. 为什么变量赋值不会触发额外属性检查？**
解释：
`e` 是一个变量，它的类型被 TypeScript 推导为 `{ name: string; age: number }`。
validUser 需要的 `name` 存在，TS 认为它是结构兼容的，不会报错。

### 联合类型赋值时——对象字面量与变量赋值的区别

**1. 直接赋值对象字面量时**
对于联合类型 `A | B`，对象字面量在直接赋值时必须满足下面的要求：

- **每个属性名必须出现在联合类型的** _至少一个_ **成员中。**这意味着：
- 如果对象字面量包含的属性，在联合类型的所有成员中都找不到，则会触发“额外属性检查”报错。
- 只要每个属性名在至少一个成员中存在，就不会因为多出属性而报错——哪怕这些属性在某个成员中不存在。

```ts
//例子
interface A {
  x: number;
}
interface B {
  y: number;
}

type AB = A | B;

// 直接赋值对象字面量
let value1: AB = { x: 1, y: 2 };
// ❌ 报错：没有任何一个成员（A 或 B）同时具有 x 和 y 属性
```

### 问题 ①

```ts
interface Bird {
  weight: number;
  wings: number;
}

interface Horse {
  weight: number;
  id: string;
}

type T = Omit<Bird | Horse, never>;
//type T = { weight: number; }

let bird: Bird = {
  weight: 10,
  wings: 2,
};

let x1: T = bird;
console.log(x1.wing); //❌报错;
// 把 bird 赋给 x1 没有 TS 类型错误，但是最后一行取 x1.wings
// 报错："Property 'wings' does not exist on type 'T'"
// 赋值的时候不报错，取值的时候报错，这里是不是有点点矛盾呢？
```

回答：
NOTE1：“子类的实例可以赋值给父类”同样可以放到子类型兼容的概念中去讲，这也是“子类型可以赋给父类型（例如将字符串字面量赋给 string 类型）”的原因。同样，这个逻辑，也就是“更具体的类型可以赋给更抽象的类型”的原因，鸟（bird）是一个更具体的“动物（Animal）”，又或者“生物”或“物”是相对于动物更抽象的数据概念，或类型。

NOTE2: 类型 T 与类型 Animal 本质上还是有些不同的。事实上因为类型 T 是通过 Omit<>计算出来的，所以它与 Bird/Horse 并没有显式的继承关系，在做兼容性计算时用的是“结构性兼容”。如果是 Animal 类型，并用 extends 声明过与 Bird/Horse 的继承性关系，那么计算时会是“子类型兼容”。尽管在这个例子中，这两种处理方法的结果一致，但概念上还是不同的。

### 问题 ②

## 📌 `declare namespace` vs `export namespace` 总结

| 特性             | declare namespace                                                             | export namespace                             |
| ---------------- | ----------------------------------------------------------------------------- | -------------------------------------------- |
| 作用             | 仅声明外部存在的命名空间（无实际代码）                                        | 组织模块代码，供 import 使用                 |
| 适用场景         | - 声明全局变量（如 jQuery）<br>- 声明全局 window 变量<br>- .d.ts 类型声明文件 | - 导出一组工具函数<br>- 导出相关的类型或变量 |
| 是否生成 JS 代码 | ❌ 不会                                                                       | ✅ 会                                        |
| 是否可以 import  | ❌ 不能（仅限全局作用域）                                                     | ✅ 需要 import { xxx }                       |

## TS 中`extends`的用法

| 场景     | 作用                         |
| -------- | ---------------------------- |
| 类继承   | 让子类继承父类的属性和方法   |
| 接口扩展 | 让新接口包含另一个接口的属性 |
| 泛型约束 | 限制泛型参数必须符合某种类型 |
| 条件类型 | 在类型判断中使用             |

```TS
type Exclude<T, U> = T extends U ? never : T;

type Result = Exclude<'a' | 'b' | 'c', 'a' | 'b'>; // 结果类型为 'c'

解析：

分布式条件类型： 当条件类型的左侧是一个裸类型参数（即未被包裹的类型参数）并且该类型是联合类型时，TypeScript 会对联合类型的每个成员分别应用条件类型，然后将结果合并。这被称为分布式条件类型。

应用到示例： 对于 Exclude<'a' | 'b' | 'c', 'a' | 'b'>，TypeScript 会将其拆分为：

'a' extends 'a' | 'b' ? never : 'a'
'b' extends 'a' | 'b' ? never : 'b'
'c' extends 'a' | 'b' ? never : 'c'
计算结果：

'a' 可以赋值给 'a' | 'b'，因此结果为 never。
'b' 可以赋值给 'a' | 'b'，因此结果为 never。
'c' 不能赋值给 'a' | 'b'，因此结果为 'c'。
合并结果： 将上述结果合并，得到 never | never | 'c'，简化后结果为 'c'。。
```

## TS 中`infer`的用法

```js
type E<T> = T extends (a: infer P) => infer R ? [P, R] : never;
type F<T> = T extends Array<inter I> ? I :never;
```

| 用法                                               | 作用                      |
| -------------------------------------------------- | ------------------------- |
| `T extends Promise<infer R>`                       | 提取 Promise 的返回值类型 |
| `T extends (...args: any[]) => infer R`            | 提取函数的返回值类型      |
| `T extends (infer U)[]`                            | 提取数组的元素类型        |
| 递归 infer                                         | 提取嵌套类型              |
| `T extends (arg1: infer A, ...args: any[]) => any` | 提取函数的第一个参数类型  |

## `Exclude` VS. `Omit`

| 对比项   | Exclude<T, U>              | Omit<T, K>                            |
| -------- | -------------------------- | ------------------------------------- |
| 作用     | 排除联合类型中的某些类型   | 移除对象类型中的某些键                |
| 适用于   | 联合类型（`A \| B`）       | 对象类型                              |
| 底层实现 | `T extends U ? never : T`  | `Pick<T, Exclude<keyof T, K>>`        |
| 示例     | `Exclude<"a" \| "b", "a">` | `Omit<{ a: string; b: number }, "a">` |

## implements VS. extends

- `implements`：用于**类实现一个或多个接口**，确保类实现了接口声明的**所有方法和属性**。

- `extends`：用于**继承类或继承接口**，可以用于**单一的类继承、接口继承或多重接口继承**。

## type 与 interface 的区别

### 1. **基本功能**

- **interface**

  - 专用于定义对象的结构
  - 可以被继承和扩展(通过`extends')
  - 支持实现类的契约(通过“implements')
  - 支持声明合并（多个同名接口会合并）
  - 更倾向于定义明确的形状或数据结构

- **type**
  - 更通用，除了定义对象结构，还可以用于创建联合类型、交叉类型、基本类型别名等
  - 不支持直接扩展，但可以通过交叉类型(`&`)或者联合类型(`|`)来实现类似扩展的功能
  - 不支持声明合并
  - 用于更灵活或复杂的类型定义

###
