# Redux
Redux는 객체의 상태를 보관,관리하는 container역활을 한다.
client, server 그리고 native환경 모두 지원하며 테스트 쉽게 할 수 있도록 해준다. 또한 [time traveling debugger](https://github.com/gaearon/redux-devtools)를 제공하여 실시간 적인 debugging을 할 수 있도록 해준다.

## 설치

### 사전 준비사항

#### NodeJs 설치
* home brew: ``` brew install node ```
* 직접 설치: [NodeJs download](https://nodejs.org/en/download/)

#### Redux 설치
안전된 버전을 설치하기 위해 다음의 명령어를 실행한다.
```
npm install --save redux
```

### import(사용준비)
Redux를 대부분 CommonJs module로서 Webpack, Browswerify 혹은 Node환경에서 import해서 사용할 수 있다. 

module bundler등을 사용하지 않을 경우, node_modules/redux/dist/redux.min.js를 참조하여 사용할 수 있다. 

그리고 대부분 다음의 package들을 함께 사용하는 경우가 많다. Redux는 React와 많이 사용되고 있다. 그래서 react-redux를 설치하는 것이고, react로 개발하지 않는다면 설치할 필요는 없을 것으로 보인다.

#### 추가 Package설치
```
npm install --save react-redux
npm install --save-dev redux-devtools
```

## 핵심 사항
application의 상태는 **store**객체내의 **object tree**내에 저장이 된다.그리고 state tree의 변경 사항(change)를 저장하는 방법은 **action**을 통해 이루어져야 한다.

**action**을 통해 변경 사항을 만들기 위해선, **reducer**들을 작성하기만 하면된다.

위의 내용을 한번 더 요약하면 아래와 같다. 
* Redux는 단 하나의 불변 객체인 State Tree로 상태 변경을 관리한다.
* Data의 흐름은 단 방향으로 이루어진다. Data의 흐름은 아래와 같다. 
	* Action --> Reducers --> Store --(subscribe)--> View --(dispatch)--> Action
* 변경 사항을 적용하기 위해선 **reducer**라는 pure function을 사용한다.


### Reducer example
reducer는 아래의 예제에 나오는 것처럼, state, action => state의 signature로 선언되어야 한다. 반환되는 state는 action을 통해 변경된 state를 의미한다. 인자로 선언된 state는 다음과 같은 타입의 변수를 가질 수 있다.
* Primitive (char, number...)
* Array
* Object
* Immutable.js data structure (List, Stack, Map, OrderedMap, Set....)

반드시 기억해야 할 것은 인자로 넘기는 state 객체가 가변 객체이면 안된다는 것이다. 

Redux store가 제공하는 API는 아래와 같다.
* subscribe : state의 변경 사항을 응답으로 받아 처리한다.
* dispatch : action을 통해 변경을 발생하도록 한다.
* getState : 저장되어있는 state를 받아 온다. 


```
import { createStore } from 'redux'

function couter(state = 0, action) {
	switch(action.type) {
	case 'INCREMENT':
		return state + 1
	case 'DECREMENT':
		return state - 1
	default:
		return state;
	}
}

//create redux store
let store = createStore(counter)

store.subscribe(() => console.log(store.getState()))

//dispatch action(simple object) to change(mutate) the internal state
store.dispatch({type: 'INCREMENT'}) // count => 1
store.dispatch({type: 'INCREMENT'}) // count => 2
store.dispatch({type: 'DECREMENT'}) // count => 1
```
위의 예제와 같이 actiond인 단순 객체(plain object)를 dispatch하여 상태를 변경한다. 

Redux는 여러개의 store를 가지지 않고, 오로지 하나만의 store를 가진다. 대신, root reducer를 여러개의 reducer로 쪼개어 사용이 가능하다. 

### Simple Example

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Redux basic example</title>
    <script src="https://unpkg.com/redux@latest/dist/redux.min.js"></script>
  </head>
  <body>
    <div class="">
      <p>
        Clicked: <span id="value">0</span> times
        <button type="button" id="increment">+</button>
        <button type="button" id="decrement">-</button>
        <button type="button" id="incrementIfOdd">increment if odd</button>
        <button type="button" id="incrementAsync">increment async</button>
      </p>
    </div>
    <script>
      function counter(state, action) {
        if(typeof state === 'undefined') {
          return 0;
        }
        switch (action.type) {
          case 'INCREMENT':
            return state + 1;
          case 'DECREMENT':
              return state - 1;
          default:
            return state;
        }
      }

      var store = Redux.createStore(counter);
      var valueE1 = document.getElementById('value')

      function render() {
        valueE1.innerHTML = store.getState().toString();
      }
      render();
      store.subscribe(render);

      document.getElementById('increment').addEventListener(
        'click',
        function() {
          store.dispatch({type: 'INCREMENT'});
        }
      );
      document.getElementById('decrement').addEventListener(
        'click',
        function() {
          store.dispatch({type: 'DECREMENT'});
        }
      );

      document.getElementById('incrementIfOdd').addEventListener(
        'click',
        function() {
          if(store.getState() % 2 !== 0) {
            store.dispatch({type: 'INCREMENT'});
          }
        }
      );

      document.getElementById('incrementAsync').addEventListener(
        'click',
        function(){
          setTimeout(function() {
            store.dispatch({type: 'INCREMENT'});
          }, 1000);
        }
      )
    </script>
  </body>
</html>

```