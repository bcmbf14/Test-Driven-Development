

# _Chapter 14: Modularizing Dependencies_

### _Section IV: TDD in Legacy Apps_
  - Chapter 11: Legacy Problems(레거시 문제)    
      > "레거시"프로젝트에서 TDD를 시작하는 것은 새 프로젝트에서 TDD를 시작하는 것과 매우 다릅니다. 예를 들어 프로젝트에 단위 테스트가 거의없고 문서가 부족하며 빌드 속도가 느릴 수 있습니다. 이 장에서는 이러한 문제를 해결하기 위한 전략을 소개합니다.    
  - Chapter 12: Dependency Maps(종속성 맵)   
      > 변경하기 전에 먼저 시스템이 작동하는 방식과 서로 관련되는 클래스를 이해해야 합니다. 이 장에서는 이를위한 도구 인 종속성 맵을 제공합니다.    
  - Chapter 13: Breaking Up Dependencies(종속성 해제)    
      > 이 장에서는 이전 장에서 배운 전략과 기술을 사용하여 레거시 앱에 대한 TDDing 변경을 시작합니다. 그러나 이를 가능하게하려면 기존 클래스 종속성을 깨야합니다. 이 장에서는 안전한(r)방식으로 이 작업을 수행하는 방법을 배웁니다.    
  - Chapter 14: Modularizing Dependencies(종속성 모듈화)    
      > 이 장에서는 관심사별로 코드를 분리하는 모듈을 생성하여 종속성 작업을 계속 분리합니다. 이동할 코드를 식별하는 기술과 레이어 간의 명확한 묘사를 유지하기 위해 인터페이스를 구성하는 방법을 배웁니다.    
  - Chapter 15: Adding Features to Existing Classes(기존 클래스에 기능 추가)    
      > 매우 큰 클래스의 종속성을 깨뜨릴 시간이 항상 있거나 불가능할 수도 있습니다. 이 장에서는 테스트되지 않은 코드의 변경을 최소화하면서 기존 클래스에 기능을 추가하는 전략을 배웁니다.   
# 
### _Getting started_
    
프레임 워크, 정적 라이브러리 또는 구조적으로 격리 된 코드 등 앱을 모듈로 분할하는 것은 깔끔한 코딩의 중요한 부분입니다. 동일한 추상화 수준에서 관련 문제가있는 파일을 사용하면 프로젝트에서 코드를 더 쉽게 유지하고 재사용 할 수 있습니다.   
이 장에서는 마지막 장의 작업을 계속하여 MyBiz 를 모듈로 더 분할하여 로그인 기능을 재사용 할 수 있습니다. 논리 단위를 만들기 위해 코드에서 깨끗한 경계를 정의하는 방법을 배웁니다. 테스트를 통해 새 아키텍처가 작동하고 앱이 계속 작동하는지 확인할 수 있습니다.
    
### _Making a place for the code to go_
    
앱을 모듈화하는 방법에는 여러 가지가 있습니다. 이 자습서에서는 가장 일반적이고 쉬운 새로운 동적 프레임 워크를 사용합니다. 새 프레임 워크를 생성합니다. 

![image](https://user-images.githubusercontent.com/60660894/91990525-cd9b9d80-ed6c-11ea-85b5-f0cb7f993e56.png)
![image](https://user-images.githubusercontent.com/60660894/91990574-de4c1380-ed6c-11ea-8ad4-aadfdab3d511.png)
![image](https://user-images.githubusercontent.com/60660894/91991465-e193cf00-ed6d-11ea-8d65-e69a1b7490bc.png)
![image](https://user-images.githubusercontent.com/60660894/91991495-ebb5cd80-ed6d-11ea-84d4-a21a1d4a8958.png)

1. 프로젝트 에디터에서 새 대상을 만들고, 프레임 워크를 선택합니다.
2. 제품 이름을 입력하고, 즉시 테스트를 추가하기위해 단위테스트 포함을 선택합니다. 
3. 새로 생성된 LoginTests Targets으로 선택하고 General에서 Host Application을 None으로 바꿔줍니다. 
4. Build Phases를 선택하고 LoginTests의 Dependencies에서 MyBiz를 삭제합니다. Login이 유일한 종속성이어야 합니다. 
    
### _Moving files_
    
종속성 맵은 LoginViewController의 싸이클에서 자유로으므로 이제 마지막으로 일부 파일을 이동할 수 있습니다.
    
![image](https://user-images.githubusercontent.com/60660894/91911294-bec0d680-eceb-11ea-93dc-f9a69f64181d.png)
    
두 개의 "녹색"파일 LoginViewController.swift 및 Validators.swift를 잡고 MyBiz 대상에서 Login 대상으로 드래그합니다.    
앱을 빌드하고 실행하면 많은 빨간색 오류가 표시됩니다. LoginViewController 는 이제 나쁜 종속성이 없을 수 있지만 종속성이 전혀 없습니다. 이제 이것을 해결해야 합니다.    

일단, 클래스 Skin과 ErrorViewController는 LoginViewController와 MyBiz안의 다른 클래스들과의 종속성을 가집니다.    
따라서 복사 또는 [순환종속성(상호재귀)](https://en.wikipedia.org/wiki/Circular_dependency) 도입을 방지하려면 또 다른 프레임 워크를 만들어야 합니다.     
위와 같은 방법으로 UIHelpers 라는 새 프레임 워크를 만듭니다.
    
다음 파일을 새 대상으로 이동합니다.
    
• UIViewController + Alert.swift
• ErrorViewController.swift
• Skin.swift
• Styler.swift
• Colors.swift
        
### _Breaking up Styler’s dependencies_   
    
Styler 는 AppDelegate 의 Configuration에 의존합니다. 이 도우미 프레임 워크에서 AppDelegate를 참조하기 위해 캡슐화가 중단되므로 Configuration을 설정하는 다른 방법이 필요합니다.
    
UI 스타일링 외에도 비즈니스 로직 및 서버 설정과 같은 항목이 포함되어 있기 때문에 구성 자체도 문제입니다. 앞으로 나아가는 가장 쉬운 방법은 바닥에서 시작하여 위로 올라가는 것입니다.


1. UIHelpers에 UIConfiguration.swift라는 새 파일을 만듭니다. 
2. Configuration.swift 에서 Configuration -> UIConfiguration으로 rename하고 해당 구조체를 UIConfiguration.swift으로 이동합니다.

```swift

struct Configuration: Codable {
  
  struct BusinessRules: Codable {
    let maxPOExpense: Double
  }

  let server: String
  let debug: Bool
  let ui: UIConfiguration
  let rules: BusinessRules
}

```
    
```swift 

struct UIConfiguration: Codable {
  struct Button: Codable {
    let cornerRadius: Double
    let borderWidth: Double
  }
  let button: Button
}

```

3. Styler.swift에서 let configuration...를 var configuration: UIConfiguration?로 바꿔줍니다. 

```swift

var configuration: UIConfiguration?

```
4. 이어서 옵셔널로 발생한 문제를 바인딩 해줍니다.
```swift

button.layer.cornerRadius = CGFloat(configuration?.button.cornerRadius ?? 0) 
button.layer.borderWidth = CGFloat(configuration?.button.borderWidth ?? 0)

```
5. ErrorViewController에서 Logger에 대한 종속성을 일단 주석처리하세요. 
```swift

//      Logger.logFatal("no action defined.")

```

### _Modularizing a storyboard_
    
앱에서 스토리 보드를 통해 ErrorViewController를 만듭니다. 현재는 메인 스토리 보드를 통해 UIViewController + Alert.swift 에서 명시적으로 수행됩니다. 이 스토리 보드는 앱 모듈에 있으므로이 프레임 워크에서 사용할 수 없습니다.   
    
이 문제를 해결하려면 다음 단계에 따라 뷰 컨트롤러를 UIHelpers 프레임워크 의 새 스토리 보드로 이동 합니다.

1. Main.storyboard를 열고 Error View Controller Scene을 선택합니다.
2. 이제 Xcode 메뉴 도구의 에디터 -> 스토리보드 리팩토링을 선택합니다.
![image](https://user-images.githubusercontent.com/60660894/91999369-41db3e80-ed77-11ea-852e-276df0247ce4.png)
3. 이름을 UIHelpers.storyboard로 지정합니다.
4. 그룹을 UIHelpers로 변경합니다.
5. 현재 타겟인 MyBiz의 체크박스를 해제하고 UIHelpers을 체크합니다.
6. 저장을 클릭 합니다.
7. Main.storyboard에서 참조된 ErrorViewController 레퍼런스를 삭제합니다.
![image](https://user-images.githubusercontent.com/60660894/91999527-764efa80-ed77-11ea-8d1d-d3597cd7782f.png)
8. 다음으로 UIViewController + Alert.swift 에서 let alertController = ... 줄을 다음으로 바꿉니다 .
```swift
let thisBundle = Bundle(for: ErrorViewController.self) 
let storyboard = UIStoryboard(name: "UIHelpers",
                              bundle: thisBundle)
let alertController = storyboard.instantiateViewController(withIdentifier: "error")
  as! ErrorViewController
```
이제 동일한 장면이 로드되지만 프레임워크 내에있는 새 스토리보드에서 로드됩니다.

### _Moving tests_

테스트도 이동할 수 있습니다. 

1. UIHelpersTests 에서 UIHelpersTests.swift를 삭제합니다.
2. 다음으로 UIHelpersTests 에서 Cases 그룹을 만들고 ErrorViewControllerTests.swift를 MyBizTests에서 이 그룹으로 이동 합니다. 
3. @testable import 행을 @testable import UIHelpers로 변경합니다. 
4. setUp()도 바꿔줍니다. 
```swift
override func setUp() {
  super.setUp()
  sut = UIStoryboard(name: "UIHelpers",
                     bundle: Bundle(for: ErrorViewController.self))
    .instantiateViewController(withIdentifier: "error") as? ErrorViewController
}
```
5. 이 새로운 setUp은 좀 전에 만든 UIHelpers.storyboard 을 사용합니다. 좌측 메뉴의 테스트 네비게이터에서 UIHelpersTests을 보면 테스트가 활성화 되어있는 것을 볼 수 있습니다. 활성화가 안되어있으면 테스트를 우클릭하고 활성화를 선택하세요. 이제 UIHelpers를 빌드하고 테스트 할 수 있으며, 이 주요 리팩터링이 작동 할 것이라는 확신을 조금 더 느낄 수 있습니다.            
![image](https://user-images.githubusercontent.com/60660894/92001056-2ffa9b00-ed79-11ea-8566-774439a02b5f.png)
      
### _Using the new framework with Login_
    
이제 UIhelpers에게 자체 프레임 워크를 제공 했으므로 로그인 프레임 워크에 이에 대해 알려야 합니다.    
![image](https://user-images.githubusercontent.com/60660894/92007063-6d165b80-ed80-11ea-9b7c-15c58de9f841.png)
1. 프로젝트 에디터에서 Targers을 Login으로 지정하고 General - Frameworks and Libraries에서 UIHelpers.framework를 지정합니다.    
2. LoginViewController.swift에서 맨 위에 import UIHelpers 추가하십시오.    
3. UIHelpers에서 일부 접근 제어 수준을 수정해야합니다. 모든 파일이 동일한 대상에 있을 때는 괜찮았지만 지금은 프레임워크를 따로 분리했으므로 일부를 접근이 가능하도록 기본 internal(해당 모듈 외 접근불가)에서 public(모든 소스파일 접근가능)으로 변경해주어야 합니다.   

UIHelpers에서 아래 목록의 것들을 public으로 바꿔줍니다.    
• Skin.
• All of the static let constants in Skin.
• Styler.
• In Styler: class, shared, configuration and all the style methods.
• InUIViewController+Alert.swift:showAlert.
• UIConfiguration.
• All of the class var in Colors.swift.
• ErrorViewController and its SecondaryAction and viewDidLoad(). 
    
4. SecondaryAction에 이니셜라이저를 추가해줍니다.
    
```swift 
public init(title: String, action: @escaping () -> ()) { 
  self.title = title
  self.action = action 
}
```
    
이제 다른 모듈이 사용할 수 있도록 이러한 유형과 함수가 노출됩니다. 이 프로젝트의 경우는 해당 모듈은 Login과 MyBiz입니다.
    
### _Further isola7ng LoginViewController_
    
아직도 많은 컴파일러 오류가 존재합니다.        
LoginViewController 를 정리하려면 많은 것들을 수정해야합니다. API 클래스가 너무 광범위하고 추가 메서드가 많은 이상한 델리게이트에 의존하고 있기 때문이죠.     
로그인과 관련된 부분만 포함하는 새 프로토콜을 만들어 API 범위를 지정할 수 있습니다.   
    

1. Login 프레임워크 아래에서 LoginAPI.swift 파일을 만들고 그 내용을 다음으로 바꿉니다.
```swift 

public protocol LoginAPI {
  func login(username: String, password: String,
  completion: @escaping (Result<String, Error>) -> ())
}

```
2. viewDidLoad에서 api.delegate = self를 제거합니다.
3. func signIn()에서 api.login을 아래 코드로 대체합니다. 
```swift

api.login(username: username, password: password) { result in
  if case .failure(let error) = result {
    self.loginFailed(error: error)
  }
}
    
```
코드가 훨씬 개선되었습니다. 종속성 맵으로 보면 아래와 같습니다.
![image](https://user-images.githubusercontent.com/60660894/92010902-4f97c080-ed85-11ea-982e-1793913835c2.png)
    
### _Don’t forget the tests_
    






### _Getting started_
### _Getting started_
### _Getting started_
### _Getting started_

    
    
    
    
    
