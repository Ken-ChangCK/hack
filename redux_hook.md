---
title: React Redux 與 Hooks
tags: React
description: View the slide with "Slide Mode".
---

# React Redux 與 Hooks!

<!-- Put the link to this slide here so people can follow -->
slide: https://hackmd.io/p/template-Talk-slide

---

自從 v7.1 版以後提供了 hooks 的函式，
不再需要仰賴 connect()，
讓取得狀態與 dispatch action 改變 redux 中的狀態變得更容易，也更清楚明瞭

---

## 前置作業

通過createStore 將 state 存入 store，
再將 store 物件透過 Provider 提供給 App 元件使用

```javascript=
const store = createStore(rootReducer)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

---

<style>
code.blue {
  color: #337AB7 !important;
}
code.orange {
  color: #F7A004 !important;
}
</style>


useSelector & useDispatch 

- <code class="blue"> useSelector </code>: 能夠取得 redux store 中的狀態。
- <code class="orange"> useDispatch </code> : 能夠 dispatch action 至 redux store。


---

### useSelector
##### 允許我們直接從 Redux store 中的狀態提取數據到元件中。

```javascript
import React from 'react'
import { useSelector } from 'react-redux'

export const CounterComponent = () => {
  const counter = useSelector((state) => state.counter)
  
  return <div>{counter}</div>
}
```

:::info
1. useSelector會訂閱store, 當action被dispatched的時候，
   會運行selector。
2. 你可能在一個組件內調用useSelector多次，
    但是對useSelector()的每個調用都會創建redux store的單個訂閱。
    由於react-reduxv7版本使用的react的批量(batching)更新行為，
    造成同個組件中，多次useSelector返回的值只會re-render一次。
:::


---

> useSelector掌握渲染的時機

在元件 A 中使用 useSelector() 取得 redux store 中 count 的數值，
當有一個 action 被 dispatch 到 redux store 時，
會用 equalityFn 嚴格判別 count 是否已經改變了，
如果改變了，才強制渲染該元件，元件 A 就會重新取得 count 的值。


---

### useDispatch
#### 直接回傳一個 dispatch 方法，可以直接透過它觸發 Reducer

###### 簡單範例
```javascript
import React from 'react'
import { useDispatch } from 'react-redux'

export const CounterComponent = ({ value }) => {
  const dispatch = useDispatch()

  return (
    <div>
      <span>{value}</span>
      <button onClick={() => dispatch({ type: 'increment-counter' })}>
        Increment counter
      </button>
    </div>
  )
}
```

---

###### 父組件傳給子組件
- 建議使用useCallback對其進行記憶，否則子組件接收props之後，
可能會產生不必要的re-render。

```javascript
import React, { useCallback } from 'react'
import { useDispatch } from 'react-redux'

export const CounterComponent = ({ value }) => {
  const dispatch = useDispatch()
  const incrementCounter = useCallback(
    () => dispatch({ type: 'increment-counter' }),
    [dispatch]
  )

  return (
    <div>
      <span>{value}</span>
      <MyIncrementButton onIncrement={incrementCounter} />
    </div>
  )
}

export const MyIncrementButton = React.memo(({ onIncrement }) => (
  <button onClick={onIncrement}>Increment counter</button>
))
```

---

###### component內自己dispatch方法
- 使用 dispatch方法會很穩定，
- 將 dispatch 作為它的依賴項之一傳遞給我們的 useEffect 函數，
因為這不會改變。傳遞dispatch這將防止無限循環並確保此函數僅被調用一次。

```javascript
// ./store/todos/actionCreators.js
export const fetchTodos = () => async () => {
  dispatch(setLoading({status: true, tip: '加載中...'}))

  const response = await fetch('/fetchTodos', () => dbTodos)

  dispatch({ type: SET_TODOS, payload: response })

  dispatch(setLoading({status: false, tip: ''}))
}
--

// TodoApp.js
import { useDispatch } from 'react-redux'
import {fetchTodos} from './store/todos/actionCreators'

const TodoApp = () => {
  const dispatch = useDispatch()

  useEffect(() => {
    dispatch(fetchTodos())
  }, [dispatch])

  ...
}

```

---

### useStore

#### 這個Hook返回redux <Provider>組件的所有store對象

```javascript
import React from 'react'
import { useStore } from 'react-redux'

export const CounterComponent = ({ value }) => {
  const store = useStore()

  // 這只是個範例! 盡量不要在專案這樣做.
  // 如果store中的state改變，這個將不會自動家更新

  return <div>{store.getState()}</div>
}

```
---




### 教材在此，可以自己去玩玩。:heart: 
:muscle: codesandbox 
https://codesandbox.io/s/infallible-silence-10lmx?file=/src/Main.js



---

### 參考資料

- https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/developing-react-redux-from-zero-to-one-e27eddfbce39
- https://ithelp.ithome.com.tw/articles/10251966


---

### Thank you! :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k
