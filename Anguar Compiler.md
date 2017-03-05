# Anguar Compiler

Inputs === Compie ===> Running App
Inputs === Parse ===> === Instantiate ===> Running App

```
@Component({
	templateUrl: 'hello_comp.html'
})

class HelloComp {
	user = {name: 'Ryu'}
}

```

```
<form>
	<div>Hello {{user.name}}</div>
	<input ngModel>
</form>
```

```
@Directive({selector: 'form'})
class NgForm {...}

//look upward to find parent form directive
@Directive({selector: '[ngModel]'})
class NgModel {...}

constructor(form: NgForm){...}
```

###### template parse output (AST: Abstract Syntax Tree)
Own javascript parser 

```
{ 
	name: 'form', 
	children: [
		{
			name: 'div', 
			children: [
				{ text: 'Hello' },
				{ text: '', 
					expr: {
						propPath: ['user', 'name'] },
						line: 2,
						col: 14
					} 
				},
			]
		},
		{
			name: 'input',
			attrs: [['ngModel', '']]
		}
	]

}
```

```
{
	name: 'input',
	attrs: [['ngModel', '']],
	directives: [
		{
			ctor: NgModel, //constructor
			deps: [NgForm], //dependency
		}
	]
}
```

### JIT Compilation
#### With browser
* Compiler runs in a browser during bootstrapping
 * Analyzing the components in the app
 * Generating code in memory
 * When browser refreshing, doing all over again.
* Using browser parser

Server |                                        Browser                                           |
Inputs  ==== parse & generate ===> View source(AST) ==== eval ===> View class ==== new ===> Running App

### AOT Compilation
* Compiling as a build step
* Component factories generated at build time.
* At runtime, creating views without compilation.


Server                                         |                     Browser                       |
Inputs  ==== parse & generate ===> View source(AST) ==== script ===> View class ==== new ===> Running App

### Comparing JIT and AOT
#### Total time to load and bootstrap
![](https://cdn-images-1.medium.com/max/800/1*bFpua6ePeJsYTjJQoXyUqQ.png)

### Template Instantiation


### References
* [The Angular 2 Compiler Tobias Bosch](https://www.youtube.com/watch?v=kW9cJsvcsGo)
* [Angular: Is AOT Worth It?](https://blog.nrwl.io/angular-is-aot-worth-it-8fa02eaf64d4#.j25dh3blv)
* [Ahead-of-Time Compilation in Angular](http://blog.mgechev.com/2016/08/14/ahead-of-time-compilation-angular-offline-precompilation/)
* [Optimizing Angular 2 Apps - Martin Probst](https://www.youtube.com/watch?v=_VnV7R4Ncwc)
* [JIT Compiler](https://ko.wikipedia.org/wiki/JIT_컴파일)
* [JIT compiler overview](http://www.ibm.com/support/knowledgecenter/SSYKE2_8.0.0/com.ibm.java.win.80.doc/diag/understanding/jit_overview.html)
* [Understanding Just-In-Time Compilation and Optimization](http://docs.oracle.com/cd/E15289_01/doc.40/e15058/underst_jit.htm)