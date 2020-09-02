

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
1. UIHelpers에 UIConfiguration.swift라는 새 파일을 만듭니다. 
2. Configuration.swift 에서 Configuration -> UIConfiguration으로 rename하고 해당 구조체를 UIConfiguration.swift으로 이동합니다.
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





### _Getting started_
### _Getting started_
### _Getting started_
### _Getting started_
### _Getting started_
    
    
    
    
    
