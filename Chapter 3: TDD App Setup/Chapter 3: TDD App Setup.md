# _Chapter 3: TDD App Setup_
    
### _Section II: Beginning TDD_
  - Chapter 3: TDD App Setup
      > 이 장의 목표는 몇 가지 테스트로 테스트 대상을 만들어 Xcode 테스트가 작동하는 방식에 대한 느낌을 제공하는 것입니다. TDD의 핵심 개념을 배우면서 이를 수행합니다.
  - Chapter 4: Test Expressions
      > 이 장에서는 XCTAssert 함수를 사용하는 방법에 대해 설명합니다. 이들은 테스트 인프라의 주요 행위자입니다. 다음으로 호스트 애플리케이션을 사용하여 뷰 컨트롤러 유닛 테스트를 구동하는 방법을 배웁니다. 그런 다음 최소 테스트 양을 확인하기 위해 코드 커버리지를 수집합니다. 마지막으로 테스트 디버거를 사용하여 테스트 오류를 찾아 수정합니다.
  - Chapter 5: Test Expectations
      > 이전 장에서는 사용자가 시작 버튼으로 수행 할 수있는 작업을 기반으로 앱의 상태를 구축했습니다. 앱의 주요 부분은 사용자가 이동하고 단계를 기록 할 때 변경 사항에 대응하는 데 의존합니다. 이러한 작업은 기본 흐름 외부에 이벤트를 만듭니다. XCTestExpectation 은 직접 흐름 외부에서 발생하는 일을 테스트하기 위한 도구입니다.
  - Chapter 6: Dependency Injection & Mocks
      > 이 장에서는 서비스를 호출 할 필요없이 Mocks을 사용하여 시스템 또는 외부 서비스에 의존하는 코드를 테스트하는 방법을 배웁니다. 이러한 기술을 사용하면 저장 실패와 같은 오류 조건을 테스트하고 CoreMotion과 같은 SDK에서 로직을 분리 할 수 있습니다.
# 
### _Getting started_
            
이 장의 목표는 몇 가지 테스트로 테스트 대상을 생성하여 Xcode 테스트가 작동하는 방식에 대한 느낌을 제공하는 것입니다.  
- 테스트 대상을 만들고 단위 테스트를 실행합니다.    
- 데이터와 상태를 확인하는 단위 테스트를 작성합니다. 
      
### _About the FitNess app_
    
이 책 섹션에서는 재미있는 단계 추적 앱인 FitNess를 구축합니다. FitNess는 "Loch Ness"운동을 기반으로 한 최고의 피트니스 코칭 앱입니다. 사용자는 피트니스 괴물 인 Nessie를 능가해야합니다. 앱의 목표는 사용자가 Nessie를 능가하도록하여 사용자의 움직임을 유도하는 것입니다. 실패하면 아바타를 먹습니다.
    
### _Your first test_
    
테스트 대상 없이는 테스트를 실행할 수 없습니다. 테스트 대상은 테스트 코드가 포함 된 바이너리이며 테스트 단계에서 실행됩니다. 앱과 함께 빌드되지만 앱 번들에 포함되어 있지 않습니다.   
    
### _Adding a test target_
    
먼저 테스트 대상을 만듭니다. 프로젝트 탐색기에서 FitNess 프로젝트를 선택하여 프로젝트 편집기를 표시합니다. 새 타겟을 추가하려면 타겟 목록 하단의 + 버튼을 클릭합니다. 테스트 섹션 까지 아래로 스크롤하고 iOS 단위 테스트 번들을 선택 합니다. 다음을 클릭 합니다. 그런 다음 마침을 클릭 합니다.     
    
![image](https://user-images.githubusercontent.com/60660894/91513199-ba2ca480-e91e-11ea-9e9c-3db275e3a675.png)
![image](https://user-images.githubusercontent.com/60660894/91513208-c153b280-e91e-11ea-87d2-407a9bfea7c9.png)
![image](https://user-images.githubusercontent.com/60660894/91513223-cb75b100-e91e-11ea-9632-b7a6098a85c6.png)
    
짜잔! 이제 FitNessTests 대상이 있습니다. Xcode는 또한 FitNessTest.swift 파일과 타겟에 대한 Info.plist 와 함께 프로젝트 탐색기에 FitNessTests 그룹을 추가했습니다.   
    
### _Figuring out what to test_
    
제일 먼저 해야 할 일은 가장 작은 기능 단위를 결정해야 합니다. 즉, 무엇부터 테스트해서 시작할 것인지를 정하는 것이죠. 그런의미에서 자동으로 생성된 FitNessTests.swift 파일은 현재 아무것도 하지 않습니다. 따라서 삭제합니다.         
        
무엇을 제일 먼저 해야 할까요? 여기서는 앱을 시작하기 위한 최소기능으로 시작버튼을 사용해서 앱을 시작 또는 진행중 상태로 만드는 것입니다. 그리고 그것을 위한 요구사항이 있죠.         
        
- 앱 시작을 위한 최소기능 : 시작 버튼을 사용해서 앱을 시작 또는 진행중 상태로 만들려고 한다. 
    * 앱은 .notStarted 상태에서 시작되어야 한다. 
    * 사용자가 시작 버튼을 탭하면 앱이 .inProgress 상태로 이동해야 한다.       
        
### _Adding a test class_

![image](https://user-images.githubusercontent.com/60660894/91593184-aa52a600-e99a-11ea-8190-5ab8ac393abb.png)

무엇을 테스트할지를 정했으니 이제 시작해봐야겠죠? 파일을 만들어줍니다. FitNessTests 그룹에 AppModelTests라는 이름으로 iOS-Unit Test Case Class를 생성해줍니다. 만약에 Xcode가 Objective-C 브리징 헤더 생성을 요구하면 Don't Create(이 프로젝트에는 Objective-C 가 없습니다.)를 클릭합니다.
        
### _Red-Green-Refactor_
        
이전 장을 복습하는 의미에서 다시 한번 정리하겠습니다. 
        
1. 실패한 테스트를 작성하십시오 (Red).       
2. 테스트를 통과 할 수 있도록 최소한의 코드를 작성합니다 (Green).      
3. 필요에 따라 테스트 및 코드를 정리합니다 (Refactor).       
4. 모든 논리 케이스를 다룰 때까지 프로세스를 반복하십시오 (Refeat).     
        
### _Writing a red test_
        
```swift 
func testAppModel_whenInitialized_isInNotStartedState() { 
    let sut = AppModel()
    let initialState = sut.appState 
    XCTAssertEqual(initialState, AppState.notStarted)
}
```

1. testAppModel_whenInitialized_isInNotStartedState()
> 메소드를 생성합니다. 1. 앱모델을 테스트할것이고 2.처음init되었을때 3.상태값이 isInNotStarted 으로 예상되는 테스트입니다.                
2. appState         
> 앱 모델의 인스턴스를 생성하고, 해당 인스턴스의 변수 appState가 이미 enum 타입으로 만들어진 AppState의 notStarted와 같은지를 테스트합니다.      
3. compilation error        
> compilation error 가 발생합니다.        
        
### _Making the test green_
        
```swift 
import Foundation

public class AppModel {

  static let instance = AppModel()
  
  public var appState: AppState = .notStarted

  public init() {}
}

```
        
```swift 
import XCTest
import FitNess

class AppModelTests: XCTestCase {
  
  func testAppModel_whenInitialized_isInNotStartedState() {
    
    let sut = AppModel()
    let initialState = sut.appState
    
    XCTAssertEqual(initialState, AppState.notStarted)
    
  }
}
```
        
1. 문제는 테스트코드가 AppModel이 뭔지 알지 못한다는 것입니다. 해결은 import FitNess를 해주면 됩니다.      
Xcode에서 애플리케이션 타겟은 프레임 워크가 아니지만 모듈이며 테스트 타겟은 마치 프레임 워크 인 것처럼 가져 오는 기능이 있습니다. 프레임 워크와 마찬가지로 각 Swift 파일에서 가져와야하므로 컴파일러는 앱에 포함된 내용을 인식합니다.     
        
2. 두번째는 AppModel에 appState라는 변수가 없다는 거죠. 해당 변수를 추가합니다. "public var appState: AppState = .notStarted"

3. 테스트가 통과합니다. 간단한 테스트이므로 리팩토링은 필요없지요. 

### _Writing a more interesting test_
        
```swift 
import Foundation

public class AppModel {

  static let instance = AppModel()
  
  public var appState: AppState = .notStarted

  public init() {}
  
  public func start(){
    appState = .inProgress
  }
  
}

```
        
```swift 
import XCTest
import FitNess

class AppModelTests: XCTestCase {
  
  func testAppModel_whenInitialized_isInNotStartedState() {
    
    let sut = AppModel()
    let initialState = sut.appState
    
    XCTAssertEqual(initialState, AppState.notStarted)
    
  }
  
  func testAppModel_whenStarted_isInInProgressState() { 
  
    // 1 given app in not started
    let sut = AppModel()
    
    // 2 when started
    sut.start()
    
    // 3 then it is in inProgress
    let observedState = sut.appState
    XCTAssertEqual(observedState, AppState.inProgress)
    
  }
}
```

1. testAppModel_whenStarted_isInInProgressState()
> 두 번째 테스트를 만듭니다. 시작 버튼이 눌렸을 때, 상태값이 inProgress가 되는거죠. 
2. start()
> start 메소드가 없으므로 빈 start() 메소드를 먼저 만들어줍니다. 
3. XCTAssertEqual() 
> 이전 테스트와 같이 appState값이 inProgress로 바뀌었는지 비교해줍니다. 
4. Red
> 테스트가 실패합니다. start()에서 아무 처리도 해주지 않았으니 앱의 상태는 아직 notStarted겠죠. 
5. Green 
> start()에서 appState = .inProgress를 지정해줍니다. 테스트가 통과됩니다.     
        
### _Test nomenclature_
        
1. testAppModel_whenStarted_isInInProgressState()을 예로 들어 테스트 명명법을 설명합니다.
> 시작은 항상 test로 시작한다.       
> test후에 오는 AppModel은 테스트할 대상을 말합니다.       
> 구분은 _ 로 해주며 whenStarted은 말그대로 언제 테스트할지를 말합니다.        
> isInInProgressState은 3의 whenStarted 상태가 발생한 후 테스트할 대상인 AppModel의 상태가 무엇이어야 하는지를 뜻합니다.        
2. let sut = AppModel()
> 테스트 중인 객체를 명시적으로 sut라는 인스턴스로 지정합니다. "system under test" 즉, 테스트 중인 시스템이라는 의미입니다. 
3. sut.start()
> 테스트 할 동작입니다. 이 경우엔 AppModel의 start 메소드가 호출됩니다.
4. let observedState = sut.appState 
> 애플리케이션 코드를 실행하는 동안 관찰 한 값을 보유하는 속성을 정의합니다.
5. XCTAssertEqual(observedSate, AppState.inProgress)
> 마지막 부분은 sut가 시작되었을 때 일어난 일에 대한 주장입니다. 현재 관찰하고 있는 앱의 상태가 inProgress와 같은지 비교합니다. 
        

### _Structure of XCTestCase subclass_
        
XCTest는 XUnit에서 파생된 테스트 프레임 워크 제품군입니다. XUnit은 SUnit에서 왔고, SUnit은 프로그래밍 언어 Smalltalk에 대한 단위 테스트 프레임 워크입니다. "X"는 프로그래밍 언어의 약자로 Java에서는 JUnit이고 Objective-C에서는 OCUnit이며 Swift는 "X"로 표현합니다.
        
XUnit에서 테스트는 이름이 테스트 케이스 클래스의 일부인 test로 시작하는 메소드입니다. Test Case는 Test Suite(Test Case를 실행환경에 따라 구분해 놓은 Test Case의 집합)로 그룹화됩니다. Test Runner는 Test Suite에서 Test Case를 찾고 실행하고 결과를 수집하고 표시하는 방법을 알고있는 프로그램입니다. 스키마의 테스트 단계를 실행할 때 실행되는 것은 Xcode의 Test Runner입니다.
        
각 테스트 케이스 클래스에는 각 테스트 메서드가 실행되기 전후에 전역 및 클래스 상태를 설정하는 데 사용되는 setUp() 및 tearDown() 메서드가 있습니다. 다른 XUnit 구현과 달리 XCTest에는 전체 테스트 클래스 또는 테스트 대상에 대해 한 번만 실행되는 lifecycle메서드가 없습니다.
        
이러한 방법은 미묘하지만 매우 중요한 몇 가지 문제가 있기 때문에 중요합니다.     
- XCTestCase 하위 클래스 lifecycle는 테스트 실행 외부에서 관리되며 모든 클래스 수준 상태는 테스트 메서드간에 유지됩니다.       
- 테스트 클래스 및 테스트 메서드가 실행되는 순서는 명시적으로 정의되지 않으며 신뢰할 수 없습니다.       
따라서 setUp() 및 tearDown() 을 사용하여 정리하고 각 테스트 전에 상태가 알려진 위치에 있는지 확인 하는 것이 중요합니다.       

### _Setting up a test, Tearing down a test_
                
각 테스트 메서드가 실행되기 전후에 전역 및 클래스 상태를 설정하는 데 사용되는 setUp() 및 tearDown() 메서드를 이용해서 중복을 제거해줍니다.     

```swift

import XCTest
import FitNess

class AppModelTests: XCTestCase {
  
  var sut:AppModel!
  
  override func setUp() {
    sut = AppModel()
    super.setUp()
  }
  
  override func tearDown() {
    sut = nil
    super.tearDown()
  }
  
  func testAppModel_whenInitialized_isInNotStartedState() {
    XCTAssertEqual(sut.appState, AppState.notStarted)
  }
  
  func testAppModel_whenStarted_isInInProgressState() {
    sut.start()
    XCTAssertEqual(sut.appState, AppState.inProgress)
  }
  
  
}

```

1. var sut:AppModel!        
> 전역변수 sut를 선언해줍니다. 생성자에 대한 접근권한이 없기 때문에 !로 강제 언래핑합니다.      
2. setUp(), tearDown()      
> setUp(), tearDown()를 업데이트 해줍니다. 순서가 중요한데 테스트가 실행되기 전에 setUp()이 실행되므로 그전에 sut값을 넣어주고, 테스트가 완료된 직후에 tearDown이 실행되므로 메모리 초기화를 위해서 sut에 nil을 할당해줍니다.      
> XCTestCases와 관련된 문제는 모든 테스트가 완료될 때까지 초기화되지 않는다는 것입니다. 즉, 메모리 사용량을 제어하거나 파일 시스템을 정리하거나 발견 된 방식으로 되돌리려면 이처럼 테스트를 실행 한 후 테스트 상태를 정리하는 것이 중요합니다.      
3. testAppModel_whenInitialized_isInNotStartedState(), testAppModel_whenStarted_isInInProgressState()
> 테스트 메소드를 리팩토링 해줍니다. 코드가 이전보다 보기좋아졌습니다. 
        
### _Your next set of tests_
        
우리는 지금 앱모델이 처음 생성됐을 때의 상태가 notStarted인지, 그리고 시작됐을 때의 상태가 inProgress로 바뀌는지에 대한 테스트를 작성했습니다.      
그러나, 아직 사용자가 볼 수 있는 기능은 없습니다. 앱 상태를 변경하고 사용자에게 반영되도록 시작버튼을 연결해야 합니다.            
또한, 우리가 지금 하고 있는 것은 TDD지요? 테스트를 먼저 작성하는 것을 뜻합니다. 따라서 StepCountControllerTests를 하나 만들어줍니다.       
                
### _Test target organization_
        
테스트 타겟에 대해 잠시 생각해보면, 당연하게도 한 파일에 테스트케이스를 계속 추가하면 찾고 유지하기가 어려워집니다. 따라서 앱 프로덕션 코드가 증가될수록 테스트코드도 그에 맞게 동일한 수준으로 체계적으로 유지해줘야 합니다. 이 책에서는 아래와 같은 구조를 사용합니다.             

```swift

 Test Target
  ⌊ Cases
  ⌊ Group 1
      ⌊ Tests 1
      ⌊ Tests 2
   ⌊ Group 2
      ⌊ Tests
⌊ Mocks
⌊ Helper Classes
⌊ Helper Extensions

```
- Cases : 테스트 케이스 그룹으로 앱 코드와 병렬 구조로 구성됩니다. 이렇게하면 앱 클래스와 해당 테스트 사이를 쉽게 탐색 할 수 있습니다.      
- Mocks : 기능 코드를 나타내는 코드로 구현과 기능을 분리 할 수 있습니다. 예를 들어 네트워크 요청은 일반적으로 Mocks처리됩니다. 나중에 이를 구축 할 것입니다.       
- Helper classes and extensions : 테스트 코드를 더 쉽게 작성하기 위해 작성하지만 기능을 직접 테스트하거나 Mocks하지 않는 추가 코드의 경우.                 
        
이제 테스트의 구조를 새롭게 그룹화 해줍니다. 변경된 테스트 그룹의 구조는 아래와 같습니다.                 
![image](https://user-images.githubusercontent.com/60660894/92674381-27492c80-f358-11ea-8299-e4f83167e7b8.png)
                
### _Using @testable import_
        
```swift

import XCTest
@testable import FitNess

class StepCountControllerTests: XCTestCase {
  
  var sut: StepCountController!
  
  override func setUp() {
    super.setUp()
    sut = StepCountController()
  }
  override func tearDown() {
    sut = nil
    super.tearDown()
  }
  
}

```

1. var sut: StepCountController!
> 이전에 배운 것과 같이 우리는 지금 StepCountController를 테스트 할 것이기 때문에 전역변수를 선언합니다.       
> 그런데 컴파일에러가 나죠? 이유는 StepCountController가 FitNessTests와 다른 모듈에 있기 때문입니다. swift의 기본 접근제어는 internal이죠? 그래서 그런겁니다. 
2. 해결 방법 
> StepCountController클래스를 public으로 지정한다.     
> 이 방법을 사용하면 테스트클래스에서 사용할 수 있지만 뷰컨트롤러를 앱 외부에서 볼 수 있으므로 SOLID원칙에 위배됩니다.      
> @testable 속성을 사용한다.        
> Xcode는 데이터 유형을 일반 용도로 사용하지 않고 테스트 용으로 노출하는 방법을 제공합니다. 이 속성은 테스트에서만 사용할 수 있습니다. import XCTest아래에 @testable import FitNess해줍니다.      
                                
이제, 세팅은 다 했으니 테스트코드를 만들어주면 됩니다. 시작버튼이 눌렸을 때, 테스트해야 할 것은 2가지 입니다.
1. 앱 상태 업데이트        
2. UI 업데이트      


### _Testing a state change_

```swift 

  func testController_whenStartTapped_appIsInProgress(){
    //when
    sut.startStopPause(nil)
    
    //then
    let state = AppModel.instance.appState
    XCTAssertEqual(state, AppState.inProgress)
}

```

1. testController_whenStartTapped_appIsInProgress() 메소드를 생성해줍니다.        
> 컨트롤러의 버튼을 탭했을 때, 앱이 inProgress인지를 검사하는거죠. 실패합니다.      
2. startStopPause()     
> startStopPause()에서 아무런 처리도 해주지 않았기 때문이죠. 메소드 내에서 AppModel.instance.start()를 해줍니다. 통과합니다.      
                
### _Testing UI update_
```swift

  func testController_whenStartTapped_buttonLabelIsPause(){
    //when
    sut.startStopPause(nil)
    
    //then
    let text = sut.startButton.title(for: .normal)
    XCTAssertEqual(text, AppState.inProgress.nextStateButtonLabel)
    
  }
  
```
1. buttonLabelIsPause
> 메소드 이름도 유사합니다. 다만 바뀐 것은 기대치가 버튼의 레이블값이 Pause로 바뀌었느냐를 묻는것이죠.
2. XCTAssertEqual(text, AppState.inProgress.nextStateButtonLabel)
> 버튼의 타이틀 값을 가져와서 비교해줍니다. 실패합니다. 
3. @IBAction func startStopPause 에서 코드를 추가해줍니다. 
> 특이한 점은 nextStateButtonLabel 입니다. extension으로 문자열 하드코딩이아니라 앱의 값을 이용하기 때문에 문자열이 변경되거나 지역화되어도 테스트는 통과됩니다.

```swift 

  @IBAction func startStopPause(_ sender: Any?) {
    AppModel.instance.start()
    
    let title = AppModel.instance.appState.nextStateButtonLabel
    startButton.setTitle(title, for: .normal)
  }
  
```

```swift

extension AppState {
  var nextStateButtonLabel: String {
    switch self {
    case .notStarted:
      return "Start"
    case .inProgress:
      return "Pause"
    case .paused:
      return "Resume"
    case .caught:
      return "Try Again"
    case .completed:
      return "Start Over"
    }
  }
}

```
        
### _Testing initial conditions_
        
테스트가 끝났나요? 아쉽게도 그건 아닙니다. 아직 개선해야할 점이 있죠. 잘보면 지금 만들어놓은 두 테스트는 문제가 있어요.       
두 테스트는 상태에 대한 특정 초기 조건에 의존합니다. 예를 들어 testController_whenStartTapped_buttonLabelIsPause에서 원하는 것은 .notStarted 에서 .inProgress 로의 전환을 테스트 하는 것 입니다. 그러나 뷰 컨트롤러가 .inProgress 에서 이미 시작된 경우에도 테스트를 통과 할 수 있다는 문제가 있는거죠.        

```swift

  // MARK: - Initial State
  
  func testController_whenCreated_buttonLabelIsStart() {
    //given
    sut.viewDidLoad()
    
    let text = sut.startButton.title(for: .normal)
    XCTAssertEqual(text, AppState.notStarted.nextStateButtonLabel)
    
  }
  
```

```swift

    override func viewDidLoad() {
    super.viewDidLoad()
    
    let title = AppState.notStarted.nextStateButtonLabel
    startButton.setTitle(title, for: .normal)
  }
  
```

1. testController_whenCreated_buttonLabelIsStart()
> tearDown()과 testController_whenStartTapped_appIsInProgress()사이에 테스트를 하나 더 만들어줍니다. 이름 그대로 처음 생성될때 버튼의 레이블값이 start가 맞냐를 물어보는 것이죠. 처음에 //given 조건을 주지 않으면 테스트는 당연히 실패합니다.        
> StepCountController의 viewDidLoad에 코드를 추가합니다. 초기값을 잡아주는 것이죠. 그리고 //given 조건을 줍니다. 이제 통과합니다.        
2. MARK: - Initial State, MARK: - In Progress       
> 또한 테스트 케이스를 섹션으로 나누는 데 도움이되도록 파일에 일부 MARK를 추가 합니다. 클래스가 더 복잡 해짐에 따라 테스트 파일이 상당히 커질 것이므로 잘 정리 된 상태로 유지하는 것이 중요합니다.     
3. 버튼의 레이블이 바뀌는지 확인     
> sut 가 xib 에서 실제로 로드되어 뷰 계층 구조에 배치 되지 않으므로 viewDidLoad()에 대한 호출이 필요하므로 뷰 라이프 사이클 메서드가 호출되지 않습니다. 테스트를 위해 적절하게로드 된 뷰 컨트롤러를 얻는 방법은 4 장, "테스트 표현식"에서 볼 수 있습니다.        
             
### _Refactoring_
                
지금 StepCountController.swift를 보면 버튼 텍스트를 설정하는 코드가 엄청나게 중복됩니다. 이걸 해결해봅시다.        

```swift

class StepCountController: UIViewController {

  @IBOutlet weak var stepCountLabel: UILabel!
  @IBOutlet var startButton: UIButton!
  @IBOutlet weak var chaseView: ChaseView!

  init() {
    // this is a cheat to simplify chapter 3, a proper way of getting an instance will be handled in chapter 4
    super.init(nibName: nil, bundle: nil)
    startButton = UIButton()
  }

  required init?(coder aDecoder: NSCoder) {
    super.init(coder: aDecoder)
  }

  override func viewDidLoad() {
    super.viewDidLoad()
  
    updateButton()
  }

  @IBAction func startStopPause(_ sender: Any?) {
    AppModel.instance.start()
    
    updateButton()
  }
  
  private func updateButton() {
    let title = AppModel.instance.appState.nextStateButtonLabel
    startButton.setTitle(title, for: .normal)
  }
}

```

1. private func updateButton()
> updateButton() 메소드를 만들어주었습니다. 이 도우미 메서드는 버튼이 앱 상태의 변경을 반영해야 할 때마다 파일의 여러 위치에서 사용됩니다. 이것은 클래스의 내부 구현 세부 사항이므로 private일 수 있습니다. 동작 방법은 내부적으로 유지 되며 여전히 테스트 할 수 있습니다.        
2. startStopPause(), viewDidLoad()
> 코드를 바꿔줍니다. startStopPause()같은 경우는 start하면서 앱상태값이 바뀌고, viewDidLoad()같은 경우는 AppModel이 생성되면서 초기값이 notStart이기 때문에 테스트가 통과할 수 있습니다. 
        
### _Challenge_
       
바꿔줘야 할 작업이 있습니다.        
1. AppModel은 실제로 internal이어야 할 때 공개 됩니다. 접근 제어를 업데이트하고 AppModelTests에서 import @testable를 사용합니다.
        
```swift 

import Foundation

class AppModel {

  static let instance = AppModel()

  private(set) var appState: AppState = .notStarted

  func start() {
    appState = .inProgress
  }
}

import XCTest
@testable import FitNess

class AppModelTests: XCTestCase {

  var sut: AppModel!

  override func setUp() {
    super.setUp()
    sut = AppModel()
  }

  override func tearDown() {
    sut = nil
    super.tearDown()
  }

  func testAppModel_whenInitialized_isInNotStartedState() {
    let initialState = sut.appState
    XCTAssertEqual(initialState, AppState.notStarted)
  }

  func testAppModel_whenStarted_isInInProgressState() {
    // when started
    sut.start()

    // then it is in inProgress
    let newState = sut.appState
    XCTAssertEqual(newState, AppState.inProgress)
  }
}

```

2. StepCountControllerTests.swift 에는 startStopPause (_ :) 호출에 중복성을 제거하세요. 

```swift

// MARK: - When

  fileprivate func whenStartStopPauseCalled() {
    sut.startStopPause(nil)
  }
  
  // MARK: - In Progress

  func testController_whenStartTapped_appIsInProgress() {
    whenStartStopPauseCalled()

    // then
    let state = AppModel.instance.appState
    XCTAssertEqual(state, AppState.inProgress)
  }

  func testController_whenStartTapped_buttonLabelIsPause() {
    whenStartStopPauseCalled()

    // then
    let text = sut.startButton.title(for: .normal)
    XCTAssertEqual(text, AppState.inProgress.nextStateButtonLabel)
  }
  
```
        
### _Key points_
        
- TDD는 앱 로직을 작성하기전에 테스트를 작성하는 것입니다.
- 논리 문을 사용하여 테스트해야 할 사항을 결정하십시오.
- 각 테스트는 첫 실행시 실패해야합니다. 컴파일하지 않으면 실패로 간주됩니다.
- 가독성과 성능을 위해 코드를 리팩토링하는 데 테스트를 사용합니다.
- 좋은 명명 규칙을 사용하면 문제를 쉽게 탐색하고 찾을 수 있습니다.
        
### _Where to go from here?_
        
테스트 주도 개발은 기본적으로 매우 간단합니다. 단위 테스트를 통과하려면 앱 코드 만 작성하세요. 이 책의 나머지 부분에서는 red-green-refactor 모델을 계속해서 따르게 될 것입니다. 더 흥미로운 유형의 테스트를 탐색하고 분명히 단위 테스트가 불가능한 것을 테스트하는 방법을 배우게됩니다.        
Xcode가 테스트 및 테스트 대상과 함께 작동하는 방법에 대한 자세한 내용은 개발자 문서를 참조하십시오. iOS 테스트에 대한 요약 된 개요를 보려면이 무료 자습서를 사용해보십시오. 다음 장에서는 XCTAssert 함수, 뷰 컨트롤러 테스트, 코드 커버리지 및 단위 테스트 디버깅 에 대해 자세히 알아봅니다.
# 
# 
# 
> 이 글은 아래의 책을 기초로 나름대로의 주관과 생각을 더하여 재가공한 것입니다.   
> 자세한 내용을 알고 싶다면 아래 링크를 참고해주세요. 교재 및 예제 코드는 저작권 문제로 제공되지 않습니다.  
>        
> iOS Test-Driven Development by Tutorials, 2020 Razeware LLC  
> https://store.raywenderlich.com/products/ios-test-driven-development
