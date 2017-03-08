# Angular2 Testing
## Why do test?
* Regression test: 기존의 코드들이 정상 작동하는 것을 보장하기 위한 것이다. 이 것을 잘 준수할 경우, 코드의 Refactoring을 지속적으로 수행할 수 있다. 
* 특정 기능의 작동을 확인하고 정상적인 조건이 아닌 경우에 대한 결과를 확인하여 조치할 수 있다.
* 설계 혹은 구현상의 실수(결함)을 확인하여, 조치하도록 한다. 테스트 코드를 작성하기 힘들거나, 수행하기 어려운 경우, 대부분 설계상의 결함이 있는 경우가 많다.
* 결함을 늦게 발견할 수록 고치기 힘들어 진다. 즉, 비용이 많이 발생하게 되므로 결함은 되도록 빨리 발견하는 것이 좋으며, 테스트는 이를 가능하게 해준다.

## With What?
* [Jasmine](http://jasmine.github.io/2.4/introduction.html): 기본적인 테스트 수행에 필요한 대부분의 것들을 제공하며, HTML test runner를 제공하여 browser에서도 테스트를 수행할 수 있다.
* Angular testing utilities: 테스트를 수행하기 위한 환경을 생성하여 준다. Angular가 실행될 수 있는 환경하에서 특정 기능이 수행될 수 있는 조건과 제어를 할 수 있도록 해준다.
* Karma: [Karma test runner](https://karma-runner.github.io/1.0/index.html)는 application 개발 동안 단위 테스트를 작성할 수 있도록 도와준다. 단위 테스트를 작성하는 것은 프로젝트의 개발과 지속적인 통합(CI)과정에서 핵심적인 부분입니다.
* Protractor: 사용자 경험과도 같은 Ent-to-end(e2e)테스트를 작성하고 실행하기 위해 사용됩니다. 실제 application이 실행되도록 프로세스가 실행되며, 또 다른 프로세스는 protractor 테스트들을 실행합니다. protractor 테스트들을 실행은 browser상에서 실행되는 application의 반응을 검증합니다. 

## How?
### Setup Project
1. 프로젝트 생성: ``` ng new angular2-testing ```
2. 프로젝트 환경 확인
	* ```1st.spec.ts```라는 파일 생성 후, 아래의 코드 작성

		```
		describe('1st tests', ()=> {
			it('true is true', () => expect(true).toBe(true));
		});
		```
	* 테스트 실행: 아래의 명령어를 실행하게 되면, application code들과 테스트 code들을  compile하고 karma를 실행하여 테스트를 수행한다. 이때 실행한 프로세스는 계속 실행 중이며, 파일의 변경을 감지하여 변경이 될때마다 compile 및 테스트를 수행한다.
	
		```ng test```
		(*위의 명령어는 package.json의 scripts에 선언되어 있다.)
		
3. 테스트 실패 결과 확인
	```1st.spec.ts``` 파일의 내용을 아래와 같이 변경 한다. 그럼 watcher가 변경을 감지하고 compile 및 테스트를 재 실행한다. **(1 FAILED)**와 같은 결과가 표시되는 것을 확인할 수 있다. 
	
	```
	describe('1st tests', ()=> {
		it('true is true', () => expect(true).toBe(true));
	});
	```
4. 디버깅 테스트
	* karma window를 선택한다.
	* **DEBUG**버튼을 선택하게 되면, 새로운 탭이 열린다. 이때 윈도우는 **ctrl + shift + I**, 맥은 **command + option + I**를 눌러 개발자 툴을 실행한다.
	* **Sourcex**탭으로 이동하여, **control 혹은 command + P**를 눌러 1st.spec.ts파일을 검색하여 찾는다. 
	* ```it```함수를 호출하는 곳에 break point를 걸고, 새로고침 하면 break point가 걸리는 것을 확인할 수 있다. 

### Component 테스트
#### 대상 Component 작성
##### src/app/banner-inline.component.ts

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-banner',
  template: '<h1>{{title}}</h1>'
})
export class BannerComponent {
  title = 'Test Tour of Heroes';
}
```	
##### src/app/banner-inline.component.spec.ts

```
/* tslint:disable:no-unused-variable */
/*
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { DebugElement } from '@angular/core';

import { BannerInlineComponent } from './banner-inline.component';

describe('BannerInlineComponent', () => {
  let component: BannerInlineComponent;
  let fixture: ComponentFixture<BannerInlineComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [ BannerInlineComponent ]
    })
    .compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(BannerInlineComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

*/

import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { DebugElement } from '@angular/core';

import { BannerInlineComponent } from './banner-inline.component';

describe('BannerComponent (inline template)', () => {
  let comp: BannerInlineComponent;
  let fixture: ComponentFixture<BannerInlineComponent>;
  let de: DebugElement;
  let el: HTMLElement;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [BannerInlineComponent], // declare the test component
    });

    fixture = TestBed.createComponent(BannerInlineComponent);

    comp = fixture.componentInstance; // BannerComponent test instance

    //query for the title <h1> by CSS element selector
    de = fixture.debugElement.query(By.css('h1'));
    el = de.nativeElement;
  });

  it('should display original title', () => {
    fixture.detectChanges();
    expect(el.textContent).toContain(comp.title);
  });

  it('should display a different test title', () => {
  comp.title = 'Test Title';
  fixture.detectChanges();
  expect(el.textContent).toContain('Test Title');
});
});

```

#### 테스트 내용
* BannerInlineComponent 객체가 가진 title속성이 정상적으로 <h1>태그 안에 들어갔는지를 검증한다. 

#### 테스트 코드 설명
##### TestBed
* Angular testing moudle(*@NgModule*)을 생성한다. 
* **configureTestingModule** 함수 호출을 통해 test 대상 class를 위한 testing module 환경을 설정한다.
* 실제로는 실 환경의 application module에서 해당 component를 떼어내고, test를 위한 module로 다시 붙여 넣게 된다. 
* **configureTestingModule**에 인자로 주는 객체는 *@NgModule*로 meta-object라고 생각하면 된다.
* 해당 meta-object는 다른 보통의 Angular module이 가질 수 있는 대부분의 속성을 가질 수 있다. 
* meta-object는 테스트 대상이 되는 BannerInlineComponent를 선언한다.
* 테스트를 수행하기 위해 다른 module이 필요한 경우 imports를 eta-object에 추가하면 된다.

#### createComponent (* synchronous)
* **createComponent** 호출 이후에는 **TestBed**를 재 설정할 수 없다. 하면 안된다.
* **TestBed.createComponent**를 호출하여, 테스트 대상이되는 component를 생성한다.
* **TestBed.createComponent**가 반환하는 객체는 ComponentFixture 타입의 객체로 일종의 테스트 대상 component의 wrapper로 생각하면된다. fixture객체는 component instance 및 debugElement를 가지고 있다.
* ComponentFixture객체가 가지고 있는 debugElement를 통해 component의 DOM element를 가지고 테스트를 수행할 수 있다.

#### ComponentFixture
Debugging과 testing을 지원하기 위한 객체이며, 아래의 API문서를 통해 상세 내용을 확인할 수 있다. 
* [ComponentFixture](https://angular.io/docs/ts/latest/api/core/testing/index/ComponentFixture-class.html)

##### detectChanges
해당 메서드를 호출하여 data bidning과 DOM element에 data를 전달하기 위해 호출한다. 해당 메서드를 호출하지 않으면, 테스트 케이스 내에서 data binding은 이루어지지 않는다.
```TestBed.createComponent```의 호출은 data binding을 실행하지 않으며 change detection을 수행하지 않는다. 

아래의 테스트 케이스를 추가하여 해당 내용을 확인할 수 있다.

```
it('no title in the DOM until manually call `detectChanges`', () => {
  expect(el.textContent).toEqual('');
});
```

##### automatic detectChanges
명시적으로 detectChanges를 호출하여 검증하는 방법을 선호하지 않는다면, 아래의 코드를 추가하여 data binding이 테스트를 수행하는 동안 자동으로 이루어 지도록 할 수 있다.

```
import { ComponentFixtureAutoDetect } from '@angular/core/testing';
...

//아래의 구문은  beforeEach에 넣는다.
providers: [{
	provide: ComponentFixtureAutoDetect, useValue: true
}]
```

위의 구문 추가후에 추가적으로 수정을 해줘야 하는 테스트 케이스가 있다. 
마지막으로 추가한 테스트 케이스이며 이를 수정하여 테스트 케이스가 성공하도록 수정해보자.

그리고 **ComponentFixtureAutoDetect**를 사용한다고 해도, component객체의 초기 속성값이 binding된 후에, 변경된 속성값을 다시 binding하기 위해선 detectChanges를 반드시 호출해줘야 한다. 그렇지 않으면 변경되기 전의 값이 계속 DOM element에 binding되어 있다. 아래의 테스트 케이스를 참고하면 된다.

```
it('should still see original title after comp.title change', () => {
  const oldTitle = comp.title;
  comp.title = 'Test Title';
  // Displayed title is old because Angular didn't hear the change :(
  expect(el.textContent).toContain(oldTitle);
});
```

#### query method
* [DebugElement class API](https://angular.io/docs/ts/latest/api/core/index/DebugElement-class.html)
* predicate 함수를 인자로 받는다. (* predicate는 주어진 인자를 비교하여 boolean값을 반환하는 함수)

#### By
대상 DOM element를 query하기 위한 predicate함수를 반환하기 API 를 제공한다. 
* [By class API](https://angular.io/docs/ts/latest/api/platform-browser/index/By-class.html)

### 외부 template을 사용하는 Component 테스트
**TestBed.createComponent**는 동기적(synchronous)으로 실행되는 반면,  **Angular template compiler**는 외부 template(templateUrl로 선언된 template)을 읽는 작업을 비동기적(asynchronous)으로 수행한다. 따라서 이전의 테스트는 외부 템플릿을 사용하는 component를 테스트하기에는 부적절하다. 

따라서 **Angular template compiler**에게 파일을 읽을 시간을 주어야 하기 때문에 beforeEach에서 실행되는 부분은 **async**함수로 한번더 감싸야 한다. 또한 **TestBed.configureTestingModule** 호출 후에, 명시적으로 component를 compile하라고 알려줘야 하기 때문에 아래와 같이 beforeEach 코드를 변경한다.

```
import { async } from '@angular/core/testing';
```

```
beforeEach(async(
    () => {
      TestBed.configureTestingModule({
        declarations: [BannerInlineComponent], // declare the test component
        providers: [{
          provide: ComponentFixtureAutoDetect, useValue: true
        }]
      });

      TestBed.compileComponents(); // compile template and css;
      fixture = TestBed.createComponent(BannerInlineComponent);
      
      comp = fixture.componentInstance; // BannerComponent test instance

      //query for the title <h1> by CSS element selector
      de = fixture.debugElement.query(By.css('h1'));
      el = de.nativeElement;
    }
  ));

```

#### compileComponents
**TestBed.compileComponents**메서드는 비동기적으로 테스트 module내의 component들을 compile한다.
**createComponent**메서드와 마찮가지로 **TestBed.compileComponents**메서드 호출 후에 TestBed를 추가로 설정할 수 없다. 또한 **TestBed.compileComponents**메서드를 **createComponent** 호출 전, 마지막 단계로 호출하는 것이 좋다.

그리고 **TestBed.compileComponents**는 [promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 반환하기때문에 아래와 같이 처리할 수 도 있다.

```
TestBed.compileComponents()
  .then(() => {
    fixture = TestBed.createComponent(BannerInlineComponent);

    comp = fixture.componentInstance; // BannerComponent test instance

    //query for the title <h1> by CSS element selector
    de = fixture.debugElement.query(By.css('h1'));
    el = de.nativeElement;
  });
```
	 
### 의존관계를 가지는 Component 테스트
	



## Isolated Unit test 

## Angular Testing
