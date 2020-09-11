# _Chapter 4: Test Expressions_
    
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
        
이 장에서는 다음에 대해 학습합니다.
- XCTAssert 함수
- UIViewController 테스트
- 코드 커버리지
- 테스트 디버깅
        
### _Assert methods_
         
XCTest에는 몇 가지 다른 assert 함수가 있습니다.       
        
- 같음 : XCTAssertEqual , XCTAssertNotEqual       
- 진실성 : XCTAssertTrue , XCTAssertFalse      
- Null 허용 여부 : XCTAssertNil , XCTAssertNotNil       
- 비교 : XCTAssertLessThan , XCTAssertGreaterThan , XCTAssertLessThanOrEqual , XCTAssertGreaterThanOrEqual        
- 오류 : XCTAssertThrowsError , XCTAssertNoThrow      

궁극적으로 모든 테스트 케이스는 조건부로 요약 될 수 있습니다.     
        
### _App state_
        
이전 장에서는 앱을 시작되지 않은 상태에서 진행중인 상태로 이동하는 기능을 구축했습니다. 이제 전체 앱 수명주기에 대해 생각할 좋은 시간입니다. AppState 열거 형 으로 표시되는 가능한 앱 상태는 다음과 같습니다.      
        
- notStarted : 앱의 초기 상태입니다.     
- inProgress : 앱이 사용자와 Nessie의 활동을 적극적으로 모니터링하고 있습니다.       
- paused : 사용자가 앱을 일시 중지했습니다. Nessie는 잠자기 상태가되고 활동 추적이 중지됩니다.       
- completed : 사용자가 Nessie가 따라 잡기 전에 활동 목표에 도달했습니다.      
- caught : Nessie는 사용자를 따라 잡고 "먹었습니다".      
        
다음 다이어그램은 가능한 상태 전환을 보여줍니다.             
![image](https://user-images.githubusercontent.com/60660894/92687220-1a3b3600-f376-11ea-9a93-944bf847d916.png)
실선은 UI에서의 사용자 작업을 나타내며 점선은 시간 또는 활동 이벤트로 인해 자동으로 발생합니다. 사용자 기반 전환은이 장 프로젝트에서 다루고 자동 전환은 5장 : "Test Expectations"에서 다룹니다.     
        
### _Asserting true and false_
        
상태 전환을 구축하려면 사용자에 대한 정보를 앱에 추가해야합니다. 완료 및 잡은 상태는 사용자 활동, 설정 한 목표와 네시의 활동에 따라 달라집니다. 아키텍처를 깨끗하게 유지하기 위해 앱 상태 정보는 사용자를 추적하는 원시 데이터와 별도로 유지됩니다.        
        
```swift

import XCTest
@testable import FitNess

class DataModelTests: XCTestCase {
  
  var sut:DataModel!
  
  override func setUp() {
    super.setUp()
    sut = DataModel()
  }
  
  override func tearDown() {
    sut = nil
    super.tearDown()
  }
  
  // MARK: - Goal
  func testModel_whenStarted_goalIsNotReached() {
    XCTAssertFalse(sut.goalReached,
        "goalReached should be false when the model is created")
  }
  
  func testModel_whenStepsReachGoal_goalIsReached() {
    // given
    sut.goal = 1000
    // when
    sut.steps = 1000
    // then
    XCTAssertTrue(sut.goalReached)
  }
  
}

```

```swift

class DataModel{
  
  var goalReached: Bool {
    if let goal = goal,
      steps >= goal {
      return true
    }
    return false
  }
  var goal: Int?
  var steps: Int = 0
}

```
 
TDD이므로 테스트코드부터 프로덕션 코드까지 순서대로 진행해보겠습니다.         
1. 테스트 케이스 그룹에 Data Model그룹을 새로 만들고 DataModelTests.swift파일을 생성합니다.      
2. DataModelTests에서 @testable import FitNess을 해서 우리가 테스트할 DataModel을 사용할 수 있게 합니다.      
3. var sut:DataModel!를 선언합니다.       
4. DataModel.swift에 아무것도 없으므로 컴파일 에러가 발생합니다. 레드케이스죠. 최소 코드를 작성해줍니다. class DataModel{} 통과됩니다.        
5. setUp()과 tearDown()을 셋팅해줍니다.         
6. testModel_whenStarted_goalIsNotReached() 테스트를 생성합니다.                 
> 이 테스트는 이름대로 모델이 시작할 때, 목표가 도착하지 않았는지를 테스트합니다.                 
> XCTAssertFalse는 예상값이 false인지를 확인합니다. 선택적으로 오류로그에 문자열을 표시할 수 있는데, 테스트 이름은 일반적으로 실패 이유를 알려주기에 충분히 설명적이지만 어설션이 명확하지 않은 경우 메시지를 추가하는 것이 유용 할 수 있습니다.          
7. 프로덕션코드에 goalReached이 존재하지 않으므로 컴파일 에러가 발생합니다. 레드케이스. 최소코드를 추가해줍니다. var goalReached: Bool { return false }. 테스트가 통과됩니다.       
8. 새로운 테스트를 생성합니다. testModel_whenStepsReachGoal_goalIsReached()     
> 이것은 "단계 수가 목표와 같거나 초과할 때 목표에 도달 함"이라는 논리를 테스트합니다.         
9. goal과 steps가 없으므로 컴파일에러가 발생합니다. 선언해줍니다.      
10. goalReached을 아래와 같이 바꿔줍니다. 통과됩니다. 
```swift

var goalReached: Bool {
  if let goal = goal,
    steps >= goal {
      return true
}
return false
}

```
### _Testing Errors_
                
이 앱은 goal이 있기 때문에 goal이 설정되지 않은 상태에서 앱이 inProgress 상태로 들어가는 것은 오류입니다. 

```swift

class AppModel {

  static let instance = AppModel()

  private(set) var appState: AppState = .notStarted
  
  let dataModel = DataModel()

  func start() throws {
    guard dataModel.goal != nil else {
      throw AppError.goalNotSet
    }
    
    appState = .inProgress
  }
}

```

```swift

  @IBAction func startStopPause(_ sender: Any?) {
    do {
      try AppModel.instance.start()
    }catch{
      showNeedGoalAlert()
    }
    
    updateUI()
  }
  
```
1. AppModel의 start메소드에 오류를 만들기위해 throws를 붙입니다.      
2. StepCountController의 startStopPause에서 do-try-catch를 붙여줍니다. 오류가 발견되면 얼랏창을 띄워줍니다.      
3. AppModelTests에서 testAppModel_whenStarted_isInInProgressState를 수정합니다. do-try-catch를 붙여줍니다.        

```swift

  func testModelWithNoGoal_whenStarted_throwsError() {
    XCTAssertThrowsError(try sut.start())
  }
  
```
4. testModelWithNoGoal_whenStarted_throwsError테스트를 새로 추가합니다. 모델이 목표 설정없이 초기 상태에서 시작된 경우 오류가 발생하는지 확인할 수 있습니다.
5. 오류가 발생하지 않았으니 테스트가 실패합니다. AppModel.swift에서 let dataModel = DataModel()를 추가합니다.       
6. start() 맨 위에 가드문을 추가합니다. 테스트가 통과합니다.         
7. 다음으로 goal설정이 start()가 오류를 발생시키지 않는다는 것을 의미하는지 확인합니다.         
8. AppModelTests.swift에 아래 코드를 추가합니다.       
```swift

  func givenGoalSet() {
    sut.dataModel.goal = 1000 
  }

```
9. 다시 AppModelTests로 돌아와서 아래 테스트를 추가합니다. 이미 로직이 추가되어 있으므로 통과합니다.     
```swift

  func testStart_withGoalSet_doesNotThrow() {
    // given
    givenGoalSet()
    // then
    XCTAssertNoThrow(try sut.start())
  }
  
```
10. 이 코드 변경으로 인해 실패하기 시작한 다른 모든 테스트를 수정할 때입니다. testAppModel_whenStarted_isInInProgressState에서 맨위에 // given givenGoalSet()을 추가해주세요.      
11. StepCountControllerTests로 가서 testController_whenStartTapped_appIsInProgress과 testController_whenStartTapped_buttonLabelIsPause에도 같은 메소드를 추가해줍니다.        
12. 모두 통과합니다.
13. 앱을 빌드하고 실행해보면 Start버튼을 눌렀을 때, 처음과는 다르게 정상적으로 얼랏창이 뜨는 것을 확인할 수 있습니다. 
        
### _View controller testing_
        
이제 모델에 goal이 있고 앱 상태에서 이를 확인할 수 있으므로 다음은 이를 사용자에게 노출하는 것입니다.        
        
### _Functional view controller testing_
        
뷰 컨트롤러를 테스트 할 때 중요한 것은 뷰와 컨트롤을 직접 테스트하지 않는 것입니다. 이것은 UI 자동화 테스트를 사용하여 더 잘 수행됩니다.        
여기서 목표는 뷰 컨트롤러의 로직과 상태를 확인하는 것입니다.      
기능 테스트는 논리 메서드 (업데이트 상태)에서 UI (콜백, 위임 메서드 등)와 상호 작용하는 별도의 메서드를 사용하여 수행됩니다.      
           
> 참고 : 다른 앱 아키텍처에 대한 경험이있는 경우 MVVM 또는 VIPER와 같은 것을 사용하면 이러한 유형의 논리를 더 깔끔하게 테스트 할 수 있습니다. 컨트롤러에서 ViewModel을 분리하면 컨트롤러에서 단위 테스트 가능한 로직이 제거됩니다. 이 섹션의 목적을 위해 기존 Apple MVC 모델을 사용하여 앱을 계속 빌드합니다. 이것은 대부분의 문서에서 다루는 내용이며 iOS 애플리케이션 개발을 시작하는 전통적인 디자인패턴입니다.              
        
```swift

  func testDataModel_whenGoalUpdate_updatesToNewGoal() {
    // when
    sut.updateGoal(newGoal: 50)
    // then
    XCTAssertEqual(AppModel.instance.dataModel.goal, 50)
  }
  
  override func tearDown() {
    sut = nil
    AppModel.instance.dataModel.goal = nil
    super.tearDown()
  }
  
```
1. 이 테스트는 데이터 모델이 골이 업데이트 됐을 때, 잘 업데이트 되는지 확인합니다.       
2. StepCountControllerTests.swift에서 testDataModel_whenGoalUpdate_updatesToNewGoal()를 추가합니다. 실패합니다.      
3. tearDown에서 AppModel.instance.dataModel.goal = nil를 추가합니다.        
4. 실패합니다. updateGoal메소드가 구현되지 않았습니다.        
5. StepCountController의 updateGoal메소드에서 AppModel.instance.dataModel.goal = newGoal를 구현합니다.      
6. 통과합니다.       
        
### _Using the host app_
        
앱에 대한 다음 요구 사항은 센트럴 뷰가 실행 중인 위치에있는 사용자의 아바타를 표시해야한다는 것입니다.      
```swift

  func testChaseView_whenLoaded_isNotStarted() {
    
    let chaseView = sut.chaseView
    XCTAssertEqual(chaseView?.state, AppState.notStarted)
    
  }
  
```
1. StepCountControllerTests.swift에서 testChaseView_whenLoaded_isNotStarted()를 만듭니다.      
2. testChaseView가 로드될 때의 상태가 notStarted인지 확인합니다. 실패합니다.         
3. 왜 실패할까요? 오류메시지를 보면 chaseView?.state가 nil이라고 나옵니다.        
> 왜 nil일까요? 그것은 테스트 앱 코드가 실행될 때 이미 chaseView가 로드되었기 때문입니다.      
> 이게 무슨 말이냐면, 여기서는 setUp()메소드에서 sut = StepCountController() 라는 식으로 직접 초기화가 됩니다. 원래 정상적인 흐름이라면       
> StepCountController가 생성되고 스토리 보드에 의해 채워지는데 말이죠. 결론적으로는 그 타이밍?이 다르다는 겁니다. 그럼 어떻게 해야 할까요?       
4. 호스트 응용 프로그램을 사용하면 됩니다.(호스트~ 주인장? 뭐 그런거죠? 지금 만드는 게 FitNessTests니까 호스트는 당연히? FitNess겠네요.)
> 단위 테스트가 앱 체계에서 테스트 작업의 일부로 실행 되면 Xcode는 대상 설정에 지정된대로 호스트 응용 프로그램을 사용합니다.      
![image](https://user-images.githubusercontent.com/60660894/92714528-49ae6a80-f397-11ea-8ee1-263962f38124.png)      
> 위 사진을 보면 FitNessTests의 타겟에 대한 응용프로그램으로 FitNess가 선택되어있는 것을 볼 수 있습니다.       
> 즉, 테스트 작업을 실행하면 지정된 대상 (시뮬레이터 또는 장치)에서 호스트 앱이 시작됩니다. 테스트 실행기는 테스트를 시작하기 전에 앱이 로드 될 때까지 대기하고 테스트는 앱의 컨텍스트에서 실행됩니다.     
> 결과적으로 UIApplication 개체와 테스트의 전체보기 계층에 접근 할 수 있다는 것이죠.     
5. 새로운 그룹과 파일을 추가합니다.       
![image](https://user-images.githubusercontent.com/60660894/92714931-ca6d6680-f397-11ea-85c7-c30e31038054.png)      
6. 코드 내용을 바꿔줍니다.
```swift
import Foundation
import UIKit
@testable import FitNess

func loadRootViewController() -> RootViewController {
  let window = UIApplication.shared.windows[0]
  return window.rootViewController as! RootViewController
}
```

```swift

import Foundation
import UIKit
@testable import FitNess

extension RootViewController {
  
  var stepController: StepCountController {
    return children.first { $0 is StepCountController } as! StepCountController
  }
  
}
```
> loadRootViewController메소드는 루트뷰컨트롤러를 리턴합니다.        
> extension의 stepController를 활용하면 stepController에 접근할 수 있습니다.      
        
### _Fixing the tests_
        
1. StepCountControllerTests.swift으로 다시 돌아와서 코드를 바꿔줍니다.      
```swift

  override func setUp() {
    super.setUp()
    let rootController = loadRootViewController()
    sut = rootController.stepController
  }
  
```
> 이제 필요없어진 sut = StepCountController()를 삭제했습니다.     
2. givenInProgress()를 추가해줍니다.       
```swift

// MARK: - Given

  func givenInProgress() {
    givenGoalSet()
    sut.startStopPause(nil)
  }
  
```
> startStopPause() 때문에 앱의 상태값이 inProgress로 됩니다.         
3. testChaseView_whenInProgress_viewIsInProgress()를 추가합니다.      
```swift

  func testChaseView_whenInProgress_viewIsInProgress() {
    // given
      givenInProgress()
    // then
    let chaseView = sut.chaseView
    XCTAssertEqual(chaseView?.state, AppState.inProgress)
  }

```
> 체이스뷰가 InProgress 일 때,  뷰가 InProgress인지 테스트합니다. 실패합니다. updateChaseView()가 구현되지 않았습니다.      
> 내용을 채워줍니다. chaseView.state = AppModel.instance.appState       
> 통과합니다.        


> 참고 : 뷰 컨트롤러를 검색하고 테스트하는 또 다른 방법은 다음과 같습니다. 먼저 스토리 보드에 대한 참조를 가져옵니다.       
> let storyboard = UIStoryboard(name: "Main", bundle: nil)      
> 둘째, 뷰 컨트롤러에 대한 참조를 가져옵니다.     
> let stepController = storyboard.instantiateViewcontroller(withIdentifier: "stepController") as! StepCountController       
> 마지막으로 필요한 경우 다음과 같이 뷰를 로드 할 수 있습니다.       
> stepController.loadViewIfNeeded()     
> 이 패턴을 따르면 각 테스트에 대해 새로운 뷰 컨트롤러를 인스턴스화 할 수 있으며 각 테스트에 대해 뷰 컨트롤러를 설정하고 해체 할 수있는 옵션이 제공됩니다.      
        
### _Test ordering macers_
        
전체 대상을 빌드하고 테스트하면 대부분의 테스트는 통과해야하지만 testController_whenCreated_buttonLabelIsStart 는 통과하지 않습니다. 이 테스트는 실패합니다. 좌측 메뉴의 레포트 네비게이터의 로그를 확인해봅니다. 콘솔창을 읽는 것 보다 이렇게 로그창을 보는게 더 보기 편합니다.              
![image](https://user-images.githubusercontent.com/60660894/92817458-8d3dbe80-f401-11ea-8e78-ecd20db4b2a2.png)      
        
1. 오류 로그를 살펴볼게요.             
> testController_whenCreated_buttonLabelIsStart()       
> failed: ("Optional("Pause")") is not equal to ("Optional("Start")")       
            
> 무슨 말일까요? 컨트롤러에서 created됐을 때, 버튼의 레이블값이 Start여야 하는데 그게 지금 Pause라는 거죠? 이게 무슨말일까요?            
> Pause는 앱상태가 inProgress일 때 나와야 하는 겁니다. 이것은 테스트가 새로운 StepCountController로 시작되지 않는다는 것을 뜻하죠.     
> 즉, 호스트 앱의 StepCountController 사용에 대한 이전 변경은 새 컨트롤러가 매 setUp()마다 생성되지 않고 앱 상태가 유지됨을 의미합니다.         
> 깨끗한 테스트를하려면 tearDown()에서 상태를 재설정해야합니다. 이를 돕기 위해 AppModel에 새 함수를 만들어 상태를 재설정 할 수 있습니다.             

> testChaseView_whenLoaded_isNotStarted(),      
> failed: ("Optional(FitNess.AppState.inProgress)") is not equal to ("Optional(FitNess.AppState.notStarted)")       
> 위의 내용과 같음               
2. AppModelTests.swift를 열고 다음을 추가합니다. 
```swift

  func givenInProgress() {
    givenGoalSet()
    try! sut.start()
  }
  
```
> 이렇게 하면 앱이 inProgress 상태로 전환되어 상태 restart 테스트가 실제로 변경 사항을 테스트할 수 있다.       
3. 그리고 맨아래에 새로운 테스트를 추가해줍니다. 
```swift

  // MARK: - Restart
  func testAppModel_whenReset_isInNotStartedState() {
    // given
    givenInProgress()
    // when
    sut.restart()
    // then
    XCTAssertEqual(sut.appState, .notStarted)
  }
  
```
```swift

  func restart() {
    appState = .notStarted
  }
  
```

> 이것은 아직 추가되지 않은 restart() 가 모델을 notStarted 로 되돌리는 지 테스트 합니다. 실패합니다.        
> AppModel.swift에 가서 restart()를 추가하고 구현합니다.         
4. StepCountControllerTests.swift의 tearDown()으로 돌아가서 메소드를 바꿔줍니다.        
```swift

  override func tearDown() {
    AppModel.instance.dataModel.goal = nil
    AppModel.instance.restart()
    sut.updateUI()
    super.tearDown()
  }
  
```
> 결론적으로 정리를 해보면, 핵심은 호스트 앱의 StepCountController사용에 대한 이전 변경이 새 컨트롤러가 매 setUp()마다 생성되지 않고 앱 상태가 유지된다는 거죠. 
> 따라서 기존에는 작업을 sut = nil로 해줬었습니다. 그러나 새로 생성되는 게 아니니까 appState이 초기화가 되지 않는게 문제였었죠.               
> 그래서 내용을 위처럼 바꿔주면서 restart() 함수를 하나 만들어줘서 앱상태 값을 초기화되도록 처리해준겁니다.       
        
### _Randomized order_
        
구성표의 테스트 작업에는 테스트 순서를 무작위로 지정 하는 옵션도 있습니다.          
![image](https://user-images.githubusercontent.com/60660894/92822119-c62c6200-f406-11ea-9d73-6aad830f76dc.png)      
![image](https://user-images.githubusercontent.com/60660894/92822191-dc3a2280-f406-11ea-9221-e89c5c47c904.png)      

1. Xcode-Product-Scheme-Edit Scheme...를 선택합니다.      
2. 가운데 창에서 옆에 옵션 버튼을 클릭합니다. 그것을 클릭하고 팝업에서 Randomize execution order를 선택하면 매번 무작위 순서로 테스트가 실행됩니다.        
        
### _Code coverage_
        
![image](https://user-images.githubusercontent.com/60660894/92823178-f45e7180-f407-11ea-80c9-9bd3479f9faa.png)      
1. Xcode-Product-Scheme-Edit Scheme...를 선택합니다.      
2. 이번에는 옵션탭을 선택합니다. Code Coverage 확인란이 있습니다. Gather coverage for... 체크박스를 선택합니다.
3. 테스트를 다시 실행하고 좌측 레포트 네비게이터를 엽니다. Build 와 Log만 있었는데 Coverage가 새로 생긴 것을 볼 수 있습니다. 
> 코드 커버리지는 테스트 중에 실행되는 앱 코드 줄 수를 측정 한 것입니다. 실행 된 코드행의 백분율과 함께 대상의 각 파일 목록이 있습니다. 파일에 대해 100% 또는 닫기가 있다는 것은 TDD를 면밀히 따르고 있음을 의미합니다. 테스트가 먼저 작성되면 테스트를 통과하는 데 필요한 코드만 추가됩니다. 개별 파일을 열면 기능별 또는 클로저별로 적용 범위가 표시됩니다. 파일 또는 함수 이름을 두 번 클릭하면 편집기에서 해당 파일이 열립니다.        
4. 코드 커버리지의 상세 메뉴를 보니 StepCountController.swift가 88.9%네요. 상세를 열고 startStopPause (_ :)를 더블클릭하면 자동으로 이동됩니다.
![image](https://user-images.githubusercontent.com/60660894/92824247-258b7180-f409-11ea-8f64-47fe7b9ba490.png)   
![image](https://user-images.githubusercontent.com/60660894/92824008-de9d7c00-f408-11ea-8b31-7289cfa434b6.png)      
> 편집기 오른쪽에 커버리지 주석이 표시됩니다. 표시된 숫자는 해당 행이 실행 된 횟수를 나타냅니다. 빨간색 또는 "0"이있는 선은 추가 테스트를 추가 할 기회를 나타냅니다. 빨간색 줄무늬 주석이있는 선은 해당 선의 일부만 실행되었음을 의미합니다. 주석 표시 줄의 스트라이프 위로 마우스를 가져 가면 실행된 부분은 녹색으로 표시되고 실행되지 않은 부분은 빨간색으로 표시됩니다.
            
> 해당 조건을 테스트 할 때의 문제는 오류가있을 때 Alert컨트롤러가 표시된다는 것입니다. 해당 Alert컨트롤러를 확인하는 테스트를 작성할 수 있지만 실제로는 UI 자동화 테스트의 도메인입니다. StepCountController를 리팩터링 하여 변수가 설정되거나 해당 오류의 경우 콜백이 호출 될 수 있지만 테스트를 추가하기 위해 앱 코드를 수정하게됩니다. 그런 다음 테스트는 값을 제공하지 않는 앱 기능이 아닌 자체 테스트가됩니다.
                
> 목표는 가능한 한 100%에 근접하는 것이어야합니다. 커버리지는 코드가 작동한다는 것을 의미하지는 않지만 커버리지가 부족하다는 것은 테스트되지 않았음을 의미합니다. 뷰 및 뷰 컨트롤러의 경우 TDD에 UI 테스트가 포함되어 있지 않기 때문에 100% 적용되지 않을 것으로 예상됩니다. 단위 테스트와 UI 자동화 테스트를 결합하면 이러한 파일 전부는 아니더라도 대부분을 다룰 수 있습니다.        
        
### _Debugging tests_
        
테스트 디버깅에 관해서는 이미 한 번 연습했습니다. 즉, "내가 옳은 것을 테스트하고 있는가?"
다음 사항을 확인하십시오.      
        
- 주어진 진술 에 올바른 가정이 있습니다.
- 당신의 다음 문은 정확하게 원하는 동작을 반영한다.
        
테스트 코드에 분명한 것이 없으면 다음으로 유지 된 상태에 대한 테스트 실행 순서를 확인합니다. 또한 코드 커버리지를 사용하여 올바른 코드 경로를 선택했는지 확인하십시오. 이를 시도한 후 Xcode에서 다른 도구를 사용할 수 있습니다. 그들을 시험해보기 위해 앱에서 다른 중요한 배우 인 Nessie에 대해 생각할 때입니다.       
        
### _Using test breakpoints_
        
그림에서 Nessie를 사용하면 데이터 모델이 조금 더 복잡해집니다. Nessie의 새로운 규칙은 다음과 같습니다.        
        
- Nessie의 거리가 사용자의 거리보다 크거나 같으면 Nessie가 승리합니다 (사용자가 잡힙니다). 거리가 시작 조건인 0일 때는 사용자를 잡을 수 없습니다.      
- 사용자가 Nessie에 잡히면 목표에 도달 할 수 없습니다.     
        
```swift

func testModel_whenStarted_userIsNotCaught() {
  XCTAssertFalse(sut.caught)
}

```

```swift

class DataModel{
 
  var goalReached: Bool {
    if let goal = goal,
      steps >= goal {
      return true
    }
    return false
  }
  var goal: Int?
  var steps: Int = 0
  
  let nessie = Nessie()
  var distance: Double = 0
  var caught: Bool {
    return nessie.distance >= distance
  }
  
}

```

1. testModel_whenStarted_userIsNotCaught()
> 새로운 DataModel로 사용자가 잡히지 않는지 테스트합니다. 역시나 실패합니다.        
2. DataModel.swift에서 코드를 변경해줍니다.                  
> 이렇게 하면 데이터 모델에 Nessie, 사용자 거리를 추적하는 변수, 거리를 비교하기 위한 계산된 변수가 추가됩니다. 나중에 계산을 더 깔끔하게 유지하기 위해 단계 대신 거리에 대한 별도의 변수가 사용됩니다.
        
업데이트 된 코드를 사용해도 테스트는 여전히 실패합니다. 문제를 진단하는 방법에는 여러 가지가 있습니다. 이미 보셨듯이 확인해야 할 몇 가지 사항이 있습니다.        
        
• 테스트 자체가 정확합니다. 주어진 것은 startUp()에서 생성된 새로운 데이터 모델입니다. 그때도 정확하고, 잡힌 것은 거짓이어야합니다.        
• 코드 커버리지에 표시된대로 DataModel코드가 실행되었습니다.      
        
좋은 다음 단계는 디버거를 사용해 보는 것입니다. Xcode 좌측 메뉴에서 BreakPoint Navigator를 클릭합니다. 그리고 좌측 맨 아래에서 +를 클릭하고 Test Failure Breakpoint를 선택합니다. 이렇게하면 단위 테스트가 실패하면 실행을 중지하는 BreakPoint가 생성됩니다. 테스트를 다시 실행하면 디버거가 테스트 실패시 중지됩니다.
![image](https://user-images.githubusercontent.com/60660894/92827019-54571700-f40c-11ea-9139-39fd0d4ea61b.png)      
![image](https://user-images.githubusercontent.com/60660894/92827597-00006700-f40d-11ea-83ae-1d2d21f17914.png)      
> 콘솔 옆의 breakpoint에서 sut를 확장합니다. 그래서 보면 여기에서 거리와 걸음수가 모두 0임을 알 수 있습니다. 따라서 앱 로직은 옳은 일을하고 있고, Nessie는 사용자와 연결되어 있습니다. 그러나 이것은 시작 조건이 캡처로 이어질 수 없는 특별한 경우입니다. 
        
이것을 해결하려면 DataModel.swift에서 caught를 아래처럼 바꿔줍니다. 이제 통과합니다.
```swift

  var caught: Bool {
//    return nessie.distance >= distance
    return distance > 0 && nessie.distance >= distance
  }
  
```
        
### _Completing coverage_
        
DataModel.swift의 코드 커버리지를 살펴보면 더 이상 100%가 아닙니다. 스트라이프 위로 마우스를 가져 가면 distance > 0 조건만 확인되었음을 표시합니다. 이것은 테스트 할 조건이 더 있음을 나타냅니다.       
![image](https://user-images.githubusercontent.com/60660894/92828026-89b03480-f40d-11ea-9e95-8fd598d7c3ca.png)      
```swift

  func testModel_whenUserAheadOfNessie_isNotCaught() {
    // given
    sut.distance = 1000
    sut.nessie.distance = 100
    // then
    XCTAssertFalse(sut.caught)
  }
  
  func testModel_whenNessieAheadofUser_isCaught() {
    // given
    sut.nessie.distance = 1000
    sut.distance = 100
    // then
    XCTAssertTrue(sut.caught)
  }
  
```
 1. nessie.distance >= distance 조건을 충족하기 위해서 테스트를 두개 추가합니다.     
 2. 코드커버리지가 100%가 되었습니다.        
        
### _Finishing out the requirements_
        
아직 설명되지 않은 마지막 부분이 하나 있습니다. 사용자가 잡히면 목표에 도달할 수 없습니다. 테스트를 추가합니다.        
        
```swift

  // MARK: - Goal
  func testGoal_whenUserCaught_cannotBeReached() {
    //given goal should be reached
    sut.goal = 1000
    sut.steps = 1000

    // when caught by nessie
    sut.distance = 100
    sut.nessie.distance = 100

    // then
    XCTAssertFalse(sut.goalReached)
  }
  
```

```swift

  var goalReached: Bool {
    if let goal = goal,
      steps >= goal, !caught {
        return true
    }
    return false
  }

```
1. DataModelTests.swift에서 testGoal_whenUserCaught_cannotBeReached를 추가해줍니다.      
2. DataModel에서 goalReached를 변경해줍니다. 이제 통과합니다.       
                
### _Challenge_
        
StepCountControllerTests.tearDown()에는 AppModel과 DataModel을 재설정하는 별도의 호출이 있습니다.      
        
1. 데이터 모델은 앱 모델의 속성이므로 적절한 테스트와 함께 데이터 모델 재설정을 AppModel.restart()로 리팩터링합니다.     
추가 챌린지의 경우 XCTAssertNil 또는 XCTAssertLessThanOrEqual과 같이 아직 사용되지 않은 다른 XCTAssert 함수를 사용하십시오.     
        
2. 두 번째 과제는 앱에 일시 중지 기능을 추가하여 사용자가 .paused와 .inProgress간에 앞뒤로 이동할 수 있도록하는 것입니다.     
직접적인 기능은 이후에 다룰 것이기 때문에 일시중지는 이 시점에서 다른 작업을 수행 할 필요가 없습니다.      
        


### _Key points_
        
- 테스트 메서드를 사용하려면 XCTAssert 함수를 호출해야 합니다.        
- 뷰 컨트롤러 로직은 데이터 / 상태 기능으로 분리될 수 있으며, 단위 테스트가 가능하고 설정 및 응답 기능을 볼 수 있으며 UI 자동화에 의해 테스트되어야합니다.        
- 테스트 실행 순서가 중요합니다.     
- 코드 커버리지 보고서를 사용하여 모든 분기에 최소 수준의 테스트가 있는지 확인할 수 있습니다.      
- 테스트 실패 중단점은 테스트 수정을 위한 일반 디버깅 도구 위에 있는 도구입니다.             
        
### _Where to go from here?_
        
코드 커버리지에 대한 자세한 내용은 이 video tutorial에서 해당 주제를 다룹니다. 또한 Advanced Apple Debugging and Reverse Engineering 책에서 디버깅에 대한 모든 것을 배울 수 있습니다. 이 책에서 배운 도구와 기술은 애플리케이션 코드와 마찬가지로 테스트 코드에 적용 할 수 있습니다.     

다음 장에서는 XCTestExpectation을 사용하여 비동기 함수를 테스트하는 방법을 배웁니다.     
# 
# 
# 
> 이 글은 아래의 책을 기초로 나름대로의 주관과 생각을 더하여 재가공한 것입니다.   
> 자세한 내용을 알고 싶다면 아래 링크를 참고해주세요. 교재 및 예제 코드는 저작권 문제로 제공되지 않습니다.  
>        
> iOS Test-Driven Development by Tutorials, 2020 Razeware LLC  
> https://store.raywenderlich.com/products/ios-test-driven-development
