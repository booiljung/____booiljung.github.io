# AngularDart

### 라이브러리 다운로드하여 빌드 준비

VSCode에서

```
Pub: GetPackages
```

그리고

```
Pub: Upgrade Packages
```

### 기본구성

#### 프로젝트 폴더 및 파일 구성

```
workspace
	lib
		컴포넌트파일들.{dart, html, css}
	test
	web
		main.dart
		index.html
		styles.css
	analysis_options.yaml
	pubspec.yaml
```

#### 기본 컴포넌트.dart

라이브러리 임포트

```dart
import 'package:angular/angular.dart';
```

컴포넌트

```dart
@Component(
	selector: 'my-selector', // 템플릿 내의 HTML 요소 이름
    template: '''<h1>Hello{{컴포넌트 프로퍼티}}</h1>''', // 직접 템플릿 기술 templateUrl과 배타적.
    templateUrl: '파일이름.html', // 템플릿 파일 경로 기술 template와 배타적.
    styleUrls: ['CSS 파일 경로', ...],
    exports: [식별자, ...], // 템플릿에서 참조할 const, static 식별자 이름 (클래스 등)
)
class 컴포넌트 클래스 이름 {
	// 프로퍼티 및 메소드
}
```

##### 템플릿에서 static, const, 식별자 참조

기본적으로 템플릿은 컴포넌트의 멤버만 참조할 수 있습니다. 최상이 식별자나 static, const 로 지정된 식별자를 참조하려면 `@Component`에서 `exports`를 해야 합니다.

```dart
@Component(
    exports: [식별자, ...], // 템플릿에서 참조할 최상위, const, static 식별자 이름 (클래스 등)
)
class 컴포넌트 클래스 이름 {
}
```

#### 컴포넌트 셀렉터

컴포넌트.dart

```dart
@Component(
	selector: '템플릿 내의 셀렉터',
)
```

템플릿에:

```html
<셀렉터/>
```

#### 템플릿 식별자

| 구분  | 설명 | 예제                   |
| :---: | :--: | ---------------------- |
|  ID   |  #   | `<input #box></input>` |
| class |  .   |                        |
| name  |      |                        |

#### 템플릿의 표현식에서 허용되지 않는 것

- 생성 `new` 또는 `const`
- 증분 `++` 또는 `--`
- 대입 연산 `+=` 또는 `-=` 등
- 비트열 연산 `|` 또는 `&` 등

#### 템플릿과 컴포넌트 데이터 연결

##### 표현식 표시

템플릿에 `{{표현식}}`으로 컴포넌트 변수 표시 합니다. 예를 들어:

```html
<h1>{{title}}</h1>
```

연산이 있는 표현식도 가능합니다.

```html
<p>The sum of 1 + 1 is {{1 + 1}}</p>
```

##### 표현식으료 요소 제어

`[요소타겟]="표현식"` 으로 제어할 수 있습니다. 예를 들어 다음은 요소를 가리거나 표시합니다.

###### 요소를 가리거나 표시

`[hidden]="true/false"`를 지정하여 요소를 가리거나 표시합니다. 예를 들어:

```html
<p [hidden]="canBeChanged">Change</p>
```

##### 표현식으로 bind-target 제어

`bind-target="표현식"`으로 바인드 타겟을 제어할 수 있습니다. 예를들어:

```html
<p bind-target="표현식"/>
```

##### 이벤트를 소스에 영향

`(target)="문"` 또는 `on-target="문"`으로 템플릿에서 발생한 이벤트를 소스에 전달 할 수 있습니다. 예를들어:

```html
<button (click)="onClick(인수, ...)">Click Me!</button>
<button on-click="onClock(인수, ...)">Click Me!</button>
```

##### 소스와 뷰의 양방향 연결

```html
[(target)] = "표현식"
```

#### 요소 값 직접 표시

템플릿에 템플리 요소의 값을 직접 표시 할 수 있습니다. 예를 들어:

```html
<input #box (keyup)="0">
<p>{{box.value}}</p>
```

`input` 요소의 이벤트는 `"0"`으로 지정하지 않았고, 아래 `{{box.value}}`로 직접 값을 표시하고 있습니다.



#### 템플릿에서 이벤트 핸들러 지정

템플리 요소에서 이벤트 처리는 `(이벤트이름) = "메소드이름(인수, ...)"`로 지정합니다. 예를 들어:

```dart
<button (click)="buttonClickHandler(인수, ...)"> Click me!</button>
```





#### 버튼 클릭시 처리

템플릿에 `<button (click)="메소드()">텍스트</button>`를 삽입합니다. 예를 들어:

```html
<button (click)="buttonClickMethod(인수, ...)">Click me!</button>
```

컴포넌트에 이벤트 핸들러를 추가 합니다.

```dart
class 컴포넌트 {
	void buttonClickMethod(파라미터, ...) {
	}
}
```

####  $event 개체에서 유저 입력

입력이 발생할때마다 이벤트를 처리하려면, 템플릿 파일에 `<input (keyup)="onKeyup(\$event)">`하여 이벤트를 전달합니다. 예를들어:

```html
<input (keyup)="onKey(\$event)">
```

컴포넌트에 `메소드(dynamic event)`핸들러를 지정합니다. 예를들어:

```dart
class 컴포넌트 {
	void onKeyup(dynamic event) {
        event.target.value 가 input 요소의 값으로 처리.
        ...
	}
}
```

##### 이벤트 파라미터 타입

이벤트 파라미터 타입을 지정할 수도 있습니다:

```dart
class 컴포넌트 {
	void onKeyup(KeyboardEvent event) {
        event.target은 InputElement 타입 요소입니다.
    }
}
```

#### Input과 컴포넌트 변수의 연동

컴포넌트.dart에 `angular_forms`을 임포트하고,  `@Component`에 `formDirectives` 지시자 추가. 예를 들어:

```dart
import 'package:angular_forms/angular_forms.dart';
@Component(
	directives: [formDirectives],
)
```

컴포넌트.html에 `[(ngModel)]="변수명"`으로 지정. 예를 들어:

```html
<input [(ngModel)]="hero.name" placeholder="name"/>
```

### 반복자 루프

컴포넌트.html의 요소에 `*ngFor = "let 변수명 of 반복자"`를 추가. 예를 들어:

```html
<li *ngFor="let hero of heroes">
	<span ... >{{hero.id}}</span>
</li>
```

이때 컴포넌트.html에 반복자가 있어야 함.

```dart
@Component (...)
class 컴포넌트 {
	List<타입> heroes; // 초기화 되어야 함.
}
```

### 클릭시 처리

컴포넌트.html의 요소에 `(click) = 메소드(인자)` 추가하여 핸들러 지정. 예를들어:

```html
<li *ngFor="let hero of heroes"
	(click)="onSelect(hero)">
	<span class="badge">{{hero.id}}</span>
</li>
```

컴포넌트.dart에 핸들러 기술

```dart
@Component (...)
class 컴포넌트 {
	void onSelect(Hero hero) {...}
}
```

### IF 조건 충족시 HTML 요소 삽입

`ngIf="조건식"` 를 요소에 삽입. 예를들어

```html
<div *ngIf="selected != null">
	...
</div>
```

### 조건 충족시 HTML 속성 삽입

#### HTML `class` 속성 삽입

`[class.클래스이름] = "조건식` 속성에 삽입. 예를들어

```html
<li *ngFor="let hero of heroes"
    [class.selected] = "hero === selected"
    (click)="onSelect(hero)">
    <span class="badge">{{hero.id}}</span>
</li>
```

### 다른 컴포넌트 삽입

컴포넌트는 다른 컴포넌트를 자신의 일부로 삽입할 수 있습니다.

`@Component`의 `directives`에 컴포넌트 클래스 추가. 예를 들어:

```dart
@Component(
	directives: [coreDirective, 다른 컴포넌트 클래스 이름],
}
class ...
```

그리고 다른 컴포넌트가 의 셀렉터가 `my-hero`라면:

```dart
@Component {
	selector: '다른 컴포넌트의 셀렉터',
}
```

그리고 소유하는 컴포넌트의 템플릿에 다른 컴포넌트의 HTML 요소 추가: 

```html
<자기 컴포넌트 셀렉터/>
<다른 컴포넌트 셀렉터/>
```

### 서비스에서 데이터 가져오기

서비스 클래스를 만듭니다. 서비스는 컴포넌트가 아니므로 

```dart
class 서비스이름 {
}
```

로 합니다.

보통 서비스는 원격에서 데이터를 가져오므로 `async`이고 퓨처로 받습니다.

```dart
class 서비스이름 {
  	Future<반환타입> 메소드명() async {
   		return ...;
  	}
}
```

원격에서 가져오는 것을 테스트하기 위해 `Future.delayed()`를 사용할 수도 있습니다.

```dart
class 서비스이름 {
  	Future<반환타입> 메소드명() async {
    	return Future.delayed(Duration(seconds: 초), n초 후 호출될 메소드);
  	}
}
```

서비스를 참조하는 컴포넌트는 `@Component`의 `providers:`에 `ClassProvider(클래스이름)`을 추가 합니다. 예를 들어:

```dart
@Component(
	providers: [ClassProvider(Service)], // 서비스 클래스들의 배열입니다.
)
```

그리고, 컴포넌트 클래스는 `OnInit` 인터페이스를 상속하여 구현합니다.

```dart
class 컴포넌트이름 implements OnInit {
	void ngOnInit() { ... }
}
```

앵귤러는 적절한 때에 `ngOnInit()`를 호출해 줍니다.

### 라우터

[angulardart.dev: 공식 튜토리얼](https://angulardart.dev/tutorial/toh-pt5), [github.com: 공식 소스코드](https://github.com/angular-examples/toh-5/)를 참조하여 작성 하었습니다. 

라우터는 URL 주소창을 변경하며 페이지(뷰)를 내비게이션 할 수 있게 합니다.

`pubspec.yaml`에 `angular_router` 라이브러리를 추가 합니다.

```yaml
dependencies:
	angular_router: any
```

#### 라우터 주입

`web/main.dart`에 라우터를 주입합니다. 라우터 라이브러리를 임포트하고,

```dart
import 'package:angular_router/angular_router.dart';
```

`web/main.dart`에 `app_component.template.dart`를 임포트 합니다.

```dart
import 'package:라이브러리이름/app_component.template.dart' as ng;
```

`web/main.dart`에 자신의 템플릿을 임포트 합니다.

```dart
import 'main.template.dart' as self;
```

`web/main.dart`의 `main()` 함수에 `@GenerateInjector`를 추가 합니다.

```dart
@GenerateInjector(
  routerProvidersHash, // routerProviders를 사용할 수 있게 됩니다.
)
final InjectorFactory injector = self.injector$Injector;

void main() {
  runApp(ng.AppComponentNgFactory, createInjector: injector);
}
```

# (실제 프러덕션에서는 하지 말것)

`web/index.html`에 `<base href="...">` 요소를 추가 합니다. (실제 프러덕션에서는 하지 말것)

```html
<head>
  <script>
    // 경고: 실제 프러덕션에서는 <base href> 를 설정하지 마세요.
    // 자세한 것은 https://webdev.dartlang.org/angular/guide/router 를 보세요.
    (function () {
      var m = document.location.pathname.match(/^(\/[-\w]+)+\/web($|\/)/);
      document.write('<base href="' + (m ? m[0] : '/') + '" />');
    }());
  </script>
```

#### 라우트 경로

라우트 경로는 `RoutePath(path: 문자열이름)`로 만듭니다. 예를 들어:

```dart
static final RoutePath변수명 = RoutePath(path: 문자열이름);
```

이 `RoutePath변수명` 은 `RouteDefinition`에 사용됩니다.

#### 라우트 정의

라우트 정의는 `RouteDefinition`로 만듭니다.  예를들어:

```dart
static final RouteDefinition변수명 = RouteDefinition(
    routePath: RoutePath변수명,
    component: 경로에 사용될 컴포넌트 팩토리 클래스 이름,
);
```

`routePath: `인자는 `RoutePath` 의 객체입니다.

#### 라우트 아웃렛 (Outlet)

`app_component.html`에서

```html
<nav>
    <a [routerLink]="RoutePath객체.toUrl()"
       [routerLinkActive]="'active'">표시 텍스트</a>
    ...
  </nav>
<router-outlet [routes]="라우트 할 RouteDefintion 반복자 변수명"></router-outlet>
```

#### 페이지로 이동

`Router`객체의 `navigate()` 메소드에 `RoutePath` 객체의 `toUrl()`을 전달하여 페이지를 이동하며, 이때 `parameters: ...`로 파라미터를 전달 할 수 있습니다. 예를들어:

```dart
class 컴포넌트 {
    Router _router;
	Future<NavigationResult> onClick(파라미터) {
        return _router.navigate(
            RoutePath객체.toUrl(
                paramters: ...
            )
        );
    }
}
```

#### 컴포넌트의 라우트 활성화 이벤트 핸들링

`OnActivate` 인터페이스를 상속하고 `onActivate()`를 구현합니다. 예를 들어:

```dart
class 컴포넌트 implements OnActivate {
	@override
    void onActivate(_, RouterState current) async {
        current.parameters ...
        ...
    }
}
```

여기서 `RouterState` 는 `Map<String, String> parameters`를 프로퍼티로 가지고 있습니다.

#### 뒤로 가기

활성화된 객체는 생성자에서 서비스 객체와 로케이션 객체 `Location`를  받습니다. 예를 들어:

```dart
class 컴포넌트 {
	서비스클래스 _service;
	Loation _location;
	생성자(this._service, this._location);
}
```

`Location`객체의 `back()` 메소드를 호출하면 뒤로 갑니다. 예를 들어:

```
class 컴포넌트 {
	void goBack() {
		_location.back();
	}
}
```

이 `goBack()` 메소드는 템플릿에서 호출 할 수 있습니다.

```html
<button (click)="goBack()"></button>
```

#### 파이프

HTML 템플릿에서 파이프를 사용하려면 `@Component`에서 `pipe`를 지정합니다. 예를 들어:

````dart
@Component(
	pipes: [commonPipes],
)
````

### HTTP 서비스

[angulardart.dev: 공식 튜토리얼](https://angulardart.dev/tutorial/toh-pt6), [github.com: 공식 소스코드](https://github.com/angular-examples/toh-6/)를 참조하여 작성 하었습니다. 

HTTP 서비스를 제공하려면 다음 라이브러리를 `pubspec.yaml`에 추가 합니다.

```yaml
dependencies:
	http: any
	stream_transform: any
```

그리고 `main.dart`에서 `ClassProverdier`를 의존성 주입을 합니다. 예를 들어:

```dart
import 'package:http/browser_client.dart';

@GenerateInjector([
    routerProviderHash, // 프러덕션에서 routerProviders 를 사용 할 수 있음.
    ClassProvider(BrowserClient),
])
```

#### HTTP 시뮬레이션

서버 없이 클라이언트를 시뮬레이션 할 수 있습니다.

```dart
import 'package:http/http.dart';

@GenerateInjector([
    routerProvidersHash, // 프러덕션에서 routerProviders 를 사용 할 수 있음.
	ClassProvider(Client, useClass: 시뮬레이션을 제공하는 클래스),
])
```

`MockClient`를 상속하고 구현하여, 시뮬레이션 제공 클래스를 작성합니다. 예를들어:

```dart
import 'dart:async';
import 'dart:convert';
import 'dart:math';

import 'package:http/http.dart';
import 'package:http/testing.dart';

class InMemoryDataService extends MockClient {
   	InMemoryDataService() : super(핸들러메소드);
	static Future<Response> 핸들러메소드(Request request) async {
        var data;
        switch (request.method) {
          	case 'GET':
                data = ...
            	break;
          	case 'POST':
                data = ...
          		break;
          	case 'PUT':
                data = ...
          		break;
          	case 'DELETE':
                data = ...
            	break;
	        default:
            	throw 'Unimplemented HTTP method ${request.method}';
        }
        return Response(
            json.encode({'data': data}), 200,
            headers: {'content-type': 'application/json'}
        );
	}
}
```

이제 웹 페이지를 구성할 컴포넌트들은 JSON으로부터 디코딩되거나 인코딩 될 것입니다. 예를 들어:

```dart
class 컴포넌트 {
	factory 생성자.fromJson(...) => ...;
	Map toJson() => { 키:밸류, ... };
}
```



 