# Angular2
## Architecture
![eight main building blocks of an Angular application](https://angular.io/resources/images/devguide/architecture/overview2.png)
(* from: [angular.io](https://angular.io/docs/ts/latest/guide/architecture.html))

### Modules
Angular로 만들어진 App은 여러개의 모듈(Module)로 구성되어 있다. 그리고 기본적으로 제공하는 module들이 있고 이 모듈들을 NgModules라고 한다. 

* Root Module (AppModule): App내에 반드시 존재해야 하는 Module
* Angular Module(root module 포함): @NgClass라는  decorator로 선언된 class.

#### NgModule
* module을 설명하는 속성들을 가진 metaobject를 하나만 가지는 decorator function

##### declarations
View class들을 나타내며, View class들은 아래와 같은 종류가 있다.
* components
* directives
* pips
  
##### exports
다른 모듈에서 사용가능한 component template을 선언.

##### imports
Module내에 선언된 component template가 필요로 하는 exported된 class들이다.

##### providers
Service들의 생성자로, 전역에 걸쳐 사용되는 service들의 모음이라고 할 수 있다. 

##### bootstrap
Application의 main view이며, root component이다. 다른 모든 app view들을 host한다.
Root module만 bootstrap 속성으로 지정할 수 있다. 

##### sample code of root module

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/browser';
//NgModule 생성
@NgModule({
	imports: [BrowserModule],
	providers: [Logger],
	declarations: [AppComponent],
	export: [AppComponent],
	bootstrap: [AppComponent]
})

export class AppModule {}
```
```
위의 예제에서 root module에 export 속성을 선언하여 사용했지만, 실제로 root module의 export속성을 사용할 일은 거의 없다. 왜냐면 root module은 전역적이라서 다른 component에서 root module을 import할 필요가 없기 때문이다.
```

#### Application 시작
Root module을 bootstrap module로 지정하여 App을 시작할 수 있다. 개발하는 동안 main.ts파일에 bootstrap을 아래와 같이 작성하는 것이 일반적이다.

```
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';

platformBrowswerDynamic().bootstrapModule(AppModule);
```

### Angular Module VS. JavaScript Module
Angular module은 Angular에서 근간이되는 기능으로, @NgModule로 decorated된 class이다.
Javascript의 module은 Javascript 객체들의 집합이다. 
따라서 Angular의 module과 Javascript의 모듈은 서로 완전히 다르며, Angular module system과 연관이 없다. 

(* ES6에는 Javascript에도 class가 있다. [ES6 Classes](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes))
(* 참고: [JavaScript 객체_모델의_세부사항](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/객체_모델의_세부사항))

### Angular libraries
Angular에 기본 제공되는 Javascript module들을 library라고 생각하면된다. Angular library들은 ```@angular```로 시작된다. 
npm을 통해 설치할 수 있고, import하여 사용한다. 

```
import { Component } from '@angular/core';
```


### Components
Component는 화면상에 표시되는 View를 제어한다. 속성 및 메서드를 제공하여 View와 연동하는 class를 Component내에 함께 선언한다. 

아래의 HeroListComponent의 예제를 보면 HeroListComponent는 heroes라는 속성과 selectHero라는 메서드를 가진다. 

```
export class HeroListComponent implements OnInit {
	heroes: Hero[];
	selectedHero: Hero;
	
	constructor(private service: HeroService){}
	
	ngOnInit(){
		this.heroes = this.service.getHeroes();
	}
	selectHero(hero: Hero) {this.selectedHero = hero;}
}
```

##### constructor 및 OnInit 호출 시점
OnInit 및 constructor의 호출 시점은 Component의 selector에 선언한 태그명을 실제 사용할때 호출된다. 아래와 같이 HeroList component의 생성자 및 OnInit 메서드를 작성했다면 console output처럼 생성자가 먼저 호출되고, ngOnInit이 나중에 호출된다.

```
constructor() {
    console.log('HeroListComponent constructor is called');
  }

  ngOnInit() {
    console.log('ngOnInit Lifecycle hook');
  }
```

###### console output

```
[Log] HeroListComponent constructor is called (main.bundle.js, line 141)
[Log] ngOnInit Lifecycle hook (main.bundle.js, line 144)
```
![LIFECYCLE HOOKS](https://angular.io/resources/images/devguide/lifecycle-hooks/hooks-in-sequence.png)

### Templates
Template은 Component의 View를 담당한다. HTML로 작성하며 Component의 속성 혹은 메서드를 binding할 수 있다.

```
<h2>Hero List</h2>
<p><i>Pick a hero from the list</i></p>
<ul>
  <li *ngFor="let hero of heroes" (click)="selectHero(hero)">
    {{hero.name}}
  </li>
</ul>
<hero-detail *ngIf="selectedHero" [hero]="selectedHero"></hero-detail>
```
위의 예제 코드에 나온 ```*ngFor, {{hero.name}}, (click), [hero], <hero-detail>```들은 Angular에서 제공하는 template syntax이다. 
```<hero-detail>```은 직접 작성한 custom element이다. 각 Component간 계층 구조는 아래와 같다. 

![Hierarchy of HeroList Component](https://angular.io/resources/images/devguide/architecture/component-tree.png)

### Metadata
Metadata는 단순한 class를 Angular가 해당 class를 사용할 수 있는 정보들을 담고 있다. Typescript에서는 **decorator**를 통해 metadata를 첨부할 수 있다. 아래은 **@Component** decorator의 예제코드이다. 

```
@Component({
moduleId: module.id,
selector: 'hero-list',
templateUrl: './heor-list.component.html',
providers: [HeroService]
})
```

*  moduleId: templateUrl과 같은 module 상대 주소를 사용하기위한  기본 주소를 설정한다. 
* selector: CSS selector이며, 커스텀 태그명을 나타낸다. 실제 해당 selector를 태그로 사용하게 되면 Angular는 해당 component의 인스턴스를 생성하여 태그에 집어넣는다. 
* templateUrl: HTML template의 상대 주소. (* template을 사용해 직접 html을 넣어도 된다.)
* providers: 해당 component가 의존하는 service를 주입시켜주는 provider이다. 






# Setup angular2
## Preperation
###install angular-cli

```
npm install -g @angular/cli
```

### create sample project

```
ng new hero-application
```

### Run create app

```
ng serve
```

### Add component
```
ng g component HeroListComponent
```
(* Angular cli does not support delete component)
