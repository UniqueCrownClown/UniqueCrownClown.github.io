---
title: react学习笔记
date: 2017-06-24 11:23:51
categories: frontend #文章分类
tags: [react基础] #文章标签，可以一次添加多个标签
---

react...

<!-- more -->

## react

### react 基本概念

1. Create React App 脚手架
   Create React App 搭建单页 react 工程
   为了不 eject 所有 webpack 配置，如果需要我们用 react-app-rewired 方案复写 webpack 就可以了

```
#### 方法1
npx create-react-app my-app  --scripts-version=react-scripts-ts
npx create-react-app my-project --scripts-version=react-scripts-ts-antd(antd的ui框架)
cd my-app
npm start
#### 方法2
npm init react-app my-app
yarn create react-app my-app
cd my-app
yarn start
npm run eject 暴露webpack的config,不可逆
```

2.  容器组件和展示组件的区别
    container 文件夹和 compoment 文件夹

3.  collection
    什么时候用私有类变量,什么时候用 state？
    需要在 render 中引用到的用 state，其他不影响渲染的用类变量
    prop-types
    检测类变量的状态和类型
    setState

        setState并不总是异步的，只有在生命周期钩子和合成事件中才是异步，在原生事件和setTimeout中是同步的

        合成事件是围绕浏览器原生事件充当跨浏览器包装器的对象。它们将不同浏览器的行为合并为一个 API。这样做是为了确保事件在不同浏览器中显示一致的属性
        写法
        	1. 向下传递 state
        	2. 在 setState 的第二个回调函数中操作 state

简单性能提升 1. 纯函数 2. React.memo 的函数式组件 3. 合并状态更新 4. react 定义组件的三种方式
ES5 写法：React.createClass
ES6 写法：React.Component
无状态的函数写法，又称为纯组件 SFC(只负责展示的纯组件)
有无状态的区别：有无 state

```js
const Todo = (props) => (
  <li
    onClick={props.onClick}
    style={{ textDecoration: props.complete ? "line-through" : "none" }}
  >
    {props.text}
  </li>
);
```

4. css-in-js

react 的 css-in-js ---> styled-components

### jsx

1. JSX 代码的外面扩上一个小括号，这样可以防止 分号自动插入 的 bug
   会带上换行和缩进，这样可以增强代码的可读性
   JSX 当中的表达式要包含在大括号里
   React DOM 使用 camelCase 小驼峰命名 来定义属性的名称，而不是使用 HTML 的属性名称
   class 变成了 className，而 tabindex 则对应着 tabIndex
   条件渲染和列表渲染

```js
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) => (
        <ListItem key={number.toString()} value={number} />
      ))}
    </ul>
  );
}
```

2. 表单怎么用 onchange 事件和 state 实现数据 value 的控制

```js
//受控组件
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: "" };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({ value: event.target.value });
  }

  handleSubmit(event) {
    alert("A name was submitted: " + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input
            type="text"
            defaultValue="哈哈哈"
            value={this.state.value}
            onChange={this.handleChange}
          />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
// 非受控组件
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(event) {
    alert("A name was submitted: " + this.input.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input
            type="text"
            defaultValue="哈哈哈"
            ref={(input) => (this.input = input)}
          />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

### state&生命周期

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }
  componentDidMount() {
    // dom首次挂载执行
    this.timerID = setInterval(() => this.tick(), 1000);
  }
  componentWillUnmount() {
    // 组件dom移除时执行
    clearInterval(this.timerID);
  }
  tick() {
    this.setState({
      date: new Date(),
    });
    // 可以获取state的前一个值
    this.setState((prevState, props) => ({
      counter: prevState.counter + props.increment,
    }));
  }
  handleClick() {
    //使用实验性的属性初始化器语法，可以用箭头函数省掉构造器那里的绑定
    alert("button Click");
  }
  render() {
    return (
      <div className="ClockComponent">
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        <button onClick={handleClick}>Activate Lasers</button>
      </div>
    );
  }
}
```

### HOC 高级组件

占个坑

### react-router 应用

    React-router提供了一些router的核心api，包括Router, Route, Switch等，但是它没有提供dom操作进行跳转的api

    React-router-dom提供了BrowserRouter, Route, Link等api,我们可以通过dom的事件控制路由。
    例如点击一个按钮进行跳转，大多数情况下我们是这种情况，所以在开发过程中，我们更多是使用React-router-dom

### 涉及到的 npm 包

- prop-types
- classnames
  react 中动态添加 class 大多使用三元运算符，用 classnames 更灵活

```jsx
<HomeWrapper>
  <h2 className={classnames("ch", { ch: show })}></h2>
</HomeWrapper>
```

- style-components(css in js)

```jsx
import styled from "styled-components";
import React, {memo,useState} from 'react';
const HomeWarpper = styled.div`
color: ${(props)=>props.color};
font-size:30px;
.ch{
  border: 1px solid red;
`;
export Demo = memo(()=>{

  const [color,setColor] = useState('blue');
  const changeY =()=>{
    setColor('yellow')
  }
  return (
    <HomeWarpper color={color}>
    <div>css in js</div>
    <div className='ch'>哈哈</div>
    <button onClick={changeY}>变黄</button>
    </HomeWarpper>
  )
})
```

### redux

#### 基本概念

    1. Redux依靠一个统一且不可变的数据存储来同步数据，并且更新那里的数据时会触发应用的更新渲染。
    2. state的更新是以一种不可变的方式进行，它会发布一条明确的action消息，这个消息必须被reducer函数处理。
    由于使用了这样明确的方式，很容易弄清楚一个action是如何影响程序的state
    state(js对象)
    action(js对象) 具体接收dispach传递的参数后怎么处理
    	action 本质上是 JavaScript 普通对象。
    	我们约定，action 内必须使用一个字符串类型的 type 字段来表示将要执行的动作。
    	多数情况下，type 会被定义成字符串常量。当应用规模越来越大时，建议使用单独的模块或文件来存放 action。
    reducer 只是一个接收 state 和 action，并返回新的 state 的函数，和view的dispatch隔离
    combineReducers()合并值，reducer其实就是state的状态定义
    dispatch(action)来改变state的值
    3. redux-thunk(中间件异步action初级方案)和 redux-saga(中间件异步action进阶方案)
    4. dva-cli
    		阿里基于redux和react和webpack 是一个基于 Redux 的 轻量级数据流方案

#### connect 和 provider 组件

    1. connect的参数
    mapStateToProps
    	入参state，return一个传递给展示组件props使用的对象，还可以使用第二个参数，代表容器组件的props对象
    	mapStateToProps会订阅 Store，每当state更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染
    mapDisapachToProps
    	入参dispatch，return一个传递给展示组件props使用的对象方法
    	可以通过import { bindActionCreators } from "redux";来把容器组件dispatch要触发的action传递给展示容器
    2. React-Redux 提供Provider组件，可以让容器组件拿到state

​

### redux-thunk，dva

#### immutable

### react16

#### hook

1. useEffect
2. useState
3. useDispatch
4. useSelector
5. userReducer

### React Portal

    Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案
    (一个组件调用一个append到body上的html片段)
    React16 ReactDOM.createPortal(child, container) 的使用
    ````
    const modalRoot = document.getElementById('modal-root');

    class Modal extends React.Component {
    constructor(props) {
        super(props);
        this.el = document.createElement('div');
    }

    componentDidMount() {
        modalRoot.appendChild(this.el);
    }

    componentWillUnmount() {
        modalRoot.removeChild(this.el);
    }

    render() {
        return ReactDOM.createPortal(
        this.props.children,
        this.el,
        );
    }
    }
    ```

// 在 simple-spa 实践一下 Portal 的 dropdown 和 modal 代码

### react-native

#### 构建初始化流程

    1. react-native init AwesomeProject
    2. cd AwesomeProject
    3. react-native run-android
    4. 通过摇晃设备来打开开发菜单(adb shell input keyevent 82)
    5.热更新通过在开发菜单选择reload,或者是8081的node服务上双击rr
    6. preload和prefetch的基本了解

    404的时候提示不可用
    adb端口映射forward和reverse
    	adb reverse tcp:8081 tcp:8081
    	Android允许我们通过ADB，把Android上的某个端口映射到电脑（adb forward），或者把电脑的某个端口映射到Android系统（adb reverse）。
    	假设电脑上开启的服务，监听的端口为8000。
    	Android手机通过USB连接电脑后，执行 adb reversetcp:8000 tcp:8000，
    	然后在手机中访问127.0.0.1:8000，就可以访问到电脑上启动的服务了

#### 无线调试的方法

    	1. 首先确保你的电脑和手机设备在同一个Wi-Fi环境下。
    	2. 在设备上运行你的React Native应用。和打开其它App一样操作。
    	3. 你应该会看到一个“红屏”错误提示。这是正常的，下面的步骤会解决这个报错。
    	4. 摇晃设备，或者运行adb shell input keyevent 82，可以打开开发者菜单。
    	5. 点击进入Dev Settings。
    	6. 点击Debug server host for device。
    	7. 输入你电脑的IP地址和端口号（譬如10.0.1.1:8081）。
    	adb connect 手机ip
    	adb tcpip 5555
    	adb reverse tcp:8081 tcp:8081
    	在Mac上，你可以在系统设置/网络里找查询你的IP地址。
    	在Windows上，打开命令提示符并输入ipconfig来查询你的IP地址。
    	在Linux上你可以在终端中输入ifconfig来查询你的IP地址。
    	8. 回到开发者菜单然后选择Reload JS。
    手机ip
    	10.8.3.111
    电脑ip
    	10.8.3.60
    	adb connect 手机ip
    全局安装react-tools
    	npm install -g react-devtools
    launch the standalone DevTools app
    	react-devtools

    npm新的包以后，需要重新react-native start

    expo(工具库？sdk?)native的关系和是否必要？

    在 react-native（android 端）使用react-native-camera
    	1.安装依赖包
    	yarn add react-native-camera
    	2.关联
    	react-native link react-native-camera

### ahooks

ahooks 是一套开源的 React Hooks 库，封装了大量好用的 Hooks。在当前 React 项目研发过程中，一套好用的 React Hooks 库是必不可少的

### react-navigation

1. this.props.navigation.navigate(xxx)在没注册路由的子组件中不可用，需要父组件把 navigation 对象在 props 中往下传递
2. 包含页面 A 和 B 的 StackNavigator 的跳转情况


### umijs
