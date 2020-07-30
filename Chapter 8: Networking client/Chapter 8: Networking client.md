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






```swift
let session: URLSession = URLSession(configuration: .default)
```

### _Getting started_
### _Getting started_
### _Getting started_
### _Getting started_
### _Getting started_
