---
title: useReducer
tags: React, Talk
---

# useReducer!

<!-- Put the link to this slide here so people can follow -->
slide: https://chekai7.github.io/c.c.k

---

- React useState: simple State
- React useReducer: complex State
- React useContext: global State

---

useState 容易管理單一狀態，但如果需要管理多個狀態，讓他比較好維護，這時候可以考慮使用 	**useReducer**

比如:
```javascript=
const initFirstUser = {
  id: "0391-3233-3201",
  firstName: "Bill",
  lastName: "Wilson",
  city: "Missoula",
  state: "Montana",
  email: "bwilson@mtnwilsons.com",
  admin: false
};
```

---

```javascript=
(state, action) => newState
```

useReducer 接受一個 (state, action) => newState ，然後回傳現在的 state 以及其配套的 dispatch 方法。（如果你熟悉 Redux，你已經知道這如何運作。）


---

## 以下是用 reducer 寫個範例：

圖片支援
![image alt](https://res.cloudinary.com/practicaldev/image/fetch/s--AMPFLPYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/vy5bq1x9f26yteauzh97.png)

---
```javascript=
import React, { useReducer } from "react";

const initialValue = { count: 0 };

const reducer = (state, { type }) => {
  switch (type) {
    case "add":
      return { ...state, count: state.count + 1 };
    case "minus":
      return { ...state, count: state.count - 1 };
       case "reset":
      return { ...state, count: 0 };
    default:
      return state;
  }
};
```
一開始先初始化 initialValue
再來就是寫出如何更改狀態的方法



---

```javascript=
export default function App() {
  const [state, dispatch] = useReducer(reducer, initialValue);
  return (
    <div className="App">
      <h1>Hello useReducer</h1>
      <h2>{JSON.stringify(state)}</h2>
      <button onClick={() => dispatch({ type: "add" })}>add</button>
      <button onClick={() => dispatch({ type: "minus" })}>minus</button>
      <button onClick={() => dispatch({ type: "reset" })}>reset</button>
    </div>
  );
}
```
每當使用者點擊某個按鈕，就會發出一個動作來更新計數值count，頁面就會展示更新之後count。

==React 確保 dispatch function 本身是穩定的，而且不會在重新 render 時改變。這就為什麼可以安全地從 useEffect 或 useCallback 的依賴列表省略它。==

==如果你在 Reducer Hook 回傳的值與目前的 state 相同，React 將會跳過 child component 的 render 及 effect 的執行。（React 使用 	[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 比較演算法。）==

---
**文本輸入(input)**

用useState的寫法

```javascript=
import React, { useState } from "react";

export default function App() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  return (
    <div className="App">
      <h2>{JSON.stringify({ email, password }, null, 2)}</h2>
      <label>
        <input
          onChange={(e) => setEmail(e.target.value)}
          value={email}
          type="text"
        />
      </label>
      <label>
        <input
          onChange={(e) => setPassword(e.target.value)}
          value={password}
          type="text"
        />
      </label>
    </div>
  );
}
```

UI State: useSate
[custom hook寫法](https://codesandbox.io/s/customhook-1my07?file=/src/App.js) :(Re-using logic)取共用logic來做抽象，可以被重複使用，**處理數據邏輯**之類的東西。通過創建自定義鉤子，將它們從組件中取出。(Not UI State)

[Render prpos寫法](https://codesandbox.io/s/renderprops-nw1yt?file=/src/FormManager.js) : (Re-using layout)可以僅重新渲染相關內容，將需要重用的任何狀態邏輯放入Container組件中，舉例 :可以用於創建多個表單的表單佈局，但是每個表單具有不同的子組件。

如果要隔離jsx的一部分並註入一些狀態而不給組件帶來副作用，則render props 非常有用。

---

**文本輸入(input)**

用useReducer的寫法

```javascript=
const initialValue = { email: "", password: "" };

const reducer = (state, { type, payload }) => {
  return { ...state, [type]: payload };
};

```
reducer會預期收到action和type，type和payload 用來回傳 current state 和 [type]: payload


---
用useReducer的寫法

```javascript=
export default function App() {
  const [state, dispatch] = useReducer(reducer, initialValue);
  return (
    <div className="App">
      <h2>{JSON.stringify(state, null, 2)}</h2>
      <label>
        <input
          type="text"
          value={state.email}
          onChange={(e) => dispatch({ type: "email", payload: e.target.value })}
        />
      </label>
      <label>
        <input
          type="text"
          value={state.password}
          onChange={(e) =>
            dispatch({ type: "password", payload: e.target.value })
          }
        />
      </label>
    </div>
  );
}

```


---

onChange事件可以改成 接受 name 和 value
```javascript=
 const onChange = ({ target: { name, value } }) => {
    dispatch({ type: name, payload: value });
  };
  
  .
  .
  .
  return (
  <>
   <input
        value={state.email}
        name="email"
        onChange={onChange}
      />  
  </>
  )
  
```


---

input 也可以改成 接受 value, type, onChange事件
```javascript=
 const InputTextfield = ({ value, type, onChange }) => {
  return <input value={value} type={type} 
  onChange={onChange} />;
  };
  
  .
  .
  .
  return (
  <>
   <InputTextfield
        value={state.email}
        type="text"
        onChange={(e) => dispatch({ type: "email", payload: e.target.value })}
      />
  </>
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

useState v.s. useReudcer


- <code class="orange"> useStatet </code>:適合管理簡單狀態
- <code class="blue"> useReducer </code>: 適合複雜的狀態對像或狀態轉換（要保持可維護性和可預測性）



---
<style>
code.blue {
  color: #337AB7 !important;
}
code.orange {
  color: #F7A004 !important;
}
</style>

useContext v.s. useReudcer


- <code class="orange"> useContext </code>:可訪問訪問狀態，避免一層的傳遞狀態。
- <code class="blue"> useReducer </code>: 用過Redux肯定不會陌生，它主要用於更新復雜邏輯的狀態。

p.s. 新手會認為 context本身是一個狀態管理系統 ❌
     他僅只是一種依賴注入的機制，所以你可以在各個所需的任何值包在context裡面，並且通常情況下，您是使用useState鉤子或useReducer鉤子在React組件中管理該狀態的人。
     
> 您是一個決定狀態的地方，處理如何更新狀態，然後將值放入Context中進行分發。
>  [name=Kai]

useReducer再加useContext上一種狀態管理系統。而且這相當於Redux對React的作用，但是useContext 本身不是狀態管理系統。



---
<style>
code.blue {
  color: #337AB7 !important;
}
code.orange {
  color: #F7A004 !important;
}
</style>

Redux v.s. useReudcer


- <code class="orange"> Reduxt </code>: 創建了一個全局狀態容器
- <code class="blue"> useReducer </code>: 在您的組件內創建了一個獨立的組件共置狀態容器

react 內建的 context 與 useReducer hooks ，可以做到 redux 的功能，但我認為 redux 還是有存在的必要的，
例如處理非同步流程時 redux 擁有 redux-thunk 或 redux-saga 等 middleware 可以使用，使用 hooks 的話可能就需要自己處理比較多東西(useEffect) ，還是依照專案的需求決定囉！

---

參考連結:
https://zh-hant.reactjs.org/docs/hooks-reference.html
https://dev.to/milu_franz/demystifying-react-hooks-usereducer-3o3n
https://dev.to/milu_franz/demystifying-react-hooks-usecontext-5g4a
https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/react-hooks-usestate-vs-usereducer-b14966ad37dd

---



### 教材在此，可以自己去玩玩。:heart: 
:muscle: codesandbox https://codesandbox.io/s/cranky-cartwright-pzij9


---

### Thank you! :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k
