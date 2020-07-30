# _Chapter 8: Networking client_

이 장에서 RESTful 네트워킹 클라이언트를 TDD하는 방법을 배웁니다. 구체적으로 다음을 수행합니다.
    
• 네트워킹 클라이언트를 설정하십시오.   
• 올바른 엔드 포인트가 호출되었는지 확인하십시오.    
• 네트워킹 오류, 유효한 응답 및 잘못된 응답을 처리합니다.    
• 결과를 응답 대기열로 발송합니다.    

### _Getting started_
이 장의 시작 디렉토리로 이동하면 DogPatch.xcodeproj를 포함하는 DogPatch 서브 디렉토리가 있습니다. 이 프로젝트 파일을 Xcode에서 열고 살펴보십시오.
몇 개의 파일이 이미 추가 된 것을 볼 수 있습니다. 이 장의 중요한 내용은 다음과 같습니다.    

• Controllers / ListingsViewController.swift는 가져온 도그 또는 오류를 표시하는 뷰 컨트롤러를 포함합니다.   
• Models / Dog.swift는 각 강아지를 나타내는 Dog 모델을 포함합니다.
또한 네트워킹을 위한 빈 그룹이 표시됩니다. 이것은 네트워킹 클라이언트 및 관련 유형를 포함합니다   

앱을 빌드하고 실행하면 다음과 같은 오류 메시지 화면이 나타납니다.

![image](https://user-images.githubusercontent.com/60660894/88854006-9bf25c80-d22b-11ea-818b-1d2137175f84.png)

아래로 당겨 새로 고치면 활동 표시기가 애니메이션되지만 완료되지 않습니다.       
ListingsViewController.swift를 열면 tableView (_ : numberOfRowsInSection :)이 1을 반환하도록 하드 코딩 된 것을 볼 수 있습니다.     
tableView (_ : cellForRowAt :) 내에서 viewModels.count가 0보다 큰지 확인합니다. 앱이 viewModels를 설정하지 않기 때문에 항상 false입니다. 오히려 네트워킹 응답에서이를 생성해야합니다.     
그러나 // TODO에 대한 의견이 있습니다 : refreshData ()를 작성하십시오. 그래서 앱은 네트워크 호출을 하지 않습니다.        
이제 업무가 명확 해졌습니다. 

네트워킹 호출을 하려면 코드를 작성해야합니다!       
ListingsViewController 내에서 이를 일회성 네트워킹 호출로 직접 작성할 수 있지만이 뷰컨트롤러는 빠르게 커질 것입니다.        
더 나은 옵션은 모든 네트워킹 논리를 처리하는 별도의 네트워킹 클라이언트를 만드는 것입니다. 이것이 이장의 초점입니다!       



### _Setting up the networking client_
프로덕션 코드를 작성하기 전에 먼저 실패한 테스트를 작성해야합니다.       
DogPatchTests / Cases / Networking 내에서 DogPatchClientTests.swift라는 새 Swift 파일을 작성하십시오. 현재 컴파일러 오류를 무시하고 내용을 다음으로 바꾸십시오.

```swift
@testable import DogPatch
import XCTest
class DogPatchClientTests: XCTestCase {
  var sut: DogPatchClient!
}
```

DogPatchClient 유형의 sut에 대한 단일 특성으로 DogPatchClientTests에 대한 새 테스트 클래스를 작성했습니다. 그러나 실제로 DogPatchClient를 작성하지 않았으므로이 코드는 컴파일되지 않습니다. 컴파일러 오류는 테스트 실패로 계산되므로 이제 프로덕션 코드를 작성할 수 있습니다.
DogPatch / Networking 내에서 DogPatch.swift라는 새 Swift 파일을 작성하고 그 내용을 다음으로 바꾸십시오.

```swift 
import Foundation
class DogPatchClient {

}
```

DogPatchClient에 대한 새 클래스를 선언했으며 결과적으로 컴파일러 오류를 수정합니다. 리팩토링 할 것이 없으므로 첫 번째 테스트 방법으로 넘어갈 수 있습니다.
DogPatchClientTests.swift를 열고 sut 선언 아래에 컴파일러 오류를 무시하고 다음을 추가하십시오.

```swift
func test_init_sets_baseURL() {
    // given
    let baseURL = URL(string: "https://example.com/api/v1/")!
    // when
      sut = DogPatchClient(baseURL: baseURL)
}
```

궁극적으로 이니셜 라이저로 전달되는 baseURL이 sut.baseURL과 일치하는지 테스트하고 싶습니다. 그러나 실제로이 초기화 프로그램을 만들지 않았으므로 컴파일되지 않습니다. 이 문제를 해결하려면 DogPatch를 열고 DogPatchClient에 다음을 추가하십시오.

```swift
let baseURL = URL(string: "https://example.com/")! 

init(baseURL: URL) {

}
```

여기서 baseURL을 선언하고 지금은 임의의 값으로 설정 한 다음 init (baseURL :)를 작성하십시오. 이것은 테스트를 컴파일하기에 충분하지만 실제로는 아직 아무것도 주장하지 않았습니다. DogPatchClientTests.swift를 열고 테스트 메소드 끝에 다음을 추가하십시오. 

```swift
// then
XCTAssertEqual(sut.baseURL, baseURL)
```
이 주장은 sut.baseURL이 이니셜 라이저에 전달 된 인수와 같아야한다는 기대치를 설정합니다. 단위 테스트를 빌드하고 실행하면이 테스트가 예상대로 실패하는 것을 볼 수 있습니다. 이것을 전달하려면 DogPatchClient 내의 let baseURL = 행을 다음과 같이 바꾸십시오.

```swift
let baseURL: URL
```
그런 다음 init (baseURL :)에 다음을 추가하십시오.

```swift
self.baseURL = baseURL
```
이제 baseURL 인스턴스 속성이 초기화 프로그램에 의해 설정되었습니다. 테스트를 빌드하고 실행하십시오.
이제 통과해야합니다. 리팩토링 할 것이 없으므로 계속 진행할 수 있습니다.
또한 URLSession에 대한 속성이 필요합니다. URLSession은 네트워킹 호출에 사용됩니다. 컴파일러 오류를 무시하고 이전 테스트 직후에 다음 테스트를 추가하십시오.

```swift
func test_init_sets_session() {
    // given
    let baseURL = URL(string: "https://example.com/api/v1/")! let session = URLSession.shared
    // when
      sut = DogPatchClient(baseURL: baseURL, session: session)
}

```
이 테스트의 목적은 초기화 프로그램을 확장하여 다른 속성을 설정하는 것입니다. 이전과 마찬가지로 세션의 속성을 선언하지 않았으므로 컴파일되지 않습니다. 이 문제를 해결하려면 DogPatchClient에서 baseURL 바로 뒤에 다음 특성을 추가하십시오.

```swift
let session: URLSession = URLSession(configuration: .default)
```
다음으로 init (baseURL :)의 메소드 서명을 다음으로 업데이트하십시오.
```swift
init(baseURL: URL, session: URLSession)
```
이를 통해 test_init_sets_session ()을 컴파일 할 수 있지만 test_init_sets_baseURL ()이 중단됩니다. 이 문제를 해결하려면 test_init_sets_baseURL () 내의 let baseURL 줄 바로 아래에 다음 줄을 추가하십시오.
```swift
let session = URLSession.shared
```
그런 다음 sut = 줄을 다음과 같이 업데이트하십시오.
```swift
sut = DogPatchClient(baseURL: baseURL, session: session)
```
이제 테스트가 다시 컴파일되지만 실제로 어설 션을 추가하지 않았습니다.
test_init_sets_session (). 테스트 방법 끝에 다음을 추가하십시오.
```swift
// then
XCTAssertEqual(sut.session, session)
```
테스트를 빌드하고 실행하면 예상대로이 테스트가 실패합니다. 전달하려면 DogPatchClient 내의 세션에 대한 특성 선언을 다음으로 변경하십시오.
```swift
let session: URLSession
```
그런 다음이 줄을 이니셜 라이저 끝에 추가하십시오.
```swift
self.session = session
```
테스트를 빌드하고 실행하면 이제 모두 통과해야합니다. 이번에는 리팩토링을해야합니다. test_init_sets_baseURL () 및 test_init_sets_session () 내의 처음 몇 줄은 정확히 동일합니다. 이 문제를 해결하려면 먼저 클래스 상단에 var sut 바로 앞에 다음 속성을 추가하십시오.
```swift
var baseURL: URL!
var session: URLSession!
```
그런 다음 속성 바로 다음에이 두 가지 방법을 추가하십시오.
```swift
override func setUp() {
    super.setUp()
    baseURL = URL(string: "https://example.com/api/v1/")! session = URLSession.shared
    sut = DogPatchClient(baseURL: baseURL, session: session)
}
override func tearDown() { 
    baseURL = nil
    session = nil
    sut = nil super.tearDown()
}
```
setUp 내에서 각 속성을 설정하고 tearDown 내에서 각 속성을 무효화합니다. 이를 통해 두 테스트의 중복성을 줄일 수 있습니다.
이제 테스트 메소드 내에서 중복 로직을 제거 할 수 있습니다. test_init_sets_baseURL ()의 내용을 다음으로 바꾸십시오.
```swift
XCTAssertEqual(sut.baseURL, baseURL)
```
그런 다음 test_init_sets_session ()의 내용을 다음으로 바꾸십시오.
```swift
XCTAssertEqual(sut.session, session)
```
테스트를 빌드하고 실행하십시오. 각 테스트는 여전히 통과해야합니다.
훌륭한 직업, 당신은 두 가지 속성을 선언했습니다! 좋아, 아마 그렇게 흥미롭지 않을 수도 있습니다. 그러나 이러한 속성은 네트워킹 호출에 중요하며 실제로 해당 코드를 작성할 수 있습니다!

### _TDDing the networking call_

서버에서 Dog 객체 목록을 가져 오려면 GET 요청을해야합니다. 이것을 몇 가지 작은 작업으로 나눕니다.
1. 올바른 URL을 호출합니다.
오류 응답 처리.
3. 성공에 대한 역 직렬화 모델.
4. 잘못된 응답 처리.

### _Calling the right URL_

올바른 URL을 호출해야합니다. 불행히도 URLSession은 실제로 어떤 URL이 호출되었는지 확인할 수있는 방법이 없습니다. 가장 쉬운 방법은 URLSession을 서브 클래 싱을 통해 조롱하는 것입니다. 단위 테스트에서 실제 네트워킹 호출이 이루어지지 않도록 URLSessionDataTask도 모의합니다.
DogPatchClientTests.swift에서 DogPatchClientTests 뒤에 다음과 같은 새로운 하위 클래스를 추가하십시오.


```swift
// 1
class MockURLSession: URLSession {
  override func dataTask(
    with url: URL,
    completionHandler:
    @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask {
          return MockURLSessionDataTask(
            completionHandler: completionHandler,
            url: url)
} }
// 2
class MockURLSessionDataTask: URLSessionDataTask {
    var completionHandler: (Data?, URLResponse?, Error?) -> Void
    var url: URL
    init(completionHandler:
    @escaping (Data?, URLResponse?, Error?) -> Void,
    url: URL) {
    self.completionHandler = completionHandler self.url = url
    super.init()
}
// 3
  override func resume() {
    // don't do anything
} }
```

수행 한 작업은 다음과 같습니다.
1. MockURLSession을 URLSession의 하위 클래스로 생성하고 dataTask (url :, completionHandler :)를 재정 의하여 MockURLSessionDataTask를 반환합니다.
2. MockURLSessionDataTask를 URLSessionDataTask의 서브 클래스로 작성하고 url 및 completionHandler에 대한 특성을 선언하고이를 초기화 프로그램 내에 설정하십시오. 이를 통해 테스트 내에서 이러한 값을 사용할 수 있습니다.
3. MockURLSessionDataTask가 실제 네트워크 요청을하지 않도록하려면 resume ()을 대체하여 아무 것도 수행하지 않습니다.        
        
실제 URLSession을 DogPatchClient로 전달하는 대신 MockURLSession 인스턴스를 전달합니다.
분명히하기 위해 이것은 모의입니다. DogPatchClientTests에서 세션 특성을 마우스 오른쪽 단추로 클릭하고 리 팩터-> 이름 바꾸기를 선택한 후 이름을 mockSession으로 변경하십시오. 그런 다음 var mockSession 줄을 다음으로 바꾸고 컴파일러 오류는 무시하십시오.

```swift
var mockSession: MockURLSession!
```
이렇게하면 유형이 MockURLSession으로 변경되지만 설정 위치를 업데이트해야합니다.
setUp () 내에서. setUp 내의 mockSession = 행을 다음과 같이 바꾸십시오.

```swift
mockSession = MockURLSession()
```
이제 테스트 내에서이 속성을 사용할 수 있습니다. 컴파일러 오류를 무시하고 기존 테스트 바로 다음에 다음 테스트를 추가하십시오.

```swift
func test_getDogs_callsExpectedURL() {
    // given
    let getDogsURL = URL(string: "dogs", relativeTo: baseURL)!
    // when
    let mockTask = sut.getDogs() { _, _ in } as! MockURLSessionDataTask
}
```
이름에서 알 수 있듯이이 테스트는 getDogs 메소드가 특정 URL을 호출하는지 확인합니다. 이 테스트는 아직 getDogs를 선언하지 않았기 때문에 컴파일되지 않습니다. 이렇게하려면 DogPatchClient에 다음을 추가하십시오.

```swift
func getDogs(completion: @escaping ([Dog]?, Error?) -> Void) -> URLSessionDataTask {
    return session.dataTask(with: baseURL) { _, _, _ in } 
}
```
이 메소드는 session.dataTask (with : completionHandler :)를 호출하여 테스트 코드를 컴파일합니다.

당신은 이제 올바른 URL이 호출되었는지 확인하기 위해 실패한 테스트 어설션이 필요하다. 테스트 방법의 끝에 다음을 추가한다.
```swift
// then
XCTAssertEqual(mockTask.url, getDogsURL)
```
이 테스트 어설션에 실패하므로 이제 생산 코드를 작성하여 올바른 URL을 호출할 수 있다. DogPatchClient 내의 getDogs(완료:)의 내용을 다음과 같이 교체하십시오.
```swift
let url = URL(string: "dogs", relativeTo: baseURL)! 
return session.dataTask(with: url) { _, _, _ in }
```

테스트를 만들고 실행하면 모두 통과해야 한다.
URLSession은 생성된 후 네트워킹 작업을 시작하지 않는다. 대신, 당신은 필수 사항이다.
작업을 시작하기 위해 resume를 요청한다.
너는 이것이 완료되었는지 검증하는 테스트 방법이 필요하다. 이것을 쓰기 전에 교체하십시오.
MockURLSessionDataTask 내에서 다음 작업을 resume()하십시오.

```swift
var calledResume = false
override func resume() {
  calledResume = true
}
```
여기서 false로 기본 설정되는 resume에 대해 새로운 부울을 선언하고 resume() 내에서 true로 설정하십시오. 당신은 이제 이것을 사용하는 시험 방법을 쓸 수 있다. 마지막 테스트 방법 뒤에 다음을 추가하십시오.
```swift
func test_getDogs_callsResumeOnTask() { // when
    let mockTask = sut.getDogs() { _, _ in } as! MockURLSessionDataTask
    // then
    XCTAssertTrue(mockTask.calledResume) 
}
```
구축 및 실행하면 이 테스트가 예상대로 실패하는 것을 볼 수 있을 것이다. 통과하려면 DogPatchClient에서 getDogs(completion:) 내의 리턴 라인을 다음과 같이 교체하십시오.
```swift
let task = session.dataTask(with: url) { data, response, error in }
task.resume() 
return task
```

```swift
let session: URLSession = URLSession(configuration: .default)
```
테스트를 만들고 실행하십시오. 이제 테스트를 통과하십시오. 여기서는 리팩터링할 것이 없으니, 계속합시다!

### _Handling error responses_

다음 과제는 오류 응답을 처리하는 것이다. 오류가 발생했음을 나타내는 시나리오는 다음과 같이 두 가지가 있다.
1. 서버는 200 외에 HTTP 상태 코드를 반환한다. 이 끝점은 성공적이면 항상 200을 돌려준다. 다른 상태 코드가 반환되면 요청이 실패함
2. 요청이 서버에 도달하지 못하거나, 시간이 초과되거나, 네트워킹 계층에서 다른 오류 조건이 발생할 수 있다. 이 경우에는 오류가 설정될 것이다.
첫 번째 시나리오를 확인하는 테스트부터 시작할 겁니다. 마지막 테스트 뒤에 다음 테스트를 추가하십시오.

```swift
func test_getDogs_givenResponseStatusCode500_callsCompletion() { // given
    let getDogsURL = URL(string: "dogs", relativeTo: baseURL)! let response = HTTPURLResponse(url: getDogsURL,
    // when
    var calledCompletion = false
    var receivedDogs: [Dog]? = nil
    var receivedError: Error? = nil
    let mockTask = sut.getDogs() { dogs, error in calledCompletion = true
    receivedDogs = dogs
    receivedError = error
    } as! MockURLSessionDataTask mockTask.completionHandler(nil, response, nil)
    // then
      XCTAssertTrue(calledCompletion)
      XCTAssertNil(receivedDogs)
      XCTAssertNil(receivedError)
}
```
네가 한 짓은 다음과 같다.     
• 주어진 범위 내에서 getDogs를 사용하여 응답 생성오류를 나타내는 URL 및 HTTP 상태 500.             
• 언제 완료 마감 호출 여부 및 반환 값을 저장할 변수를 생성하고, 그 다음 mockTask에서 완료 핸들을 호출하십시오.       
• 이때 완료 핸들러가 호출되었으며, 개와 오류에 대해 수신된 값이 영이라고 주장하십시오.      
테스트를 작성하고 실행하십시오. 예상대로 이 테스트는 완료 핸들러가 호출되지 않아 실패함.      
이 문제를 해결하려면 DogPatchClient의 getDogs 내에서 session.dataTask(with: url)에 대한 폐쇄 내부에 다음을 추가하십시오.     

```swift
guard let response = response as? HTTPURLResponse, 
response.statusCode == 200 else { completion(nil, error)
return
}
```
이 가드 문장은 상태 코드가 예상된 200개 결과인지 확인하고, 그렇지 않을 경우 완료 처리기를 호출한다.         
테스트를 작성하고 실행하십시오. 이제 테스트를 통과하십시오. 리팩터링 할 거 있어? 그래, 겟도그스URL은 두 가지 테스트에서 정확히 동일하다.        
        
이 중복을 제거하려면 DogPatchClientTests에서 sut 선언 바로 뒤에 다음 계산 속성을 추가하십시오.
```swift
var getDogsURL: URL {
  return URL(string: "dogs", relativeTo: baseURL)!
}
```
그런 다음 test_getDogs_callsExpectedURL에서 지정된 섹션 전체를 삭제하십시오.        
let getDogsURL 라인을 삭제test_getDogs_givedResponseStatusCode500_callsCompletion  

테스트를 만들고 실행하십시오. 테스트는 모두 계속 통과하십시오.         
당신이 처리해야 할 또 다른 오류 시나리오는 오류가 반환된 경우 입니다. 다음 테스트 케이스를 추가하여 확인하십시오.       


```swift
func test_getDogs_givenError_callsCompletionWithError() throws { // given
    let response = HTTPURLResponse(url: getDogsURL,
                                     statusCode: 200,
                                     httpVersion: nil,
                                     headerFields: nil)
    let expectedError = NSError(domain: "com.DogPatchTests", code: 42)
    // when
      var calledCompletion = false
      var receivedDogs: [Dog]? = nil
      var receivedError: Error? = nil
    let mockTask = sut.getDogs() { dogs, error in calledCompletion = true
    receivedDogs = dogs
    receivedError = error as NSError?
    } as! MockURLSessionDataTask mockTask.completionHandler(nil, response, expectedError)
    // then
      XCTAssertTrue(calledCompletion)
      XCTAssertNil(receivedDogs)
      let actualError = try XCTUnwrap(receivedError as NSError?)
  XCTAssertEqual(actualError, expectedError)
}
```
네가 한 짓은 다음과 같다.
        
• 지정된 기간 내에 상태코드 200과 예상 오류의 응답을 생성하십시오. "성공" 응답 코드가 200이 될 것 같지도 않고 오류도 있을 것 같지 않다. 하지만 아마도 서버가 잘못 작동하고 있거나, 현실에서 어떤 종류의 에지 케이스에 부딪혔을 겁니다. 이봐, 서버 개발자들도 완벽하지 않아. 그러나 실용적으로, 이것은 이 경우에 코드를 유발하지 않는 상태를 당신의 이전의 보호가 보장한다.        
        
• 언제 완료가 호출되었는지 여부와 수신된 값을 확인하기 위해 변수를 설정하십시오. 그런 다음 mockTask에서 recompleteHandler를 호출하여 응답 및 이전 예상Error를 확인하십시오.        
        
• 그 안에서 당신은 완성을 부르고, 받은 개는 영(0)이며, 오류는 예상한 것과 일치한다고 주장한다.       
        
테스트를 작성하고 실행하면 이 코드를 아직 작성하지 않았기 때문에 예상되는 완료 및 수신 해제 에러라는 두 가지 주장이 모두 실패하는 것을 볼 수 있다. 또한 일시적으로 빈 도그 배열로 수신된 도그 할당을 변경하여 XCTAssertNil(수신된 도그)이 실패함을 증명할 수 있지만 계속하기 전에 이 속성을 다시 0으로 설정하십시오.
        
모든 어설트를 통과시키려면 DogPatchClient의 getDogs에 있는 전체 가드 라인을 다음과 같이 교체하십시오.

```swift
guard let response = response as? HTTPURLResponse, response.statusCode == 200,
error == nil else {
```
테스트를 만들고 실행하십시오. 이제 모두 통과하십시오. 하지만, 이 테스트와 이전 테스트 사이에는 많은 코드 중복이 있다.
이 문제를 해결하려면 공통 코드에 대한 도우미 방법을 꺼내십시오. 여러 테스트에서 호출되므로 해체 직후에 다음 방법을 추가하십시오.
```swift
func whenGetDogs(data: Data? = nil, statusCode: Int = 200, error: Error? = nil) -> (calledCompletion: Bool, dogs: [Dog]?, error: Error?) {
    let response = HTTPURLResponse(url: getDogsURL,
                                   statusCode: statusCode,
                                   httpVersion: nil,
                                   headerFields: nil)
    var calledCompletion = false
    var receivedDogs: [Dog]? = nil
    var receivedError: Error? = nil
    let mockTask = sut.getDogs() { dogs, error in calledCompletion = true
    receivedDogs = dogs
    receivedError = error as NSError?
          } as! MockURLSessionDataTask
    mockTask.completionHandler(data, response, error)
        return (calledCompletion, receivedDogs, receivedError)
}
```
작동 방법은 다음과 같다.
        
• 이 방법은 데이터, statusCode 및 오류에 대한 입력을 허용하며, 편의상 각 입력에 적절한 기본값을 제공한다. 그것은 completion, dog 및 error에 대한 값이 있는 튜플을 반환한다.     
• getDogs를 사용하여 응답 생성URL 및 전달된 상태코드.        
• 로컬 변수를 생성하고, getDogs on sut을 호출하고, 이전의 시험방법이 그랬던 것처럼, mockTask에 대한 완성 핸들러.        
• 마지막으로, 로컬 변수에서 생성된 튜플을 callCompletion, receivedDogs 및 receivedError.      

이 방법을 사용하여 테스트 방법에서 중복 코드를 제거할 수 있다.        
먼저 test_getDogs_givedResponseStatusCode500_callsCompletion의 내용을 다음과 같이 교체한다.
```swift
// when
let result = whenGetDogs(statusCode: 500)
// then
XCTAssertTrue(result.calledCompletion) XCTAssertNil(result.dogs) XCTAssertNil(result.error)
```
이 방법은 현재 대부분의 작업이 whenGetDogs안에서 이루어지기 때문에 크게 단순화된다.
다음으로 test_getDogs_givedError_callsCompletionWithError with this:의 내용을 바꾸십시오.

```swift
// given
let expectedError = NSError(domain: "com.DogPatchTests", code: 42)
// when
let result = whenGetDogs(error: expectedError)
// then
XCTAssertTrue(result.calledCompletion) XCTAssertNil(result.dogs)
let actualError = try XCTUnwrap(result.error as NSError?) XCTAssertEqual(actualError, expectedError)
```
이 방법은 마찬가지로 크게 단순화되었으며, 현재는 예상 에러 설정 및 올바르게 반환되었는지 테스트하는 데에만 고유한 부품만 취급한다.
테스트를 만들고 실행하면 모두 계속 통과해야 한다. 그것은 훌륭한 리팩터였고, 곧 있을 당신의 테스트는 분명 이 도우미 방법을 잘 활용할 것이다!


### _Deserializing Dog models_

마침내 성공적인 대응을 하면서 happy-path사건을 처리할 준비가 되었다.
하기 전에, 당신이 알아야 할 프로젝트에 이미 있는 편의 증설이 있다. Open Data+JSONFile.swift을 열면, 너는 JSON(fileName:file:line:) 데이터를 가져오는 정적 방법을 가지고 있음을 알 수 있다.

이것은 시험 방법에서 사용하기 위한 것이다. 파일을 찾을 수 없으면 어설션에 실패하고 XCERTnwrap을 통해 예외를 발생시킨다. 예를 들어, 추가되지 않았거나 메서드에 잘못된 파일 이름이 입력된 경우.
또한 친절한 개발자 동료인 - 천만에요;] - 이미 GET_Dogs_Response.json이라는 테스트 데이터 파일을 제공했다.
이 정보로 무장한 당신은 해피패스 테스트를 쓸 준비가 되셨군요! 이전 테스트 직후에 다음을 추가하십시오.
```swift
func test_getDogs_givenValidJSON_callsCompletionWithDogs() throws {
    // given
    let data =
    try Data.fromJSON(fileName: "GET_Dogs_Response")
    let decoder = JSONDecoder()
    let dogs = try decoder.decode([Dog].self, from: data)
    // when
        let result = whenGetDogs(data: data)
    // then
    XCTAssertTrue(result.calledCompletion) XCTAssertEqual(result.dogs, dogs) XCTAssertNil(result.error)
}
```
이것이 하는 일은 다음과 같다.
• 먼저 Data.from JSON에 지정된 JSON 파일 이름으로 전화를 걸어 데이터를 생성하십시오.       
• JSONDecoder 유형의 디코더를 새로 생성하여 데이터를 디코딩하는 데 사용하십시오. 이것은 Dog가 이미 디코더블을 준수하고 있으며, DogTests.swift 내에서 작동하는 것을 검증하는 테스트가 이미 있기 때문에 가능하다.        
• 그런 다음 다른 테스트 방법과 마찬가지로 GetDogs를 호출하지만, 이번에는 데이터를 전송한다.        
• 당신은 마지막으로 완성이 호출되고, 개는 결과.dogs와 동일하며, 그 결과.error는 nil이라고 주장한다.        
테스트를 작성하고 실행하면 예상대로 이 테스트가 실패한다는 것을 알 수 있을 것이다. 통과하려면 DogPatchClient에서 getDogs(완료:) 내의 가드 문을 다음과 같이 교체하십시오.

```swift
guard let response = response as? HTTPURLResponse, 
response.statusCode == 200, error == nil,
let data = data else {
```
여기서 차이점은 당신이 데이터를 가드가 통과할 수 있는 조건으로 추가했다는 것이다.
그런 다음 가드 블록의 닫힌 곱슬 브레이스 뒤에 다음을 추가하십시오.
```swift
let decoder = JSONDecoder()
let dogs = try! decoder.decode([Dog].self, from: data) completion(dogs, nil)
```
시도해라! 여기 진술은 위험해 보이고, 확실히... 그러나 이것은 시험을 통과하기 위한 최소 코드의 양이며, 또 다른 시험이 필요하다는 지표다.
유닛 테스트를 만들고 실행하면 모두 통과해야 한다. 리팩터링 할 건 없지만, 그 시도는 버려야 해!
어떤 조건에서 이것이 문제가 될 것인가? 서버가 200개의 응답을 반환했지만 JSON을 Dogs로 구문 분석할 수 없는 경우, 앱이 다운될 수 있다.
다행히 단위 테스트가 정확히 어떤 문제를 잡아내고 예방에 도움이 될 수 있는 유형이다. 이전 테스트 후에 다음 테스트를 추가하여 정확한 시나리오를 작성하십시오.
```swift
func test_getDogs_givenInvalidJSON_callsCompletionWithError() throws {
    // given
    let data = try Data.fromJSON(
    fileName: "GET_Dogs_MissingValuesResponse")
      var expectedError: NSError!
      let decoder = JSONDecoder()
      do {
    _ = try decoder.decode([Dog].self, from: data) } catch {
        expectedError = error as NSError
      }
    // when
      let result = whenGetDogs(data: data)
    // then
    XCTAssertTrue(result.calledCompletion) XCTAssertNil(result.dogs)
    let actualError = try XCTUnwrap(result.error as NSError?) XCTAssertEqual(actualError.domain, expectedError.domain)
    XCTAssertEqual(actualError.code, expectedError.code) 
}
```
이것이 하는 일은 다음과 같다.
• GET_Dogs_MissingValuesResponse 파일에서 데이터를 설정하십시오. 이것은 유효하다.
JSON 어레이. 하지만 Dog 개체를 역직렬화하는 데 필요한 ID가 누락됨.     
• 그런 다음 JSONDecoder 유형의 디코더를 생성하고 탈직렬화를 시도하십시오.
데이터. 예상 에러로 던져지는 오류를 캡처하는 것이다.      
• GetDogs를 호출한 다음 완료가 호출되었고, 반환된 개는 0이며, 오류는 예상된 Error와 동일한 도메인 및 코드를 가지고 있다고 주장하는 경우. 오류 개체가 직접 비교할 수 없기 때문에 NSError에 대한 캐스팅이 필요하다. NSError에 캐스팅하면 오류에 대한 도메인과 코드를 서로 비교할 수 있는데, 이는 동일한 오류를 나타내기에 충분하다.
테스트를 작성하고 실행하십시오. 이 시험은 실패할 뿐만 아니라, 추락하기도 해! 음, 코드는 생산으로 보내진 후가 아니라 TDD를 하면서 이걸 잡아서 다행이지, 그렇지?
이 문제를 해결하려면 DogPatchClient 내에서 이러한 라인을 교체하십시오.

```swift
let dogs = try! decoder.decode([Dog].self, from: data) completion(dogs)
```
대신 다음 코드를 사용하십시오.

```swift
do {
let dogs = try decoder.decode([Dog].self, from: data) completion(dogs, nil)
} catch {
  completion(nil, error)
}
```
장치 테스트를 만들고 다시 실행하십시오. 이제 모두 통과하십시오.


### _Dispatching to a response queue_

당신의 DogPatchClient는 사장처럼 네트워킹을 처리하고 있다! 단 한 가지 문제가 있는데, 실제 네트워킹 전화가 걸려오는 것을 막기 위해 URLSessionDataTask를 조롱해왔지만, 불행하게도 URLSessionDataTask의 행동도 숨겼다.

URLSessionDataTask는 실제로 백그라운드 대기열에서 닫기를 호출한다. 이것은 앱이 Dogs 또는 Error 결과를 사용하여 UI 작업을 수행해야 하며, 이것은 Main 대기열에서 수행해야 하기 때문에 문제가 된다.

메인 큐에 디스패치하는 것을 소비자에게 맡길 수 있지만, 이것은 문제를 밀어내고 네트워킹 클라이언트를 소비하기 어렵게 만든다. 더 나은 설계는 DogPatchClient가 응답Queue를 수락하고 발송을 처리하도록 하는 것이다. 응답Queue를 선택 사항으로 설정하여 기존 장치 테스트를 중단하지 않고도 이 작업을 수행할 수 있다.


### _Adding a response queue_

지금은 컴파일러 오류를 무시하고 test_init_sets_session() 직후에 다음 테스트를 추가하십시오.
```swift
func test_init_sets_responseQueue() {
    // given
    let responseQueue = DispatchQueue.main
    // when
      sut = DogPatchClient(baseURL: baseURL,
                           session: mockSession,
}
```
DogPatchClient에서 실제로 responseQueue를 정의하지 않았으므로 이 테스트는 현재 컴파일되지 않는다. 아, 아까 이 춤을 추셨군요!;;] 오류를 수정하려면 다음 속성을 DogPatchClient에 다른 속성 뒤에 추가하십시오.
```swift
let responseQueue: DispatchQueue? = nil
```
그런 다음 결과 컴파일러 오류를 무시하고 초기화를 위한 서명을 이것으로 대체하십시오. 유닛 테스트에서:
```swift
 init(baseURL: URL,
     session: URLSession,
     responseQueue: DispatchQueue?)
```
setup에서 설정 sut을 업데이트해야 하므로 단위 테스트가 컴파일되지 않는 경우. 이 라인을 다음으로 교체하십시오.

```swift
sut = DogPatchClient(baseURL: baseURL,
                     session: mockSession,
                     responseQueue: nil)
```
마지막으로 test_init_sets_responseQueue()의 끝에 다음 코드를 추가한다.
```swift 
// then
XCTAssertEqual(sut.responseQueue, responseQueue)
```
테스트를 작성하고 실행하며 예상대로 이 새로운 테스트 방법은 실패할 것이다. 이 문제를 해결하려면 DogPatchClient 내의 let responseQueue 라인을 다음으로 교체하십시오.
```swift
let responseQueue: DispatchQueue?
```
다음으로, 초기화 내에 이 줄을 추가하십시오.
```swift
self.responseQueue = responseQueue
```
테스트를 만들고 다시 실행하십시오. 이제 모두 통과하십시오.

### _Updating the mocks_

다음 번에 MockURLS세션 및 MockURLSessionDataTask를 업데이트하여 디스패치 대기열에서 완료 처리기를 호출하십시오. 테스트를 작성하고 실행하며 예상대로 이 새로운 테스트 방법은 실패할 것이다. 이 문제를 해결하려면 DogPatchClient 내의 let responseQueue 라인을 다음으로 교체하십시오.
```swift
var queue: DispatchQueue? = nil
```
다음으로 바로 아래에 이 방법을 추가하십시오.
```swift
func givenDispatchQueue() {
    queue = DispatchQueue(label: "com.DogPatchTests.MockSession") 
}
```
기존 테스트 방법은 이 대기열이 필요하지 않으므로 다음에 추가할 새로운 테스트 방법에 대해서만 이 대기열을 호출하십시오.
MockURLSessionDataTask의 이니셜라이저 시그니처도 변경해야 한다. 지금은 컴파일러 오류를 무시하고 다음을 대체하십시오.
```swift
init(completionHandler:
@escaping (Data?, URLResponse?, Error?) -> Void,
  url: URL,
  queue: DispatchQueue?)
```
그런 다음 다음 다음 라인을 교체하십시오.
```swift
self.completionHandler = completionHandler
```
대신 이 코드를 사용하여:
```swift
if let queue = queue {
self.completionHandler = { data, response, error in
queue.async() {
      completionHandler(data, response, error)
    }
}
} else {
self.completionHandler = completionHandler }
```
대기열이 이 이니셜라이저로 전달되는 경우, 완료 핸들을 호출하기 전에 대기열에 비동기식으로 발송하도록 self.completionHandler를 설정하십시오.        
이는 실제 URLDataTask가 디스패치 대기열에 디스패치하는 방식과 유사하다.
컴파일러 오류를 수정하려면 MockURLSession의 dataTask(url:completionHandler:) 내에서 반환 문을 다음과 같이 교체하십시오.
```swift
return MockURLSessionDataTask(
  completionHandler: completionHandler,
  url: url,
  queue: queue)
```
이렇게 하면 대기열이 MockURLSessionDataTask의 새 이니셜라이저로 전달된다. 장치 테스트를 작성하고 실행하십시오. 어떤 테스트도 어떤 대기열에 따라 달라지지 않기 때문에 completion 핸들러를 호출한다. 그들은 모두 계속 통과해야 한다.


### _Handling dispatch scenarios_

다음에는 완료 핸들이 응답 큐에 파견되었는지 확인하십시오. 이 경우 다음과 같은 상황이 발생해야 한다.
1. HTTP 상태 코드는 실패 응답을 나타낸다.
2. HTTP 오류가 수신된다.
3. 유효한 JSON 응답을 수신하여 역직렬화에 성공한다.
4. 부적합한 JSON 응답이 수신되고, 탈직렬화가 실패한다.
첫 번째 경우 기존 테스트 다음에 다음 테스트를 추가하십시오.
```swift
func test_getDogs_givenHTTPStatusError_dispatchesToResponseQueue() { // given
    mockSession.givenDispatchQueue()
    sut = DogPatchClient(baseURL: baseURL,
                           session: mockSession,
    responseQueue: .main) let expectation = self.expectation(
        description: "Completion wasn't called")
    // when
    var thread: Thread!
    let mockTask = sut.getDogs() { dogs, error in
    thread = Thread.current expectation.fulfill()
    } as! MockURLSessionDataTask
      let response = HTTPURLResponse(url: getDogsURL,
                                     statusCode: 500,
                                     httpVersion: nil,
    headerFields: nil) mockTask.completionHandler(nil, response, nil)
    // then
    waitForExpectations(timeout: 0.2) { _ in
    XCTAssertTrue(thread.isMainThread) }
}
```                       
이 코드의 작동 방식은 다음과 같다.        
• 지정된 섹션 내에서 MockSession.givedDispatchQueue를 호출하여 MockSession에 대기열을 설정하십시오. 또한 응답Queue를 DogPatchClient로 전달하는 .main을 생성하십시오. 마지막으로 기대치를 생성하면 나중에 완료 핸들을 호출할 때까지 대기할 때 사용할 수 있다.      
기술적으로, 당신은 어떤 응답Queue도 사용할 수 있었다. 그러나 실용적으로 완성 핸들러는 주 대기열로 파견될 필요가 있을 것이다. 안타깝게도 iOS는 현재 어떤 대기열에서 코드가 실행 중인지 파악하는 것을 어렵게 한다... 오, 애플! 이게 유닛 테스트에 필요한 거 몰라?!     
다행히 현재의 스레드가 메인 스레드인지 확인하기가 쉽고, 메인 디스패치 대기열은 항상 메인 스레드에서 실행된다. 따라서, 당신의 테스트는 이 사실에 의존하여 코드가 "메인 큐에 디스패치"되었는지 검증할 것이다. 사실, 물론, 당신은 기술적으로 그 코드가 메인 스레드에서 실행되는지 확인하고 있다. Apple에 비해 어떤 디스패치 대기열을 사용하는지 쉽게 테스트하고 검증할 수 있는 "충분히 좋은" 기능.       
• 시기 내에 먼저 스레드에 대한 로컬 변수를 만든 다음 sut.getDogs()를 호출하십시오. 완료 핸들러 내에서 스레드를 설정하고 기대치를 충족하십시오. 그런 다음 오류 상태 코드가 500인 반응 변수를 생성하고 이를 사용하여 completeHandler를 호출하십시오.      
• waitForExpections를 호출하여 기대치가 달성될 때까지 기다리십시오. 대기 핸들러 내부에서 스레드가 주 나사산이라고 주장하는 경우.
후우, 거긴 좀 와퍼 테스트였어!;]
        
유닛 테스트를 작성하고 실행하면, 이 테스트가 실패하는 것을 알 수 있을 것이다.
현재 DogPatchClient의 responseQueue로 발송.
        
이 문제를 해결하려면 DogPatchClient의 getDogs(completion:) 내에서 이 라인을 교체하십시오.
```swift
let task = session.dataTask(with: url) { data, response, error in
```
다음 코드를 사용하여 일단 경고를 무시하십시오.
```swift
let task = session.dataTask(with: url) { [weak self] data, response, error in
guard let self = self else { return }
```
[weak self]와 가드를 사용하여 이러한 방식으로 자아를 보호함으로써, 그대신 자아를 직접 참조했다면 가능한 강력한 기준 사이클을 만들지 못하게 된다.
다음으로, 응답 폐쇄를 허용한 가드 내에서 발견된 이 코드의 첫 번째 인스턴스를 교체하십시오.
```swift
completion(nil, error)
```
다음 코드 포함:
```swift
guard let responseQueue = self.responseQueue else { completion(nil, error)
    return
}
responseQueue.async {
  completion(nil, error)
}
```
이는 responseQueue가 설정되었는지 확인하고, 설정되면 completion로 call을 발송한다.
유닛 테스트를 만들고 실행하면 모두 통과해야 한다. 아직 리팩터링할 것이 없으므로 다음 시나리오 테스트로 이동하여 HTTP 오류가 응답 대기열에서 전송되도록 하십시오.
        
이전 테스트 다음에 다음 테스트를 추가하십시오.
```swift
func test_getDogs_givenError_dispatchesToResponseQueue() { // given
    mockSession.givenDispatchQueue()
    sut = DogPatchClient(baseURL: baseURL,
    session: mockSession, responseQueue: .main)
    let expectation = self.expectation( description: "Completion wasn't called")
    // when
    var thread: Thread!
    let mockTask = sut.getDogs() { dogs, error in
    thread = Thread.current expectation.fulfill()
    } as! MockURLSessionDataTask
      let response = HTTPURLResponse(url: getDogsURL,
                                     statusCode: 200,
                                     httpVersion: nil,
    headerFields: nil)
    let error = NSError(domain: "com.DogPatchTests", code: 42)
    mockTask.completionHandler(nil, response, error)
    // then
    waitForExpectations(timeout: 0.2) { _ in
    XCTAssertTrue(thread.isMainThread) }
}
```
이번 시험은 이전 시험과 매우 비슷하다. 차이점은 mockTask.completionHandler에 오류를 전달하는 시간 섹션이다.
테스트를 만들고 실행하십시오. 놀랍게도 이 테스트는 실제로 통과하십시오!왜 그래?
getDogs 내에서 오류 및 HTTP 상태 코드에 대한 검사가
사실 같은 경비원의 진술의 일부인데, 이것은 다음과 같이 보인다.
```swift
guard let response = response as? HTTPURLResponse, response.statusCode == 200,
error == nil,
let data = data else {
```
결과적으로, 이것은 우연하게도 이미 응답 큐에 오류를 전송한다.

이것은 이 시험이 유용하지 않다는 것을 의미하는가? 아니, 아직 쓸모가 있어. 나중에 이 코드를 리팩터링하고 이 체크가 현재와 같은 가드로 결합되지 않은 경우에도 응답 큐에서 오류가 전송되는지 확인하십시오. 따라서 이 테스트는 그대로 두고 리팩터링으로 넘어가면 된다.
여기에 리팩터링해야 할 코드가 있다. 이 두 시험 사이에는 엄청난 양의 중복 코드가 있다. 이 문제를 해결하려면 GetDogs(...) 직후 파일 상단에 다음 도우미 방법을 추가하십시오.

```swift
func verifyGetDogsDispatchedToMain(data: Data? = nil,
                                   statusCode: Int = 200,
                                   error: Error? = nil,
                                   line: UInt = #line) {
    mockSession.givenDispatchQueue()
    sut = DogPatchClient(baseURL: baseURL,
    session: mockSession, responseQueue: .main)
    let expectation = self.expectation( description: "Completion wasn't called")
    // when
    var thread: Thread!
    let mockTask = sut.getDogs() { dogs, error in
    thread = Thread.current expectation.fulfill()
    } as! MockURLSessionDataTask
      let response = HTTPURLResponse(url: getDogsURL,
                                     statusCode: statusCode,
                                     httpVersion: nil,
    headerFields: nil) mockTask.completionHandler(data, response, error)
    // then
    waitForExpectations(timeout: 0.2) { _ in
    XCTAssertTrue(thread.isMainThread, line: line) }
}
```
이 방법은 데이터, statusCode 및 오류에 대한 입력을 허용한다. 이는 시험방법이 검증하고자 하는 실제 행동에 따라 달라진다. 또한 이 도우미 방법 자체 대신 XCTAssertTrue가 테스트 방법 라인 번호에 오류를 지정하는 데 사용되는 라인에 대한 입력을 허용한다.
이제 이 도우미 방법을 사용하여 중복된 코드를 제거할 수 있다. test_getDogs_gived의 내용 바꾸기HTTPStatusError_dispatchToResponseQueue를 사용하여 다음을 수행하십시오.
```swift
verifyGetDogsDispatchedToMain(statusCode: 500)
```
```swift
let task = session.dataTask(with: url) { data, response, error in
```
```swift
let task = session.dataTask(with: url) { data, response, error in
```
```swift
let task = session.dataTask(with: url) { data, response, error in
```




### _Getting started_
