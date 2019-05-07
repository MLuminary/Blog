# <Element /> 与 renderElement 杂交

​	最近因为工作和学校论文上的事情始终没有让自己能静下心来去好好写点总结，但感觉其实时间还是有的，而且写东西确实能让我身心愉快。感觉脱离了这纷杂的尘世，好似乘一片竹榻，游荡在山水之间。以后还是希望自己能坚持下来，多总结总结，多写一些对自己有用的东西。

下面直接切入正题，自己「引入」组件一般是两种方式，<Element />  与 ` renderElement`, 这里为什么要加引号呢，是因为 ` renderElement `可能不算引入，它本来就在其要调用的组件的内部，这里并不是想记录这两种方式，大家可以稍微往下看一下。

#### 外部导入

```ts
import * as React from "react"

const Input = props => {
  const { inputValue, setInputValue } = props
  const onChange = event => {
    const value = event.target.value
    setInputValue(value)
  }
  return <input onChange={onChange} value={inputValue} type="text" />
}

export const Counter: React.FunctionComponent = props => {
  const [inputValue, setInputValue] = React.useState("")

  return <Input inputValue={inputValue} setInputValue={setInputValue} />
}

```

#### 内部渲染

```ts
import * as React from "react"

export const Counter: React.FunctionComponent = props => {
  const [inputValue, setInputValue] = React.useState("")
  
  const renderInput = props => {
  const { inputValue, setInputValue } = props
  const onChange = event => {
    const value = event.target.value
    setInputValue(value)
  }
  return <input onChange={onChange} value={inputValue} type="text" />
}

  return (
  	{renderInput({inputValue, setInputValue})}
  )
}
```

#### 不走寻常路🐒

这两种情况我们在开发过程中是司空见惯的，但是你有没有想过为什么不像下面这样去用

```ts
import * as React from "react"

export const Counter: React.FunctionComponent = props => {
  const [inputValue, setInputValue] = React.useState("")
  
  const Input = props => {
  const { inputValue, setInputValue } = props
  const onChange = event => {
    const value = event.target.value
    setInputValue(value)
  }
  return <input onChange={onChange} value={inputValue} type="text" />
}

  return (
  	<Input inputValue={inputValue} setInputValue={setInputValue} />
  )
}
```

我们在组件内部创建了一个函数组件，并用 `<Element / >` 的方式去调用了它，看似好像合情合理，没有什么太大的问题，但其实有很大的差别，大家可以看一下 [示例](https://codesandbox.io/s/k95lky3n97?fontsize=14)，你会发现每次在输入框输入内容的时候输入框都会失焦，我的推测大体如下：

当输入框输入值时触发 `setInputValue` 导致 `inputValue` 改变，`Counter` 组件重新渲染，`Input` 组件因为写在`Counter`组件内部，所以也会重新创建，也就是说此时的 `Input` 已经不是上次的 `Input`，而以 `<Element />` 这种形式渲染组件会根据 `Input` 组件变化与否去选择复用或是重新创建之前的组件，因为此时 `Input` 已经变化，所以每次都会重新创建 `Input` ，也就会导致失焦。

外部导入那种情况 `Input` 组件本身在 `Counter` 重新渲染时不会重新创建，因此也就不会出现这种问题。 那内部渲染那种情况为什么不会出现失焦这种情况呢，同样，当输入框输入值时 `Counter` 组件也会重新渲染，此时 `renderInput` 也会重新创建，但在渲染组件时只相当于函数调用，虽然方法不同，但是返回的结果是一样的，其实也就相当于下面这种形式

```ts
import * as React from "react"

export const Counter: React.FunctionComponent = props => {
  const [inputValue, setInputValue] = React.useState("")
  
  return (
 	<input onChange={onChange} value={inputValue} type="text" />
  )
}
```

其实这个问题也属于比较小的问题，自己也是在开发中偶然想到就去玩了一下并把过程记录了下来，人嘛，玩还是要玩的🤣



