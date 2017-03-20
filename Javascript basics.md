# Javascript basics
## Values & Types
보통 Javascript를 Type이 없는 언어라고 하지만, 실제로는 그렇지 않다. 내부적으로 아래와 같은 Type들을 가지고 있으며, 필요에 따라 변수의 타입을 동적으로 변경해 줄 뿐이다. 즉, 할당되는 값의 Type에 따라 변수의 타입을 변경해주는 것이다.
### Types
* string
* number
* boolean
* null and undefined
* object
* symbol (new to ES6)

###### type examples

```
var a;
typeof a; 			// "undefined"

a = 'hello world';
typeof a; 			// "string"

a = 42;
typeof a;				// "number"

a = true;
typeof a;				// "boolean"

a = null;
typeof a;				// "object" -- weird, bug

a = undefined;
typeof a;				// "undefined"

a = { b: "c" };
typeof a;				// "object"
```
#### 주의 사항
* 위의 예제에서 주의할 점은 typeof 연산자가 반환하는 값은 항상 string (e.g. "string")이라는 것이다. 따라서 typeof를 사용하여 type을 비교할때 아래와 같이 코드를 작성해야 한다.
* typeof 연산자는 변수 a의 type을 반환해주는 것이 아니라, a가 현재 가지고 있는 값의 타입을 반환해주는 것이다. 
*  **typeof null**의 반환값이 object인것에 항상 주의해야 한다. 이는 오래된 javascript의 버그로 이를 고치게 되면, 또 다른 어마무시한 버그들이 생산될 것이기때문에 고치지 못하는 이유가 있다.

```
if(typeof a === 'string') { // not typeof a === string // this statement throws string is undefined
...
}
```

### Objects
속성(properties)를 가지는 type으로 각각의 속성(property)은 어떤 type의 값이라도 가질 수 있다. 속성(property)는 location으로도 해석할 수 있다. 그 이유는 아래의 예제를 보면 이해 할 수 있을 것이다. 

###### object examples

```
var obj = {
	a: "hello world",
	b: 42,
	c: true
};

obj.a;		// "hello world"
obj.b;		// 42
obj.c;		// true

obj["a"];	// "hello world"
obj["b"];	// 42
obj["c"];	// true

for( let location in obj ) {
	console.log(location);
}
/*
//output
a
b
c
*/
```
아래와 같은 구조라고 생각하면 쉬울 것이다. 
| ^a: "hello world"  | ^b: 42  | ^c: true | 
|--------|--------|--------|

아래와 같은 코드를 작성할 수 도 있다.

```
var obj = {
	a: "hello world",
	b: 42
};

var b = "a";

obj[b];			// "hello world"
obj["b"];		// 42
```

### Functions
Function은 Object의 하위 type이다. 그리고 typeof연산자를 사용하면 "function"을 반환한다. 

###### function examples

```
function foo() {
	return 42;
}

foo.bar = "hello world";  // object의 하위 타입이기때문에, 해당 코드처럼 속성을 지정할 수 있다.

typeof foo;			// "function"
typeof foo();		// "number"
typeof foo.bar;		// "string"
```

### 형변환
#### 명시적

```
var a = "42";

var b = Number( a );

a;				// "42"
b;				// 42 -- the number!
```

#### 암묵적

```
var a = "42";

var b = a * 1;	// "42" implicitly coerced to 42 here

a;				// "42"
b;				// 42 -- the number!
```


### 참 그리고 거짓인 값들
#### truthy
* "" (empty string)
* 0, -0, NaN (invalid number)
* null, undefined
* false

#### falsy
* "hello"
* 42
* true
* [ ], [ 1, "2", 3 ] (arrays)
* { }, { a: 42 } (objects)
* function foo() { .. } (functions)

### 동치비교
**==**와 **===**의 차이는 
* **==**는 값의 동치만을 비교한다. type이 다른 두 값을 비교할때 두 값이 동일한 타입이 될때까지 암묵적인 형변환을 한다.
* **===**는 값과 타입 모두의 동치를 비교한다. 즉, 타입과 값 모두 동일해야 true를 반환한다. 그리고 암묵적인 형변환을 허용하지 않는다.

###### equality example1
```
var a = "42";
var b = 42;

a == b;			// true
a === b;		// false
```

###### equality example2
```
var a, b; // a and b are "undefined"
a === b // guess true? false?

```

#### 배열의 동치 비교

```
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";

a == c;		// true
b == c;		// true
a == b;		// false  a와 b는 각각 동일한 요소를 가진 배열(object) instance들이다. 즉, 개별적인 배열 객체(object)이기때문에 false가 반환된다. 

```
```a == b;```의 경우  false를 반환하는데, 그 이유는 a와 b는 각각 동일한 요소를 가진 배열(object) instance들이다. 즉, 개별적인 배열 객체(object)이기때문에 false가 반환된다. 

Javascript에서의 정확한 동치 비교 알고리즘을 알고 싶다면 아래의 링크된 문서를 참조하면된다. 

* [The Abstract Equality Comparison Algorithm](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)

