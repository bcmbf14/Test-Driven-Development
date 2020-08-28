# _Chapter 3: TDD App Setup_
    
이 장의 목표는 몇 가지 테스트로 테스트 대상을 생성하여 Xcode 테스트가 작동하는 방식에 대한 느낌을 제공하는 것입니다.  
- 테스트 대상을 만들고 단위 테스트를 실행합니다.    
- 데이터와 상태를 확인하는 단위 테스트를 작성합니다. 
      
### _About the FitNess app_
    
이 책 섹션에서는 재미있는 단계 추적 앱인 FitNess를 구축합니다. FitNess는 "Loch Ness"운동을 기반으로 한 최고의 피트니스 코칭 앱입니다. 사용자는 피트니스 괴물 인 Nessie를 능가하거나, 수영하거나, 능가해야합니다. 앱의 목표는 사용자가 Nessie를 능가하도록하여 사용자의 움직임을 유도하는 것입니다. 실패하면 아바타를 먹습니다.
    
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
### _About the FitNess app_
