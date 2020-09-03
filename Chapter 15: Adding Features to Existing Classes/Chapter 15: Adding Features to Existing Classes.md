# _Chapter 15: Adding Features to Existing Classes_

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
    
매우 큰 클래스의 종속성을 깨뜨릴 시간이 항상있는 것은 아니며 단순히 실행 가능하지 않을 수도 있습니다. 새로운 기능을 추가 할 기한이 있고 앱이 스파게티인 경우 먼저 모든 것을 정리할 시간이 없습니다. 다행히도 이 상황을 해결하기위한 TDD 기술이 있습니다. 이 장에서는 기존 클래스에 기능을 추가하는 동시에 수정을 피하는 전략을 배웁니다.   
    
기능을 추가할 목표는 공지 사항, 달력, 조직도, 구매 주문 및 설정의 5가지 주요 뷰 컨트롤러 각각에 대한 분석 이벤트를 볼 수 있는 화면을 추가하는 것입니다. 이렇게하면 제품 소유자가 가장 많이 사용되는 화면을 식별하고 시간과 리소스를 어디에 투자할지 파악할 수 있습니다.   
    
- 분석 이벤트 보고에 포함되는 것들
  - 화면보기 또는 버튼 탭과 같은 사용자 시작 작업.
  - 이벤트에 대한 메타 데이터가 포함 된 보고서 입니다.
  - 해당 보고서를 백엔드로 보냅니다.  
      
### _Laying a foundation_
      
백엔드 API와 통신하는 클래스가 이미 있습므로 보고서를 백엔드로 보내는 기능 추가하는 것부터 시작합니다.   
종속성을 깨끗하게 유지하고 작업을 테스트하기 쉽게 만들기 위해 프로토콜을 사용합니다.   

1. MyBiz 그룹 아래의 Start프로젝트에서 Analytics라는 새 그룹을 만들고 그룹 안에 AnalyticsAPI.swift 파일을 만들고 아래와 같이 정의합니다. 
```swift

protocol AnalyticsAPI {

}

```
2. MyBizTests/Cases 그룹에 AnalyticsAPITests을 추가하고 내용을 아래와 같이 바꿉니다. 
```swift

import XCTest
@testable import MyBiz

class AnalyticsAPITests: XCTestCase {
  
  var sut: AnalyticsAPI!
  
  override func setUp() {
    super.setUp()
  }
  
  override func tearDown() {
    super.tearDown()
  }
  
  func testAPI_whenReportSent_thenReportIsSent() { // given
    let date = Date()
    let interval: TimeInterval = 20.0
    let report = Report(name: "name",
                        recordedDate: date,
                        type: "type",
                        duration: interval,
                        device: "device",
                        os: "os",
                        appVersion: "appVersion")
    // when send a report?
    // ???
    // then assert a report was sent
    // ???
  }
}

```
새 코드를 추가 할 때마다 먼저 테스트를 추가해야합니다. testAPI_whenReportSent_thenReportIsSent() 는 AnalyticsAPI가 보고서를 보낼 수 있다고 가정하면 보고서가 전송되었는지 확인할 수 있습니다. 어떻게 해야 할까요? 아쉽게도 이 작업을 쉽게 수행 할 수 있는 좋은 확장 포인트가 이 앱에는 없습니다.   
    
### _Extending the API_
    
첫 번째 단계는 보고서를 보내는 것입니다. 백엔드로 물건을 보내는 클래스인 API가 이미 있습니다. 하지만, 이전 장에서 보았듯이 이 클래스는 번거롭고 나머지 앱 코드와 얽혀 있습니다.     
우리는 지금 복잡성을 증가시키거나 새로운 종속성을 도입하지 않고 기능을 추가하려고 하죠? 고맙게도 Swift를 사용하면 Extension을 사용하여 파일간에 구현을 분할 할 수 있습니다.        

1. Analytics 그룹에 API + Analytics.swift라는 새 파일을 만듭니다. 이 명명 규칙을 사용하면 파일에 분석과 관련된 API 확장이 포함된다는 것을 알 수 있습니다. 아래 코드를 입력합니다. 
```swift

extension API: AnalyticsAPI {

}

```
2. AnalyticsAPITests.swift로 되돌아가서 코드를 일부 변경합니다. 
sutImplementation에는 API의 인스턴스가 생성되지만 sut 변수를 통해 AnalyticsAPI로만 노출됩니다. 

```swift

  var sut: AnalyticsAPI { return sutImplementation }
  var sutImplementation: API!
  
  override func setUp() {
    super.setUp()
    sutImplementation = API(server: "test")
  }
  
  override func tearDown() {
    sutImplementation = nil
    super.tearDown()
  }

```
### _Sending a report_
    
1. AnalyticsAPI.swift를 열고 다음 메소드를 프로토콜에 추가하고 API + Analytics.swift에서 구현합니다. 
```swift

protocol AnalyticsAPI {
  func sendReport(report: Report)
  
}

extension API: AnalyticsAPI {
  func sendReport(report: Report) {
    
  }
}

```
2. AnalyticsAPITests.swift의 testAPI_whenReportSent_thenReportIsSent()에서 //when... 주석 부분을 sut.sendReport(report: report)으로 채워줍니다. 
    
3. API.swift를 열고 다음 프로토콜을 파일에 추가합니다. 그리고 파일 맨 아래에 다음 확장자를 추가하십시오.
```swift

protocol RequestSender {
  func send<T: Decodable>(request: URLRequest, success: ((T) -> ())?, failure: ((Error) -> ())?)
}

extension API: RequestSender {
  func send<T>(request: URLRequest,
               success: ((T) -> ())?,
               failure: ((Error) -> ())?) where T : Decodable {
    let task = loadTask(request: request,
                        success: success,
                        failure: failure)
    task.resume()
  }
}
```
보고서가 보내졌는지 확인하기 위해서 필요한 것은 백엔드를 나타내는 모의 객체입니다. 또한 실제 API 구현을 사용합니다. AnalyticsAPI를 모의 처리하는 경우 테스트는 개체 메서드를 호출 할 때 메서드가 실행되는지 확인하기 때문에 실제 API가 필요합니다.   

이것을 해결하기 위해 RequestSender 프로토콜을 추가하였습니다. 이는 기존 프로젝트의 프로젝션 코드인 loadTask (request : success : failure :)를 재사용하여 URLSessionTask를 만들고 성공 및 실패 블록을 전달합니다. 또한 이 메서드는 값을 반환하지 않고 작업을 실행할 다른 방법이 없기 때문에 작업을 시작합니다.

4. 마지막으로 var token 아래의 API에 다음 var 를 추가합니다. 
```swift
lazy var sender : RequestSender = self
```
이것은 나중에 삽입 할 수 있는 요청 발신자를 설정하지만 자신을 기본 값으로 사용합니다. 이것은 다음 단계에서 API에 테스트를 추가하기 위한 레버리지 포인트가 될 것입니다. 이와 같은 자기 참조를 갖는 것은 약간 간접적으로 보일 수 있습니다. 그러나 이 단계를 수행하면 이 클래스를 그대로두고 테스트를 포함한 새로운 기능을 추가 할 수 있습니다.
    
### _Testing the API_

1. MyBizTests에서 Mocks이라는 그룹을 만들고 MockSender.swift라는 새 파일을 만듭니다. 그리고 아래의 내용을 입력합니다.
```swift

import XCTest
@testable import MyBiz

class MockSender: RequestSender {
  var lastSent: Decodable? = nil
  
  func send<T: Decodable>(request: URLRequest, success: ((T) -> ())?, failure: ((Error) -> ())?) {
    
    let decoder = JSONDecoder()
    decoder.dateDecodingStrategy = .iso8601
    do {
      let obj = try decoder.decode(T.self,
                                   from: request.httpBody!)
      lastSent = obj
      success?(obj)
    } catch {
      print("error decoding a \(T.self): \(error)")
      failure?(error)
    }
  }
}

```
이 클래스는 request body를 만드는 데 사용한 객체를 반환한 다음 lastSent에 저장 하여 RequestSender 프로토콜을 구현합니다.

2. AnalyticsAPITests.swift로 돌아가서 var mockSender: MockSender! 를 추가합니다. 
3. setUp(), tearDown()을 아래 코드로 바꿔줍니다. 
```swift
override func setUp() {
  super.setUp()
  sutImplementation = API(server: "test")
  mockSender = MockSender()
  sutImplementation.sender = mockSender
}

override func tearDown() {
  sutImplementation = nil
  mockSender = nil
  super.tearDown()
}
```

4. testAPI_whenReportSent_thenReportIsSent()의 주석 then 이후에 아래 코드를 입력합니다.
```swift
// then
XCTAssertNotNil(mockSender.lastSent) 
XCTAssertEqual(report.name, "name") 
XCTAssertEqual((mockSender.lastSent as? Report)?.name, "name")
```
MockSender는 전송된 개체를 lastSent에 저장하므로 이를 사용하여 전달된 보고서가 전송되었는지 확인할 수 있습니다.
    
### _Sprouting the send method_

API에는 이미 객체를 받아서 백엔드로 보내는 func submitPO(po: PurchaseOrder)가 있습니다. 이것이 특히 구매 주문서를 보내기 위한 것이라는 점은 너무 안타깝습니다. 종속성을 매핑하고, 특성화 및 단위 테스트를 작성하고, 재사용 가능한 방식으로 API 기능을 확장하여이 메서드를 리팩터링 할 수 있습니다.    

하지만 지금 당장은 그 정도의 리팩토링을 할 시간이 없습니다. 이 경우 코드 복사를 할 수 밖에는 없습니다. 좋지 않지만 이 복사 된 방법에 대한 테스트를 받게되며 이 작업은 분석을 추가 할 때만 지원하기 위한 것입니다.   

1. API + Analytics.swift를 열고 파일 끝에 다음 API Extension을 추가합니다.
```swift

extension API {
  // 1
  func logAnalytics(analytics: Report,
                    completion: @escaping (Result<Report, Error>) -> ()) throws {
    // 2
    let url = URL(string: server + "api/analytics")!
    var request = URLRequest(url: url)
    if let token = token?.token {
      let bearer = "Bearer \(token)"
      request.addValue(bearer,
                       forHTTPHeaderField: "Authorization")
    }
    
    request.addValue("application/json",
                     forHTTPHeaderField: "Content-Type")
    request.httpMethod = "POST"
    
    let coder = JSONEncoder()
    coder.dateEncodingStrategy = .iso8601
    let data = try coder.encode(analytics)
    request.httpBody = data
    
    // 3
    sender.send(
      request: request, success: { savedEvent in
        completion(.success(savedEvent)) },
      failure: { error in
        completion(.failure(error))
    })
  }
  
}

```

이 코드는 몇 가지 눈에 띄는 변경 사항과 함께 func submitPO(po: PurchaseOrder) 코드를 복제합니다.
    
- func logAnalytics (analytics : completion :)은 PurchaseOrder대신 분석 보고서를 받습니다. 또한 중요한 것은 원래 앱 코드와 함께 제공되는 이해하기 어렵고 버그가 많은 델리게이트에 의존하는 대신 결과를 반환하는 클로저 블록이 있다는 것입니다. 코드를 개선하거나 추가 할 때 롤아웃 할 수 있다면 새로운 언어기능과 현대적인 패턴을 활용하는 것이 좋습니다.    
    
- 구매 주문에 대해 하드코딩 된 엔드 포인트 대신 하드코딩 된 분석 엔드 포인트가 있습니다.    
    
- 이는 앞서 API에 도입 한 새로운 RequestSender.send (request : success : failure :) 를 사용합니다. 즉,  이 방법을 테스트 할 수 있습니다.
    
여기에서는 새 기능을 추가 할 수 있도록 기존 기능을 향상 시키거나 복제하는 기존 클래스에 새 메서드를 추가 하는 방법인 sprouting a method 라는 기술을 사용하고 있습니다. 이 기술을 사용하면 클래스를 리팩토링하거나 아직 테스트되지 않은 항목을 잠재적으로 파괴하는 구멍을 피할 수 있습니다. 이를 통해 코드의 레거시 부분과 완전히 분리된 새 인터페이스를 정의 할 수 있습니다. 이 경우 인터페이스는 별도의 파일에 정의되어 있습니다.
    
2. API + Analytics.swift의 func sendReport(report: Report)에 아래 코드를 추가합니다. 
```swift

try? logAnalytics(analytics: report) { _ in }

```
이제 빌드하고 테스트하면 테스트가 통과됩니다. 성공적으로 테스트가 가능한 신규 메소드를 기존 코드베이스에 최소한의 침입만으로 API 에 연결할 수 있습니다.     

### _Adding analytics to the view controllers_
    
1. MyBizTests/Cases에 AnnouncementsTableViewControllerTests.swift 이름의 파일을 생성하고 아래 코드를 작성합니다.
```swift
import XCTest
@testable import MyBiz

class AnnouncementsTableViewControllerTests: XCTestCase {
  
  var sut: AnnouncementsTableViewController!
  
  override func setUp() {
    super.setUp()
    sut = UIStoryboard(name: "Main", bundle: nil)
      .instantiateViewController(withIdentifier: "announcements")
      as? AnnouncementsTableViewController
  }
  
  override func tearDown() {
    sut = nil
    super.tearDown() }
  
  func whenShown() {
    sut.viewWillAppear(false)
  }
  
  func testController_whenShown_sendsAnalytics() { // when
    whenShown()
    // then the report will be sent
    // ???
  }
  
}

```
이 코드는 테스트중인 시스템이 AnnouncementsTableViewController인 테스트를 설정합니다. 목적은 viewWillAppear(_ :)가 분석 보고서를 전송하는지 테스트하는 것이고 whenShown()이 트리거 역할을 합니다.
    
### _Not mocking all of the API_
    

1. AnalyticsAPI 프로토콜을 이미 정의했기 때문에 새로 만들거나 RequestSender의 mock out 할 필요가 없습니다. MyBizTests/Mocks에서 MockAnalyticsAPI.swift을 하고 다음과 같이 내용을 바꿉니다.
```swift

import XCTest
@testable import MyBiz

class MockAnalyticsAPI: AnalyticsAPI {
  
  var reportSent = false
  
  func sendReport(report: Report) {
    reportSent = true
  }
  
}

```
이 클래스는 AnalyticsAPI를 구현 하지만 보고서를 보내는 대신 reportSent 를 사용 하여 트리거 된 플래그를 지정합니다. API에 대한 이전 테스트는 보고서가 서버로 전달되는지 확인합니다.    

2. AnnouncementsTableViewControllerTests.swift로 돌아가서 새 변수를 추가합니다. 
```swift

var mockAnalytics: MockAnalyticsAPI!

```
MockAnalyticsAPI는 초기화시 reportSent를 false로 설정되어있기 때문에 보고서가 서버로 전달됐다면 해당 변수가 true로 변경됩니다.   
            
3. AnnouncementsTableViewController.swift에서 var api밑에 새 변수를 추가합니다. 
```swift

var analytics : AnalyticsAPI ?

```
    
4. 이제 마지막으로 viewWillAppear (_ :)를 AnalyticsAPI에 연결해야합니다. viewWillAppear (_ :) 끝에 아래 코드를 추가합니다. 보고서가 생성되고  AnalyticsAPI 로 전달하여 백엔드로 보냅니다.    
```swift

  override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    api.delegate = self
    api.getAnnouncements()
    
    let screenReport = Report(name: AnalyticsEvent.announcementsShown.rawValue,
    recordedDate: Date(),
    type: AnalyticsType.screenView.rawValue, duration: nil,
    device: UIDevice.current.model,
    os: UIDevice.current.systemVersion, appVersion: Bundle.main
    .object(forInfoDictionaryKey: "CFBundleShortVersionString")
    as! String)
    analytics?.sendReport(report: screenReport)
  }
  
```
    
### _Another interesting use case_
      
이미 만들어놓은 메소드를 통해 할 수 있는 일은 메서드가 호출 된 횟수 나 메서드가 호출되는 순서를 확인하는 것입니다.    

1. MockAnalyticsAPI.swift를 열고 var reportSent 아래에 var reportCount = 0 을 추가합니다.     
2. 그리고 sendReport(report :) 끝에 reportCount = reportCount + 1 을 추가합니다.     
3. 다시 AnnouncementsTableViewControllerTests.swift 돌아와서 아래 코드를 추가합니다. 
```swift

func testController_whenShownTwice_sendsTwoReports() { // when
  whenShown()
  whenShown()
// then
  XCTAssertEqual(mockAnalytics.reportCount, 2)
}

```
이처럼 미리 만들어놓은 코드를 활용해서 화면이 표시 될 때마다 보고서가 전송되는지 테스트 할 수 있었습니다.    
    
### _Passing around dependencies_

이제 분석 기능이 테스트에서 작동하지만 앱을 실행할 때는 작동하지 않습니다.    
이는 여전히 AnalyticsAPI를 AnnouncementsTableViewController에 전달해야하기 때문입니다. 

스토리 보드를 사용할 때 prepare (for : sender :) segue 메서드에서 이를 수행하여 필요한 모든 종속성을 다음 뷰 컨트롤러 (또는 유사하게 뷰 모델 또는 기타 도우미에서)에 주입하려고합니다.

이 앱은 화면에 수동으로 추가 된 일반 UITabBarController를 사용합니다. 재정의 할 prepare(for : sender :) 메서드가 없습니다.

따라서 분석도 수동으로 설정해야합니다.

잠재적으로 많은 뷰 컨트롤러에 추가 할 것임을 알고 있습니다.

최소한의 영향으로 기존 클래스에 추가 할 수있는 방법에 대해 생각하는 것이 좋습니다.

다시 한 번 구조 프로토콜을 의미합니다.

1. AnalyticsAPI.swift를 열고 파일 끝에 다음 프로토콜을 추가합니다.
```swift

protocol ReportSending: AnyObject {
  var analytics: AnalyticsAPI? {get set}
}

```
변수를 추가하고 모든 클래스에서이 프로토콜을 준수하면 AnalyticsAPI 구현을 삽입 할 수 있습니다.   
    
2. AnnouncementsTableViewController.swift를 열고 파일 끝에 다음 확장자를 추가합니다.
```swift

extension AnnouncementsTableViewController: ReportSending {}

```
그리고 마찬가지로 AnnouncementsTableViewController에 대한 추가 정보를 노출하지 않고도 분석 개체를 제공 할 수 있습니다.    
    
3. AppDelegate.swift을 열고 handleLogin(userId:)을 아래와 같이 변경합니다. 
```swift

func handleLogin(userId: String) {
  self.userId = userId
  let storyboard = UIStoryboard(name: "Main", bundle: nil)
  let tabController = storyboard.instantiateViewController(
   withIdentifier: "tabController") as! UITabBarController

  tabController.viewControllers?.compactMap { $0 as? ReportSending }.forEach { $0.analytics = api }

  window?.rootViewController = tabController
}

```
이제 ReportSending 을 준수하는 모든 탭바의 뷰컨트롤러에 AnalyticsAPI가 추가됩니다.    
여기에는 AnnouncementsTableViewController가 포함되어 있으므로 이제 viewWillAppear(_ :)가 실행될 때마다 로깅이 표시 됩니다.

앱을 빌드하고 실행합니다. 로그인하면 AnnouncementsTableViewController 탭이 표시됩니다.     
브라우저에서 http://localhost:8080/api/analytics를 열면 아래와 유사한 기록 된 이벤트가 표시됩니다.
![image](https://user-images.githubusercontent.com/60660894/92065419-a8457880-edda-11ea-9587-a42345cce68a.png)

### _Adding more events_
    
이제 보고서를 보내는 하나의 화면이 있습니다. 추가 화면에 보고서를 추가하는 것은 간단해야 합니다.   
    
1. OrgTableViewController.swift 에서 var analytics : AnalyticsAPI?를 추가합니다.    
2. 마지막으로 파일 제일 아래에 extension OrgTableViewController: ReportSending {}를 추가합니다.       
3. MyBizTests/Cases에 OrgTableViewControllerTests.swift를 만들고 내용을 다음으로 바꿉니다.    
```swift

class OrgTableViewControllerTests: XCTestCase {
  
  var sut: OrgTableViewController!
  var mockAnalytics: MockAnalyticsAPI!
  
  override func setUp() {
    super.setUp()
    sut = UIStoryboard(name: "Main", bundle: nil)
      .instantiateViewController(withIdentifier: "org") as? OrgTableViewController
    mockAnalytics = MockAnalyticsAPI()
    sut.analytics = mockAnalytics }
  
  override func tearDown() { sut = nil
    mockAnalytics = nil
    super.tearDown()
  }
  
  func whenShown() {
    sut.viewWillAppear(false)
  }
  
  func testController_whenShown_sendsAnalytics() { // when
    whenShown()
    // then
    XCTAssertTrue(mockAnalytics.reportSent)
    
  }
}

```
AnnouncementsTableViewControllerTests 와 매우 유사하므로 익숙해 보일 것 입니다.    
testController_whenShown_sendsAnalytics()는 OrgTableViewController가 표시 될 때 보고서가 전송되는지 테스트 합니다.     
테스트를 통과하려면 OrgTableViewController가 보기가 표시 될 때 보고서를 보내야합니다.      
그러나 viewWillAppear(_ :) 를 수정하기 전에 상용구를 복사 할 필요가 없도록 도우미 메서드를 만드는 것이 좋습니다.    
    
4. Report.swift를 열고 Report에 다음 메소드를 추가합니다.
```swift

struct Report: Codable {
  var name: String
  var recordedDate: Date
  var type: String
  var duration: TimeInterval?
  var device: String
  var os: String
  var appVersion: String
  
  static func make(event: AnalyticsEvent, type: AnalyticsType)
    -> Report {
    return Report(name: event.rawValue,
                  recordedDate: Date(),
                  type: type.rawValue,
                  duration: nil,
                  device: UIDevice.current.model,
                  os: UIDevice.current.systemVersion,
                  appVersion: Bundle.main
                    .object(forInfoDictionaryKey:
                      "CFBundleShortVersionString")
                    as! String)
  }
  
}

```

이 팩토리 메서드는 보고서에 포함되는 모든 상수를 처리하므로 호출자는 각 화면의 세부 사항에 대해서만 걱정하면됩니다. 

5. 이제 OrgTableViewController.swift 에서 이 메서드를 사용할 수 있습니다. OrgTableViewController로 돌아가서 viewWillAppear(_ :) 끝에 다음을 추가합니다.   

```swift

let report = Report.make(event: .orgChartShown, type: .screenView)
analytics?.sendReport(report: report)
    
```
빌드하고 실행하면 탭을 변경할 때 기록 된 두 개의 서로 다른 화면 이벤트가 표시됩니다.   




### _Challenge_
    
처리해야 할 실행 취소 된 작업이 몇 가지 있습니다.
    
- Report.make 메서드를 사용 하도록 AnnouncementsTableViewController 를 정리합니다.   
- 다른 화면에 screenView 분석을 추가합니다. 힌트로 UINavigationController를 통해 AnalyticsAPI를 전달해야합니다.   

### _Key points_
    
- 새로운 기능을 추가하기 위해 전체 수업을 테스트 할 필요는 없습니다.    
- 약간의 중복성을 추가하더라도 기능을 확장하는 방법을 만들 수 있습니다.   
- 프로토콜을 사용하여 종속성 및 확장을 삽입하여 레거시 코드에서 새 코드를 분리합니다.   
- TDD 방법은 깨끗하고 테스트 된 기능을위한 길을 안내합니다.    

### _Where to go from here?_
        
튜토리얼을 끝냈습니다.    
이제 API를 AnalyticsAPI 및 LoginAPI와 같은 특정 프로토콜로 계속 분해 할 수 있습니다.        
delegate를 결과로 바꾸고 RequestSender를 사용하여 코드를 더 테스트 가능하게 만들어 API를 점진적으로 개선 할 수도 있습니다.   
RequestSender를 자체 개체로 재작업하여 서버 세부 정보가 포함 된 API로 전달할 수도 있습니다.    
그런 다음 기존 테스트에서 MockAPI를 대체하여 더 좋고 포괄적인 단위 테스트를 작성할 수 있습니다.    
이렇게하면 라이브 서버에 완전히 접촉하기 위해 특성화 테스트가 필요하지 않습니다. 당신의 일은 결코 끝나지 않습니다.   

이 접근 방식에는 몇 가지 단점도 있습니다.    
많은 작은 프로토콜에 의해 도입된 간접적인 방법으로 인해 코드를 디버그하기가 더 어려워 질 수 있으므로 포괄적인 테스트를 수행하는 것이 중요합니다. 메서드를 돋보이게 할 때 이전 코드로 돌아가서 다시 방문하지 않으려는 유혹이 생길 수 있으며, 신규 사용자에게는 혼란스러울 수있는 상태로 앱을 남겨 둡니다.

이것으로 레거시 코드 튜토리얼의 끝입니다. 코드 재구성 및 격리를위한 더 많은 기술을 보려면 자습서 https://store.raywenderlich.com/products/design-patterns-by-tutorials의 디자인 패턴을 확인하십시오.




