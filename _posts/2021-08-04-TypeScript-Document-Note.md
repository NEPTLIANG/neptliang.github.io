---
layout:       post
title:        "TypeScript部分文档机翻整理"
subtitle:     "null"
date:         2021-08-04 14:34:56
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 面向 JavaScript 开发者的 TypeScript

TypeScript 与 JavaScript 有着不同寻常的关系。TypeScript 提供了 JavaScript 的所有功能，以及在这些功能之上的附加层：TypeScript 的类型系统。

例如，JavaScript提供`string`和`number`之类的关键字，但不会检查你始终如一地指定这些。TypeScript 会。

这意味着您现有的 JavaScript 代码也是 TypeScript 代码。TypeScript 的主要好处是它可以突出显示代码中的意外行为，从而降低出现错误的机会。

本教程简要概述了 TypeScript，重点介绍了它的类型系统。

## 类型推断

TypeScript 了解 JavaScript 语言，并且会在许多情况下为您生成类型。例如，在创建变量并将其分配给特定值时，TypeScript 将使用该值作为其类型。

```ts
let helloWorld = "Hello World";
```

> ```ts
>         let helloWorld: string
> ```

通过了解 JavaScript 的工作原理，TypeScript 可以构建一个接受 JavaScript 代码但具有类型的类型系统。这提供了一个类型系统，而无需添加额外的字符来使代码中的类型变成显式。这就是 TypeScript 如何知道的helloWorld在上面的例子中是一个`string`。

您可能已经在 Visual Studio Code 中编写了 JavaScript，并且拥有编辑器自动完成功能。Visual Studio Code 在底层使用 TypeScript 来更轻松地使用 JavaScript。

## 定义类型

您可以在 JavaScript 中使用多种设计模式。但是，某些设计模式很难自动推断类型（例如，使用dynamic programming的模式）。为了涵盖这些情况，TypeScript 支持 JavaScript 语言的扩展，它为您提供了地方来告诉 TypeScript 类型应该是什么。

例如，要创建一个包含`name: string`和`id: number`的推断类型的对象，您可以编写：

```ts
const user = {
  name: "Hayes",
  id: 0,
};
```

您可以使用`interface`声明显式描述此对象的模型：

```ts
interface User {
  name: string;
  id: number;
}
```

然后，您可以在变量声明后面使用类似于`: TypeName`的语法来声明符合您的新`interface`模型的 JavaScript 对象：

```ts
const user: User = {
  name: "Hayes",
  id: 0,
};
```

如果您提供的对象与您提供的接口不匹配，TypeScript 会警告您：

```ts
interface User {
  name: string;
  id: number;
}

const user: User = {
  username: "Hayes",
```

> ```
> Type '{ username: string; id: number; }' is not assignable to type 'User'.
>   Object literal may only specify known properties, and 'username' does not exist in type 'User'.
> ```
> （类型'`{ username: string; id: number; }`'不可分配给类型'`User`'。 对象字面量只能指定已知属性，并且“`User`”类型中不存在“`username`”。）

```ts
  id: 0,
};
```

由于 JavaScript 支持类和面向对象编程，因此 TypeScript 也支持。您可以对类使用接口声明：

```ts
interface User {
  name: string;
  id: number;
}

class UserAccount {
  name: string;
  id: number;

  constructor(name: string, id: number) {
    this.name = name;
    this.id = id;
  }
}

const user: User = new UserAccount("Murphy", 1);
```

您可以使用接口来注解函数的参数和返回值：

```ts
function getAdminUser(): User {
  //...
}

function deleteUser(user: User) {
  // ...
}
```

JavaScript 中已经有一小组原始类型可用：`boolean`, `bigint`, `null`, `number`, `string`, `symbol`, 和 `undefined`，您可以在接口中使用它们。TypeScript 用更多类型扩展了这个列表，例如`any`（允许任何东西），`unknown`（确保使用该类型的人声明了该类型是什么），`never`（这种类型不可能发生），和`void`（一个返回`undefined`或没有返回值的函数）。

您将看到构建类型有两种语法：Interfaces 和 Types。你应该更喜欢`interface`。`type`当您需要特定功能时使用。

## 组合类型

使用 TypeScript，您可以通过组合简单类型来创建复杂类型。有两种流行的方法可以做到这一点：使用联合和使用泛型。

### 联合

使用联合，您可以声明一个类型可以是多种类型之一。例如，您可以将`boolean`类型描述为`true`或`false`：

```ts
type MyBool = true | false;
```

*注意*：如果您将鼠标悬停在MyBool上方，您会看到它被归类为`boolean`。这是结构类型系统的一个属性。下面有更多相关内容。

联合类型的一个流行用例是描述一个值所允许的`string`或`number`字面值的集合：

```ts
type WindowStates = "open" | "closed" | "minimized";
type LockStates = "locked" | "unlocked";
type PositiveOddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;
```

联合也提供了一种处理不同类型的方法。例如，您可能有一个接受一个`array`或一个`string`的函数：

```ts
function getLength(obj: string | string[]) {
  return obj.length;
}
```

要了解变量的类型，请使用`typeof`：


类型	|断言
-------|-----------------------------
string	|`typeof s === "string"`
number	|`typeof n === "number"`
boolean	|`typeof b === "boolean"`
undefined	|`typeof undefined === "undefined"`
function	|`typeof f === "function"`
array	|`Array.isArray(a)`

例如，您可以根据传递的是字符串还是数组，使函数返回不同的值：

```ts
function wrapInArray(obj: string | string[]) {
  if (typeof obj === "string") {
    return [obj];
```

> ```ts
>             (parameter) obj: string
> ```

```ts
  }
  return obj;
}
```

### 泛型

泛型为类型提供变量。一个常见的例子是数组。没有泛型的数组可以包含任何东西。带有泛型的数组可以描述数组包含的值。

```ts
type StringArray = Array<string>;
type NumberArray = Array<number>;
type ObjectWithNameArray = Array<{ name: string }>;
```

您可以声明自己的使用泛型的类型：

```ts
interface Backpack<Type> {
  add: (obj: Type) => void;
  get: () => Type;
}

// This line is a shortcut to tell TypeScript there is a
// constant called `backpack`, and to not worry about where it came from.
// 这一行是一个快捷方式，告诉TypeScript这里有一个
// 名为“backpack”的常量，不用担心它来自哪里。
declare const backpack: Backpack<string>;

// object is a string, because we declared it above as the variable part of Backpack.
// object是一个字符串，因为我们在上面把它声明为Backpack的变量部分。
const object = backpack.get();

// Since the backpack variable is a string, you can't pass a number to the add function.
// 由于backpack变量是一个字符串，所以不能向add函数传递数字。
backpack.add(23);
```

> ```
> Argument of type 'number' is not assignable to parameter of type 'string'.
> ```
> （类型'number'的参数不能赋给类型'string'的参数。）

## 结构类型系统

TypeScript 的核心原则之一是类型检查侧重于值的*模式（Shape）*。这有时被称为“鸭子类型”或“结构类型”。

在结构类型系统中，如果两个对象具有相同的模式，则认为它们属于同一类型。

```ts
interface Point {
  x: number;
  y: number;
}

function logPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}

// logs "12, 26"
const point = { x: 12, y: 26 };
logPoint(point);
```

`point`变量从未被声明为一个`Point`类型。但是，TypeScript 会在类型检查中将 `point` 的模式与`Point`的模式进行比较。它们具有相同的模式，因此代码通过检查。

模式匹配只需要匹配对象字段的子集。

```ts
const point3 = { x: 12, y: 26, z: 89 };
logPoint(point3); // logs "12, 26"

const rect = { x: 33, y: 3, width: 30, height: 80 };
logPoint(rect); // logs "33, 3"

const color = { hex: "#187ABF" };
logPoint(color);
```
> ```
> Argument of type '{ hex: string; }' is not assignable to parameter of type 'Point'.
>   Type '{ hex: string; }' is missing the following properties from type 'Point': x, y
> ```
> （参数类型'`{ hex: string; }`'不能赋给类型为'Point'的参数。）  
>   （类型'`{ hex: string; }`'缺少类型'Point'中的以下属性:x, y）

类和对象如何符合模式之间没有区别：

```ts
class VirtualPoint {
  x: number;
  y: number;

  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}

const newVPoint = new VirtualPoint(13, 56);
logPoint(newVPoint); // logs "13, 56"
```

如果对象或类具有所有必需的属性，则无论实现细节如何，TypeScript 都会说它们匹配。

---


# 基础

> 欢迎来到手册的第一页。如果这是您第一次使用 TypeScript - 您可能希望从[“入门”](https://www.typescriptlang.org/docs/handbook/intro.html#get-started)指南之一开始

JavaScript 中的每个值都有一组行为，您可以通过运行不同的操作来观察这些行为。这听起来很抽象，但作为一个简单的例子，考虑一些我们可能在名为`message`的变量上运行的操作。

```js
// Accessing the property 'toLowerCase'
// on 'message' and then calling it
// 在“message”上访问属性“toLowerCase”，
// 然后调用它
message.toLowerCase();
// Calling 'message'
message();
```

如果我们将其分解，第一行可运行的代码会访问一个名为`toLowerCase`的属性，然后调用它。第二行尝试直接调用`message`。

但是假设我们不知道 `message` 的值——这很常见——我们不能可靠地说明尝试运行这些代码会得到什么结果。每个操作的行为完全取决于我们最初拥有的值。

* `message`可以调用吗？
* 它是否有一个名为 `toLowerCase` 的属性？
* 如果有，`toLowerCase`可以调用吗？
* 如果这两个值都是可调用的，它们会返回什么？

这些问题的答案通常是我们在编写 JavaScript 时牢记在心的事情，我们必须希望我们得到了正确的所有细节。

假设`message`是按以下方式定义的。

```ts
const message = "Hello World!";
```

你可能猜到了，如果我们尝试运行`message.toLowerCase()`，我们只会得到小写的相同字符串。

那第二行代码呢？如果您熟悉 JavaScript，您会知道这会失败并出现异常：

```
TypeError: message is not a function
```

如果我们能避免这样的错误，那就太好了。

当我们运行我们的代码时，我们的 JavaScript 运行时选择做什么的方式是确定值的类型——它具有什么样的行为和能力。这就是 TypeError 所暗示的部分内容——它表示字符串`"Hello World!"`不能作为函数调用。

对于某些值，例如primitives `string`和`number`，我们可以在运行时使用`typeof`运算符识别它们的类型。但是对于其他的东西，比如函数，没有相应的运行时机制来识别它们的类型。例如，考虑这个函数：

```ts
function fn(x) {
  return x.flip();
}
```

我们可以通过阅读代码*观察*到，这个函数只有在给定一个具有可调用`flip`属性的对象时才能工作，但是 JavaScript 并没有以我们可以在代码运行时检查的方式显示这些信息。在纯 JavaScript 中，判断fn对某一值做什么的唯一方法是调用它并查看会发生什么。这种行为使得在运行之前很难预测代码会做什么，这意味着更难在编写代码时知道代码会做什么。

这样看来，*类型*是描述哪些值可以被传递给 `fn` 而哪些值被传递会导致崩溃的概念。JavaScript 仅真正提供*动态*类型——运行代码以查看发生了什么。

另一种方法是使用*静态*类型系统在代码运行*之前*预测预期的代码。

## 静态类型检查

回想一下我们之前尝试将`string`作为函数调用时得到的 `TypeError`。 *大多数人*不喜欢在运行他们的代码时遇到任何类型的error——那些被认为是bug！当我们编写新代码时，我们会尽力避免引入新的bug。

如果我们只添加一点代码，保存我们的文件，重新运行代码，然后立即看到error，我们也许可以快速isolate问题；但情况并非总是如此。我们可能没有对这个功能进行足够彻底的测试，所以我们可能永远不会真正遇到被抛出的潜在error！或者，如果我们有幸目睹了这个error，我们可能最终会进行大规模的重构并添加许多不同的代码，我们不得不深入研究这些代码。

理想情况下，我们可以有一个工具来帮助我们在代码运行*之前*找到这些错误。这就是像 TypeScript 这样的静态类型检查器所做的。 *静态类型系统*描述了当我们运行程序时我们的值的模式和行为。像 TypeScript 这样的类型检查器使用这些信息并告诉我们什么时候事情可能会冲出轨道。

```ts
const message = "hello!";
 
message();
```
> ```
> This expression is not callable.
>   Type 'String' has no call signatures.
> ```
> （此表达式不可调用。）  
>   （类型'String'没有call signatures。）

在我们首先运行代码之前，使用 TypeScript 运行最后一个示例会给我们一个错误消息。

## 非异常故障

到目前为止，我们一直在讨论某些类似运行时错误的东西——JavaScript 运行时告诉我们它认为某些事情是荒谬的情况。出现这些情况是因为[ ECMAScript 规范](https://tc39.github.io/ecma262/)明确说明了语言在遇到意外情况时应该如何表现。

例如，规范说尝试调用不可调用的东西应该会抛出error。也许这听起来像是“明显的行为”，但您也许会认为访问对象上不存在的属性也会抛出error。相反，JavaScript 为我们提供了不同的行为并返回`undefined`：

```ts
const user = {
  name: "Daniel",
  age: 26,
};

user.location; // returns undefined
```

最终，静态类型系统必须调用应该在其系统中标记为错误的代码，即使它是不会立即抛出错误的“有效”JavaScript。在 TypeScript 中，以下代码会产生关于`location`未定义的error：

```ts
const user = {
  name: "Daniel",
  age: 26,
};
 
user.location;
```

> ```
> Property 'location' does not exist on type '{ name: string; age: number; }'.
> ```

虽然有时这意味着在您可以表达的内容上进行权衡，但其目的是捕捉我们程序中的合法错误。TypeScript 捕获了*很多*合法的错误。

例如：拼写错误，

```ts
const announcement = "Hello World!";
 
// How quickly can you spot the typos?
announcement.toLocaleLowercase();
announcement.toLocalLowerCase();
 
// We probably meant to write this...
announcement.toLocaleLowerCase();
```

未调用的函数，

```ts
function flipCoin() {
  // Meant to be Math.random()
  return Math.random < 0.5;
```
> ```
> Operator '<' cannot be applied to types '() => number' and 'number'.
> ```
```ts
}
```

或基本逻辑错误。

```ts
const value = Math.random() < 0.5 ? "a" : "b";
if (value !== "a") {
  // ...
} else if (value === "b") {
```
> ```
> This condition will always return 'false' since the types '"a"' and '"b"' have no overlap.
> ```
```ts
  // Oops, unreachable
}
```

## 类型工具

当我们在代码中出错时，TypeScript 可以捕获错误。这很好，但 TypeScript *也*可以从一开始就阻止我们犯这些错误。

类型检查器具有检查诸如我们是否正在访问正确的变量属性和其他属性之类的信息。一旦有了这些信息，它还可以开始*建议*您可能想要使用哪些属性。

这意味着 TypeScript 也可以用于编辑代码，核心类型检查器可以在您在编辑器中键入时提供错误消息和代码补全。这是人们在谈论 TypeScript 中的工具时经常提到的部分内容。

![自动补全示例（https://www.typescriptlang.org/docs/handbook/2/basic-types.html#types-for-tooling）](https://www.typescriptlang.org/docs/handbook/2/basic-types.html#types-for-tooling)

TypeScript 非常重视工具，而且超出了您键入时的补全和错误。支持 TypeScript 的编辑器可以提供“快速修复”以自动修复错误、重构以轻松重新组织代码，以及用于跳转到变量定义或查找对给定变量的所有引用的有用导航功能。所有这些都建立在类型检查器之上，并且是完全跨平台的，因此[您最喜欢的编辑器很可能具有可用的 TypeScript 支持](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Editor-Support)。

## `tsc`——TypeScript 编译器

我们一直在谈论类型检查，但我们还没有使用我们的类型*检查器*。让我们熟悉一下我们的新朋友`tsc`——TypeScript 编译器。首先，我们需要通过 npm 获取它。

```bash
npm install -g typescript
```

> 这将全局安装 TypeScript 编译器`tsc`。如果您希望从本地node_modules包运行`tsc`，则可以使用`npx`或类似的工具。

现在让我们移动到一个空文件夹并尝试编写我们的第一个 TypeScript 程序`hello.ts`：

```ts
// Greets the world.
console.log("Hello world!");
```

请注意，这里没有多余的装饰；这个“hello world”程序看起来与您用 JavaScript 编写的“hello world”程序相同。现在让我们通过运行typescript包为我们安装的`tsc`命令来对它进行类型检查。

```sh
tsc hello.ts
```

我们跑了`tsc`，什么也没发生！好吧，没有类型错误，所以我们没有在控制台中得到任何输出，因为没有什么要报告的。

但再次检查 - 我们得到了一些*文件*输出。如果我们查看当前目录，我们会在 `hello.ts` 旁边看到一个 `hello.js` 文件。这是我们的`hello.ts`文件在 `tsc` *编译*或*转换*为纯 JavaScript 文件后的输出。如果我们检查内容，我们会看到 TypeScript 在处理`.ts`文件后会吐出什么：

```ts
// Greets the world.
console.log("Hello world!");
```

在这种情况下，TypeScript 几乎不需要转换，所以它看起来和我们写的一样。编译器试图发出看起来像人会写的东西的干净可读的代码。虽然这并不总是那么容易，但 TypeScript 会始终如一地缩进，注意我们的代码何时跨越不同的代码行，并试图保留注释。

如果我们*确实*引入了类型检查错误怎么办？让我们重写`hello.ts`：

```ts
// This is an industrial-grade general-purpose greeter function:
function greet(person, date) {
  console.log(`Hello ${person}, today is ${date}!`);
}
 
greet("Brendan");
```

如果我们再次运行`tsc hello.ts`，注意我们在命令行上收到error！

```
Expected 2 arguments, but got 1.
```

TypeScript 告诉我们，我们忘记将参数传递给`greet`函数，这是理所当然的。到目前为止，我们只编写了标准的 JavaScript，但类型检查仍然能够发现我们代码的问题。感谢TypeScript！

## Emitting错误

从上一个示例中您可能没有注意到的一件事是我们的`hello.js`文件再次改变了。如果我们打开该文件，我们会看到内容与我们的输入文件看起来基本相同。考虑到 `tsc` 报告了关于我们的代码的错误，这可能有点令人惊讶，但这基于 TypeScript 的核心价值观之一：很多时候，*你*会比 TypeScript 更了解情况。

重申一下，类型检查代码限制了您可以运行的程序种类，因此需要权衡类型检查器认为可以接受的类型。大多数时候没关系，但在某些情况下，这些检查会妨碍您。例如，假设您将 JavaScript 代码迁移到 TypeScript 并引入类型检查错误。最终，您将开始为类型检查器清理内容，但原始的 JavaScript 代码已经可以工作了！为什么要将其转换为 TypeScript 会阻止您运行它？

所以 TypeScript 不会妨碍你。当然，随着时间的推移，您可能希望对错误更具防御性，并使 TypeScript 的行为更加严格。在这种情况下，您可以使用[`noEmitOnError`](https://www.typescriptlang.org/tsconfig#noEmitOnError)编译器选项。尝试更改您的`hello.ts`文件并使用该标志运行`tsc`：

```sh
tsc --noEmitOnError hello.ts
```

你会注意到 hello.js 永远不会更新。

## 显式类型

到目前为止，我们还没有告诉 TypeScript 什么是`person`、什么是`date`。让我们编辑代码以告诉 TypeScript：`person`是一个`string`，并且`date`应该是一个`Date`对象。我们还将使用`date`上的 `toDateString()` 方法。

```ts
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
```

我们所做的是在 `person` 和 `date` 上添加*类型注解*来描述`greet`可以以什么类型的值调用。您可以将该标记解读为“`greet` 接受一个`string`类型的`person`，以及一个`Date`类型的`date`。

有了这个，TypeScript 可以告诉我们其他`greet`可能被错误调用的情况。例如…

```ts
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
 
greet("Maddison", Date());
```
> ```
> Argument of type 'string' is not assignable to parameter of type 'Date'.
> ```

嗯？TypeScript 在我们的第二个参数上报告了一个错误，但是为什么呢？

Date()也许令人惊讶的是，在 JavaScript 中调用`Date()`会返回一个`string`。另一方面，用 `new Date()` 构造一个 `Date` 实际上给我们我们所期望的。

无论如何，我们可以快速修复错误：

```ts
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
 
greet("Maddison", new Date());
```

请记住，我们并不总是必须编写显式类型注解。在许多情况下，TypeScript 甚至可以为我们*推断*出（或“找出”）类型，即使我们省略它们。

```ts
let msg = "hello there!";
```
> ```ts    
>   let msg: string
> ```
> （如果您将鼠标悬停在单词上，这就是您的编辑器将显示的内容）

尽管我们没有告诉 TypeScript `msg` 有`string`类型，但它能够弄清楚这一点。这是一个特性，当类型系统最终会推断出相同的类型时，最好不要添加注解。

## 擦除类型

我们来看看当我们用 `tsc` 编译上面的函数 `greet` 来输出 JavaScript 时会发生什么：

```js
"use strict";
function greet(person, date) {
    console.log("Hello " + person + ", today is " + date.toDateString() + "!");
}
greet("Maddison", new Date());
```

这里注意两点：

1. 我们的`person`和`date`参数不再有类型注解。
2. 我们的“模板字符串”——那个使用反引号（``` ` ```字符）的字符串——被转换为带有连接（`+`）的纯字符串。

稍后会详细介绍第二点，但现在让我们关注第一点。类型注解不是 JavaScript 的一部分（或者迂腐地说 ECMAScript），所以实际上没有任何浏览器或其他运行时可以在未经修改的情况下运行 TypeScript。这就是 TypeScript 首先需要一个编译器的原因——它需要某种方式来剥离或转换所有 TypeScript 特有的代码，以便您可以运行它。大多数 TypeScript 特有的代码都被删除了，同样地，我们的类型注解也被完全删除了。

> **请记住**：类型注解永远不会改变程序的运行时行为。

## 降级

与上面的另一个区别是我们的模板字符串被从

```ts
`Hello ${person}, today is ${date.toDateString()}!`;
```

改写成

```js
"Hello " + person + ", today is " + date.toDateString() + "!";
```

为什么会这样？

模板字符串是一个称为 ECMAScript 2015（又名 ECMAScript 6、ES2015、ES6 等——不要问）的 ECMAScript 版本中的一项功能。TypeScript 能够将代码从较新版本的 ECMAScript 重写为较旧的版本，例如 ECMAScript 3 或 ECMAScript 5（又名 ES3 和 ES5）。从 ECMAScript 的较新版本或“较高”版本向下移动到较旧版本或“较低”版本的过程有时称为*降级（downleveling）*。

默认情况下，TypeScript 以 ES3 为目标，这是一个非常旧的 ECMAScript 版本。我们可以通过使用 [`target`](https://www.typescriptlang.org/tsconfig#target) 选项来选择一些较新的东西。使用 `--target es2015` 运行会将 TypeScript 更改为以 ECMAScript 2015 为目标，这意味着代码应该能够在任何支持 ECMAScript 2015 的地方运行。所以运行`tsc --target es2015 hello.ts`会给我们以下输出：

```js
function greet(person, date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
greet("Maddison", new Date());
```

> 虽然默认target是 ES3，但当前绝大多数浏览器都支持 ES2015。因此，大多数开发人员可以安全地将 ES2015 或更高版本指定为target，除非与某些古老的浏览器的兼容性很重要。

## 严格

不同的用户使用 TypeScript 在类型检查器中寻找不同的东西。有些人正在寻找一种更宽松的选择加入体验，它可以帮助验证他们程序的某些部分，并且仍然拥有不错的工具。这是 TypeScript 的默认体验，其中类型是可选的，推断采用最宽松的类型，并且不检查潜在的`null`/`undefined`值。就像 `tsc` 在面对错误时发出的一样，这些默认设置是为了不妨碍你。如果您要迁移现有的 JavaScript，那么这可能是理想的第一步。

相比之下，许多用户更喜欢让 TypeScript 尽可能立即验证，这就是该语言也提供严格设置的原因。这些严格设置将静态类型检查从开关（或检查或不检查您的代码）变成更接近仪表盘的东西。你把这个仪表盘调得越远，TypeScript 就会越多地为你检查。这可能需要一些额外的工作，但一般来说，从长远来看，它会为自己付出代价，并且可以进行更彻底的检查和更精确的工具。如果可能，新的代码库应始终打开这些严格检查。

TypeScript 有几个可以打开或关闭的类型检查严格标志，除非另有说明，否则我们所有的示例都将在启用所有这些标志的情况下编写。CLI 中的 [`strict`](https://www.typescriptlang.org/tsconfig#strict) 标志，或 [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html) 中的 `"strict": true` 会同时将它们全部打开，但我们可以分别选择退出它们。你最应该知道的两个是`noImplicitAny`和`strictNullChecks`。

## `noImplicitAny`

回想一下，在某些地方，TypeScript 不会尝试为我们推断类型，而是回退到最宽松的类型：`any`. 这并不是可能发生的最糟糕的事情——毕竟，无论如何，回退到`any`只是纯 JavaScript 体验。

但是，使用`any`往往一开始就违背了使用 TypeScript 的目的。您程序的类型越多，您获得的验证和工具就越多，这意味着您在编写代码时遇到的错误就越少。打开 [`noImplicitAny`](https://www.typescriptlang.org/tsconfig#noImplicitAny) 标志将对所有类型被隐式推断为 `any` 的变量发出错误。

## `strictNullChecks`

默认情况下，类似`null`和`undefined`的值可以分配给任何其他类型。这可以使编写一些代码更容易，但是忘记处理 `null` 和 `undefined` 是世界上无数错误的原因——有些人认为这是一个[十亿美元的错误](https://www.youtube.com/watch?v=ybrQvs4x0Ps)！ [`strictNullChecks`](https://www.typescriptlang.org/tsconfig#strictNullChecks)标志使处理 `null` 和 `undefined` 更加明确，*让我们不必*担心是否*忘记*处理 `null` 和 `undefined`。

---


# 日常类型

在本章中，我们将介绍 JavaScript 代码中一些最常见的值类型，并解释在 TypeScript 中描述这些类型的相应方法。这不是一个详细的列表，未来的章节将描述更多命名和使用其他类型的方法。

类型也可以出现在更多的*地方*，而不仅仅是类型注解。当我们了解类型本身时，我们还将了解可以引用这些类型以形成新结构的地方。

我们将首先回顾您在编写 JavaScript 或 TypeScript 代码时可能遇到的最基本和最常见的类型。这些稍后将形成更复杂类型的核心构建块。

## primitives：`string`、`number`和`boolean`

JavaScript 具有三个非常常用的primitives：`string`、`number`和`boolean`。每个在 TypeScript 中都有对应的类型。如您所料，如果您对这些类型的值使用 JavaScript `typeof` 运算符，您会看到这些名称：

* `string`表示字符串值，如`"Hello, world"`
* `number`适用于像`42`. JavaScript 对整数没有特殊的运行时值，因此没有等价于`int`或`float`的类型——一切都只是`number`
* `boolean` 用于两个值：`true` 和 `false`

类型名称`String`, `Number`, 和`Boolean`（以大写字母开头）是合法的，但指的是一些很少出现在代码中的特殊内置类型。*始终*使用`string`、`number`或`boolean`表示类型。

## 数组

要指定像 `[1, 2, 3]` 这样的数组的类型，可以使用语法 `number[]`；此语法适用于任何类型（例如 `string[]` 是字符串数组，等等）。您可能还会看到这写为`Array<number>`，意思是一样的。当我们介绍*泛型*时，我们将了解更多关于语法`T<U>`的知识。

> 请注意，`[number]` 是另一回事；请参阅[元组](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types)部分。

## `any`

TypeScript 也有一个特殊的类型，`any`，当你不希望某个特定的值导致类型检查错误时，你可以使用它。

当一个值是 `any` 类型时，您可以访问它的任何属性（这又将是 `any` 类型），像函数一样调用它，将它分配给（或将任何类型的值分配给它）任何类型的值，或几乎任何其他在语法上合法的东西：

```ts
let obj: any = { x: 0 };
// None of the following lines of code will throw compiler errors.
// Using `any` disables all further type checking, and it is assumed 
// you know the environment better than TypeScript.
obj.foo();
obj();
obj.bar = 100;
obj = "hello";
const n: number = obj;
```

当你不想写出一个长类型来让 TypeScript 相信特定的代码行没问题时，`any` 类型很有用。

### `noImplicitAny`

当您不指定类型，并且 TypeScript 无法从上下文中推断出它时，编译器通常会默认为`any`.

但是，您通常希望避免这种情况，因为`any`没有经过类型检查。使用编译器标志[`noImplicitAny`](https://www.typescriptlang.org/tsconfig#noImplicitAny)将任何隐式`any`标记为错误。

## 变量的类型注解

当您使用`const`、`var`或`let`声明变量时，您可以选择添加类型注解以显式指定变量的类型：

```ts
let myName: string = "Alice";
```

> TypeScript 不使用“types on the left”风格的声明，如`int x = 0`; 类型注解将始终跟在被指定类型的内容*之后*。

但是，在大多数情况下，这不是必需的。TypeScript 会尽可能地尝试自动*推断*代码中的类型。例如，变量的类型是根据其初始化程序的类型推断的：

```ts
// No type annotation needed -- 'myName' inferred as type 'string'
let myName = "Alice";
```

在大多数情况下，您不需要明确学习推断规则。如果您刚开始，请尝试使用比您想象的更少的类型注解——您可能会惊讶于 TypeScript 完全理解正在发生的事情需要这么少。

## 函数

函数是在 JavaScript 中传递数据的主要方式。TypeScript 允许您指定函数的输入和输出值的类型。

### 参数类型注解

声明函数时，可以在每个参数后面加上类型注解，声明函数接受哪些类型的参数。参数类型注解在参数名称之后：

```ts
// Parameter type annotation
function greet(name: string) {
  console.log("Hello, " + name.toUpperCase() + "!!");
}
```

当参数具有类型注解时，该函数的参数将被检查：

```ts
// Would be a runtime error if executed!
greet(42);
```
> ```
> Argument of type 'number' is not assignable to > parameter of type 'string'.
> ```

> 即使您的参数上没有类型注解，TypeScript 仍会检查您是否传递了正确数量的参数。

### 返回类型注解

您还可以添加返回类型注解。返回类型注解出现在参数列表之后：

```ts
function getFavoriteNumber(): number {
  return 26;
}
```

与变量类型注解非常相似，您通常不需要返回类型注解，因为 TypeScript 会根据其`return`语句推断函数的返回类型。上面例子中的类型注解并没有改变任何东西。一些代码库将明确指定返回类型以用于文档目的，以防止意外更改，或仅出于个人喜好。

### 匿名函数

匿名函数与函数声明有点不同。当一个函数出现在 TypeScript 可以确定如何调用它的地方时，该函数的参数会自动被赋予类型。

这是一个例子：

```ts
// No type annotations here, but TypeScript can spot the bug
const names = ["Alice", "Bob", "Eve"];
 
// Contextual typing for function
names.forEach(function (s) {
  console.log(s.toUppercase());
```
> ```
> Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
> ```
```ts
});
 
// Contextual typing also applies to arrow functions
names.forEach((s) => {
  console.log(s.toUppercase());
```
> ```
> Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
> ```
```ts
});
```

即使参数`s`没有类型注解，TypeScript 还是使用`forEach`函数的类型以及推断的数组类型来确定 `s` 将具有的类型。

这个过程称为*上下文类型*，因为函数发生的*上下文*告知它应该具有什么类型。

与推断规则类似，您不需要明确了解这是如何发生的，但明白它*确实*会发生可以帮助您注意到何时不需要类型注解。稍后，我们将看到更多关于值出现的上下文如何影响其类型的示例。

## 对象类型

除了primitives之外，您会遇到的最常见的类型是*对象类型*。这指的是任何具有属性的 JavaScript 值，几乎是所有这些值！要定义对象类型，我们只需列出其属性及其类型。

例如，这是一个接受点状对象的函数：

```js
// The parameter's type annotation is an object type
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```

在这里，我们使用具有两个属性——x和y——的类型来注解参数，这两个属性都是 `number` 类型。您可以使用`,`或`;`分隔属性，最后一个分隔符是可选的。

每个属性的类型部分也是可选的。如果不指定类型，则假定为`any`。

### 可选属性

对象类型还可以指定它们的部分或全部属性是*可选的*。为此，请在属性名称后添加一个`?`：

```ts
function printName(obj: { first: string; last?: string }) {
  // ...
}
// Both OK
printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" });
```

在 JavaScript 中，如果你访问一个不存在的属性，你会得到 `undefined` 而不是运行时错误。因此，当您从可选属性中*读取数据*时，您必须在使用它之前检查 `undefined`。

```ts
function printName(obj: { first: string; last?: string }) {
  // Error - might crash if 'obj.last' wasn't provided!
  console.log(obj.last.toUpperCase());
```
> ```
> Object is possibly 'undefined'.
> ```
```ts
  if (obj.last !== undefined) {
    // OK
    console.log(obj.last.toUpperCase());
  }
 
  // A safe alternative using modern JavaScript syntax:
  console.log(obj.last?.toUpperCase());
}
```

## 联合类型

TypeScript 的类型系统允许您使用各种运算符从现有类型中构建新类型。现在我们知道如何编写几种类型，是时候开始以有趣的方式*组合*它们了。

### 定义联合类型

您可能会看到的第一种组合类型的方法是*联合*（union）类型。联合类型是由两种或多种其他类型组成的类型，表示可能是这些类型中的*任何*一种的值。我们将这些类型中的每一种都称为联合的成员。

让我们编写一个可以对字符串或数字进行操作的函数：

```ts
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
// OK
printId(101);
// OK
printId("202");
// Error
printId({ myID: 22342 });
```
> ```
> Argument of type '{ myID: number; }' is not assignable to parameter of type 'string | number'.
>   Type '{ myID: number; }' is not assignable to type 'number'.
> ```

### 使用联合类型

*提供*与联合类型匹配的值很容易——只需提供与联合的任一成员匹配的类型即可。如果你*有*一个联合类型的值，你如何使用它？

TypeScript 只有在对联合体的*每个*成员都有效的情况下才允许操作。例如，如果您有联合 `string | number`，您不能使用仅在字符串上可用的方法：

```ts
function printId(id: number | string) {
  console.log(id.toUpperCase());
```
> ```
> Property 'toUpperCase' does not exist on type 'string | number'.
>   Property 'toUpperCase' does not exist on type 'number'.
> ```
```ts
}
```

解决方案是用代码*压缩*联合，就像在没有类型注解的 JavaScript 中一样。 当 TypeScript 可以根据代码的结构为某个值推断出更具体的类型时，就会发生*压缩*。

例如，TypeScript 知道只有`string`值的`typeof`才会为`"string"`：

```ts
function printId(id: number | string) {
  if (typeof id === "string") {
    // In this branch, id is of type 'string'
    console.log(id.toUpperCase());
  } else {
    // Here, id is of type 'number'
    console.log(id);
  }
}
```

另一个例子是使用像 `Array.isArray` 这样的函数：

```ts
function welcomePeople(x: string[] | string) {
  if (Array.isArray(x)) {
    // Here: 'x' is 'string[]'
    console.log("Hello, " + x.join(" and "));
  } else {
    // Here: 'x' is 'string'
    console.log("Welcome lone traveler " + x);
  }
}
```

请注意，在`else`分支中，我们不需要做任何特别的事情——如果`x`不是 `string[]`，那么它一定是 `string`。

有时你会有一个联合，所有成员都有共同点。例如，数组和字符串都有一个`slice`方法。如果联合中的每个成员都有一个共同的属性，则可以在不压缩的情况下使用该属性：

```ts
// Return type is inferred as number[] | string
function getFirstThree(x: number[] | string) {
  return x.slice(0, 3);
}
```

> 类型的*联合*似乎具有这些类型的属性的*交集*，这可能会令人困惑。这不是意外——*联合*这个名字来源于类型论。*联合* `number | string`是通过取每种类型的*值的*联合组成的。请注意，给定两个集合，每个集合都有相应的事实，只有这些事实的*交集*适用于集合本身的*并集*。例如，如果我们有一个房间里有戴帽子的高个子，而另一个房间里有戴帽子、讲西班牙语的人，在组合这些房间后，我们对*每个*人的唯一了解就是他们一定戴着帽子。

## 类型别名

我们一直通过直接在类型注解中编写对象类型和联合类型来使用它们。这很方便，但通常希望多次使用同一个类型并用一个名称引用它。

*类型别名*就是这样——任意*类型*的*名称*。类型别名的语法是：

```ts
type Point = {
  x: number;
  y: number;
};
 
// Exactly the same as the earlier example
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

实际上，您可以使用类型别名来为任何类型命名，而不仅仅是对象类型。例如，类型别名可以命名联合类型：

```ts
type ID = number | string;
```

请注意，别名*只是*别名——您不能使用类型别名来创建相同类型的不同/不同“版本”。当您使用别名时，就好像您已经编写了别名类型。换句话说，这段代码可能*看起来*非法，但根据 TypeScript 是没问题的，因为这两种类型都是同一类型的别名：

```ts
type UserInputSanitizedString = string;
 
function sanitizeInput(str: string): UserInputSanitizedString {
  return sanitize(str);
}
 
// Create a sanitized input
// 创建一个无害化输入
let userInput = sanitizeInput(getInput());
 
// Can still be re-assigned with a string though
// 然而仍然可以重新分配一个string
userInput = "new input";
```

## 接口

*接口声明*是命名对象类型的另一种方式：

```ts
interface Point {
  x: number;
  y: number;
}
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

就像我们在上面使用类型别名时一样，该示例就像我们使用匿名对象类型一样工作。TypeScript 只关心我们传递给`printCoord`的值的*结构*——它只关心它是否具有预期的属性。只关心类型的结构和功能是我们称 TypeScript 为*结构类型*类型系统的原因。

### 类型别名和接口的区别

类型别名和接口非常相似，在很多情况下您可以在它们之间自由选择。`interface`的几乎所有特性都可以在`type`中使用，关键区别在于type不能重新打开以添加​​新属性，而interface始终可扩展。

![类型别名和接口的区别（https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces）](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)
        
您将在后面的章节中了解有关这些概念的更多信息，因此如果您不能立即理解所有这些概念，请不要担心。

* 在 TypeScript 4.2 版之前，类型别名[可能会出现在错误消息中](https://www.typescriptlang.org/play?#code/PTAEGEHsFsAcEsA2BTATqNrLusgzngIYDm+oA7koqIYuYQJ56gCueyoAUCKAC4AWHAHaFcoSADMaQ0PCG80EwgGNkALk6c5C1EtWgAsqOi1QAb06groEbjWg8vVHOKcAvpokshy3vEgyyMr8kEbQJogAFND2YREAlOaW1soBeJAoAHSIkMTRmbbI8e6aPMiZxJmgACqCGKhY6ABGyDnkFFQ0dIzMbBwCwqIccabcYLyQoKjIEmh8kwN8DLAc5PzwwbLMyAAeK77IACYaQSEjUWZWhfYAjABMAMwALA+gbsVjoADqgjKESytQPxCHghAByXigYgBfr8LAsYj8aQMUASbDQcRSExCeCwFiIQh+AKfAYyBiQFgOPyIaikSGLQo0Zj-aazaY+dSaXjLDgAGXgAC9CKhDqAALxJaw2Ib2RzOISuDycLw+ImBYKQflCkWRRD2LXCw6JCxS1JCdJZHJ5RAFIbFJU8ADKC3WzEcnVZaGYE1ABpFnFOmsFhsil2uoHuzwArO9SmAAEIsSFrZB-GgAjjA5gtVN8VCEc1o1C4Q4AGlR2AwO1EsBQoAAbvB-gJ4HhPgB5aDwem-Ph1TCV3AEEirTp4ELtRbTPD4vwKjOfAuioSQHuDXBcnmgACC+eCONFEs73YAPGGZVT5cRyyhiHh7AAON7lsG3vBggB8XGV3l8-nVISOgghxoLq9i7io-AHsayRWGaFrlFauq2rg9qaIGQHwCBqChtKdgRo8TxRjeyB3o+7xAA)，有时会代替等效的匿名类型（可能是也可能不是可取的）。接口将始终在错误消息中命名。
* 类型别名可能不参与[声明合并，但接口可以](https://www.typescriptlang.org/play?#code/PTAEEEDtQS0gXApgJwGYEMDGjSfdAIx2UQFoB7AB0UkQBMAoEUfO0Wgd1ADd0AbAK6IAzizp16ALgYM4SNFhwBZdAFtV-UAG8GoPaADmNAcMmhh8ZHAMMAvjLkoM2UCvWad+0ARL0A-GYWVpA29gyY5JAWLJAwGnxmbvGgALzauvpGkCZmAEQAjABMAMwALLkANBl6zABi6DB8okR4Jjg+iPSgABboovDk3jjo5pbW1d6+dGb5djLwAJ7UoABKiJTwjThpnpnGpqPBoTLMAJrkArj4kOTwYmycPOhW6AR8IrDQ8N04wmo4HHQCwYi2Waw2W1S6S8HX8gTGITsQA)。
* 接口只能用于[声明对象的模式，不能重命名原语](https://www.typescriptlang.org/play?#code/PTAEAkFMCdIcgM6gC4HcD2pIA8CGBbABwBtIl0AzUAKBFAFcEBLAOwHMUBPQs0XFgCahWyGBVwBjMrTDJMAshOhMARpD4tQ6FQCtIE5DWoixk9QEEWAeV37kARlABvaqDegAbrmL1IALlAEZGV2agBfampkbgtrWwMAJlAAXmdXdy8ff0Dg1jZwyLoAVWZ2Lh5QVHUJflAlSFxROsY5fFAWAmk6CnRoLGwmILzQQmV8JmQmDzI-SOiKgGV+CaYAL0gBBdyy1KCQ-Pn1AFFplgA5enw1PtSWS+vCsAAVAAtB4QQWOEMKBuYVUiVCYvYQsUTQcRSBDGMGmKSgAAa-VEgiQe2GLgKQA)。
* 接口名称将[*始终*以其原始形式出现](https://www.typescriptlang.org/play?#code/PTAEGEHsFsAcEsA2BTATqNrLusgzngIYDm+oA7koqIYuYQJ56gCueyoAUCKAC4AWHAHaFcoSADMaQ0PCG80EwgGNkALk6c5C1EtWgAsqOi1QAb06groEbjWg8vVHOKcAvpokshy3vEgyyMr8kEbQJogAFND2YREAlOaW1soBeJAoAHSIkMTRmbbI8e6aPMiZxJmgACqCGKhY6ABGyDnkFFQ0dIzMbBwCwqIccabcYLyQoKjIEmh8kwN8DLAc5PzwwbLMyAAeK77IACYaQSEjUWY2Q-YAjABMAMwALA+gbsVjNXW8yxySoAADaAA0CCaZbPh1XYqXgOIY0ZgmcK0AA0nyaLFhhGY8F4AHJmEJILCWsgZId4NNfIgGFdcIcUTVfgBlZTOWC8T7kAJ42G4eT+GS42QyRaYbCgXAEEguTzeXyCjDBSAAQSE8Ai0Xsl0K9kcziExDeiQs1lAqSE6SyOTy0AKQ2KHk4p1V6s1OuuoHuzwArMagA)在错误消息中，但*仅*在按名称使用时才出现。

在大多数情况下，您可以根据个人喜好进行选择，TypeScript 会告诉您是否需要其他类型的声明。如果您想要启发式方法，请使用`interface`直到您需要使用`type`中的功能。

## 类型断言

有时你会得到关于 TypeScript 无法知道的值类型的信息。

例如，如果您正在使用`document.getElementById`，TypeScript 只知道这将返回*某种* HTMLElement，但您可能知道您的页面将始终具有具有给定 ID 的 `HTMLCanvasElement`。

在这种情况下，您可以使用*类型断言*来指定更具体的类型：

```ts
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
```

与类型注解一样，类型断言被编译器删除，不会影响代码的运行时行为。

您还可以使用尖括号语法（除非代码在`.tsx`文件中），它是等效的：

```ts
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```

> 提醒：因为类型断言在编译时被删除，所以没有与类型断言关联的运行时检查。如果类型断言错误，则不会出现异常或生成`null`。

TypeScript 只允许类型断言转换为*更具体*或*更不具体*的类型版本。此规则可防止“不可能”的强制，例如：

```ts
const x = "hello" as number;
```
> ```
> Conversion of type 'string' to type 'number' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.
> ```
> （将“string”类型转换为“number”类型可能是一个错误，因为这两种类型都没有与另一种充分重叠。如果这是故意的，请先将表达式转换为“unknown”。）

有时，此规则可能过于保守，并且不允许可能有效的更复杂的强制转换。如果发生这种情况，您可以使用两个断言，首先是`any`（或`unknown`，我们稍后会介绍），然后是所需的类型：

```ts
const a = (expr as any) as T;
```

## 字面量类型

除了一般类型`string`和`number`之外，我们还可以在类型位置引用*特定*的字符串和数字。

考虑这一点的一种方法是考虑 JavaScript 如何使用不同的方法来声明变量。`var` 和 `let` 都允许更改变量中保存的内容，而 `const` 则不允许。这反映在 TypeScript 如何为字面量创建类型。

```ts
let changingString = "Hello World";
changingString = "Olá Mundo";
// Because `changingString` can represent any possible string, that
// is how TypeScript describes it in the type system
changingString;
```      
> ```ts
> let changingString: string
> ```
```ts 
const constantString = "Hello World";
// Because `constantString` can only represent 1 possible string, it
// has a literal type representation
constantString;
```
> ```ts
> const constantString: "Hello World"
> ```

就其本身而言，字面量类型并不是很有价值：

```ts
let x: "hello" = "hello";
// OK
x = "hello";
// ...
x = "howdy";
```
> ```
> Type '"howdy"' is not assignable to type '"hello"'.
> ```

变量只能有一个值并没有多大用处！

但是通过将字面量*组合*成联合，你可以表达一个更有用的概念——例如，只接受一组已知值的函数：

```ts
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left");
printText("G'day, mate", "centre");
```
> ```
> Argument of type '"centre"' is not assignable to parameter of type '"left" | "right" | "center"'.
> ```

数字字面量类型的工作方式相同：

```ts
function compare(a: string, b: string): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1;
}
```

当然，您可以将这些与非字面量类型结合使用：

```ts
interface Options {
  width: number;
}

function configure(x: Options | "auto") {
  // ...
}

configure({ width: 100 });
configure("auto");
configure("automatic");
```
> ```
> Argument of type '"automatic"' is not assignable to parameter of type 'Options | "auto"'.
> ```

还有一种字面量类型：布尔字面量。只有两种布尔字面量类型，正如您可能猜到的那样，它们是类型`true`和`false`。`boolean` 类型本身实际上只是联合 `true | false` 的别名。

### 字面量推断

当您使用对象初始化变量时，TypeScript 假定该对象的属性以后可能会改变值。例如，如果您编写如下代码：

```js
const obj = { counter: 0 };
if (someCondition) {
  obj.counter = 1;
}
```

TypeScript 不假定分配`1`给先前具有`0`的字段是错误的。另一种说法是`obj.counter`必须有类型`number`，而不是`0`，因为类型用于确定*读取*和*写入*行为。

这同样适用于字符串：

```ts
const req = { url: "https://example.com", method: "GET" };
handleRequest(req.url, req.method);
```
> ```
> Argument of type 'string' is not assignable to parameter of type '"GET" | "POST"'.
> ```

在上面的例子中 `req.method` 被推断为`string`，不是`"GET"`。因为可以在创建 `req` 和调用 `handleRequest` 之间执行代码，可以为 `req.method` 分配一个像`“GUESS”`这样的新字符串，TypeScript 认为这个代码有错误。

有两种方法可以解决这个问题。

1. 您可以通过在以下二者之任一位置添加类型断言来改变推断：

    ```ts
    // Change 1:
    const req = { url: "https://example.com", method: "GET" as "GET" };
    // Change 2
    handleRequest(req.url, req.method as "GET");
    ```

    更改 1 的意思是“我打算让 `req.method` 始终具有*字面量类型*`"GET"`”，从而防止在之后可能将`"GUESS"`分配给该字段。更改 2 的意思是“由于其他原因，我知道 `req.method` 的值是`"GET"`“。

2. 您可以使用`as const`将整个对象转换为类型字面量：

    ```ts
    const req = { url: "https://example.com", method: "GET" } as const;
    handleRequest(req.url, req.method);
    ```

`as const`后缀的作用类似于`const`，但用于类型系统，确保为所有属性分配字面量类型而不是更通用的版本，如`string`或`number`。

## `null`和`undefined`

JavaScript 有两个原始值用于表示不存在或未初始化的值：`null`和`undefined`。

TypeScript 有两个对应的同名*类型*。这些类型的行为方式取决于您是否启用了 [`strictNullChecks`](https://www.typescriptlang.org/tsconfig#strictNullChecks) 选项。

### `strictNullChecks`关闭

*关闭* `strictNullChecks`，仍然可以正常访问可能为 `null` 或 `undefined` 的值，并且可以将值 `null` 和 `undefined` 分配给任何类型的属性。这类似于没有空值检查的语言（例如 C#、Java）的行为方式。缺乏检查这些值往往是错误的主要来源；如果在他们的代码库中这样做是切实可行的，我们总是建议人们打开`strictNullChecks`。 

### `strictNullChecks`开启

*启用* `strictNullChecks` 后，当值为 `null` 或`undefined`时，您需要在使用该值的方法或属性之前测试这些方法或属性的值。就像在使用可选属性之前检查`undefined`一样，我们可以使用*压缩*来检查可能是`null`的值：

```ts
function doSomething(x: string | null) {
  if (x === null) {
    // do nothing
  } else {
    console.log("Hello, " + x.toUpperCase());
  }
}
```

### 非空断言运算符（后缀`!`）

TypeScript 还具有一种特殊的语法，可以在不进行任何显式检查的情况下从类型中删除 `null` 和 `undefined`。在任一表达式之后写`!`实际上是一个类型断言，意为该值不是`null`或`undefined`：

```ts
function liveDangerously(x?: number | null) {
  // No error
  console.log(x!.toFixed());
}
```

就像其他类型断言一样，这不会改变代码的运行时行为，所以只在您知道值*不能*为`null`或`undefined`时使用 `!`。

## 枚举

枚举是 TypeScript 添加到 JavaScript 的一项特性，它允许描述一个值，该值可能是一组可能的命名常量之一。与大多数 TypeScript 功能不同，这*不是*对 JavaScript 的类型拓展，而是添加到语言和运行时的东西。正因为如此，这是一个你应该知道存在的功能，但除非你确定，否则可能先别使用好点。您可以在[枚举参考页](https://www.typescriptlang.org/docs/handbook/enums.html)中阅读有关枚举的更多信息。

## 不太常见的原语

值得一提的是类型系统中表示的 JavaScript 中的其他原语。虽然我们不会在这里深入。

### `bigint`

从 ES2020 开始，JavaScript 中有一个用于非常大整数的原语 `BigInt`：

```js
// Creating a bigint via the BigInt function
const oneHundred: bigint = BigInt(100);
 
// Creating a BigInt via the literal syntax
const anotherHundred: bigint = 100n;
```

您可以在 [TypeScript 3.2 发行说明](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-2.html#bigint)中了解有关 BigInt 的更多信息。

### `symbol`

JavaScript 中有一个原语用于通过函数`Symbol()`创建全局唯一引用：

```ts
const firstName = Symbol("name");
const secondName = Symbol("name");
 
if (firstName === secondName) {
```
> ```
> This condition will always return 'false' since the types 'typeof firstName' and 'typeof secondName' have no overlap.
> ```
> （此条件将始终返回 'false'，因为类型 'typeof firstName' 和 'typeof secondName' 没有重叠。）
```ts
  // Can't ever happen
}
```

您可以在[Symbol参考页](https://www.typescriptlang.org/docs/handbook/symbols.html)中了解有关它们的更多信息。

---


# 压缩

假设我们有一个名为 `padLeft` 的函数。

```ts
function padLeft(padding: number | string, input: string): string {
  throw new Error("Not implemented yet!");
}
```

如果 `padding` 是一个`number`，它会将其视为我们要在`input`前添加的空格数。如果`padding`是`string`，它应该只是在`input`之前添加`padding`。让我们尝试实现 `padLeft` 被传递一个`number`作`padding`的逻辑。

```ts
function padLeft(padding: number | string, input: string) {
  return " ".repeat(padding) + input;
```
> ```
> Argument of type 'string | number' is not assignable to parameter of type 'number'.
>   Type 'string' is not assignable to type 'number'.
> ```
```ts
}
```

啊哦，我们在`padding`时遇到错误。TypeScript 警告我们添加`number | string`到`number`可能不会给我们想要的结果，这是正确的。换句话说，我们没有先明确检查`padding`是否是`number`，也没有处理它是`string`的情况，所以让我们这样做。

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```

如果这看起来像是无趣的 JavaScript 代码，那就是重点。除了我们放置的注解之外，这个 TypeScript 代码看起来像 JavaScript。这个想法是 TypeScript 的类型系统旨在尽可能简单地编写典型的 JavaScript 代码，而无需bending over backwards以获得类型安全。

虽然它可能看起来不多，但实际上这里有很多东西。就像 TypeScript 使用静态类型分析运行时值的方式一样，它在 JavaScript 的运行时控制流结构（如`if/else`、条件三元组、循环、真值检查等）上进行类型分析，这些都会影响这些类型。

在我们的 `if` 检查中，TypeScript 看到 `typeof padding === "number"` 并将其理解为一种称为*类型警卫*的特殊形式的代码。TypeScript 遵循我们的程序可以采用的可能执行路径来分析给定位置的值的最具体的可能类型。它着眼于这些特殊检查（称为*类型警卫*）和分配，将类型提炼为比声明的更具体的类型的过程称为*压缩*。在许多编辑器中，我们可以观察这些类型的变化，我们甚至会在示例中这样做。

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
```
> ```ts                        
>                     (parameter) padding: number
> ```
```ts
  }
  return padding + input;
```           
> ```ts
>         (parameter) padding: string
> ```
```ts
}
```

TypeScript 可以理解几种不同的结构来缩小范围。

## `typeof`类型警卫

正如我们所见，JavaScript 支持 typeof 运算符，它可以提供关于我们在运行时拥有的值类型的非常基本的信息。TypeScript 期望它返回一组特定的字符串：

* `"string"`
* `"number"`
* `"bigint"`
* `"boolean"`
* `"symbol"`
* `"undefined"`
* `"object"`
* `"function"`

就像我们看到的 `padLeft`，这个运算符经常出现在许多 JavaScript 库中，TypeScript 可以理解它以压缩不同逻辑分支中的类型。

在 TypeScript 中，检查`typeof`返回的值是一种类型警卫。因为 TypeScript 编码了`typeof`对不同值的操作方式，所以它知道它在 JavaScript 中的一些怪癖。例如，请注意在上面的列表中，`typeof`不返回字符串`null`。查看以下示例：

```ts
function printAll(strs: string | string[] | null) {
  if (typeof strs === "object") {
    for (const s of strs) {
```
> ```
> Object is possibly 'null'.
> ```
```ts
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  } else {
    // do nothing
  }
}
```

在 `printAll` 函数中，我们尝试检查 `strs` 是否是一个对象，看看它是否是一个数组类型（现在可能是强化数组在 JavaScript 中是对象类型这个知识点的好时机）。但事实证明，在 JavaScript 中，`typeof null`实际上是`"object"`！这是历史上一个不幸的事故。

有足够经验的用户可能不会感到惊讶，但并不是每个人都在 JavaScript 中遇到过这种情况；幸运的是，TypeScript 让我们知道 `strs` 仅压缩范围到 `string[] | null` 而不仅仅是 `string[]`。

这可能是对我们所谓的“真值”检查的一个很好的转场。

## 真值压缩

真值可能不是您在字典中可以找到的词，but it’s very much something you’ll hear about in JavaScript.

在 JavaScript 中，我们可以在条件、`&&`、`||`、`if`语句、布尔否定 (`!`) 等中使用任何表达式。例如，`if` 语句不期望它们的条件总是具有`boolean`类型。

```ts
function getUsersOnlineMessage(numUsersOnline: number) {
  if (numUsersOnline) {
    return `There are ${numUsersOnline} online now!`;
  }
  return "Nobody's here. :(";
}
```

在 JavaScript 中，像 `if` 这样的构造首先将它们的条件“强制”为`boolean`以理解它们，然后根据结果是`true`还是`false`来选择它们的分支。像这样的值：

* `0`
* `NaN`
* `""`（空字符串）
* `0n`（零的 `bigint` 版本）
* `null`
* `undefined`

全部强制为`false`，其他值强制为`true`。您始终可以通过`Boolean`函数或使用较短的双布尔否定来将值强制为`boolean`值。（后者的优点是 TypeScript 推断出一个压缩的字面量布尔类型 `true`，而将第一个推断为`boolean`类型。）

```ts
// both of these result in 'true'
Boolean("hello"); // type: boolean, value: true
!!"world"; // type: true,    value: true
```

利用这种行为是相当流行的，尤其是在防范 `null` 或 `undefined` 之类的值时。作为一个例子，让我们尝试将它用于我们的`printAll`函数。

```ts
function printAll(strs: string | string[] | null) {
  if (strs && typeof strs === "object") {
    for (const s of strs) {
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  }
}
```

你会注意到我们已经通过检查 `strs` 是否为真消除了上面的错误。这至少可以防止我们在运行代码时出现以下可怕的错误：

```
TypeError: null is not iterable
```

请记住，尽管对原语进行真值检查通常容易出错。例如，考虑编写 `printAll` 的不同尝试

```ts
function printAll(strs: string | string[] | null) {
  // !!!!!!!!!!!!!!!!
  //  DON'T DO THIS!
  //   KEEP READING
  // !!!!!!!!!!!!!!!!
  if (strs) {
    if (typeof strs === "object") {
      for (const s of strs) {
        console.log(s);
      }
    } else if (typeof strs === "string") {
      console.log(strs);
    }
  }
}
```

我们将整个函数体包装在一个真值检查中，但这有一个不易察觉的缺点：我们可能不再正确处理空字符串的情况。

TypeScript 在这里根本不会伤害我们，但如果您对 JavaScript 不太熟悉，这是值得注意的行为。TypeScript 通常可以帮助您及早发现错误，但如果您选择对值*不做任何事情*，那么它可以做的事情就只有这么多，而不会过于规范。如果您愿意，您可以使用 linter 确保处理此类情况。

关于真值压缩的最后一句话是布尔否定`!`从否定分支中过滤掉。

```ts
function multiplyAll(
  values: number[] | undefined,
  factor: number
): number[] | undefined {
  if (!values) {
    return values;
  } else {
    return values.map((x) => x * factor);
  }
}
```

## 相等压缩

TypeScript 还使用`switch`语句和相等性检查，如`===`、`!==`、`==`和`!=`来压缩类型。例如：

```ts
function example(x: string | number, y: string | boolean) {
  if (x === y) {
    // We can now call any 'string' method on 'x' or 'y'.
    x.toUpperCase();
```          
> ```
>     (method) String.toUpperCase(): string
> ```
```ts
    y.toLowerCase();
```          
> ```
>     (method) String.toLowerCase(): string
> ```
```ts
  } else {
    console.log(x);
```               
> ```
>               (parameter) x: string | number
> ```
```ts
    console.log(y);
```               
> ```
>               (parameter) y: string | boolean
> ```
```ts
  }
}
```

当我们在上面的示例中检查 `x` 和 `y` 是否相等时，TypeScript 知道它们的类型也必须相等。由于 `string` 是 `x` 和 `y` 都可以采用的唯一共同类型，TypeScript 知道 `x` 和 `y` 在第一个分支中必须是`string`。

检查特定的字面量值（而不是变量）也可以。在我们关于真值压缩的部分中，我们编写了一个容易出错的 `printAll` 函数，因为它意外地没有正确处理空字符串。相反，我们可以做一个特定的检查来阻止`null`，TypeScript 仍然可以正确地从 `strs` 的类型中删除`null`。

```ts
function printAll(strs: string | string[] | null) {
  if (strs !== null) {
    if (typeof strs === "object") {
      for (const s of strs) {
```                       
> ```
>                     (parameter) strs: string[]
> ```
```ts
        console.log(s);
      }
    } else if (typeof strs === "string") {
      console.log(strs);
```                   
> ```
>               (parameter) strs: string
> ```
```ts
    }
  }
}
```

JavaScript 更宽松的 `==` 和 `!=` 等式检查也正确地缩小了范围。如果您不熟悉，检查某物是否`== null`实际上不仅检查它是否明确地是`null`值——它还检查它是否潜在地是`undefined`。这同样适用于`== undefined`：它检查一个值是否是`null`或`undefined`。

```ts
interface Container {
  value: number | null | undefined;
}
 
function multiplyValue(container: Container, factor: number) {
  // Remove both 'null' and 'undefined' from the type.
  if (container.value != null) {
    console.log(container.value);
```                           
> ```
>                         (property) Container.value: number
> ```
```ts

    // Now we can safely multiply 'container.value'.
    container.value *= factor;
  }
}
```

## `in` 运算符压缩

JavaScript 有一个运算符，用于确定一个对象是否具有是某个属性名的属性：`in`运算符。TypeScript 将这一点视为压缩潜在类型的一种方式。

例如，使用代码：`"value" in x`。其中`"value"`是字符串字面量，`x` 是联合类型。“true”分支压缩了 `x` 具有可选或必需属性`value`的类型，而“false”分支压缩到具有可选或缺少属性`value`的类型。

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };
 
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
  }
 
  return animal.fly();
}
```

重申一下可选属性将存在于压缩范围的两侧，例如人类既可以游泳也可以飞行（使用正确的设备），因此应该出现在`in`检查的两侧：

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };
type Human = { swim?: () => void; fly?: () => void };
 
function move(animal: Fish | Bird | Human) {
  if ("swim" in animal) {
    animal;
```      
> ```
>   (parameter) animal: Fish | Human
> ```
```ts
  } else {
    animal;
```      
> ```
>   (parameter) animal: Bird | Human
> ```
```ts
  }
}
```

## `instanceof`压缩

JavaScript 有一个运算符来检查一个值是否是另一个值的“实例”。更具体地说，在 JavaScript 中 `x instanceof Foo` 检查 `x` 的原型链是否包含 `Foo.prototype`。虽然我们不会在这里深入探讨，当我们进入类时你会看到更多内容，但它们对于大多数可以用 `new` 构造的值仍然很有用。你可能已经猜到了，`instanceof` 也是一个类型警卫，TypeScript 在由 `instanceof` 保护的分支中压缩。

```ts
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
```               
> ```
>             (parameter) x: Date
> ```
```ts
  } else {
    console.log(x.toUpperCase());
```               
> ```
>             (parameter) x: string
> ```
```ts
  }
}
```

## 赋值

正如我们前面提到的，当我们为任一变量赋值时，TypeScript 会查看赋值的右侧并适当地压缩左侧。

```ts
let x = Math.random() < 0.5 ? 10 : "hello world!";
```   
> ```ts
> let x: string | number
> ```
```ts
x = 1;
 
console.log(x);
```           
> ```ts
>         let x: number
> ```
```ts
x = "goodbye!";
 
console.log(x);
```           
> ```ts
>         let x: string
> ```

请注意，这些赋值中的每一个都是有效的。即使在我们第一次赋值后观察到 `x` 的类型更改为`number`，我们仍然能够将字符串赋值给 `x`。这是因为 `x` *声明的类型*——`x` 开始的类型——是`string | number`，并且可分配性始终根据声明的类型检查。

如果我们为 `x` 分配了一个`boolean`值，我们会看到一个错误，因为它不是声明类型的一部分。

```ts
let x = Math.random() < 0.5 ? 10 : "hello world!";
```   
> ```ts
> let x: string | number
> ```
```ts
x = 1;
 
console.log(x);
```           
> ```ts
>         let x: number
> ```
```ts
x = true;
```
> ```
> Type 'boolean' is not assignable to type 'string | number'.
```
 
console.log(x);
```           
> ```ts
>         let x: string | number
> ```
```ts
```

## 控制流分析

到目前为止，我们已经通过一些基本示例来了解 TypeScript 如何在特定分支中缩小范围。但是除了从每个变量中走出来并在 `if`、`while`、条件等中寻找类型警卫之外，还有更多的事情要做。例如

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```

`padLeft`从其第一个`if`块内返回。TypeScript 能够分析此代码并发现在`padding`是`number`的情况下，函数体的其余部分（`return padding + input;`）是*无法到达*的。因此，它能够为函数的其余部分从`padding`的类型中删除`number`（从`string | number`压缩到`string`）。

这种基于可达性的代码分析被称为*控制流分析*，TypeScript 在遇到类型警卫和赋值时使用这种流分析来压缩类型。当分析一个变量时，控制流可以一次又一次地分裂和重新合并，并且可以观察到该变量在每个点具有不同的类型。

```ts
function example() {
  let x: string | number | boolean;
 
  x = Math.random() < 0.5;
 
  console.log(x);
```             
> ```ts
>           let x: boolean
> ```
```ts
 
  if (Math.random() < 0.5) {
    x = "hello";
    console.log(x);
```               
> ```ts
>             let x: string
> ```
```ts
  } else {
    x = 100;
    console.log(x);
```               
> ```ts
>             let x: number
> ```
```ts
  }
 
  return x;
```        
> ```ts
>       let x: string | number
> ```
```ts
}
```

## 使用类型谓词

到目前为止，我们已经使用现有的 JavaScript 结构来处理缩小范围，但是有时您希望更直接地控制类型在整个代码中的变化方式。

要定义用户自定义的类型警卫，我们只需要定义一个返回类型为*类型谓词*的函数：

```ts
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

`pet is Fish`是我们在这个例子中的类型谓词。谓词采用 `parameterName is Type` 的形式，其中 `parameterName` 必须是当前函数签名中的参数名称。

任何时候使用某个变量调用 `isFish` 时，如果原始类型兼容，TypeScript 就会将该变量*压缩*到该特定类型。

```ts
// Both calls to 'swim' and 'fly' are now okay.
let pet = getSmallPet();
 
if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```

请注意，TypeScript 不仅知道 `pet` 是 `if` 分支中的 `Fish` ；它也知道在 `else` 分支中，你*没有* `Fish`，所以你一定有 `Bird`。

您可以使用类型警卫`isFish`来过滤`Fish | Bird`数组并获得`Fish`数组：

```ts
const zoo: (Fish | Bird)[] = [getSmallPet(), getSmallPet(), getSmallPet()];
const underWater1: Fish[] = zoo.filter(isFish);
// or, equivalently
// 或者，等效地
const underWater2: Fish[] = zoo.filter(isFish) as Fish[];
 
// The predicate may need repeating for more complex examples
// 对于更复杂的示例，谓词可能需要重复
const underWater3: Fish[] = zoo.filter((pet): pet is Fish => {
  if (pet.name === "sharkey") return false;
  return isFish(pet);
});
```

此外，类可以[用`this is Type`](https://www.typescriptlang.org/docs/handbook/2/classes.html#this-based-type-guards)来压缩它们的类型。

## 有区别的联合

到目前为止，我们看到的大多数示例都集中在缩小具有简单类型（如`string`、`boolean`和`number`）的单个变量的范围。虽然这很常见，但大多数时候在 JavaScript 中我们将处理稍微复杂一些的结构。

出于某种动机，假设我们正在尝试对圆形和正方形等形状进行编码。圆记录它们的半径，正方形记录它们的边长。我们将使用一个名为`kind`的字段来辨别我们正在处理的形状。这是定义 `Shape` 的第一次尝试。

```ts
interface Shape {
  kind: "circle" | "square";
  radius?: number;
  sideLength?: number;
}
```

请注意，我们使用了字符串字面量联合的类型：`"circle"`和`"square"`来告诉我们应该将形状分别视为圆形还是方形。通过使用 `"circle" | "square"` 而不是`string`，我们可以避免拼写错误的问题。

```ts
function handleShape(shape: Shape) {
  // oops!
  if (shape.kind === "rect") {
```
> ```
> This condition will always return 'false' since the types '"circle" | "square"' and '"rect"' have no overlap.
> ```
```ts
    // ...
  }
}
```

我们可以编写一个`getArea`函数，根据它是处理圆形还是正方形来应用正确的逻辑。我们将首先尝试处理圆。

```ts
function getArea(shape: Shape) {
  return Math.PI * shape.radius ** 2;
```
> ```
> Object is possibly 'undefined'.
> ```
```ts
}
```

在 `strictNullChecks` 下给我们一个错误——这是合适的，因为`radius`可能没有被定义。但是如果我们对 `kind` 属性进行适当的检查呢？

```ts
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2;
```
> ```
> Object is possibly 'undefined'.
> ```
```ts
  }
}
```

嗯，TypeScript 还是不知道在这里做什么。我们已经达到了比类型检查器更了解我们的值的地步。我们可以尝试使用非空断言（`shape.radius` 之后的 `!`）来表示`radius`肯定存在。

```ts
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius! ** 2;
  }
}
```

但这感觉并不理想。我们不得不用那些非空断言（`!`）对类型检查器大喊大叫，以说服它 `shape.radius` 已定义，但如果我们开始移动代码，这些断言很容易出错。此外，在 `strictNullChecks` 之外，我们仍然可以意外访问这些字段中的任何一个（因为在读取它们时假定可选属性始终存在）。我们绝对可以做得更好。

这样编码 `Shape` 的问题在于，类型检查器无法根据 `kind` 属性知道是否存在 `radius` 或 `sideLength`。我们需要将*我们*所知道的信息传达给类型检查器。考虑到这一点，让我们再一次定义 `Shape`。

```ts
interface Circle {
  kind: "circle";
  radius: number;
}
 
interface Square {
  kind: "square";
  sideLength: number;
}
 
type Shape = Circle | Square;
```

在这里，我们已经正确地将 `Shape` 分成了两种类型，它们的 `kind` 属性具有不同的值，但是 `radius` 和 `sideLength` 在它们各自的类型中被声明为必需的属性。

让我们看看当我们尝试访问 `Shape` 的 `radius` 时会发生什么。

```ts
function getArea(shape: Shape) {
  return Math.PI * shape.radius ** 2;
```
> ```
> Property 'radius' does not exist on type 'Shape'.
>   Property 'radius' does not exist on type 'Square'.
> ```
```ts
}
```

就像我们对 `Shape` 的第一个定义一样，这仍然是一个错误。当 `radius` 是可选的时，我们得到一个错误（仅在 `strictNullChecks` 中），因为 TypeScript 无法判断该属性是否存在。现在 Shape 是一个联合，TypeScript 告诉我们 `shape` 可能是一个 `Square`，而 `Square` 上没有定义 `radius`！两种解释都是正确的，但只进行我们对 `Shape` 的新编码仍然会在 `strictNullChecks` 之外导致错误。

但是如果我们再次尝试检查 `kind` 属性呢？

```ts
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2;
```                      
> ```
> (parameter) shape: Circle
> ```
```ts
  }
}
```

这摆脱了错误！当联合中的每个类型都包含具有字面量类型的公共属性时，TypeScript 认为这是一个*可区分的联合*，并且可以缩小联合成员的范围。

在这种情况下，`kind`是那个共同属性（这被认为是 `Shape` 的判别属性）。检查 `kind` 属性是否为 `"circle"` 摆脱了 Shape 中没有类型为`"circle"`的 `kind` 属性的所有类型。将`shape`压缩到`Circle`类型。

同样的检查也适用于`switch`语句。现在我们可以尝试在没有任何讨厌的`!`非空断言的情况下编写完整的`getArea`代码。

```ts
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
```                        
> ```
>                     (parameter) shape: Circle
> ```
```ts
    case "square":
      return shape.sideLength ** 2;
```              
> ```
>           (parameter) shape: Square
> ```
```ts
  }
}
```

这里重要的是 `Shape` 的编码. 向 TypeScript 传达正确的信息（实际上`Circle`和`Square`是具有特定`kind`字段的两种不同类型）至关重要。这样做可以让我们编写类型安全的 TypeScript 代码，看起来与我们原本编写的 JavaScript 没有什么不同。从那里，类型系统能够做“正确”的事情并找出我们的`switch`语句的每个分支中的类型。

> 顺便说一句，尝试使用上面的示例并删除一些返回关键字。您会看到类型检查可以帮助避免在意外遇到`switch`语句中的不同子句时出现错误。

有区别的联合不仅仅用于讨论圆形和正方形。它们非常适合在 JavaScript 中表示任何类型的消息传递方案，例如通过网络发送消息（客户端/服务器通信）或在状态管理框架中编码突变。

## `never`类型

缩小范围时，您可以将联合的选项减少到您已消除所有可能性并且一无所有的程度。在这些情况下，TypeScript 将使用 `never` 类型来表示不应该存在的状态。

## 穷举检查

类型`never`可分配给每种类型；但是，没有类型可以分配给`never`（除了 `never` 本身）。这意味着您可以使用缩小范围并依靠`never`出现在 switch 语句中进行详尽的检查。

例如，在我们的`getArea`函数中添加一个尝试将形状分配给`never`的`default`将在未处理所有可能的情况时引发。

```ts
type Shape = Circle | Square;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```

向`Shape`联合中添加新成员会导致 TypeScript 错误：

```ts
interface Triangle {
  kind: "triangle";
  sideLength: number;
}
 
type Shape = Circle | Square | Triangle;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
```      
> ```
> Type 'Triangle' is not assignable to type 'never'.
> ```
```ts
      return _exhaustiveCheck;
  }
}
```

---


# 更多关于函数

函数是所有应用程序的基本组件，无论它们是本地函数、从另一个模块导入的函数，还是类中的方法。它们也是值，就像其他值一样，TypeScript 有很多方法来描述如何调用函数。让我们学习如何编写描述函数的类型。

## 函数类型表达式

描述函数的最简单方法是使用*函数类型表达式*。这些类型在语法上类似于箭头函数：

```ts
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}
 
function printToConsole(s: string) {
  console.log(s);
}
 
greeter(printToConsole);
```

语法`(a: string) => void`的意思是“具有一个参数的函数，名为`a`，类型为字符串，没有返回值”。就像函数声明一样，如果未指定参数类型，则它是隐式的`any`。

> 请注意，参数名称是**必需**的。函数类型`(string) => void`意思是“一个函数，其参数名为`string`，参数类型为`any`”！

当然，我们可以使用类型别名来命名函数类型：

```ts
type GreetFunction = (a: string) => void;
function greeter(fn: GreetFunction) {
  // ...
}
```

## 调用签名

在 JavaScript 中，函数除了可调用之外还可以具有属性。但是，函数类型表达式语法不允许声明属性。如果我们想用属性描述可调用的东西，我们可以在对象类型中编写*调用签名*：

```ts
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}
```

请注意，与函数类型表达式相比，语法略有不同——在参数列表和返回类型之间使用`:`而不是`=>`。

## 构造签名

JavaScript 函数也可以通过`new`操作符调用。TypeScript 将它们称为*构造函数*，因为它们通常会创建一个新对象。您可以通过在调用签名前添加`new`关键字来编写*构造签名*：

```ts
type SomeConstructor = {
  new (s: string): SomeObject;
};
function fn(ctor: SomeConstructor) {
  return new ctor("hello");
}
```

一些对象，比如 JavaScript 的 `Date` 对象，可以在有或没有 `new` 的情况下调用。您可以任意组合同一类型的调用和构造签名。

```ts
interface CallOrConstruct {
  new (s: string): Date;
  (n?: number): number;
}
```

## 泛型函数

通常会编写一个函数，其中输入的类型与输出的类型相关，或者两个输入的类型以某种方式相关。让我们考虑一个返回数组第一个元素的函数：

```ts
function firstElement(arr: any[]) {
  return arr[0];
}
```

这个函数完成了它的工作，但很遗憾返回类型为 `any`。如果函数返回数组元素的类型会更好。

在 TypeScript 中，当我们想要描述两个值之间的对应关系时，会使用*泛型*。我们通过在函数签名中声明一个*类型参数*来做到这一点：

```ts
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```

通过向这个函数添加一个类型参数`Type`并在两个地方使用它，我们在函数的输入（数组）和输出（返回值）之间创建了一个链接。现在当我们调用它时，会出现一个更具体的类型：

```ts
// s is of type 'string'
const s = firstElement(["a", "b", "c"]);
// n is of type 'number'
const n = firstElement([1, 2, 3]);
// u is of type undefined
const u = firstElement([]);
```

### 推理

请注意，我们不必在此示例中指定`Type`。类型是由 TypeScript *推断*（自动选择）的。

我们也可以使用多个类型参数。例如，独立版本的`map`如下所示：

```ts
function map<Input, Output>(arr: Input[], func: (arg: Input) => Output): Output[] {
  return arr.map(func);
}
 
// Parameter 'n' is of type 'string'
// 'parsed' is of type 'number[]'
const parsed = map(["1", "2", "3"], (n) => parseInt(n));
```

请注意，在此示例中，TypeScript 可以基于函数表达式的返回值 (`number`) 推断`Input`类型参数的类型（从给定的`string`数组），以及`Output`类型参数。

### 约束

我们编写了一些泛型函数，可以处理*任何*类型的值。有时我们想关联两个值，但只能对值的某个子集进行操作。在这种情况下，我们可以使用*约束*来限制类型参数可以接受的类型种类。

让我们编写一个返回两个值中较长者的函数。为此，我们需要一个`length`属性，它是一个number。我们通过编写一个`extends`子句将类型参数*约束*为该类型：

```ts
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
 
// longerArray is of type 'number[]'
// longArray 的类型为“number[]”
const longerArray = longest([1, 2], [1, 2, 3]);
// longerString is of type 'alice' | 'bob'
const longerString = longest("alice", "bob");
// Error! Numbers don't have a 'length' property
const notOK = longest(10, 100);
```
> ```
> Argument of type 'number' is not assignable to parameter of type '{ length: number; }'.
> ```

在这个例子中有一些有趣的事情需要注意。我们允许 TypeScript 推断`longest`的返回类型。返回类型推断也适用于泛型函数。

因为我们将 `Type` 约束为 `{ length: number }`，所以我们可以访问 `a` 和 `b` 参数的 `.length` 属性。如果没有类型约束，我们将无法访问这些属性，因为这些值可能是没有长度属性的其他类型。

`longerArray`和`longerString`的类型是根据参数推断出来的。请记住，泛型就是将两个或多个具有相同类型的值关联起来！

最后，正如我们所愿，对 `longest(10, 100)` 的调用被拒绝，因为`number`类型没有 `.length` 属性。

### 使用约束值

这是使用泛型约束时的一个常见错误：

```ts
function minimumLength<Type extends { length: number }>(
  obj: Type,
  minimum: number
): Type {
  if (obj.length >= minimum) {
    return obj;
  } else {
    return { length: minimum };
```
> ```
> Type '{ length: number; }' is not assignable to type 'Type'.
>   '{ length: number; }' is assignable to the constraint of type 'Type', but 'Type' could be instantiated with a different subtype of constraint '{ length: number; }'.
> ```
```ts
  }
}
```

看起来这个函数没问题——`Type`被约束为 `{ length: number }`，并且函数返回`Type`或匹配该约束的值。问题是该函数承诺返回与传入对象*相同*类型的对象，而不仅仅是与约束匹配的*某个*对象。如果这段代码是合法的，你可以编写绝对行不通的代码：

```ts
// 'arr' gets value { length: 6 }
const arr = minimumLength([1, 2, 3], 6);
// and crashes here because arrays have
// a 'slice' method, but not the returned object!
// 在这里崩溃，因为有'slice'方法的是数组，而不是返回的对象!
console.log(arr.slice(0));
```

### 指定类型参数

TypeScript 通常可以在泛型调用中推断出预期的类型参数，但并非总是如此。例如，假设您编写了一个函数来组合两个数组：

```ts
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}
```

通常，使用不匹配的数组调用此函数会出错：

```ts
const arr = combine([1, 2, 3], ["hello"]);
```
> ```
> Type 'string' is not assignable to type 'number'.
> ```

但是，如果您打算这样做，您可以手动指定`Type`：

```ts
const arr = combine<string | number>([1, 2, 3], ["hello"]);
```

### 编写良好泛型函数的指南

编写泛型函数很有趣，而且很容易被类型参数迷住。拥有太多类型参数或在不需要的地方使用约束可能会降低推断的成功率，让函数的调用者感到沮丧。

#### 下推类型参数

以下是编写函数的两种看起来相似的方法：

```ts
function firstElement1<Type>(arr: Type[]) {
  return arr[0];
}
 
function firstElement2<Type extends any[]>(arr: Type) {
  return arr[0];
}
 
// a: number (good)
const a = firstElement1([1, 2, 3]);
// b: any (bad)
const b = firstElement2([1, 2, 3]);
```

乍一看，这些似乎相同，但 `firstElement1` 是编写此函数的更好方法。它推断的返回类型是`Type`，但`firstElement2`的推断返回类型是`any`，因为 TypeScript 必须使用约束类型解析`arr[0]`表达式，而不是在调用期间“等待”着去解析元素。

> **规则**：如果可能，使用类型参数本身而不是约束它

#### 使用更少的类型参数

这是另一对相似的函数：

```ts
function filter1<Type>(arr: Type[], func: (arg: Type) => boolean): Type[] {
  return arr.filter(func);
}
 
function filter2<Type, Func extends (arg: Type) => boolean>(
  arr: Type[],
  func: Func
): Type[] {
  return arr.filter(func);
}
```

我们创建了一个*不关联两个值*的类型参数`Func`。这总是一个危险信号，因为这意味着想要指定类型参数的调用者必须无缘无故地手动指定额外的类型参数。`Func`除了使函数更难阅读和推理之外，什么也没做！

> **规则**：始终使用尽可能少的类型参数

#### 类型参数应该出现两次

有时我们会忘记函数可能不需要是泛型的：

```ts
function greet<Str extends string>(s: Str) {
  console.log("Hello, " + s);
}
 
greet("world");
```

我们可以很容易地编写一个更简单的版本：

```ts
function greet(s: string) {
  console.log("Hello, " + s);
}
```

请记住，类型参数用于*关联多个值的类型*。如果一个类型参数只在函数签名中使用一次，它就没有关联任何东西。

> **规则**：如果一个类型参数只出现在一个位置，强烈重新考虑是否真的需要它。

## 可选参数

JavaScript 中的函数通常采用可变数量的参数。例如， `number` 的 `toFixed` 方法采用可选的位数：

```ts
function f(n: number) {
  console.log(n.toFixed()); // 0 arguments
  console.log(n.toFixed(3)); // 1 argument
}
```

我们可以在 TypeScript 中通过使用 `?` 将参数标记为*可选*来对此进行建模：

```ts
function f(x?: number) {
  // ...
}
f(); // OK
f(10); // OK
```

尽管参数被指定为类型`number`，但`x`参数实际上将具有类型`number | undefined`，因为 JavaScript 中未指定的参数会得到值`undefined`。

您还可以提供参数*默认值*：

```ts
function f(x = 10) {
  // ...
}
```

现在在 `f` 的主体中，`x` 将具有类型`number`，因为任何`undefined`参数都将被替换为 `10`。请注意，当参数是可选的时，调用者总是可以传递`undefined`，因为这只是模拟了一个“缺失”的参数：

```ts
declare function f(x?: number): void;
// cut
// All OK
f();
f(10);
f(undefined);
```

### 回调中的可选参数

一旦你了解了可选参数和函数类型表达式，在编写调用回调的函数时很容易犯以下错误：

```ts
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i], i);
  }
}
```

人们在编写`index?`为可选参数时通常想要的是他们希望这两个调用都是合法的：

```ts
myForEach([1, 2, 3], (a) => console.log(a));
myForEach([1, 2, 3], (a, i) => console.log(a, i));
```

这*实际上*意味着*callback可能会被用一个参数调用*。换句话说，函数定义表明实现可能如下所示：

```ts
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    // I don't feel like providing the index today
    // 我今天不想提供index
    callback(arr[i]);
  }
}
```

反过来，TypeScript 将强制执行此含义并发出实际上不可能的错误：

```ts
myForEach([1, 2, 3], (a, i) => {
  console.log(i.toFixed());
```
> ```
> Object is possibly 'undefined'.
> ```
```ts
});
```

在 JavaScript 中，如果你用多于形参的实参调用一个函数，多余的实参将被忽略。TypeScript 的行为方式相同。具有较少（相同类型的）参数的函数总是可以代替具有更多参数的函数。

> 为回调编写函数类型时，*切勿*编写可选​​参数，除非您打算在不传递该参数的情况下*调用*该函数

## 函数重载

一些 JavaScript 函数可以以各种参数数量和类型调用。例如，您可以编写一个函数来生成一个`Date`，这个函数接受时间戳（一个参数）或月/日/年格式（三个参数）。

在 TypeScript 中，我们可以通过编写*重载签名*来指定一个可以以不同方式调用的函数。为此，请编写一些函数签名（通常是两个或更多），然后是函数体：

```ts
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3);
```
> ```
> No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
> ```

在这个例子中，我们写了两个重载：一个接受一个参数，另一个接受三个参数。前两个签名称为*重载签名*。

然后，我们编写了一个具有兼容签名的函数实现。函数有一个*实现*签名，但是这个签名不能直接调用。即使我们编写了一个在必需的参数之后带有两个可选参数的函数，也不能用两个参数调用它！


# 参考

## Utility类型

TypeScript 提供了多种 utility 类型来便利常见的类型转换。这些 utility globally 可用。

### `Record<Keys, Type>`

构造一个对象类型，其属性键为`Keys`，属性值为`Type`。该utility可用于将一种类型的属性映射到另一种类型。

**例子**

```ts
interface CatInfo {
  age: number;
  breed: string;
}

type CatName = "miffy" | "boris" | "mordred";

const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 5, breed: "Maine Coon" },
  mordred: { age: 16, breed: "British Shorthair" },
};

cats.boris;
```

> ```
> const cats: Record<CatName, CatInfo>
> ```


---

***`//未完待xu`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)