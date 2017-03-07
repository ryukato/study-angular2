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

#### 테스트 코드 설명
##### TestBed
* Angular testing moudle(*@NgModule*)을 생성한다. 
* **configureTestingModule** 함수 호출을 통해 test 대상 class를 위한 testing module 환경을 설정한다.
* 실제로는 실 환경의 application module에서 해당 component를 떼어내고, test를 위한 module로 다시 붙여 넣게 된다. 
* **configureTestingModule**에 인자로 주는 객체는 *@NgModule*로 meta-object라고 생각하면 된다.
* 해당 meta-object는 다른 보통의 Angular module이 가질 수 있는 대부분의 속성을 가질 수 있다. 
* meta-object는 테스트 대상이 되는 BannerInlineComponent를 선언한다.
* 테스트를 수행하기 위해 다른 module이 필요한 경우 imports를 eta-object에 추가하면 된다.

#### createComponent
* **createComponent** 호출 이후에는 **TestBed**를 재 설정할 수 없다. 하면 안된다.
* **TestBed.createComponent**를 호출하여, 테스트 대상이되는 component를 생성한다.
* **TestBed.createComponent**가 반환하는 객체는 ComponentFixture 타입의 객체로 일종의 테스트 대상 component의 wrapper로 생각하면된다. fixture객체는 component instance 및 debugElement를 가지고 있다.
* ComponentFixture객체가 가지고 있는 debugElement를 통해 component의 DOM element를 가지고 테스트를 수행할 수 있다.

#### query method
* [DebugElement class API](https://angular.io/docs/ts/latest/api/core/index/DebugElement-class.html)
* predicate 함수를 인자로 받는다. (* predicate는 주어진 인자를 비교하여 boolean값을 반환하는 함수)

#### By
* [By class API](https://angular.io/docs/ts/latest/api/platform-browser/index/By-class.html)

#### 테스트 내용
* BannerInlineComponent 객체가 가진 title속성이 정상적으로 <h1>태그 안에 들어갔는지를 검증한다. 	 
	



## Isolated Unit test 

## Angular Testing
