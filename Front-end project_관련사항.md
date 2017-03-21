# Front-end project
## project name 
**norman**

## 프로젝트 진행에 필요한 고려사항
### 프로젝트 구조
* 구조 설명 
### 코드 관리
#### 코드 작성 규칙
##### Lint (코드 작성 규칙)
* tslint.json: 코드 작성 규칙 설정을 포함하고 있는 파일이다. 

##### Module, Component
Component는 Module의 구성요소이기때문에 모듈의 하위에 위치 시킨다.
* 생성: Module, Component의 생성은 직접 생성 가능하나, 되도록 @angular/cli를 통해 생성한다. 
	* moduel 생성 명령어: ```ng g module [module명]```
	* component 생성 명령어 ```ng g component [component명]```
* 위치: Module, Component 코드의 위치는 src/app하위에 위치한다. 

##### 설정파일
개발 환경별 환경 변수는 아래의 경로에 위치한 각각의 환경에 맞는 파일에 작성한다.
* root folder: src/environments
* dev: environment.ts
* production: environment.prod.ts

###### examples

```
// src/environments/environment.ts
export const environment = {
  production: false,
  url: 'http://localhost:3000/getResults'
};
```

```
// src/app/test.service.ts
import { environment } from '../environments/environment';

@Injectable()
export class TestService {
    url: environment.url
}
```

```
ng serve --environment=test
or
ng build --environment=test
```

##### 리소스 파일
리소스의 유형(css, data, img)별로 folder를 생성하여 따로 관리한다.
* root folder: **assets**
* css : css file들의 root path
* data: data file들의 root path
* img: img file들의 root path




