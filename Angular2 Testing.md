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
ngOnInit 메서드는 component life cycle상의 init과 같은 이벤트를 처리하는 메서드로 Angular가 component 생성을 완료하였을때 호출되는 메서드 이다. 그런데 twain.service는 Promise를 반환하고 있고, 그 반환된 Promise를 twain.component에서 받아 처리하고 있다. 
즉, twain.component의 생성이 완료된 다음에 Promise를 resolve하여 처리한다는 것이다. 
그래서 .....


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
* BannerInlineComponent 객체가 가진 title속성이 정상적으로 \<h1\>태그 안에 들어갔는지를 검증한다. 

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
일반적으로 Component는 특정 service객체에 대한 의존성을 가지게 된다. 이런 경우 어떻게 테스트를 하는지에 대해 알아보자.
아래의 코드에서 보듯이, ```WelcomeComponent```	는```UserSerivce```에 대한 의존관계가 있고 ```UserSerivce```를 통해 현재 로그인한 사용자에 대한 정보를 가져온다.

아래의 명령어를 통해 WelcomeComponent 및 UserService를 추가한다.

```
ng g component Welcome
ng g service welcome/model/User
```

##### src/app/welcome.component.ts

```
import { Component, OnInit } from '@angular/core';
import { UserService } from './model/user.service'
@Component({
  selector: 'app-welcome',
  template: '<h3 class="welcome"><i>{{welcome}}</i></h3>',
  styleUrls: ['./welcome.component.css']
})
export class WelcomeComponent implements OnInit {
  welcome = '-- not initailized yet --';

  constructor(private userService: UserService) {

  }

  ngOnInit() {
    this.welcome = this.userService.isLoggedIn() ? 'Welcome, ' + this.userService.userName() : 'Please log in.';
  }

}

```
아래는 WelcomeComponent를 테스트하는 전체 코드이다.
아래의 테스트 코드 중 UserService를 injection하는 코드는 아래와 같다.

```
userService = TestBed.get(UserService);
```

혹은

```
userService = fixture.debugElement.injector.get(UserService);
```

위의 방법은 test의 root injector를 통해 injection한다. 단 component가 provider를 override하는 경우에는 작동하지 않는다. [component-override](https://angular.io/docs/ts/latest/guide/testing.html#!#component-override) 참조

##### src/app/welcome.component.spec.ts

```
/* tslint:disable:no-unused-variable */
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { DebugElement } from '@angular/core';

import { WelcomeComponent } from './welcome.component';
import { UserService } from './model/user.service';

describe('WelcomeComponent', () => {
  let component: WelcomeComponent;
  let fixture: ComponentFixture<WelcomeComponent>;
  let de: DebugElement;
  let el: HTMLElement;
  let userServiceStub;
  let userService;
  beforeEach(async(() => {
    userServiceStub = {
      isLoggedIn: function(){
        return true;
      },
      userName: function(){
        return 'Test User';
      }
    }

    TestBed.configureTestingModule({
      declarations: [ WelcomeComponent ],
      // providers: [ UserService ] // No, Do NOT provide real UserService
      providers: [ { provide: UserService, useValue: userServiceStub } ]
    })
    .compileComponents()
    .then(() => {
      fixture = TestBed.createComponent(WelcomeComponent);
      component = fixture.componentInstance; // BannerComponent test instance
      userService = TestBed.get(UserService);

      de = fixture.debugElement.query(By.css('.welcome'));
      el = de.nativeElement;
    });
    ;
  }));

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should welcome the user', () => {
  fixture.detectChanges();
  const content = el.textContent;
  expect(content).toContain('Welcome', '"Welcome ..."');
  expect(content).toContain('Test User', 'expected name');
});

it('should welcome "Test User"', () => {
  userService.userName = function(){
    return 'Bubba';
  } // welcome message hasn't been shown yet
  fixture.detectChanges();
  expect(el.textContent).toContain('Bubba');
});

it('should request login if not logged in', () => {
  userService.isLoggedIn = function(){
    return false;
  }; // welcome message hasn't been shown yet
  fixture.detectChanges();
  const content = el.textContent;
  expect(content).not.toContain('Welcome', 'not welcomed');
  expect(content).toMatch(/log in/i, '"log in"');
});
});

```

### Async service 테스트 하기
대부분의 Service들은 HTTP 요청을 보내고, 응답을 받아 처리하는 등의 비동기(async)한 처리를 많이 하게 된다. 이런 Service에 의존 관계를 가지는 Component가 있을 경우, 해당 Service에 대한 의존 관계를 테스트시에 잘 처리를 해줘야 Component를 격리된(Isolated)상태에서 테스트 할 수 있게 된다. 
아래의 코드를 통해 비동기적인 작업을 처리하는 서비스에 의존 관계가 있는 Component를 테스트하는 방법을 살펴 보자

###### src/app/shared/twain.component.ts
```
import { Component, OnInit } from '@angular/core';
import { TwainService } from './twain.service';

@Component({
  selector: 'twain-quote',
  templateUrl: './twain.component.html',
  styleUrls: ['./twain.component.css']
})
export class TwainComponent implements OnInit {
  intervalId: number;
  quote = '...';

  constructor(private twainService: TwainService) {
    console.log('twain.component constructor is called.')
  }

  ngOnInit() {
    console.log('twain.component onInit!!! before getQuote');
    this.twainService.getQuote()
    .then((quote) => {
      console.log('twain.component resolve quote!!');
      this.quote = quote;
    });
    console.log('twain.component onInit!!! after getQuote');
  }

}
```
###### src/app/shared/twain.component.spec.ts
아래의 코드에서 유심히 봐야 할 것은 spy와 spyOn이다. spy와 spyOn을 통해 twain.component에 주입된 twainService의 특정 메서드가 호출을 감시하고 있다가, 해당 메서드가 호출되면 우리가 원하는 값을 반환하도록 할 수 있다. Java에서의 Mockito를 사용한 경험이 있다면 이와 비슷하다.

```
/* tslint:disable:no-unused-variable */
import { async, ComponentFixture, TestBed, fakeAsync, tick} from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { DebugElement } from '@angular/core';

import { HttpModule } from  '@angular/http';

import { TwainComponent } from './twain.component';
import { TwainService } from './twain.service';

describe('TwainComponent', () => {
  let component: TwainComponent;
  let fixture: ComponentFixture<TwainComponent>;
  let de: DebugElement;
  let el: any;
  let spy: any;
  let twainService: TwainService;
  let testQuote = 'test quote';
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [ HttpModule ],
      declarations: [ TwainComponent ],
      providers: [ TwainService ]
    })
    .compileComponents()
    .then(() => {
      fixture = TestBed.createComponent(TwainComponent);
      component = fixture.componentInstance;
      twainService = fixture.debugElement.injector.get(TwainService);
      spy = spyOn(twainService, 'getQuote')
      .and
      .callFake(() => {
        console.log('spy twain.service getQuote is called!');
        return Promise.resolve(testQuote);
      })

      de = fixture.debugElement.query(By.css('.twain'));
      el = de.nativeElement;
    });
  }));

  // synchrnous test
  it('should create', () => {
    expect(component).toBeTruthy();
  });

  // synchrnous test
  it('should not show quote before OnInit', () => {
    expect(el.textContent).toBe('', 'nothing displayed');
    expect(spy.calls.any()).toBe(false, 'getQuote not yet called');
  });

  // synchrnous test
  it('should still not show quote after component initialized', () => {
    fixture.detectChanges();
    // getQuote service is async => still has not returned with quote
    expect(el.textContent).toBe('...', 'no quote yet');
    expect(spy.calls.any()).toBe(true, 'getQuote called');
  });
});
```
#### synchronous test
``` // synchrnous test ``` 주석으로 표시된 it 메서드들은  모두 동기적으로 twain.component를 테스트하는 테스트 케이스들이다. 이들 테스트 케이스을 가지고는 component에 quote가 표시되는지를 테스트 할 수 없다. 그 이유는 twain.service혹은 twain.service를 spy하는 spy객체로부터 quote가 아직 반환되지 않았기 때문이다. ```npm run test```혹은 ```ng test```를 통해 테스트를 수행하면 아래와 같은 로그가 표시되는 것을 확인할 수 있다. 

표시된 로그를 유심히 살펴 보면 **ngOnInit** 메서드 후에 service로 반환된 promise가 resolve되어 quote를 반환한 것을 확인 할 수 있다.
즉, quote가 정상적으로 표시되는 것을 확인하기 위해선 테스트 케이스는 반드시 비동기(asynchronous)해야 한다.

```
LOG: 'twain.component constructor is called.'
LOG: 'twain.component onInit!!! before getQuote'
LOG: 'spy twain.service getQuote is called!'
LOG: 'twain.component onInit!!! after getQuote'
LOG: 'twain.component resolve quote!!'
```

#### asynchronous test
아래의 두 테스트 케이스 메서드를 twain.component.spec.ts에 추가한다. 그리고 it 함수에 전달되는 두번째 인자가 **async**로 되어 있다.
**async**는 Angular testing utilities에서 제공하는 함수로 해당 테스트 케이스를 **async test zone**에서 실행해주어, 비동기적으로 만들어 준다. 

아래 두개의 테스트케이스들은 동일한 내용을 확인한다. 두개의 테스트 케이스 모두 getQuote가 반환하는 promise가 resolve되는 것을 기다린 후 quote 내용이 표시되는 것을 확인해야한다. 

##### whenStable
첫번째 테스트 케이스는  **whenStable**메서드를 호출하고 있다. **whenStable**는 또 다른 promise를 반환하는데, 반환된 promise는 해당 테스트 케이스내에서 보류(pending)되어 있는 비동기 활동들이 완료(resolve)될때 resolve 된다. **whenStable**가 반환한 promise내에서 quote를 확인하면 된다.

```
// asynchronous test
  it('shoud show quote after getQuote promise (async)', async(() => {
      fixture.detectChanges();

      fixture.whenStable()
      .then(() => { // wait for async getQuote
        fixture.detectChanges(); // update view the quote
        expect(el.textContent).toBe(testQuote);
      });
  }));
```

##### fakeAsync
**fakeAsync**를 사용하면 whenStable과는 달리, fakeAsync test zone에서 실행되는 synchronous한 코드 스타일로 작성할 수 있다.
whenStable을 대체하기 위해선 **tick**함수를 호출하면 된다. **tick**함수는 **fakeAsync**내에서 사용해야 한다. **tick**함수는 보류(pending)되어 있는 비동기 활동들이 끝날때까지 지연시키는 역활을 한다.
코드 흐름을 파악하기 쉬운 장점이 있지만, fakeAsync내에서는 **XHR**을 호출할 수는 없다. 

```
  // asynchronous test
  it('shoud show quote after getQuote promise (fakeAsync)', fakeAsync(() => {
    fixture.detectChanges();
    tick(); // wait for async getQuote
    fixture.detectChanges();
    expect(el.textContent).toBe(testQuote);
  }));
```

#### jasmine done
만약 jasmine에 익숙하다면, 아래의 코드와 같이 jasmine이 제공하는 **done**으로도 비동기 테스트를 할 수 있는 것을 알 수 있을 것이다. 
그리고 ```intervalTimer```을 포함하는 코드를 테스트 하는 경우, ```async```, ```fakeAsync```를 사용할 수 없는데, 이럴때 **done**을 사용하여 처리하면 된다. 

```
it('should show quote after getQuote promise (done)', done => {
	fixture.detectChanges();

	// get the spy promise and wait for it to resolve
	spy.calls.mostRecent().returnValue.then(() => {
  	fixture.detectChanges(); // update view the quote
  	expect(el.textContent).toBe(testQuote);

  	done();
});
```

###### src/app/shared/twain.service.ts
아래의 서비스 코드는 http로 요청을 보내고 응답을 Promise로 변환하는 예제 코드이다. 요청 주소를 보면 알겠지만 예제 용 코드이다. 
```
import { Injectable } from '@angular/core';
import { Http} from  '@angular/http';
import 'rxjs/add/operator/toPromise';
@Injectable()
export class TwainService {

  constructor(private http: Http) { }

  getQuote(): Promise<string> {
    console.log('twain.service getQuote is called');
    return this.http.get('http://test.quote.com/twain')
    .toPromise().then((response) => response.json()[1]);
  }
}

```

###### src/app/shared/twain.service.spec.ts
아래의 코드는 twain.service를 테스트 하는 코드이다. Http를 twain.service에 inject해야 하기 때문에, TestBed module설정 시(configureTestingModule)에서 imports에 HttpModule를 설정하였다. 이렇게 하면 Http에 대한 provider가 설정된다. HttpModule을 imports에 선언하지 않으면 **No Provider for Http**라는 에러를 보게 될 것이다. 
```
/* tslint:disable:no-unused-variable */

import { TestBed, async, inject } from '@angular/core/testing';
import { TwainService } from './twain.service';
import { HttpModule } from  '@angular/http';

describe('TwainService', () => {
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpModule],
      providers: [TwainService]
    });
  });

  it('should ...', inject([TwainService], (service: TwainService) => {
    expect(service).toBeTruthy();
  }));
});
```


## Isolated Unit test 

## Angular Testing
