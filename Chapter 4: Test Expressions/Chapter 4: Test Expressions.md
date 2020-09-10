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
        







### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_
### _Assert methods_

        







# 
# 
# 
> 이 글은 아래의 책을 기초로 나름대로의 주관과 생각을 더하여 재가공한 것입니다.   
> 자세한 내용을 알고 싶다면 아래 링크를 참고해주세요. 교재 및 예제 코드는 저작권 문제로 제공되지 않습니다.  
>        
> iOS Test-Driven Development by Tutorials, 2020 Razeware LLC  
> https://store.raywenderlich.com/products/ios-test-driven-development
