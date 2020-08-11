


# _Chapter 10: Image Client_

이 장에서 RESTful 네트워킹 클라이언트를 TDD하는 방법을 배웁니다. 구체적으로 다음을 수행합니다.
    
• 네트워킹 클라이언트를 설정하십시오.   
• 올바른 엔드 포인트가 호출되었는지 확인하십시오.    
• 네트워킹 오류, 유효한 응답 및 잘못된 응답을 처리합니다.    
• 결과를 응답 대기열로 발송합니다.    

### _Getting started_
이 장의 시작 디렉토리로 이동하면 DogPatch.xcodeproj를 포함하는 DogPatch 서브 디렉토리가 있습니다. 이 프로젝트 파일을 Xcode에서 열고 살펴보십시오.
몇 개의 파일이 이미 추가 된 것을 볼 수 있습니다. 이 장의 중요한 내용은 다음과 같습니다.    


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


In the last chapter, you used DogPatchClient to download and display dogs. Each Dog has an imageURL, but you haven’t used it so far. While you could download images by making network requests directly within ListingsViewController, you wouldn’t be able to use that logic anywhere else.
Instead, you’ll do TDD to create an ImageClient for handling images. You can use that ImageClient anywhere you need it in the app.
As you work through this chapter, you’ll:
• Set up the image client.
• Create an image client protocol.
• Download an image from a URL.
• Cache data tasks and images based on their URL.
• Set an image from a URL on an image view.
• Use the image client to display images.
GeXng started
Feel free to use your project from the last chapter. If you want a fresh start, navigate to this chapter’s starter directory, open the DogPatch subdirectory and then open DogPatch.xcodeproj.
 raywenderlich.com 193
iOS Test-Driven Development by Tutorials Chapter 10: Image Client
Your first step is going to be to get everything set up for your image client. Here’s how.
SeXng up the image client
Another developer (ahem, you’re welcome) has already done TDD for ImageClient and its properties. To keep the focus on new concepts, this section will fast-track you through adding this code.
Under DogPatch/Networking, create a new Swift File called ImageClient.swift and replace its contents with the following:
 // 1
import UIKit
class ImageClient {
// MARK: - Static Properties
// 2
static let shared = ImageClient(responseQueue: .main,
session: .shared) var cachedImageForURL: [URL: UIImage]
  var cachedTaskForImageView: [UIImageView: URLSessionDataTask]
  let responseQueue: DispatchQueue?
  let session: URLSession
// MARK: - Object Lifecycle
// 4
init(responseQueue: DispatchQueue?,
session: URLSession) { self.cachedImageForURL = [:]
self.cachedTaskForImageView = [:]
self.responseQueue = responseQueue
self.session = session }
}
// MARK: - Instance Properties // 3
Here’s what this does:
1. You first import UIKit to access UIImage and UIImageView, then you create a
new class for ImageClient.
raywenderlich.com 194
 
iOS Test-Driven Development by Tutorials Chapter 10: Image Client
2. You next declare a static property for shared. You’ll use this in your app code, but you’ll create one-off instances in your unit tests. This is just like
DogPatchClient.
3. You then declare two cache properties, cachedImageForURL and cachedTaskForImageView. You also declare one property for session, which you’ll use to make the networking calls, and one for responseQueue, which you’ll use to dispatch the results.
4. Last, you create an initializer that sets each property.
You also need to add the tests for this class. Under DogPatchTests/Cases/ Networking, create a new Swift File called ImageClientTests.swift and replace its contents with the following:
 // 1
@testable import DogPatch
import XCTest
class ImageClientTests: XCTestCase {
// 2
  var mockSession: MockURLSession!
  var sut: ImageClient!
// MARK: - Test Lifecycle // 3
override func setUp() {
super.setUp()
mockSession = MockURLSession()
sut = ImageClient(responseQueue: nil,
}
session: mockSession)
override func tearDown() { mockSession = nil
sut = nil super.tearDown()
}
// MARK: - Static Properties - Tests // 4
func test_shared_setsResponseQueue() {
XCTAssertEqual(ImageClient.shared.responseQueue, .main) }
func test_shared_setsSession() {
XCTAssertEqual(ImageClient.shared.session, .shared) }
 raywenderlich.com 195

iOS Test-Driven Development by Tutorials Chapter 10: Image Client
 // MARK: - Object Lifecycle - Tests
// 5
func test_init_setsCachedImageForURL() {
XCTAssertEqual(sut.cachedImageForURL, [:]) }
func test_init_setsCachedTaskForImageView() {
XCTAssertEqual(sut.cachedTaskForImageView, [:]) }
func test_init_setsResponseQueue() {
XCTAssertEqual(sut.responseQueue, nil) }
func test_init_setsSession() {
XCTAssertEqual(sut.session, mockSession) }
}
Here’s how this works:
1. You import both DogPatch and XCTest and then create a test class for
ImageClientTests.
2. You declare two instance properties: mockSession keeps hold of a MockURLSession, which you’ll use instead of making real networking calls and sut keeps hold of the ImageClient you’re testing.
3. You set each instance property within setUp() and nil them within tearDown().
4. You create tests that validate that the shared instance has expected values.
5. Lastly, you add tests to validate that the initializer sets properties like you expected.
There’s a bit of final clean up you need to do. Under DogPatchTests/Test Types/ Mocks, create a new Swift File called MockSession.swift and another file called MockURLSessionDataTask.swift.
Open DogPatchClientTests.swift and cut (or copy and delete) the entire MockURLSession class, then paste it into MockSession.swift, right after import Foundation. Likewise, cut and paste the entire MockURLSessionDataTask class into MockURLSessionDataTask.swift.
This makes it clear that MockURLSessionDataTask and MockURLSessionDataTask are separate types from DogPatchClientTests. It’s best to move these, now that you’ll be using these mocks in more than one test case.
 raywenderlich.com 196

iOS Test-Driven Development by Tutorials Chapter 10: Image Client
Build and run your tests to verify that they all pass.
Wow, you covered a lot in short amount of time! While this code is definitely important, you learned how to do TDD for this in previous chapters. You’re now ready to dive into new concepts for this chapter!
Crea7ng an image client protocol
Similar to DogPatchClient, you’ll create a protocol for the ImageClient to enable you to mock and verify its use.
As always, you first need to write a failing test. Add the following to ImageClientTests, right after the last test method:
You cast sut as AnyObject to prevent a compiler warning and then assert this conforms to ImageService. However, this doesn’t compile because you haven’t declared ImageService.
To fix this, add the following to the top of ImageClient.swift after the imports:
Build and run the tests to validate the last one fails.
To make it pass, add the following after the class closing curly brace for
ImageClient:
Build and run the tests again to verify the last one now passes. There’s nothing to refactor, so you can simply continue.
You next need a test to define the downloadImage method signature. Add this right after the last test:
 // MARK: - ImageService - Tests
func test_conformsTo_ImageService() {
  XCTAssertTrue((sut as AnyObject) is ImageService)
}
 protocol ImageService {
}
 // MARK: - ImageService
extension ImageClient: ImageService {
}
 raywenderlich.com 197

iOS Test-Driven Development by Tutorials Chapter 10: Image Client
func test_imageService_declaresDownloadImage() {
// given
let url = URL(string: "https://example.com/image")! let service = sut as ImageService
// then
_ = service.downloadImage(fromURL:url) { _, _ in } }
You create service by casting sut as ImageService and then call service.downloadImage to verify the method exists.
Since you’ve yet to declare this method, this causes a compiler error. Add the following code within ImageService to fix this:
You also need to make ImageClient implement this method to make it conform to ImageService. Add the following inside the extension on ImageClient:
You return a new URLSessionDataTask() because this is the simplest way to make it compile.
Build and run the tests again to verify they all pass. Lastly, you need one more method, to set an image onto an image view from a URL. Add this test next:
func downloadImage(
fromURL url: URL,
completion: @escaping (UIImage?, Error?) -> Void) -> URLSessionDataTask
func downloadImage(
fromURL url: URL,
completion: @escaping (UIImage?, Error?) -> Void)
-> URLSessionDataTask { return URLSessionDataTask()
}
func test_imageService_declaresSetImageOnImageView() { // given
let service = sut as ImageService
let imageView = UIImageView()
let url = URL(string: "https://example.com/image")! let placeholder = UIImage(named: "image_placeholder")!
// then
service.setImage(on: imageView, fromURL: url,
}
withPlaceholder: placeholder)
 


