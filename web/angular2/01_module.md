# Module

세부 구현이 숨겨지고 공개 API를 이용해 다른 코드에서 재사용 가능한 코드

## ES6 Module

변수의 스콥이 모듈로 제한
각각의 파일들이 하나의 모듈이 되고, 그 각각의 모듈 내 변수들은 해당 모듈 범위에서만 유효

## angular module

컴퍼넌트, 파이프, 서비스 등과 같은 앵귤러 어플리케이션의
주요 부분을 기능단위로 그룹핑 하게 해준다.

- 모든 앵귤러 어플리케이션은 하나의 Root Module을 가진다.
- 여러 Feature Module을 가질 수 있다.
- 재사용할 수 있는 기능을 외부에 배포하기 위해 사용되기도 한다.

## Component

- 빌딩 블록
- HTML 요소들의
- 뷰와 로직으로 구성

- app-root
  - title
  - todos
    - todo
    - add-todo

## Angular Template

- HTML 코드로서 템플릿을 표현
- Template 표현식(expression) 과 Template 문장(statement)을 가짐
- 바인딩
  - 바인딩의 대상 : 속성, 이벤트, ngModel, class, style

## 컴퍼넌트 커뮤니케이션

### 부모 => 자식

- @Input() 을 사용
- ES6 setter 사용가능
- @ViewChild() 를 사용

### 자식 => 부모

- @Output() 을 사용
- EventEmitter 를 사용하여 부모에게 이벤트 전달
- 부모 컴퍼넌트는 $event 로 이벤트의 데이터를 전달 받음
- 자식이 부모 컴퍼넌트를 직접 주입받을 수 있음