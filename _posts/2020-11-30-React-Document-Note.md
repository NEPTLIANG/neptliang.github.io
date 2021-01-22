---
layout:       post
title:        "React文档笔记"
subtitle:     "null"
date:         2020-11-30 14:07:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 0x00 前言

初到公司不久，可能上司觉得我还太菜，暂时没有分配工作给我，于是我想着先把React文档的核心概念部分康康，复习一下之前在b站学的内容。在这里总结一下重点

---


# 一、开始

React是一个用于构建用户界面的JavaScript库

---


# 二、Create React App

```shell
npx create-react-app my-app
cd my-app
npm start
```

* npx：npm 5.2+ 附带的**package运行**工具
* 准备好部署到生产环境时，执行`npm run build`会在build文件夹内生成你应用的**优化**版本

---


# 三、JSX
* 因为JSX语法上更接近JavaScript而不是HTML，所以React DOM使用**camelCase**（小驼峰命名）来定义属性的名称，而不使用HTML属性名称的命名约定。例如，JSX里的class变成了`className`，而tabindex则变为`tabIndex`
* React DOM在渲染所有输入内容之前，默认会进行**转义**。这样可以有效地防止XSS
* Babel会把JSX转译成一个名为`React.createElement()`函数调用

---


# 四、元素渲染

* 与浏览器的DOM元素不同，React元素是**创建开销极小**的普通对象
* React DOM会负责更新DOM来与React元素保持一致
* React元素是**不可变**对象，更新UI唯一的方式是创建一个全新的元素，并将其传入`ReactDOM.render()`
* React DOM会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使DOM达到预期的状态

---


# 五、组件 & props

```jsx
class Welcome extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>;
    }
}

ReactDOM.render(
    <Welcome name="sara" />,
    document.getElementById('root')
);
```

* 组件名称必须**以大写字母开头**。React会将以小写字母开头的组件视为原生DOM标签
* 组件无论是使用函数声明还是通过`class`声明，都决**不能修改**自身的`props`

> 不会尝试更改入参、且多次调用下相同的入参始终返回相同的结果的函数被称为“纯函数”

---


# 六、State & 生命周期

`state`是私有的（局部的，封装的），并且完全受控于当前组件

```js
class Clock extends React.Component {
    constructor() {
        this.state = {date: new Date()};  //初始化this.state，构造方法中直接给state赋值，其他方法中用setState
    }

    render() {
        return (
            <div>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>  {/* 读取state */}
            </div>
        )
    }
}
```

通过以下方式将`props`传递到父类的构造函数中：

```js
    constructor(props) {
        super(props);
        this.state = {date: new Date()};
    }
```

使用`this.setState()`来更新组件`state`**（构造方法中直接给state赋值，其他方法中用setState）**：

```js
this.setState({
    date: new Date()
});
```

* 当你调用`setState()`的时候，React会把你提供的对象**合并**到当前的`state`。若`state`包含几个独立的变量，可以分别调用`setState()`来**单独地**更新它们（浅合并）
* 得益于`setState()`的调用，React能够知道`state`已经改变了
* **不要直接修改State**，否则不会重新渲染组件（**构造方法中直接给`state`赋值，其他方法中用`setState`**）

组件第一次被渲染到DOM中的时候：**挂载（mount）**

组件被删除的时候：**卸载（unmount）**

`componentDidMount()`方法会在组件已经被渲染到DOM中后运行

`componentWillUnmount()`方法会在组件从DOM中被移除时运行

```js
class Clock extends React.Component{
    componentDidMount() {}
    componentWillUnmount() {}
}
```

---


# 七、事件处理

React事件的命名采用**小驼峰式（camelCase）**，而不是纯小写

使用JSX语法时你需要传入一个**函数**作为事件处理函数，而不是函数名字符串

```jsx
class Toggle extends React.Component {
    constructor(props) {
        this.state = {isToggleOn: true};
        this.handleClick = this.handleClick.bind(this);
        //为了在回调中使用`this`，这个绑定是必不可少的，
        //否则下面调用这个函数的时候this的值为undefined。
        //通常情况下，如果你没有在方法后面添加()，例如onClick={this.handleClick}，你应该为这个方法绑定this
        //或在回调中使用箭头函数：onClick={() => this.handleClick()}，此语法确保`handleClick`内的`this`已被绑定
    }

    handleClick() {}

    render() {
        return (
            <button onClick={this.handleClick}>  {/* 小驼峰，传函数 */}
                {this.state.isToggleOn ? 'ON' : 'OFF'}
            </button>
        );
    }
}
```

**阻止默认行为**：
```js
function ActionLink() {
    function hadleClick(e) {
        e.preventDefault();  //阻止默认行为
    }  //在这里，e是一个合成事件。React根据W3C规范来定义这些合成事件

    return (
        <a href="#" onClick={handleClick}>Click me</a>
    )
}
```

以下两种方式都可以**向事件处理程序传递参数**：

```jsx
<button onClick={(e) => this.deleteRow(id, e) }>Delete Row</button>
//通过箭头函数的方式，事件对象必须显式地进行传递

<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
//通过bind的方式，事件对象以及更多的参数将会被隐式地进行传递
```

在这两种情况下，React的事件对象`e`会被作为**第二个参数**传递

---


# 八、条件渲染

## 1. 使用JavaScript运算符`if`或者条件运算符

```jsx
function Greeting(props) {
    const isLoggedIn = props.isLoggedIn;
    if (isLoggedIn) {  //使用JavaScript运算符if
        return <UserGreeting />;
    }
    return <GuestGreeting />;
}
```

**元素变量**：可以使用变量来储存元素

```js
render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;
    if (isLoggedIn) {  //使用变量来储存元素
        button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
        button = <LoginButton  onClick={this.handleLoginClick} />;
    }

    return (
        <div>
            {button}  {/* 使用使用变量来储存的元素 */}
        </div>
    )
}
```

## 2. 与运算符`&&`

```jsx
function Mailbox(props) {
    const unreadMessages = props.unreadMessages;

    return (
        <div>
            {
                unreadMessages.length > 0 &&
                    <h2>You have {unreadMessages.length} unread messages.</h2>
            }
            {/* 在JavaScript中，true && expression 总是会返回 expression， */}
            {/*             而 false && expression 总是会返回 false */}
        </div>
    );
}
```

返回`false`的表达式会使`&&`后面的元素被跳过，但会**返回false表达式**：

```jsx
render() {
    const count = 0;
    return (
        <div>
            { count && <h1>Messages: { count }</h1> }
        </div>
    )  //返回：<div>0</div>
}
```

## 3. 三目运算符

## *. 阻止组件渲染

让`render`方法直接返回`null`

```jsx
function WarningBanner(props) {
    if (!props.warn) {
        return null;  //直接返回null
    }
    return (
        <div>Warning</div>  //正常渲染
    );
}
```

不会影响组件中的生命周期

---


# 九、列表 & Key

使用JavaScript中的`map()`方法

`key`帮助React识别哪些元素改变了，比如被添加或删除，因此你应当给数组中的每一个元素赋予一个确定的标识

```jsx
const listItems = numbers.map((number) =>
    <li key={number.toString()}>  {/* 赋予一个确定的标识 */}
        {number}
    </li>
)
```

一个元素中的`key`最好是这个元素在列表中拥有的一个**独一无二**的字符串。通常，我们使用**数据中的id**来作为元素的`key`

```jsx
const todoItems = todos.map((todo) =>
    <li key={todo.id}>  {/* 数据中的id */}
        {todo.text}
    </li>
)
```

当元素没有确定id的时候，万不得已你可以使用元素索引`index`作为`key`：

```jsx
const todoItems = todos.map((todo, index) =>
    <li key={index}>  {/* Only do this if items have no stable IDs */}
        {todo.text}
    </li>
)
```

如果列表项目的顺序可能会变化，我们**不建议**使用索引来用作key值，因为这样做会导致性能变差，还可能引起组件状态的问题

* 如果你选择不指定显式的`key`值，React将默认使用索引用作为列表项目的`key`值
* 元素的`key`只有放在就近的数组上下文中才有意义
* `key`只是在兄弟节点之间必须唯一
* `key`不会传递给组件
* 如果一个`map()`嵌套了太多层级，那可能就是你提取组件的一个好时机

---


# 十、表单

```jsx
class NameForm extends React.Component {
    constructor(props) {
        this.state = {value: ''};  //可变状态保存在组件的state属性中

        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleChange(event) {
        this.setState({value: event.target.value});  //事件处理函数中更新React的state
    }

    handleSubmit(event) {
        alert('提交的名字：' + this.state.value);  //提交时打印出输入的值
        event.preventDefault();
    }

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    名字：
                    <input type="text" value={this.state.value}   {/* 使React的state成为数据源，显示的值将随着用户输入而更新 */}
                        onChange={this.handleChange} />
                </label>
                <input type="submit" value="提交" />
            </form>
        );
    }
}
```

## `textarea`标签

在HTML中，`<textarea>`元素通过其子元素定义其文本，而在React中，`<textarea>`使用`value`属性代替。这样，可以使得使用`<textarea>`的表单和使用单行`input`的表单非常类似：

```jsx
<textarea value={this.state.value} onChange={this.handleChange} />
```

## `select`标签

React并不会使用`selected`属性，而是在根`select`标签上使用`value`属性选中默认选项

```jsx
class FlavorForm extends React.Component {
    constructor(props) {
        this.state = {value: 'coconut'};  //在state中指定默认选项

        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleChange(event) {}
    handleSubmit(event) {}

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    选择你喜欢的风味：
                    <select value={this.state.value}  //在根select标签上使用value属性选中默认选项
                        onChange={this.handleChange}>
                        <option value="grapefruit">葡萄柚</option>
                        <option value="coconut">椰子</option>
                    </select>
                </label>
                <input type="submit" value="提交" />
            </form>
        );
    }
}
```

总的来说，这使得`<input type="text">`，`<textarea>`和`<select>`之类的标签都非常相似——它们都接受一个`value`属性

你可以将数组传递到`value`属性中，以支持在`select`标签中选择多个选项：

```jsx
<select multiple={true} value={['b', 'c']}
```

## 文件input标签

它的value只读

## 处理多个输入

给每个元素添加`name`属性，并让处理函数根据`event.target.name`的值选择要执行的操作

```jsx
class Reservation extends React.Component {
    constructor(props) {
        this.state = {
            isGoing: true,
            numberOfGuests: 2
        };

        this.handleInputChange = this.handleInputChange.bind(this);
    }

    handleInputChange(event) {
        const value =
            event.target.type === 'checkbox'  //处理函数根据event.target.type与.name的值选择要执行的操作
                ? event.target.checked  //如果type是checkbox，赋checked属性的true/false值
                : event.target.value  //如果type不是checkbox，赋value属性的输入值
        ;
        const name = event.target.name;  //设置event.target.name对应的state

        this.setState({
            [name]: value
        });
    }

    render() {
        return (
            <form>
                参与：
                <input
                    type="checkbox"
                    name="isgoing"  {/* 给每个元素添加name属性 */}
                    checked={this.state.isGoing}
                    onChange={this.handleInputChange} />  {/* 绑定同一个事件处理函数 */}
                来宾人数：
                <input
                    type="number"
                    name="numberOfGuests"  {/* 给每个元素添加name属性 */}
                    value={this.state.numberOfGuests}
                    onChange={this.handleInputChange} />  {/* 绑定同一个事件处理函数 */}
            </form>
        );
    }
}
```

指定`value`的`prop`会阻止用户更改输入。如果你指定了`value`，但输入仍可编辑，则可能是你意外地将`value`设置为`undefined`或`null`

```jsx
ReactDOM.render(<input value="hi" />, mountNode);  //用户无法更改输入

ReactDOM.render(<input value={null} />, mountNode);  //指定了value但输入仍可编辑
```

---


# 十一、状态提升

通常，多个组件需要反映相同的变化数据，这时我们建议**将共享状态提升到最近的共同父组件中去**

```jsx
function BoilingVerdict(props) {  //接受celsius温度作为一个prop，并据此打印出温度是否足以将水煮沸的结果的子组件
    if (props.celsius >= 100) {
        return <p>The water would boil.</p>;
    }
    return <p>The water would not boil.</p>;
}

const scaleNames = {
    c: 'Celsius',
    f: 'Fahrenheit'
};

class TemperatureInput extends React.Component {
    constructor(props) {  //prop: scale，温度单位，可以是"c"或是"f"
        this.handleChange = this.handleChange.bind(this);
    }

    handleChange(e) {
        this.props.onTemperatureChange(e.target.value);  //在输入值改变事件回调中调用父组件通过props传进来的方法来修改父组件的state
    }

    render() {
        return (
            <fieldset>
                <legend>Enter temprature in {scaleNames[this.props.scale]}:</legend>  {/* 根据温度单位props.scale是c还是f显示不同的提示信息 */}
                <input value={this.props.temperature} onChange={this.handleChange} />  {/* 设置值为父组件通过props传进来的温度 */}
            </fieldset>
        );
    }
}

function toClsius(fahrenheit) {}  //华氏度转摄氏度
function toFahrenheit(celsius) {}  //摄氏度转华氏度
function tryConvert(temperature, convert)  //将字符串类型的温度temperature用传给convert的上面两个函数之一进行转换

class Calculator extends React.Component {  //渲染用于输入温度的子组件，并将其值保存在this.state.temperature中；根据当前输入值渲染BoilingVerdict组件
    constructor(props) {
        this.state = {  //把输入的温度temperature和单位scale保存在父组件的state中
            temperature: '',
            scale: 'c'
        };
        this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
        this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    }

    handleCelsiusChange(temperature) {  //在父组件中进行setState
        this.setState({
            temperature,
            scale: 'c'
        });
    }

    handleFahtenheitChange(temperature) {  //在父组件中进行setState
        this.setState({
            temperature,
            scale: 'f'
        })
    }

    render() {
        const celsius =  //获取摄氏度值
            this.state.scale === 'f'
                ? tryConvert(this.state.temperature, toCelsius)
                : this.state.temperature
        const fahrenheit =  //获取华氏度值
            this.state.scale === 'c'
                ? tryConvert(this.state.temperature, toFahrenheit)
                : this.state.temperatrue

        return (
            <div>
                <TemperatureInput  {/* 渲染用于摄氏度输入的子组件 */}
                    scale="c"
                    temperature={celsius}
                    onTemperatureChange={this.handleCelsiusChange} />
                <TemperatureInput  {/* 渲染用于华氏度输入的子组件 */}
                    scale="f"
                    temperature={fahrenheit}
                    onTemperatureChange={this.handleFahrenheigChange} />
                <BoilingVerdict celsis={parseFloat(celsius)} />  {/* 渲染BoilingVerdict组件，并将摄氏温度值以组件props方式传入 */}
            </div>
        );
    }
}
```

对输入框进行编辑时会发生什么：

* React调用DOM中`<input>`的`onChange`方法
* 子组件中的`onChange`方法会调用`this.props`中父组件更新`state`的函数，并传入新输入的值作为参数
* 父组件通过使用新的输入值与当前输入输入框对应的温度计量单位来调用`this.setState()`进而请求React重新渲染自己本身
* React调用父组件的`render`方法得到组件的UI呈现。温度转换在这时进行
* React使用父组件提供的新`props`分别调用两个子组件的`render`方法来获取子组件的UI呈现
* React调用子组件`BoilingVerdict`的`render`方法，并将摄氏温度值以组件`props`方式传入
* ReactDOM根据输入值匹配水是否沸腾，并将结果更新至DOM。我们刚刚编辑的输入框接收其当前值，另一个输入框内容更新为转换后的温度值

---


# 十二、组合 vs 继承

**推荐使用组合**而非继承来实现组件间的代码重用

有些组件无法提前知晓它们子组件的具体内容。建议这些组件使用一个特殊的`children prop`来将它们的子组件传递到渲染结果中

```jsx
function FancyBorder(props) {
    return (
        <div className={'fnacyBorder FancyBorder-' + props.color}>
            {props.chindren}  {/* 使用一个特殊的 childrenprop 来将他们的子组件传递到渲染结果中 */}
        </div>
    );
}

function WelcomeDialog() {
    return (
        <FancyBorder color="blue">  {/* <FancyBorder> JSX 标签中的所有内容都会作为一个 children prop 传递给 FancyBorder 组件 */}
            <h1>Welcome</h1>
            <p>Thank you for visiting our spacecraft</p>
        </FancyBorder>
    );
}
```

`<FancyBorder>` JSX 标签中的所有内容都会作为一个 `children prop` 传递给 `FancyBorder` 组件

少数情况下，你可能需要在一个组件中预留出几个“洞”。这种情况下，我们可以不使用 `children`，而是自行约定：将所需内容传入 `props`，并使用相应的 `prop`

```jsx
function SpintPane(props) {
    return (
        <div>
            <div>
                {props.left}   {/* 使用相应的 prop */}
            </div>
            <div>
                {props.right}  {/* 使用相应的 prop */}
            </div>
        </div>
    );
}

function App() {
    return (
        <SplitPane
            left={<Contacts />}  {/* 将所需内容传入 props */}
            right={<Chat />}     {/* 将所需内容传入 props */}
        />
    );
}
```

`<Contacts />` 和 `<Chat />` 之类的 React 元素本质就是对象（`object`），所以你可以把它们当作 `props`，像其他数据一样传递。你可以将任何东西作为 `props` 进行传递。

有些时候，我们会把一些组件看作是其他组件的特殊实例，比如 `WelcomeDialog` 可以说是 `Dialog` 的特殊实例。在 React 中，我们也可以通过组合来实现这一点。“特殊”组件可以通过 `props` 定制并渲染“一般”组件：

```jsx
function Dialog(props) {
    return (
        <FancyBorder color="blue">
            <h1>
                {props.title}
            </h1>
            <p>
                {props.message}
            </p>
        </FancyBorder>
    );
}

function WelcomeDialog() {
    return (
        <Dialog  {/* “特殊”组件通过 props 定制并渲染“一般”组件 */}
            title="Welcome"
            message="Thank you for visiting our spacecraft" />
        />
    );
}
```

Facebook在成百上千个组件中使用 React，并没有发现需要使用继承来构建组件层次的情况

组件可以接受任意 `props`，包括基本数据类型，React 元素以及函数

如果想要在组件间复用非 UI 的功能，建议将其提取为一个单独的 JavaScript 模块，如函数、对象或者类。组件可以直接引入（`import`）而无需通过 `extend` 继承它们

---


# 十三、React哲学

## 第一步：将设计好的 UI **划分为组件层级**

如果你是和设计师一起完成此任务，那么他们可能已经做过类似的工作，所以请和他们进行交流！他们的 Photoshop 的图层名称可能最终就是你编写的 React 组件的名称

确定了设计稿中应该包含的组件，接下来我们将把它们描述为更加清晰的层级。设计稿中被其他组件包含的子组件，在层级上应该作为其子节点。

* `FilterableProductTable`
    * `SearchBar`
    * `ProductTable`
        * `ProductCategoryRow`
        * `ProductRow`

## 第二步：用 React 创建一个**静态版本**

当你的应用比较简单时，使用自上而下的方式更方便；对于较为大型的项目来说，自下而上地构建，并同时为低层组件编写测试是更加简单的方式

## 第三步：确定 UI `state` 的最小（且完整）表示

只保留应用所需的可变 `state` 的最小集合，其他数据**均由它们计算产生**

通过问自己以下三个问题，你可以逐个检查相应数据是否属于 `state`：

1. 该数据是否是**由父组件通过 `props` 传递而来**的？如果是，那它应该不是 `state`。
2. 该数据是否随时间的推移而**保持不变**？如果是，那它应该也不是 `state`。
3. 你能否**根据其他 `state` 或 `props` 计算出**该数据的值？如果是，那它也不是 `state`

## 第四步：确定 `state` 放置的位置

确定哪个组件能够改变这些 `state`，或者说*拥有*这些 `state`

React 中的数据流是**单向**的，并顺着组件层级**从上往下**传递

对于应用中的每一个 `state`：

* 找到**根据这个 `state` 进行渲染**的所有组件。
* 找到他们的共同所有者（common owner）组件（在组件层级上**高于所有需要该 `state` 的组件**）。
* 该共同所有者组件或者比它层级更高的组件应该拥有该 `state`。
* 如果你找不到一个合适的位置来存放该 `state`，就可以直接创建一个**新的组件**来存放该 `state`，并将这一新组件置于高于共同所有者组件层级的位置

## 第五步：添加反向数据流

父组件必须将一个**能够触发`state`改变的回调函数**（callback）传递给子组件。我们可以在子组件通知父组件传递给子组件的回调函数。然后该回调函数将调用`setState()`，从而更新应用

---

***`//End of Article`***

---


# 参考文献

* [React 官方中文文档 – 用于构建用户界面的 JavaScript 库（zh-hans.reactjs.org）](https://zh-hans.reactjs.org/docs/getting-started.html)


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)