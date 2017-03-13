# @Inject and @Injectable

## @Inject
Parameter앞에 @Inject decorator를 사용하면 명시적으로 Angular가 해당 parameter는 주입되어야 할 parameter라는 것을 알 수 있게 해줍니다.

```
import { Component, OnInit, Inject } from "@angular/core";
import { GreeterService } from "./service/greeter.service";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html"
})

export class AppComponent implements OnInit {
  public greeting = "";
  constructor(@Inject(GreeterService) private greeterService) {}

  ngOnInit() {
    this.greeting = this.greeterService.getMessage();
  }
}
```
Angualar는 **@Inject(GreeterService)**를 GreeterService 타입의 singleton 객체를 주입하라고 해석하게 됩니다. 단, **@Inject(GreeterService)**를 사용하지 않아도 Angular는 GreeterService 객체를 주입해 줍니다. 

## @Injectable
**@Injectable**을 사용하게 되면, Angular로 하여금 해당 클래스는 Injector에 의해 주입될 수 있다는 것을 알게 해줍니다. 단 **@Injectable**은 반드시 사용해야 하는 것은 아니지만, Angular에게 해당 클래스가 주입될 수 있음을 확실히 알려줄 수 있습니다.


```
import { Injectable } from "@angular/core";

@Injectable()
export class GreeterService {
    private message = "title from Greeter service";

    getMessage() {
      return this.message;
    }
}
```
### 참고사항
Angular의 injector는 보통의 경우, 주입될 대상의 type을 보고 주입할 수 있습니다. 따라서, @Inject, @Injectale decorator를 사용하지 않아도 의존객체의 타입을 보고 주입을 실행합니다. 
단 type정보만 가지고 주입을 할 수 없을 경우에는 명시적으로 @Inject, @Injectable을 사용하는 것이 좋습니다.


## Injection beyond classes
Angular의 injection은 클래스외에 것들을 주입할 수 있도록 되어 있습니다. 이전에도 보아왔던 @NgModule decorator에서 사용된 **providers**를 이용하여 class뿐만 아니라 다른 주입 가능한 값 등을 선언할 수 있습니다.

아래의 예제는 난수를 제공하는 **randomNumberFactory**를 통해 화면에 표시하는 예제입니다.
**"Random"**이라는 이름을 제공자(factory) 및 소비자(component)에서 동일하게 사용해야 합니다.
또한 **"Random"** 선언 시, **useFactory**로 제공자를 선언한것에 유의하시면 됩니다.

```
const randomNumberFactory = () => {return Math.random(); };
```

```
providers: [
	{provide: GreeterService, useClass: GreeterService},
	{provide: "Random", useFactory: randomNumberFactory} // {provide: "RandomNumber", useValue: Math.random()}와 동일
],

```


###### app.module.ts
```
import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
import { AppComponent } from "./app.component";
import { GreeterService } from "./service/greeter.service";
import { VisitRangleComponent } from "./visit-rangle/visit-rangle.component";
import { ConfirmDirective } from "./visit-rangle/confirm.directive";
import { HighLightDirective } from "./visit-rangle/highright.directive";
import { ButtonPressDirective } from "./visit-rangle/button-press.directive";

const randomNumberFactory = () => {return Math.random(); };

@NgModule({
  imports: [BrowserModule],
  providers: [
    {provide: GreeterService, useClass: GreeterService},
    {provide: "Random", useFactory: randomNumberFactory}
  ],
  declarations: [AppComponent, VisitRangleComponent, ConfirmDirective, HighLightDirective, ButtonPressDirective],
  bootstrap: [AppComponent]
})

export class AppModule {}
```

###### app.component.ts
```
import { Component, OnInit, Inject } from "@angular/core";
import { GreeterService } from "./service/greeter.service";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html"
})

export class AppComponent implements OnInit {
  public greeting = "";
  public randomNumber = 0;
  constructor(
    @Inject(GreeterService) private greeterService,
    @Inject("Random") random
  ) {
    this.randomNumber = random;
  }

  ngOnInit() {
    this.greeting = this.greeterService.getMessage();
  }
}
```

###### app.component.html

```
<h1>
  {{greeting}}
</h1>

<p>random number: {{randomNumber}}</p>

```

## Avoiding Injection Collisions: OpaqueToken
개발하는 application의 사이즈가 커질 수록 다양한 service 혹은 다른 객체들을 선언하게 되고, 의존관계들을 맺게 됩니다. 이때 서로 아무런 관련이 없는 객체이지만 서로 같은 Token(예, "Random")을 사용하는 경우가 발생 할 수 있습니다. 이런 경우, 때때로 Angular는 동일한 Token을 사용하는 객체가 존재하기때문에 엉뚱한 객체를 주입할 수 있는 상황이 발생할 수 있고, 이는 전혀 기대하지 않은 결과를 초래할 수 있습니다.

다음의 코드를 통해 위의 내용을 살펴보겠습니다.

###### app/email/email.service.ts
```
export const apiConfig = 'api-config';

@Injectable()
export class EmailService {
  constructor(@Inject(apiConfig) public apiConfig) { }
}
```

###### app/email/email.module.ts

```
@NgModule({
  providers: [ EmailService ],
})
export class EmailModule { }
```

###### app/logger/logger.service.ts
```
export const apiConfig = 'api-config';

@Injectable()
export class LoggerService {
  constructor(@Inject(apiConfig) public apiConfig) { }
}
```

###### app/logger/logger.module.ts

```
@NgModule({
  providers: [ LoggerService ],
})
export class LoggerModule { }
```

위의 예제의 경우, EmailService와 LoggerService는 동일한  Token의 apiConfig를 사용합니다. 두 모듈간 충돌을 피하기 위해 아래와 같이 import 구문에서 각각을 구분할 수 있지만, 전체 application이 실행될 경우에는 동일한 Token을 사용한 module간 충돌을 피할 수는 없습니다.

**providers**에 마지막으로 선언된 loggerApiConfig가 emailApiConfig를 덮어 쓰게 되는 현상이 발생하게 됩니다. 

```
import { apiConfig as emailApiConfig } from './email/index';
import { apiConfig as loggerApiConfig } from './logger/index';

@NgModule({
  ...
  providers: [
    { provide: emailApiConfig, useValue: { apiKey: 'email-key', context: 'registration' } },
    { provide: loggerApiConfig, useValue: { apiKey: 'logger-key' } },
  ],
  ...
})
export class AppModule { }
```

### OpaqueToken
위와 같이 동일한 Token 사용시 발생하는 충돌을 피하기 위해 제공되는 객체가 [OpaqueToken](https://angular.io/docs/ts/latest/api/core/index/OpaqueToken-class.html)입니다. OpaqueToken은 유일하고 불변의 값이며, 의존성 주입에 사용될 수 있는 id값으로 사용될 수 있습니다.

따라서 OpaqueToken을 사용하여 위의 예제를 아래와 같이 변경합니다.

###### app/email/email.service.ts
```
export const apiConfig = new OpaqueToken('api-config');

@Injectable()
export class EmailService {
  constructor(@Inject(apiConfig) public apiConfig: EmailConfig) { }
}
```
app/email/logger.service.ts
```
export const apiConfig = new OpaqueToken('api-config');

@Injectable()
export class LoggerService {
  constructor(@Inject(apiConfig) public apiConfig: LoggerConfig) { }
}
```

[Plunker example](https://plnkr.co/edit/SHfTH9R6JVDwJKnzRFSH?p=preview)


## The Injector Tree
Angular Injector는 일반적으로 singleton객체들을 반환합니다. 이는 기본적으로  application당 하나의 injector가 기본으로 제공되며 해당 injector만을 통해 의존 객체를 주입 받을 경우 singletone객체가 반환됩니다. 

하지만 Angular는 하나의 injector만을 제공하는 것은 아닙니다. 즉, Component별로 injector를 가질 수 있으며, **@Component** meta에 **providers**를 재정의하여 사용하면 됩니다. 이때 상위 Injector의 하위 Injector가 생성이 되어 사용이 됩니다.

반대로 **@Component** meta에 **providers**를 재정의하지 않았다면, 상위 Component 혹은 Module에 선언된 **providers**를 사용하게 됩니다. 즉, 상위 Injector를 사용하게 되는 것입니다. 
특정 Component가 Injector를 가지고 있지 않다면, Injector를 찾을때까지 상위로 올라가면서 Injector를 찾아 갑니다. 

[Plunker example](http://plnkr.co/edit/abeUOuG8AdHDUcvjial8?p=preview)
