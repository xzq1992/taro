在 Taro 中，我们使用 JSX 作为一种 DSL 进而编译成各端通用的语法。 JSX 是一种看起来很像 XML 的 JavaScript 语法扩展，比起模板语言，它具有以下优点：

1. 各大编辑器和 IDE 都能提供非常良好的支持；
2. 可以做到类型安全，在编译期就能发现错误；
3. 提供语义化并且可以移动的标签；
4. 背后的社区支持非常强力；

如果你是一名新手的话，可能一开始学习一种新语法会产生一些抵触。但当你熟悉了之后，就能发现 JSX 的更符合程序语言的书写逻辑，在处理一些精细复杂需求的时候也会比模板语言更为得心应手。

## 使用 JSX

请观察以下代码：

```javascript
import Taro, { Component } from '@tarojs/taro'
import { View } from '@tarojs/components'

class Home extends Component {
	render () {
		return (
			<View>Hello World!</View>
		)
	}
}
```

### 必须声明 `Taro` 和组件

在这段代码中，大写开头的 JSX 标签 `View` 表示它是一个 Taro 组件，尽管在整段代码中，变量 `View`  看起来并没有被调用，但也必须从 `@tarojs/components`  中引入声明。变量 `Taro` 也是一个必须引入声明的变量，因为我们在编译期和运行时会依赖这个变量做一些特殊处理。**当你引入了组件时，一定要使用，不要出现没有使用的变量。**

> 当你只用支持微信小程序时，可以不用引入组件例如  `View` 这样的声明。但我们仍然强烈推荐你在顶部引入你将要使用的组件，这样编辑器/IDE 能更好地提前发现可能出现的问题，也为将来可能需要的多端转换留有余地。

### 首字母大写与驼峰式命名

在 Taro 中，所有组件都应当首字母大写并且使用大驼峰式命名法（Camel-Case）。

例如，下面的代码将无法按预期运行：

```jsx
import Taro, { Component } from '@tarojs/taro'
// 引入一个自定义组件组件
import home_page from './page'

// 错误！组件名应该首字母大写:
class App extends Component {
	render () {
		return (
			<home_page message="Hello World!" />
		)
	}
}
```

为了解决这个问题，我们将 `home_page ` 重命名为 `HomePage`，然后使用 `<HomePage />` 引用：

```jsx
import Taro, { Component } from '@tarojs/taro'
// 引入一个自定义组件组件
import HomePage from './page'

class App extends Component {
	render () {
		return (
			<HomePage message="Hello World!" />
		)
	}
}
```

> 和 React/Nerv 不一样的地方在于，Taro 不支持使用 `点表示法` 和[运行时指定类型](https://reactjs.org/docs/jsx-in-depth.html#choosing-the-type-at-runtime)来引用组件，例如 `<MyComponents.DatePicker />` 这样的写法在 Taro 中是无法正确编译的。


## 属性

在 JSX 中有几种不同的方式来指定属性。

### 使用 JavaScript 表达式

你可以任意地在 JSX 当中使用 [JavaScript 表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Expressions_and_Operators#%E8%A1%A8%E8%BE%BE%E5%BC%8F)，在 JSX 当中的表达式要包含在大括号里。例如，在这个 JSX 中：

```jsx
<MyComponent foo={1 + 2 + 3 + 4} />
```

对于 MyComponent 来说， props.foo 的值为 10，这是 1 + 2 + 3 + 4 表达式计算得出的。

if 语句和 for 循环在 JavaScript 中不是表达式，因此它们不能直接在 JSX 中使用，所以你可以将它们放在周围的代码中。

```jsx
import Taro, { Component } from '@tarojs/taro'


class App extends Components {
	render () {
		return (
		  let description;
		  if (props.number % 2 == 0) {
		    description = <Text>even</Text>;
		  } else {
		    description = <Text>odd</Text>;
		  }
  	      return <View>{this.props.number} is an {description} number</View>;
		)
	}
}
```


### 字符串常量

你可以将字符串常量作为属性值传递。下面这两个 JSX 表达式是等价的：

```jsx
<MyComponent message="hello world" />

<MyComponent message={'hello world'} />
```

### 默认为 True

如果你没有给属性传值，它默认为 true。因此下面两个 JSX 是等价的：

```js
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```

> 和 React/Nerv 的不同：
> React 可以使用 `...` 拓展操作符来传递属性，但在 Taro 中你不能这么做。例如：
> ```
> const props = {firstName: ‘Plus’, lastName: ’Second’}
> return <Greeting {...props} />
> ```
> 这样的操作会报错。你只能手动地把所有需要引用的 props 写上去：
> `<Greeting firstName="Plus" lastName="Second" />`

### 嵌套

如果 JSX 标签是闭合式的，那么你需要在结尾处用 />, 就好像 XML/HTML 一样：

```javascript
const element = <Image src={user.avatarUrl} />;
```

JSX 标签同样可以相互嵌套：

```javascript
const element = (
  <View>
    <Text>Hello!</Text>
    <Text>Good to see you here.</Text>
  </View>
);
```

JavaScript 表达式也可以嵌套：

```javascript
render () {
	  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((todo) => <Text>{todo}</Text>)}
    </ul>
  );
}
```

### 布尔值、Null 和 Undefined 被忽略

false、null、undefined 和 true 都是有效的 children，但它们不会直接被渲染。下面的表达式是等价的：

```javascript
<View />

<View></View>

<View>{false}</View>

<View>{null}</View>

<View>{undefined}</View>

<View>{true}</View>
```

这在根据条件来确定是否渲染 元素时非常有用。以下的JSX只会在showHeader为true时渲染<Header />组件。

```javascript
<View>
  {showHeader && <Header />}
  <Content />
</View>
```

## 与 React/Nerv 最大的不同

### 属性不能传入 JSX 元素

考虑如下代码：

```javascript
const element = <Content footer={<View />} />
```

这样的代码在 Nerv/React 中使用是没有问题的，但是在 Taro 中不能这么做。

> 由于微信小程序内置的组件化的系统不能通过属性（props） 传函数，而 props 传递函数可以说 React 体系的根基之一，我们只能自己实现了一套组件化系统。而自制的组件化系统则不能使用内置组件化的 `slot` 功能。两权相害取其轻，我们暂时只能对这个功能忍痛割爱了。

### 自定义组件不能嵌套子元素

考虑如下代码：

```javascript
// Tabs.js
class Tabs extends Componenet {
	render () {
		return <View> {this.props.children} </View>
	}
}
// App.js
import Tabs from './tabs'
import TabItem from './tab-item'
class App extends Componenet {
	render () {
		return (
			<Tabs>
				<TabItem content='首屏' />
				<TabItem content='二屏' />
			</Tabs>
		)
	}
}
```

这样的代码在 Taro 中也不能运行。在 Nerv/React 中，自定义组件嵌套实际上也是通过 `props` 来实现的，只是 `children` 是一个特殊的 `prop` 。而对于 Taro，前文已阐述过不能通过 `props` 来传递 JSX 元素。上述的代码可改写成：

```javascript
// Tabs.js
class Tabs extends Componenet {
	render () {
		return <View>
			{this.props.items.map(item => {
				return <TabItem content ={item} />
			})}
		</View>
	}
}
// App.js
import Tabs from './tabs'
class App extends Componenet {
	state = {
		items: ['首屏', '二屏']
	}
	render () {
		return (
			<Tabs items={this.state.items} />
		)
	}
}
```

> 原生组件（从 `@tarojs/components` 引入进来的组件）是可以嵌套子元素的。

### 自定义组件的名称必须和引入时一致

考虑如下代码：

```javascript
// Tabs.js
export default class Tabs extends Componenet {
	render () {
		return <View> {this.props.children} </View>
	}
}
// App.js
import Tabset from './tabs'
class App extends Componenet {
	render () {
		return (
			<Tabset >
		)
	}
}
```

在 React/Nerv 中，你可以把组件的名称命名为任何遵循 JavaScript 规范的名字，通过 `export default` 导出时再通过 `import` 引入，又可以给他命名为一个不同的名字。但在 Taro 中你不能这么做，当转换成小程序时，自定义组件实际上会包一层 `template` 组件，而 `template` 的 `name` 属性必须是和 `class` 定义的名称一致的，否则 Taro 无法找到对应的组件：

```javascript
// Tabs.js
export default class Tabs extends Componenet {
	render () {
		return <View> {this.props.children} </View>
	}
}
// App.js
// 定义 `Tabs`，引入名称也必须是 `Tabs`
import Tabs from './tabs'
class App extends Componenet {
	render () {
		return (
			<Tabs >
		)
	}
}
```
