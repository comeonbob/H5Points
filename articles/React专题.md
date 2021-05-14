[TOC]

# React

官网：https://zh-hans.reactjs.org/docs/getting-started.html

## 安装

**单页应用**

- 全局安装脚手架：`npm install create-react-app -g`
- 创建demo： `create-react-app demo1`

**多页应用**

- todo

**工程项目**

- react + ts， todo；

## 核心概念

### 1. JSX

1. jsx是什么

   jsx是javascript的语法扩展，可以是一个表达式；

2. 为什么要用jsx

   因为React认为，渲染逻辑和UI逻辑存在耦合；

3. 特性

   - jsx是一个表达式；
   - jsx中采用小驼峰命名；

### 2. 元素

1. react 元素是什么

   ```javascript
   const element = <h1>hello world</h1>
   ```

   元素是构成react应用的最小块。

   

2. 如何将元素渲染成浏览器DOM；

   ```javascript
   const element = <h1> hello </h1>
   ReactDOM.render(element, document.getElemntById('root'))
   ```

3. React 只更新它需要更新的部分；

### 3. 组件

1. 组件是什么

   组件，就是一个javascript函数。接受任意的参数props， 返回用于描述页面内容的react元素；

2. 函数组件

   ```javascript
   function demoComponent(props) {
     return <h1>Hello, {props.name}</h1>
   }
   const element = <DemoComponent name="Bob" />
   ```

3. class组件

   ```javascript
   class demoComponent extends React.Component {
     return() {
       return <h1>Hello, {props.name}</h1>
     }
   }
   const element = <DemoComponent name="Bob" />
   ```


### 4. 生命周期

```javascript
class clock extends React.component {
  constructor(props) {
    super(props)
    this.state = { date: new Date() }
  }
  
  // 组件加载后
  componentDidMount() {}
  
  // 组件卸载前
  componentWillUnmount() {}
  
  render() {
    return ()
  } 
}
```

1. 不要直接修改State；
2. State的更新可能是异步的；
3. State的更新会被合并；
4. 数据是向下流动的；

### 5. 事件处理

```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```

1. React事件的命名采用小驼峰式；
2. 必须手动绑定this；
3. 可以通过箭头函数或者`Function.prototype.bind`传参；

### 6.列表 & Key

```javascript
// 单行
function ListItem(props) {
  return <li>{props.value}</li>
}

// 列表
function List(props) {
  const nums = props.numbers
  const list = nums.map(number => {
    <ListItem key={number.toString()} value={number} />
  })
  return (
    <ul>
			{list}
    </ul>
  )
}
```

### 7.表单

1. 受控组件：通常自己维护state，并根据用户输入进行更新。

   ```javascript
   class NameForm extends React.Component {
     constructor(props) {
       super(props)
       this.state = { value: '' }
       this.handleChange = this.handleChange.bind(this)
     }
     
     handleChange(event) {
       this.setState({ value: event.target.value })
     }
     
     render() {
       return (
       	<form>
         	<input type="text" value={this.state.value} onChange={this.handleChange} />
         	<input type="button" value="提交" />
         </form>
       )
     }
   }
   ```

2. 常见受控元素： `input`, `textarea`, `select`;

### 8.状态提升

- 通常，我们建议将共享状态提升到最近的共同父组件中；
- 应当依靠自上而下的数据流，而不是尝试在不同组件间同步state；

### 9.组合 vs 继承

### 10.React 哲学



## 高级特性





## HOOK

## 内置API

## FAQ

1. this.setState的用法
2. 