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
