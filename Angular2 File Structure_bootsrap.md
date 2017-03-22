# Angular2 File Structure
Angular2 프로젝트는 아래 네개의 파일을 기본적으로 가져야 합니다.
* **app/app.component.ts**: root component를 선언하는 파일
* **app/app.module.ts**: bootstrap될 module을 선언하는 파일
* **index.html**: application을 구성하는 component들이 rendering되어 표시되는 파일, main view라고 생각하면 될 것으로 보입니다.
* **app/main.ts**: component와 page를 함께 결함하는 코드들을 작성하는 파일

## Bootstrap

### Bootstrap process
Bootstrap process는 application의 진입점이 되는 main.ts를 로딩합니다. 

**AppModule**은 application의 root module로서 작동이 되는 module이고, AppComponent를 사용하도록 설정되어 집니다. 마지막으로 **app-root** HTML요소를 찾아 해당 요소에 rendering되어 집니다.

Angular는 web만을 고려해서 만들어진 framework이 아닙니다. Angular는 NativeScript, Cordova 혹은  Angular로 작성된 application이 실행될 수 있는 환경하에서 작동할 수 있는 application을 작성할 수 있도록 합니다. 

따라서, Angular의 bootstrap process을 통해 어느 platform을 사용할 지 지정할 수 있습니다. 보통 Angular application이 browser를 기반으로 작동하기때문에 **@angular/platform-browser-dynamic**내에 선언된 bootstrapping process를 사용합니다. 

Angular의 dependency injection system은  bootstrap process를 통해 시작됩니다. 


## Angular Component
Angular component는 반드시 재 사용 가능해야 합니다.
Angular1.x의 directive와 유사합니다.

* selector: component instance를 생성하고 삽입하도록하는 속성, 속성값은 태그로 사용됩니다.
* template: DOM에서 rendering되서 표시될 html로 구성합니다.


