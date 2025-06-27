###  1. usePreventLeave
\- 웹사이트를 나가려고 할 때 알람문구를 띄워 물어볼 수 있는 hook
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";
import useAxios from "./useAxios";

const usePreventLeave = () => {
  const listener = (event) => {
    event.preventDefault();
    event.returnValue = "";
  };
  const enablePrevent = () => window.addEventListener("beforeunload", listener);
  const disablePrevent = () =>
    window.removeEventListener("beforeunload", listener);
  return { enablePrevent, disablePrevent };
};

const App = () => {
  const { enablePrevent, disablePrevent } = usePreventLeave();
  return (
    <div className="App" style={{ height: "1000vh" }}>
      <button onClick={enablePrevent}>Protect</button>
      <button onClick={disablePrevent}>Unprotect</button>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경

```
***
#### 📑정리
**beforeunload - webAPI**
window,문서(document)및 해당 리소스 언로드(undload) 되려고 할 때 시작되는 이벤트.
메서드에서 이벤트 이름을 사용하거나, 이벤트 핸들러 속성을 설정함.

`event.preventDefault()`와 `event.returnValue = ""`
\- 설정으로 "정말 떠나시겠어요?" 라는 팝업 띄워줌.

`enablePrevent()`
\- 알림창 활성화

`disablePrevent()`
\- 알림창 비활성화


### 2. useBeforeLeave
\- 페이지를 벗어날 때 사용자에게 팝업이나 알림을 띄워줄 수 있는 hook.

***
#### 💻코드

``` js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";
import useAxios from "./useAxios";

const useBeforeLeave = (onBefore) => {
  if (typeof onBefore !== "function") {
    return;
  }
  useEffect(() => {
    const handle = (event) => {
      const { clientY } = event;
      if (clientY <= 0) {
        onBefore();
      }
    };
    document.addEventListener("mouseleave", handle);
    return () => document.removeEventListener("mouseleave", handle);
  }, []);
};

const App = () => {
  const begForLife = () => console.log("Pls dont leave");
  useBeforeLeave(begForLife);
  return (
    <div className="App" style={{ height: "1000vh" }}>
      <h1> hello </h1>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경

```
***
#### 📑정리
\- 해당 코드에서는 사용자의 화면에서가 아닌 console 에서만 확인할 수 있도록 되어있음.


`const useBeforeLeave = (onBefore)`
\- `onebefore()`를 매개변수로 사용자가 이탈하려고 할 때 실행 되는 것


` const handle = (event) => {
      const { clientY } = event;
      if (clientY <= 0) {
        onBefore();
      }`
\- `mouseleave` 이벤트 발생시 마우스 위치(clientY)가 0보다 작거나 같을 때, `onBefore()`실행함.


#### ❓직접접근과 구조분해 차이

정답은 코드 스타일/간결성 차이

\- 직접접근
`const handle = (event) => {
    if (event.clientY <= 0)  `
=> 가장 직관적이고 명확함.

\- 구조 분해
`const handle = (event) => {
      const { clientY } = event;
      if (clientY <= 0)`
=> 간결하고, 재사용 시 편함


### 3. useFadeIn
\- 투명도(opacity)를 사용하여 서서히 나타나는 기능의 hook.
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";
import useAxios from "./useAxios";

const useFadeIn = (duration = 1, delay = 0) => {
  if ((typeof duration !== "number") | (typeof delay !== "number")) {
    return;
  }
  const element = useRef();
  useEffect(() => {
    if (element.current) {
      const { current } = element;
      current.style.transition = `opacity ${duration}s ease-in-out ${delay}`;
      current.style.opacity = 1;
    }
  }, []);
  return { ref: element, style: { opacity: 0 } };
};

const App = () => {
  const fadeInH1 = useFadeIn(1, 2);
  const fadeInP = useFadeIn(5, 10);
  return (
    <div className="App" style={{ height: "1000vh" }}>
      <h1 {...fadeInH1}> hello </h1>
      <p {...fadeInP}>lorem impsum</p>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경

```
***
#### 📑정리
` current.style.transition`
\- 모든 property들 각각 지정하여 원하는대로 만들 수 있음

`const useFadeIn = (duration = 1, delay = 0)`
\- duration : 페이드 인이 걸리는 시간
delay : 애니메이션이 언제 시작할지 딜레이

`const element = useRef();`
\- DOM 요소를 참조하기 위해`ref`생성.

`current.style.transition`
\- 여러 다른 스타일들로 바꾸며 애니메이션 효과 부여할 수 있음.

`const element = useRef();`
\- element들을 props로 활용할 수 있도록 작성됨.

`<h1 {...fadeInH1}> hello </h1>
 <p {...fadeInP}>lorem impsum</p>`
\- `{ref, style}`을 h1 태그에 props로 넘겨줌,

### 4. useNetwork
\- 네트워크의 상태가 온라인인지 오프라인인지 확인 할 수 있는 hook.
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";
import useAxios from "./useAxios";

const useNetwork = (onChange) => {
  const [status, setStatus] = useState(navigator.onLine);
  const handleChange = () => { //네트워크 상태가 바뀔 때 실행할 함수
    if (typeof onChange === "function") {
      onChange(navigator.onLine);
    }
    setStatus(navigator.onLine);
  };
  useEffect(() => {
    window.addEventListener("online", handleChange);
    window.addEventListener("offline", handleChange);

    return () => {
      window.removeEventListener("online", handleChange);
      window.removeEventListener("offline", handleChange);
    };
  }, []);
  return status;
};

const App = () => {
  const handleNetworkChange = (online) => {
    alert(online ? "We just went online" : "We are offline");
  };
  const onLine = useNetwork(handleNetworkChange);
  return (
    <div className="App" style={{ height: "1000vh" }}>
      <h1> {onLine ? "OnLine" : "Offline"} </h1>
      <p>lorem impsum</p>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```
***
#### 📑정리
`const useNetwork = (onChange) => {
  const [status, setStatus] = useState(navigator.onLine);`
\-  status 네트워크 상태를 저장하는 state

`navigator.onLine` 
\- 현재 브라우저의 온라인 상태를 알려주는 내장 API

`const handleChange = ()`
\- 네트워크 상태가 바뀔 때 실행할 함수,

`onChange(navigator.onLine);`
\- 외부에서 전달된 콜백 함수, 현재상태를 인자로 넘김.

`return status;`
\- true(online)/false(offline) 값 반환함.

### 5. useScroll
\-스크롤 위치 감지해서 동적으로 스타일을 바꾸는 훅
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";
import useAxios from "./useAxios";

const useScroll = () => {
  const [state, setState] = useState({
    x: 0,
    y: 0,
  });
  const onScroll = () => {
    setState({ y: window.scrollY, x: window.scrollX });
  };
  useEffect(() => {
    window.addEventListener("scroll", onScroll);
    return () => window.removeEventListener("scroll", onScroll);
  }, []);
  return state;
};

const App = () => {
  const { y } = useScroll();
  return (
    <div className="App" style={{ height: "1000vh" }}>
      <h1 style={{ position: "fixed", color: y > 100 ? "red" : "blue" }}>
        {" "}
        hi{" "}
      </h1>
      <p>lorem impsum</p>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경

```
***
#### 📑정리
`const [state, setState] = useState({
    x: 0,
    y: 0,
  });`
\- x:수평 스크롤 위치 /  y:수직 스크롤 위치 

` return () => window.removeEventListener("scroll", onScroll);`
\-`window`는 다른 변수들 보다 조금더 직접적으로 마우스 값을 전달 받을 수 있음.

### 6. useFullscreen
\- 지정 요소를 풀화면으로 보여주는 hook
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";
import useAxios from "./useAxios";

const useFullscreen = (callback) => {
  const element = useRef();
  const triggerFull = () => {
    if (element.current) {
      element.current.requestFullscreen();
      if (callback && typeof callback === "function") {
        callback(true);
      }
    }
  };
  const exitFull = () => {
    document.exitFullscreen();
    if (callback && typeof callback === "function") {
      callback(false);
    }
  };
  return { element, triggerFull, exitFull };
};

const App = () => {
  const onFulls = (isFull) => {
    console.log(isFull ? "We are full" : "We are small");
  };
  const { element, triggerFull, exitFull } = useFullscreen(onFulls);
  return (
    <div style={{ height: "1000vh" }}>
      <div ref={element}>
        <img
          src="https://i.pinimg.com/564x/60/b3/b8/60b3b888914534cfa940f458c2143798.jpg"
          alt={"img"}
        />
        <button onClick={exitFull}>Exit fullscreen</button>
      </div>
      <button onClick={triggerFull}>Make fullscreen</button>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />);
```
***
#### 📑정리

` element.current.requestFullscreen();`
\- 해당 DOM 요소를 전체화면으로 전환

`document.exitFullscreen();`
\- 전체화면 모드를 종료

\- `onFulls`는 전체화면 여부에 따라 로그 찍는 콜백함수

\- `useFullscreen`에서 리턴한 `element`,`triggerFull`,`exitFull` 사용함.

### 7. useNotification
- 사용자의 기기에 운영체제의 알림기능을 사용하는 hook
***
#### 💻코드
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";

const useNotification = (title, options) => {
  if (!("Notification" in window)) {
    return;
  }
  const fireNotif = () => {
    if (Notification.permission !== "granted") {
      Notification.requestPermission().then((permisiion) => {
        if (permission === "granted") {
          new Notification(title, options);
        }
      });
    } else {
      new Notification(title, options);
    }
  };
  return fireNotif;
};

const App = () => {
  const triggerNotifi = useNotification("Title", { body: "context" });
  return (
    <div className="App" style={{ height: "1000vh" }}>
      <button onClick={triggerNotifi}>hi</button>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```
***
#### 📑정리
`fireNotif`
\- 운영체제 내에서 알림설정이 허용 되어있을때만 실행됨.

\- useNotification = (알림의 타이틀 , 옵션 )
 => `useNotification = (title,options) =>{}`
 
 
`if (!("Notification" in window)) {
    return;}`
\- 웹의 경우에만 알림이 뜨도록 설정함.

`Notification.requestPermission().then() `
\- 권한을 획득하기 위한 구문(권한 허용되지 않았을경우).


### 8. useAxios
\- 데이터 요청을 쉽게 처리 할 수 있는 사용자 정의 hook

***
#### 💻코드
index.js
```js
import React, { useState, useEffect, useRef } from "react";
import ReactDOM from "react-dom/client";
import useAxios from "./useAxios";

const App = () => {
  const { loading, error, data, refetch } = useAxios({
    url: "https://yts.mx/api/v2/list_movies.json",
  });
  console.log(`Loading:${loading}\nError:${error}\nData:${data}`);
  return (
    <div className="App" style={{ height: "1000vh" }}>
      <h1>{data && data.status}</h1>
      <h2>{loading && "Loading"}</h2>
      <button onClick={refetch}>Refetch</button>
    </div>
  );
};

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement); // ReactDOM.createRoot 사용
root.render(<App />); // render는 root.render로 변경
```



useAxios.js
```js
import axios from "axios"; //
import defaultAxios from "axios";
import { useEffect, useState } from "react";

const useAxios = (opts, axiosInstance = defaultAxios) => {
  const [state, setState] = useState({
    loading: true,
    error: null,
    data: null,
  });
  const [trigger, setTrigger] = useState(0);
  if (!opts.url) {
    return;
  }
  const refetch = () => { //새로고침
    setState({
      ...state,
      loading: true,
    });
    setTrigger(Date.now()); //newdata는 이상한 숫자를 만들어냄,,?
  };
  useEffect(() => {
    axiosInstance(opts)
      .then((data) => {
        setState({
          ...state,
          loading: false,
          data,
        });
      })
      .catch((error) => (1)[setState({ ...state, loading: flase, error })]);
  }, [trigger]); // axios를 부르지만 update로 부터 보호해줌
  return { ...state, refetch };
}; //instance를 가지고 있지 않는다면 defaultAxios를 기본값으로 세팅함.

export default useAxios;
```
***
#### 📑정리
`useAxios({
    url: "https://yts.mx/api/v2/list_movies.json",
  });`
\- opts를 오브젝트에 전달 함

`const useAxios = (opts, axiosInstance = defaultAxios)`
요청 설정 객체(필수) , 커스텀 Axios인스턴스(선택)


`opts` 
- Axios 요청 옵션 , Api 등롱 가능. ex)url, method, params,data 등

`axiosInstance`
- 기본값으로는 `defaultAxios` 사용하지만, 사용자 정의 인스턴스도 주입이 가능함.

`const [state, setState] = useState({
    loading: true,
    error: null,
    data: null,
  });`
\- 로딩, 에러, 데이터 관한 key와 value의 디폴트를 설정함
 
 
 ` useEffect(() => {
    axiosInstance(opts)
      .then((data) => {
        setState({
          ...state,
          loading: false,
          data,
        });`
        
 \- `fetch`와 같은 기능, 로딩 되면 `(then) setState` 통해 state 값 바뀜.
 \-리패치 (요청 보내는 함수) 위한 함수로, 함수 실행시 `setTrigger`로 인해 계속해서 다른값 반환.
 
 #### ❓fetch
 
 - 브라우저 내장함수로 서버에 HTTP 요청 보낼 수 있음.
 - Axios와 차이점

