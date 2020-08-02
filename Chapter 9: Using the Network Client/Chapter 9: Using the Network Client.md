# Chapter 9: Using the Network Client


In the last chapter, you identified that ListingsViewController isn’t actually doing any networking. Rather, it has a // TODO comment in refreshData().
In response, you created DogPatchClient to handle networking logic. However, you haven’t used it yet.
In this chapter, your job is to update ListingsViewController to use DogPatchClient to actually network! Specifically, you’ll:
• Add a shared instance to DogPatchClient.
• Add a property for the network client on ListingsViewController.
• Create a network client protocol.
• Create a mock network client using the protocol.
• Use the mock to stub and validate behavior.
GeXng started
Feel free to use your project from the last chapter. If you want a fresh start, navigate to this chapter’s starter directory, open the DogPatch subdirectory and then open DogPatch.xcodeproj.
Once your project is ready, it’s time to jump in and set DogPatchClient up for networking by adding a shared instance.
 raywenderlich.com 174
iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
Crea7ng a shared instance
While you could instantiate DogPatchClient directly, this has disadvantages:
1. You’d have to duplicate creation data, including the baseURL, session and
responseQueue, anywhere you instantiate DogPatchClient.
2. You’d make more network calls in parallel, since there’s a limit on the number of these you can run per URLSession. You could harm battery or network performance as a result.
A better alternative is to add a static shared property on DogPatchClient. This uses the singleton plus pattern: You’ll use the shared instance most of the time, but you can also create one-off DogPatchClient instances, such as in your unit tests.
Before you’re allowed to write app code, you first need to write a failing test. Open DogPatchClientTests.swift and add this test right before test_init_sets_baseURL(), ignoring the compiler error like usual:
 func test_shared_setsBaseURL() { // given
let baseURL = URL(
string: "https://dogpatchserver.herokuapp.com/api/v1/")!
// then
XCTAssertEqual(DogPatchClient.shared.baseURL, baseURL) }
You first create an expected baseURL, and you assert this equals DogPatchClient.shared.baseURL. Since you haven’t defined shared on DogPatchClient, however, this doesn’t compile.
A compiler error counts as a failing test, so you’re allowed to write app code to fix it. Open DogPatchClient.swift, and add the following right before
init(baseURL:session:responseQueue:):
Here you’ve defined a static shared property with dummy values for its inputs. This is enough to fix the compiler error in the unit tests.
 static let shared = DogPatchClient(
baseURL: URL(string:"https://example.com")!, session: URLSession(),
responseQueue: nil)
 raywenderlich.com 175

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
Build and run the unit tests and, as expected, this last test fails. That’s because the baseURL on DogPatchClient.shared is not equal to the expected baseURL. To make it pass, open DogPatchClient.swift and replace the baseURL input value on shared with the following:
 baseURL: URL( string:"https://dogpatchserver.herokuapp.com/api/v1/")!
 Warning: Due to the way URL(string:relativeTo:) resolves URLs, you must include the trailing slash at the end of the URL string. If you don’t, the URL created within getDogs won’t include the v1 in its path and, consequently, the server will not recognize it.
Build and run the unit tests; they should all pass now. However, you still need a couple more tests to ensure you’ve set the correct values for DogPatchClient.shared.
Add the following test below test_shared_setsBaseURL():
This test will evaluate the session property you created in shared. Build and run this test; you’ll see it fails because session on DogPatchClient.shared is not equal to URLSession.shared. To make it pass, open DogPatchClient.swift, and update the input parameter value for session to .shared. Build and run the tests again, and verify they all pass.
Finally, add the following test below test_shared_setsSession():
 func test_shared_setsSession() { // given
let session = URLSession.shared
// then
XCTAssertEqual(DogPatchClient.shared.session, session) }
 func test_shared_setsResponseQueue() { // given
let responseQueue = DispatchQueue.main
// then
XCTAssertEqual(DogPatchClient.shared.responseQueue, responseQueue)
}
 raywenderlich.com 176

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
This test will check the final property in the shared instance, responseQueue. Build and run this test, and you’ll see it fails because responseQueue on DogPatchClient.shared is currently set to nil. To fix this, open DogPatchClient.swift, and update the input parameter value for responseQueue to .main. Build and run the tests again to verify they all pass.
Ultimately, your static shared property inside DogPatchClient should look like this:
Adding a network client property
Next, you need to add a networkClient property to ListingsViewController. Before you can write app code, of course, you need a failing test.
Open ListingsViewControllerTests.swift and add the following right after // MARK: - Instance Properties - Tests:
You assert that sut.networkClient has pointer equality to DogPatchClient.shared. Since you haven’t defined networkClient on DogPatchClient, this test won’t compile yet.
To fix this, open ListingsViewController.swift and add the following property right after // MARK: - Instance Properties:
You declare this as a var to allow your tests to replace it with a mock object later on. By defining this property, you’ve also fixed the compiler error.
 static let shared =
  DogPatchClient(
baseURL: URL(string: "https://dogpatchserver.herokuapp.com/api/v1/")!,
session: .shared, responseQueue: .main)
 func test_networkClient_setToDogPatchClient() {
XCTAssertTrue(sut.networkClient === DogPatchClient.shared) }
 var networkClient =
DogPatchClient(baseURL: URL(string: "http://example.com")!,
                   session: URLSession(),
                   responseQueue: nil)
 raywenderlich.com 177

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
Build and run the unit tests and you’ll see the last test fails because networkClient isn’t set to DogPatchClient .shared. To make it pass, replace the declaration for var networkClient inside ListingsViewController.swift with the following:
var networkClient = DogPatchClient.shared Build and run your tests again to verify they all pass.
Using the network client
While you could use DogPatchClient directly in your unit tests, this has several drawbacks:
• You’d make real network calls, which would require an internet connection.
• The network calls would fail if an internet connection wasn’t available or the server was down.
• You wouldn’t be able to predict the network response in advance, so you couldn’t verify the values are what you expected.
• Your unit tests would be slow to run because each would need to wait for a network response.
Fortunately, there’s a better option: Use a mock network client. This lets you avoid making real network calls while completely controlling the response results.
There are two ways you can create a mock network client in Swift:
1. You can create a mock by subclassing DogPatchClient and overriding each of its methods. This works, but you may accidentally make real network calls if you forget to override a method. You may also cause side effects, such as caching fake network responses.
2. You can create a network client protocol and depend on this instead of DogPatchClient directly. You can create a mock object by implementing it. In turn, you eliminate the possibility of making real network calls or causing side effects. Nice! The main downside of this approach is that you must create an extra type for the protocol. However, this is usually very quick and easy to do.
In general, you should create a mock network client using a protocol instead of subclassing-and-overriding.
  raywenderlich.com 178

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
One reason you might choose to subclass-and-override over creating a protocol is if your app is tightly coupled to the network client or its related types. Even then, you should strive to replace it with a protocol in the long term.
Crea7ng the network client protocol
What should you put in the network client protocol? Any methods and properties that consumers need to use! In turn, you’ll be able to use your mock to validate that you’re calling these correctly.
Okay, that’s enough theory! You’re ready to TDD the protocol now.
As always, you’ll write a test first. Open DogPatchClientTests.swift and add the
following method, right before test_shared_setsBaseURL():
Here’s how this works, from the inside out:
• First, you cast sut as AnyObject to prevent a compiler warning later.
• Finally, you assert sut is DogPatchService.
This currently causes a compiler error because you haven’t defined DogPatchService. To fix this, open DogPatchClient.swift and add the following, right before the class declaration:
Build and run the unit tests and, as expected, the last test will fail. To make it pass, add the following to the end of DogPatchClient.swift, after the closing class curly brace:
extension DogPatchClient: DogPatchService { } Build and run the tests again, and verify they all pass.
 func test_conformsTo_DogPatchService() {
  XCTAssertTrue((sut as AnyObject) is DogPatchService)
}
 protocol DogPatchService {
}
  raywenderlich.com 179

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
This protocol isn’t very useful yet because it doesn’t have any methods. For this, add the following test below test_conformsTo_DogPatchService():
This test won’t compile because DogPatchService doesn’t know anything about getDogs. To fix this, adding the following inside the DogPatchService protocol:
Build and run your tests now, and they should all pass.
Are there any other properties or methods you should add to DogPatchService? For example, what about init(baseURL:session:responseQueue:) or the shared property?
No, you don’t need to add these because they are implementation details. A consumer doesn’t need to know how you constructed its dependency; they only need to know the behavior the dependency provides. This, in turn, defines which methods and properties go into the protocol.
For now, this one method is all you need in DogPatchService! Crea7ng the mock network client
You now need to create the mock network client. Your first step is to write a test for... Oh, wait! You don’t need a test. ;]
Your mock network client won’t be part of your production code. It enables you to write unit tests, and this, in turn, enables you to write app code. Okay, carry on then...!
Within DogPatchTests/Test Types, create a new group for Mocks and create a new Swift File called MockDogPatchService.swift within it. Then, right-click on Test Types and select Sort by Name.
 func test_dogPatchService_declaresGetDogs() { // given
let service = sut as DogPatchService
// then
_ = service.getDogs() { _, _ in } }
 func getDogs(completion:
@escaping ([Dog]?, Error?) -> Void) -> URLSessionDataTask
 raywenderlich.com 180

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
Your file hierarchy for DogPatchTests should look like this:
Replace the contents of MockDogPatchService.swift with the following:
  // 1
@testable import DogPatch
import Foundation
// 2
class MockDogPatchService: DogPatchService {
// 3
var getDogsCallCount = 0
var getDogsDataTask = URLSessionDataTask()
var getDogsCompletion: (([Dog]?, Error?) -> Void)!
// 4
func getDogs(
completion: @escaping ([Dog]?, Error?) -> Void) ->
URLSessionDataTask {
    getDogsCallCount += 1
    getDogsCompletion = completion
    return getDogsDataTask
} }
Here’s what you’ve done:
1. You declare @testable import DogPatch to import all of the public and internal types from DogPatch. You also import Foundation, which includes URLSessionDataTask.
2. You create a new type for MockDogPatchService that conforms to DogPatchService.
3. You add properties for getDogsCallCount, getDogsDataTask and getDogsCompletion. You’ll use them to verify the mock gets called as expected, and to return stubbed responses.
 raywenderlich.com 181

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
4. You implement getDogs(completion:), which DogPatchService requires. Whenever it’s called, you increment getDogsCallCount, set getDogsCompletion and return getDogsDataTask.
Fantastic, you implemented this mock like a pro! It mirrors how DogPatchClient works, but it allows you to fully control the response that’s returned, doesn’t require a network connection and doesn’t have any network delay. So now, it’s time to put it to work.
Using the mock network client
You’re finally ready to use the mock network client!
Open ListingsViewControllerTests.swift, and you’ll see that several tests are included for you for the existing functionality. Your job is to do TDD for refreshData().
Your first test will assert that the view controller holds onto the returned data task. To do this, add the following code right after test_viewWillAppear_calls_refreshData():
Here, you create mockNetworkClient and attempt to set this as sut.networkClient. Unfortunately, this causes a compiler error. What’s up with that?
Xcode actually gives a helpful error message:
The compiler expects networkClient to be of type DogPatchClient, yet you’re attempting to set it to MockDogPatchClient, which doesn’t inherit from DogPatchClient. To fix this error, you need to explicitly set the type of networkClient to be DogPatchService.
Replace this code within ListingsViewController: var networkClient = DogPatchClient.shared
 func test_refreshData_setsRequest() {
// given
let mockNetworkClient = MockDogPatchService() sut.networkClient = mockNetworkClient
}
 Cannot assign value of type 'MockDogPatchService' to type
'DogPatchClient'
  raywenderlich.com 182

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
With this instead:
var networkClient: DogPatchService = DogPatchClient.shared
Both MockDogPatchService and DogPatchClient conform to DogPatchService, so this eliminates the compiler error. However, you’ll notice that test_networkClient_setToDogPatchClient no longer compiles because Swift cannot use the identical-to operator, ===, to compare DogPatchClient and DogPatchService. To fix this, you need to cast the protocol type to the object type you want to compare. Replace the contents of test_networkClient_setToDogPatchClient with:
Run your tests and they should all pass again.
Now, add this next code within test_refreshData_setsRequest(), right before its closing method brace:
Since you haven’t declared dataTask on ListingsViewController, this doesn’t compile. To fix this, open ListingsViewController.swift and add the following right after var viewModels:
var dataTask: URLSessionDataTask?
This fixes the compiler error, so build and run the tests and verify that it fails. To
make it pass, you need to set dataTask whenever refreshData() is called. Replace the // TODO: - Write this comment inside refreshData() with the
following:
Build and run the tests again, and they’ll all pass.
  XCTAssertTrue((sut.networkClient as? DogPatchClient) === DogPatchClient.shared)
 // when
sut.refreshData()
// then
XCTAssertEqual(sut.dataTask, mockNetworkClient.getDogsDataTask)
  dataTask = networkClient.getDogs() { dogs, error in }
 raywenderlich.com 183

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
It’s possible that this code will call refreshData more than once in quick succession. For example, this could happen if the user "pulls to refresh" when a network call is already in progress.
However, if dataTask is already set, you don’t want to call getDogs multiple times. Add the following test to ensure you’re only calling getDogs once, even if refreshData is called in quick succession below test_refreshData_setsRequest():
func test_refreshData_ifAlreadyRefreshing_doesntCallAgain() { // given
let mockNetworkClient = MockDogPatchService() sut.networkClient = mockNetworkClient
// when
sut.refreshData() sut.refreshData()
// then
XCTAssertEqual(mockNetworkClient.getDogsCallCount, 1) }
This test calls refreshData twice in succession to simulate that scenario.
Build and run this test to verify it fails. To make it pass, open ListingsViewController.swift and add the following code just after the opening curly brace for refreshData():
guard dataTask == nil else { return }
This guard returns early if dataTask is not nil. Build and run your unit tests, and
they should all now pass.
Do you see anything that needs to be refactored? The app code looks fine, but what about the unit tests? Yep, you’ve duplicated the code for setting sut.networkClient to mockNetworkClient.
To eliminate this duplication, first add this new property right after the var sut line:
   var mockNetworkClient: MockDogPatchService!
 raywenderlich.com 184
iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
Next, add the following method right after the givenDogs(count:) method:
Then add the following within tearDown(), right after its opening method brace: mockNetworkClient = nil
This ensures mockNetworkClient is set to nil after each test run completes. Finally, replace the following two lines in both test_refreshData_setsRequest
and test_refreshData_ifAlreadyRefreshing_doesntCallAgain:
With this one line instead:
   givenMockNetworkClient()
This gets rid of the duplicate code. Now, build and run the tests to verify each still passes.
For the next test, you need to ensure that you set dataTask back to nil once the completion is called for getDogs. Add the following test below test_refreshData_ifAlreadyRefreshing_doesntCallAgain():
 func givenMockNetworkClient() {
  mockNetworkClient = MockDogPatchService()
sut.networkClient = mockNetworkClient }
  let mockNetworkClient = MockDogPatchService() sut.networkClient = mockNetworkClient
  func test_refreshData_completionNilsDataTask() { // given
// 1
givenMockNetworkClient()
  let dogs = givenDogs()
// when
// 2 sut.refreshData()
// 3
mockNetworkClient.getDogsCompletion(dogs, nil)
// then
// 4 XCTAssertNil(sut.dataTask)
}
 raywenderlich.com 185

iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
Here’s how this test works:
1. You make excellent use of your helper methods to create mockNetworkClient
and dogs.
2. You then call sut.refreshData() to set the dataTask.
3. You pass dogs to the getDogsCompletion closure on the mockNetworkClient. This executes the passed-in closure from ListingsViewController and should set the dataTask to nil.
4. You assert that the sut.dataTask is actually nil.
Build and run this test, and you’ll see it fails. Of course, that’s because you haven’t
actually set dataTask to nil within the getDogs completion closure.
To make this pass, add this line right inside the completion closure within
refreshData on ListingsViewController: self.dataTask = nil
Build and run the tests to verify the last one now passes.
You’re now finally ready to test the "happy path", which returns dogs successfully and sets it on the ListingsViewController. Add the following test below test_refreshData_completionNilsDataTask():
func test_refreshData_givenDogsResponse_setsViewModels() { // given
// 1
givenMockNetworkClient()
let dogs = givenDogs()
let viewModels = dogs.map { DogViewModel(dog: $0) }
// when
// 2
sut.refreshData() mockNetworkClient.getDogsCompletion(dogs, nil)
// then
// 3
XCTAssertEqual(sut.viewModels, viewModels)
}
Here’s how this test works:
1. First, you use your helper methods to create mockNetworkClient and dogs, then
you create viewModels by mapping each dog to a DogViewModel. raywenderlich.com 186
 
iOS Test-Driven Development by Tutorials Chapter 9: Using the Network Client
2. Next, you call sut.refreshData() and execute the getDogsCompletion with the given dogs.
3. Finally, you assert that sut.viewModels is equal to viewModels.
Build and run this test to verify it fails. You need to set viewModels on
ListingsViewController to make it pass.
Add the following right after dataTask = nil within the refreshData() on
ListingsViewController:
self.viewModels = dogs?.map { DogViewModel(dog: $0) } ?? []
This likewise calls map to turn dogs into a DogViewModel array. If there’s an error, dogs might be nil so you use the optional unwrap operator ? and provide the default value as an empty array.
Build and run your tests, and you’ll see this last one now passes. Is there anything more to refactor here? Well, maybe...
At first glance, the code between test_refreshData_completionNilsDataTask and test_refreshData_completionNilsDataTask looks similar. However, you’ve already factored out several helper methods, but you’re using them here.
You could try to refactor these tests further, but you’d likely make them harder to understand. Consequently, it’s okay to leave them as is!
For the next test, you need to actually reload the tableView after the viewModels are set. Add the following test below test_refreshData_givenDogsResponse_setsViewModels():
