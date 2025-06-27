## 🔷USESTATE

### useInput 1
\- input상태 관리를 간단하게 재사용할 수 있도록 만든 hook
***
#### 💻코드
```js
import React, { useState } from "react";
import ReactDOM from "react-dom/client";

import "./styles.css";

const useInput = (initialValue) => {
  const [value, setValue] = useState(initialValue);

  const handleChange = (e) => {
    setValue(e.target.value);
  };

  return {
    value,
    onChange: handleChange,
  };
};

const App = () => {
  const name = useInput("");
  return (
    <div className="App">
      <h1>Hello</h1>
      <input type="text" placeholder="Name" {...name} />
    </div>
  );
};

// React 18에서 사용하는 방식
const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경

```
***

#### 📑정리
`const [value, setValue] = useState(initialValue)`
\- `initialValue` 받아 `value`라는 상태로 관리함.

`<input type="text" placeholder="Name" {...name} />`
\- `{...name}` 은 `value={name.value}` 이렇게 쓰이기도 함

`return {
    value,
    onChange: handleChange,
  };`
\- `value`와 `onChange`를 객체로 반환 => `input`에 `{...name}`을 그대로 넘길 수 있음.


### useInput 2
\-input상태 관리를 간단하게 재사용할 수 있도록 만든 hook
***
#### 💻코드
```js
const useInput = (initialValue) => {
  const [value, setValue] = useState(initialValue);
  const onChange = (event) => {
    console.log(event.target);
  };
  return { value };
};
=> useInput("") // 사이에 들어가있는 값에서 변화되지 않음

value={name.value} onChange={name.onChange} // 를 짧게 표현하면 {...name}
const useInput = (initialValue, validator) => {
  const [value, setValue] = useState(initialValue);
  const onChange = (event) => {
    const {
      target: { value },
    } = event;
    let willUpdate = true;
    if (typeof validator === "function") {
      willUpdate = validator(value); //유효성검사
    }
    if (willUpdate) {
      setValue(value);
    }
  };
  return { value, onChange };
};
```


### useTabs
\-현재 선택한 content의 값, 즉 위에서 만들어 놓은 배열에서 내가 현재 선택한 content의 인덱스를 언어내려는 hook
***
#### 💻코드
```js
import React, { useState } from "react";
import ReactDOM from "react-dom/client"; 

import "./styles.css";

const content = [
  {
    tab: "Section 1",
    content: "I`m the content of the Section 1",
  },
  {
    tab: "Section 2",
    content: "I`m the content of the Section 2",
  },
];

const useTabs = (initialTab, allTabs) => {
  if (!allTabs || !Array.isArray(allTabs)) {
    return; 
  }
  const [currentIndex, setCurrentIndex] = useState(initialTab);
  return {
    currentItem: allTabs[currentIndex],
    changeItem: setCurrentIndex, //return값은 cuurentItem / allTabs는 기본적으로 0 ,
  };
};

const App = () => {
  const { currentItem, changeItem } = useTabs(0, content); //(기본값, 현재값) 지정 필요함 , 여기서 0이라 하는것과 위에서 content[0] 이라 하는것 같음
  return (
    <div className="App">
      {content.map((section, index) => (
        <button onClick={() => changeItem(index)}>{section.tab}</button>
      ))}
      <div>{currentItem.content}</div>
    </div>
  );
};


// React 18에서 사용하는 방식
const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```
***

#### 📑정리
`if (!allTabs || !Array.isArray(allTabs)) {
    return;`
\- `allTabs` 없거나 배열 아니면 `return`처리.

`const [currentIndex, setCurrentIndex] = useState(initialTab);`
\- `currentIndex` : 현재 선택된 탭의 인덱스 /  기본값 `initilaTab`

`return {
    currentItem: allTabs[currentIndex],
    changeItem: setCurrentIndex,`
\-`currentItem`현재의 선택된 탭의 데이터 / `changeItem`:탭을 변경하는 함수

\- allTabs 가 true가 아니거나 , 배열이 아닌경우 `Array.isArray()` 메서드 에서 해당인자 Array인지 알려줌


## 🔷USEEFFECT
### useEffect
`useEffect(function, deps)`
\- 부수 효과를 처리할 때 사용하는 hook
 ***
 #### 💻코드
```js
import React, { useState, useEffect } from "react";
import ReactDOM from "react-dom/client";

const App = () => {
  const sayHello = () => console.log("hello");

  const [number, setNumber] = useState(0);
  const [aNumber, setAnumber] = useState(0);
  useEffect(sayHello, []);
  return (
    <div className="App">
      <div>hi</div>
      <button onClick={() => setNumber(number + 1)}>{number}</button>
      <button onClick={() => setAnumber(aNumber + 1)}>{aNumber}</button>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```
***

#### 📑정리
`function`
\-수행하고자 하는거
`deps` 
\-배열 형태,배열에 검사하고자하는 특정값 or 빈 배열
`componentDidMount`
\-렌더링 끝난 후 실행
`componentWillUnMount`
\-unmount 되기 직전의 작업 
`componentDidUpdate`
\-update 일어난 후 작업


### useTitle
\- 탭의 제목(`title`)을 동적으로 변경하기 위한 hook.
***
#### 💻코드
```js
import React, { useState, useEffect } from "react";
import ReactDOM from "react-dom/client";

const useTitle = (initialTitle) => {
  const [title, setTitle] = useState(initialTitle);
  const updateTitle = () => {
    const htmlTitle = document.querySelector("title");
    htmlTitle.innerText = title;
  };
  useEffect(updateTitle, [title]);
  return setTitle;
};

const App = () => {
  const titleUpdater = useTitle("Loading...");
  setTimeout(() => titleUpdater("Home"), 5000); 
  return (
    <div className="App">
      <div>hi</div>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```
***

#### 📑정리
`const [title, setTitle] = useState(initialTitle)`
\- 상태값 `title`로 현재 제목을 저장함, `setTitle`로 수정할 수 있도록 함.

`const updateTitle = () => {
    const htmlTitle = document.querySelector("title");
    htmlTitle.innerText = title;
  };`
\- 실제 브라우저의 `title`태그를 찾아서 내용을 `title`로 갱신.

`useEffect(updateTitle, [title])`
\- `title` 변화할때 마다 `updateTitle()` 실, `setTitle` 호출되면 실제로 탭 제목 변경.

`setTimeout (() => function("바뀔인수"), 초);`
시간지연 함수로 만약 컴포넌트가 마운트 되는 시점에 딱 한번 실행되기를 원한다면, `useEffect` 를 통해 사용하면 됨
ex) 
`useEffect(() =>{
const title = setTimeout(() => titleUpdater("Home",5000);
}[]);
`


### useRef
`reference` : componet의 어떤 부분을 선택할 수 있는 방법`document.getElementByID()`를 사용한것과 동등하게 하는 법
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";

const App = () => {
  const Q = useRef(); 
  setTimeout(() => Q.current.focus(), 5000);
  return (
    <div className="App">
      <div>hi</div>
      <input ref={Q} placeholder="la" />
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```
***

#### 📑정리
`const Q = useRef()`
\- `Q` 참조 객체

`setTimeout(() => Q.current.focus(), 5000);`
\- 사용자 입력을 받을 수 있도록 커서가 자동으로 `Q.querrent`가 가리키는 `input`으로 이동.

#### ❓REF
-`ref` React에서 Dom 요소나 컴포넌트 인스턴스를 직접 참조할 수 있게해주는 기능
-`useRef()` 사용하면 렌더링과 무관하게 값이 유지되는 참조 만들 수 있음


### useClick
//DOM요소에 직접 클릭이벤트 연결하는 hook
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";

const useClick = (onClick) => {
  if (typeof onClick !== "function") {
    return;
  }
  const element = useRef();
  useEffect(() => {
    if (element.current) {
      element.current.addEventListener("click", onClick);
    }
    return () => {
      if (element.current) {
        element.current.removeEventListener("click", onClick);
      }
    };
  }, []);
  return element;
}; 기

const App = () => {
  const sayHello = () => console.log("say Hello");
  const title = useClick(sayHello);
  return (
    <div className="App">
      <h1 ref={title}>hi</h1>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```
***

#### 📑정리
`const useClick = (onClick) => {
  if (typeof onClick !== "function") {
    return;
  }`
\- `onClick`이 함수가 아니라면 아무것도 활성화 되지 않음.

`if (element.current) {
      element.current.addEventListener("click", onClick);`
\- 컴포넌트 마운트시 `click` 이벤트 등록

`if (element.current) {
        element.current.removeEventListener("click", onClick);`
\- 언마운트시 `click` 이벤트 제거


### useConfilm
\- 사용자가 작업을 하기전에(이벤트를 실행하기 전에) 메세지를 보여줘 확인하는 단계 / browser 활용
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";

const useConfirm = (message = " ", callback) => {
  if (typeof callback !== "function") {
    return;
  }
  const confirmAction = () => {
    if (confirm(message)) {
      callback();
    }
  };
  return confirmAction;
};

const App = () => {
  const deleteWorld = () => console.log("Deleting the world..");
  const confirmDelete = useConfirm("Are you sure", deleteWorld);
  return (
    <div className="App">
      <button onClick={confirmDelete}> Delete the world </button>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경


```
***
#### 📑정리

`message`
\- 확인창에 띄울 메시지

`callback`
\- 사용자의 반응이 확인되었을 때 실행할 함수

\- `confirm(message)`는 브라우저의 기본 확인 창 띄우고, 사용자가 "확인" 클릭할때 `callback()` 실행. "취소" 클리시 아무일도 발생하지 않음.
