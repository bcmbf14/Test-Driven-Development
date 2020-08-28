# _Chapter 2: The TDD Cycle_
    
이전 장에서 TDD가 4단계의 주기가 있다는 것을 배웠습니다. 그것은 색상으로 구분됩니다.  
        
![image](https://user-images.githubusercontent.com/60660894/91355821-b1a67200-e829-11ea-92e0-b607b5bb4049.png)
    
1. Red : 앱 코드를 작성하기 전에 실패한 테스트를 작성합니다.    
2. Green : 테스트를 통과하기위한 최소한의 코드를 작성합니다.   
3. Refactor : 앱과 테스트 코드를 모두 정리합니다.    
4. Repeat : 모든 기능이 구현 될 때까지이주기를 다시 수행합니다.   
    
이것을 Red-Green-Refactor Cycle 이라고도합니다. 이것이 이런 색상으로 구분되는 이유는 통상적으로 Xcode를 포함한 대부분의 코드 편집기에 표시되는 색상에 해당하기 때문입니다. 
    
### _Getting started_
    
이 장에서는 TDD Cycle을 배우기 위해 간단한 CashRegister를 만들겁니다. TDD에 집중하기 위해 플레이그라운드를 사용합니다. 고고!!
여기서 CashRegister는 한국말로하면 금전등록기, 이해하기 쉽게는 편의점에 있는 POS기를 말합니다. POS기의 원리?를 모르고 코드를 해석하면 개인적으로는 좀 헷갈렸어요. 다행히도 제가 편의점, 식당등의 아르바이트를 좀 해봐서 그런지 겨우겨우 이해했습니다. 

### _현금등록기(POS기)_
![image](https://user-images.githubusercontent.com/60660894/91504217-71b5bc80-e907-11ea-8aad-bcc09f1eb772.png)
> POS기에는 시재라고 하는 이용가능한금액이 있습니다. 손님이 현금을 내면 잔돈을 거슬러줘야하므로 존재하는 돈입니다.      
> 시재가 100원이 있을때, 손님이 물건을 가져가고 현금을 40원 줬다면 시재는 140원으로 증가합니다. 당연하게도 현금을 받았으니까요.       
> 그리고 현금등록기에는 받은 현금을 합산할 수 있는 키(버튼)이 존재합니다. 50원/100원/1,000원/10,000원/50,000원과 같은 식으로요. 천원버튼을 두번 누르면 화면에 받은 현금 2천원이라는 표시가 뜨고, 바코더?를 이용하거나 다른 버튼으로 물건값 얼마를 입력하면 잔돈 혹은 거슬러줘야되는 돈이 화면에 나타납니다.     
> 이 프로젝트에서는 availableFunds가 시재이고, transactionTotal가 거스름돈 혹은 잔돈, payment가 물건 값 이라고 이해하면 편합니다.        

    
### _Red: Write a failing test_
    
```swift
import Foundation
import XCTest


class CashRegister {
    
}

class CashRegisterTests: XCTestCase {
    
    
    func testInit_createsCashRegister(){
        XCTAssertNotNil(CashRegister())
    }
    
}

CashRegisterTests.defaultTestSuite.run()
```
    
1. import XCTest를 추가하고 XCTestCase의 하위클래스를 생성합니다. 
> XCTestCase 클래스는 XCTest 프레임워크의 일부이며, 거의 대부분 XCTestCase를 하위클래스로 테스트 클래스를 생성합니다. 
2. CashRegisterTests.defaultTestSuite.run()을 작성합니다.
> 이것은 CashRegisterTests 내에 정의 된 테스트 메소드를 실행하도록 플레이 그라운드에 지시합니다.
3. func testInit_createsCashRegister() 메소드를 작성합니다. 
> 실행할 테스트는 프로덕션코드 클래스 CashRegister를 테스트하기위해 CashRegisterTests라고 명명하며, XCTestCase을 상속받습니다. 또한 실행할 테스트 메소드를 작성하는데 구분을 위한 언더바를 기준으로 앞에는  " _test + 테스트할 분석법_ "  뒤에는  " _예상되는 결과_ " 를 적어줍니다.        
4. XCTAssertNotNil(CashRegister())
> 예상되는 결과값을 확인하는 메소드로 XCT로 시작하는 유형이 많다. XCTAssertEqual, XCTAssertTrue 등이 있는데 위 코드의 경우엔 매개변수로 들어가는 값이 nil이 아니다 라는 것을 확인해주는 메소드이다.            
5. class CashRegister {}
> 처음 CashRegister 클래스를 작성하지 않았을 때에는 테스트를 실행해도 CashRegister 클래스를 만들지 않았기 때문에 컴파일 에러가 발생합니다. 다행?스럽게도 컴파일 실패는 TDD의 빨간색 단계로 인정되기 때문에 다음인 녹색단계로 넘어가면 됩니다.        
    
위처럼 작성을 마치고 테스트를 실행해보면 아래와 같이 콘솔창에 나오게 됩니다.
    
> Test Suite 'CashRegisterTests' started at 2020-08-27 08:52:27.866         
> Test Case '-[__lldb_expr_34.CashRegisterTests testInit_createsCashRegister]' started.         
> Test Case '-[__lldb_expr_34.CashRegisterTests testInit_createsCashRegister]' passed (0.077 seconds).      
> Test Suite 'CashRegisterTests' passed at 2020-08-27 08:52:27.944.     
> Executed 1 test, with 0 failures (0 unexpected) in 0.077 (0.078) seconds          
    
테스트를 통과했고, 0개의 실패와 걸린 시간 등이 나오게 되죠. 이제 녹색 단계를 지난겁니다. 그렇다면 이제 다음 단계인 리팩토링을 해야겠죠? 
    
### _Refactor: Clean up your code_
    
리팩터링 단계에서 프로덕션 코드와 테스트 코드를 모두 정리합니다. 이 방법은 코드를 지속적으로 유지하고 개선 할 수 있습니다.    
다음은 리팩토링 할 수있는 몇 가지 사항입니다.    

- 중복 : 중복되는 프로퍼티, 메소드, 클래스를 제거합니다.
- 주석 : 코드의 작동방식을 설명하는 주석을 제거하세요. 큰 메소드를 여러개의 잘 명명된 메소드로 나누고 프로퍼티와 메소드의 이름을 더 명확하게 바꾸거나 떄로는 단순히 코드를 더 잘 구성하여 전달하면 됩니다.
- 냄새나는 코드 : 때때로 그냥 딱 봐도 해당 코드 블록이 좀 이상하게 보일 때가 있습니다. 너무많은 if문, 하드코딩된 문자열 같은 것들 말이죠. 이것들을 제거합니다.
    
지금은 로직이 많지 않아 리팩토링 할 것이 없으므로 넘어갑니다. 마지막 단계인 반복입니다.    
    
### _Repeat: Do it again_
    
이제 처음부터 다시 시작하면 됩니다. 우리는 CashRegister를 만들려고 했으므로 그에 맞는 추가로 만들어야할 요구사항들을 정리해봅니다. 
    
- availableFunds를 허용하는 이니셜 라이저를 작성하십시오.
- 트랜잭션에 추가하는 addItem 메서드를 작성 합니다.
- acceptPayment 메소드를 작성하십시오. 
        
차례차례 하나씩 만들어볼까요~
    
### _TDDing init(availableFunds:)_
    
```swift

class CashRegister {
    var availableFunds: Decimal
    
    init(availableFunds : Decimal = 0) {
        self.availableFunds = availableFunds
    }
        
}

class CashRegisterTests: XCTestCase {
    
    
    func testInit_createsCashRegister(){
        XCTAssertNotNil(CashRegister())
    }
    
    func testInitAvailableFunds_setsAvailableFunds() {
        // given
        let availableFunds = Decimal(100)
        
        // when
        let sut = CashRegister(availableFunds: availableFunds)
        
        //then
        XCTAssertEqual(sut.availableFunds, availableFunds)
    }
    
}

CashRegisterTests.defaultTestSuite.run()

```

1. testInitAvailableFunds_setsAvailableFunds()
> 새로운 테스트 메소드를 만듭니다. 이름을 보니 availableFunds이 init됐는지 확인하는 테스트겠네요. 
2. given, when, then 
> 이전 테스트보다 복잡하니 메소드를 3단계로 나누어 줍니다. 특정 조건이 주어지면(given), 특정 때(when)에 행동이 발생하고, 그리고 나서(then) 특정 조건과 예상되는 결과를 비교합니다.(sut는 통상적으로 사용되는 변수명으로 system under test의 약자다.) 
3. init(availableFunds : Decimal = 0) {}
> class CashRegister에 Decimal타입의 변수 availableFunds를 만들고 init을 바꿔줍니다. 초기값을 주지 않으면 testInit_createsCashRegister에서 컴파일 에러가 발생하니 초기값도 0으로 세팅해줍니다. 
    
> Test Case '-[__lldb_expr_53.CashRegisterTests testInit_createsCashRegister]' started.     
> Test Case '-[__lldb_expr_53.CashRegisterTests testInit_createsCashRegister]' passed (0.086 seconds).      
> Test Case '-[__lldb_expr_53.CashRegisterTests testInitAvailableFunds_setsAvailableFunds]' started.        
> Test Case '-[__lldb_expr_53.CashRegisterTests testInitAvailableFunds_setsAvailableFunds]' passed (0.004 seconds).     
> Test Suite 'CashRegisterTests' passed at 2020-08-27 11:16:36.623.     
>   	 Executed 2 tests, with 0 failures (0 unexpected) in 0.089 (0.090) seconds      
    
2 개의 테스트가 실행됐고, 실패는 없습니다. 애초에 테스트 메소드를 만들고 init(availableFunds:)이 없어 컴파일에러가 났을테니 레드케이스였고, 해당 클래스를 수정해주어 그린케이스가 나왔습니다. 
        
테스트가 통과했으니 다시 리팩토링을 해보죠~ 
        
일단, init()이 없어지고 availableFunds가 생겼죠? 그 말은 testInit_createsCashRegister가 필요없어졌다는 것을 뜻합니다. 그러니 일단 testInit_createsCashRegister는 제거를 해줘야겠죠. 그리고 init(availableFunds : Decimal = 0)에서 초기값을 0으로 주었습니다. 이것은 컴파일에러를 막기위한 임시방편이긴 했습니다만 지금도 정상작동하죠. 이 기본값을 그대로 둘지 아니면 없앨지는 프로젝트의 설계에 따라 달라집니다. 일단 여기서는 제거하겠습니다. 

```swift

class CashRegister {
    var availableFunds: Decimal
    
    init(availableFunds : Decimal) {
        self.availableFunds = availableFunds
    }
        
}

class CashRegisterTests: XCTestCase {
    
    func testInitAvailableFunds_setsAvailableFunds() {
        // given
        let availableFunds = Decimal(100)
        
        // when
        let sut = CashRegister(availableFunds: availableFunds)
        
        //then
        XCTAssertEqual(sut.availableFunds, availableFunds)
    }
    
}

CashRegisterTests.defaultTestSuite.run()

```
        
그리고 다시 테스트를 실행하면 정상적으로 통과하는 것을 볼 수 있습니다. 여기서 알 수 있는 것은 뭘까요? 바로 "프로덕션 코드를 수정했음에도 테스트가 통과한다." 라는 겁니다. 이 말을 다르게 해석하면, "기존 기능을 손상시키지 않았다."는 거죠. 이제 다음 단계로 넘어가도 되겠습니다. 
        
### _TDDing addItem_
        
```swift

class CashRegister {
    var availableFunds: Decimal
    var transactionTotal: Decimal = 0
    
    init(availableFunds : Decimal) {
        self.availableFunds = availableFunds
    }
    
    func addItem(_ cost: Decimal){
        transactionTotal = cost
    }
        
}

class CashRegisterTests: XCTestCase {
    
    func testInitAvailableFunds_setsAvailableFunds() {
        // given
        let availableFunds = Decimal(100)
        
        // when
        let sut = CashRegister(availableFunds: availableFunds)
        
        //then
        XCTAssertEqual(sut.availableFunds, availableFunds)
    }
    
    func testAddItem_oneItem_addsCostToTransactionTotal(){
        // given
        let availableFunds = Decimal(100)
        let sut = CashRegister(availableFunds: availableFunds)
        
        let itemCost = Decimal(42)
        
        // when
        sut.addItem(itemCost)
        
        //then
        XCTAssertEqual(sut.transactionTotal, itemCost)
    }
    
}

CashRegisterTests.defaultTestSuite.run()

```
        
항상 그렇듯이 실패한 케이스 먼저 작성하였습니다. 1. testAddItem_oneItem_addsCostToTransactionTotal() 메소드 먼저 생성하고, 주어진 조건을 지정했습니다. 그리고 addItem이라는 메소드를 만들어서 조건에 맞는 비용이 더해지면 거래토탈금액과 비교하는 코드입니다. 
        
transactionTotal 변수와 addItem 메소드가 없어 컴파일 에러가 발생하므로 class CashRegister도 수정해주었습니다. 테스트가 정상적으로 통과합니다. 
        
다시 리팩토링 ㄱㄱ 

1. var availableFunds: Decimal!, var sut: CashRegister!
> 중복이 있습니다. class CashRegisterTests 내에 추가합니다.
2. setUp(), tearDown()
> 테스트를 원활하게 할 수 있도록 XCTestCase에서는 메소드를 제공합니다. setUp()과 tearDown()도 그 중에 하나인데요.setUp()은 각 테스트 메소드가 실행되기 직전에 호출되고, tearDown()은 각 테스트 메소드가 완료된 직후에 호출됩니다. 이런 메소드는 중복된 로직을 처리하기 아주 좋은 곳이죠.       
> 먼저 super.setUp()을 실행하고 테스트가 실행되기 전에 초기값을 주어야 하므로 setUp()값을 설정합니다. 그러나 tearDown()에서는 setUp()에서 설정한 값들에 대해 꼭 nil을 할당해주어야 합니다. 그리고 마지막에 super.tearDown()을 실행합니다.     
> setUp()내에서 설정한 tearDown()내의 모든 속성은 항상 nil이어야합니다. 이는 XCTest 프레임워크가 작동하는 방식 때문입니다. 테스트 대상 내에서 각 XCTestCase 하위 클래스를 인스턴스화 하고 모든 테스트 케이스가 실행될 때까지 릴리스하지 않습니다. 따라서 테스트 케이스가 많고 tearDown 내에서 해당 속성을 nil로 설정하지 않으면 필요한 것보다 더 오래 속성의 메모리를 보유하게됩니다. 충분한 테스트 케이스가 주어지면 테스트를 실행할 때 메모리 및 성능 문제가 발생할 수도 있습니다.     
> testInitAvailableFunds_setsAvailableFunds 메소드가 한줄로 줄었고, testAddItem_oneItem_addsCostToTransactionTotal 메소드도 한결 간단해졌네요. 리팩토링 후의 코드는 아래와 같습니다.         

```swift 


class CashRegister {
    var availableFunds: Decimal
    var transactionTotal: Decimal = 0
    
    init(availableFunds : Decimal) {
        self.availableFunds = availableFunds
    }
    
    func addItem(_ cost: Decimal){
        transactionTotal = cost
    }
    
}

class CashRegisterTests: XCTestCase {
    
    var availableFunds:Decimal!
    var sut:CashRegister!
    
    override func setUp() {
        super.setUp()
        availableFunds = 100
        sut = CashRegister(availableFunds: availableFunds)
    }

    override func tearDown() {
        availableFunds = nil
        sut = nil
        super.tearDown()
    }
    
    func testInitAvailableFunds_setsAvailableFunds() {
        XCTAssertEqual(sut.availableFunds, availableFunds)
    }
    
    func testAddItem_oneItem_addsCostToTransactionTotal(){
        // given
        let itemCost = Decimal(42)
        
        // when
        sut.addItem(itemCost)
        
        // then
        XCTAssertEqual(sut.transactionTotal, itemCost)
    }
    
}

CashRegisterTests.defaultTestSuite.run()

```


### _Adding two items_
        
```swift

class CashRegister {
    var availableFunds: Decimal
    var transactionTotal: Decimal = 0
    
    init(availableFunds : Decimal) {
        self.availableFunds = availableFunds
    }
    
    func addItem(_ cost: Decimal){
        transactionTotal += cost
    }
    
}

class CashRegisterTests: XCTestCase {
    
    var availableFunds:Decimal!
    var itemCost:Decimal!
    var sut:CashRegister!
    
    override func setUp() {
        super.setUp()
        availableFunds = 100
        itemCost = 42
        sut = CashRegister(availableFunds: availableFunds)
    }

    override func tearDown() {
        availableFunds = nil
        itemCost = nil
        sut = nil
        super.tearDown()
    }
    
    func testInitAvailableFunds_setsAvailableFunds() {
        XCTAssertEqual(sut.availableFunds, availableFunds)
    }
    
    func testAddItem_oneItem_addsCostToTransactionTotal(){
        // when
        sut.addItem(itemCost)
        // then
        XCTAssertEqual(sut.transactionTotal, itemCost)
    }
    
    func testAddItem_twoItems_addCostsToTransactionTotal(){
        // given
        let itemCost2 = Decimal(20)
        let expectedTotal = itemCost + itemCost2
        
        // when
        sut.addItem(itemCost)
        sut.addItem(itemCost2)
        
        // then
        XCTAssertEqual(sut.transactionTotal, expectedTotal)
    }
    
}

CashRegisterTests.defaultTestSuite.run()

```
        
1. func testAddItem_twoItems_addCostsToTransactionTotal()
> 새로운 메소드를 추가합니다. 
2. sut.addItem(itemCost), sut.addItem(itemCost2)
> 메소드를 완성하고 테스트를 진행하면 실패합니다. addItem을 두 번 호출하는데 값이 누적되지 않는 것이죠. 
3. transactionTotal += cost
> addItem 메소드 내부를 일부 수정합니다. 이제 테스트가 통과됩니다. 
4. itemCost 리팩토링  
> 중복값이 있습니다. 이전에 했던 것처럼 CashRegisterTests에 변수를 만들고 setUp()에서 초기값을 지정해줍니다. 이후에는 당연히 tearDown에도 nil값으로 할당해줍니다. 
5. addItem 리팩토링?
> 중복되는 것이 또 보입니다. sut.addItem()입니다. 이것도 중복되니 리팩토링해야 할까요?        
> 정답은 "No"입니다. 이유는 해당 메소드는 func testInitAvailableFunds_setsAvailableFunds()에서는 쓰이지 않고 있죠. 그리고 계속 테스트를 진행하다보면 sut.addItem()를 호출할 필요가 없는 다른 메소드를 작성할 수도 있습니다. 따라서 해당 메소드를 중복 제거할 필요는 없습니다.        
        
### _Challenge_
### _acceptPayment_
        
지금까지 CashRegister를 TDD를 이용해서 구현해봤습니다. 하지만 아직 지불을 수락하는 방법을 하지 않았으므로 마저 진행해보겠습니다. 간단함을 위해 신용카드나 IOU(i owe you, 차용증서)는 구현하지 않습니다. 처음에 코드 내용이 잘 이해가지 않을 수 있어요. 그러면 맨 위에 현금등록기(POS기)에 대해서 다시 한 번 읽어보고 오세요~ 
        
```swift

class CashRegister {
    var availableFunds: Decimal
    var transactionTotal: Decimal = 0
    
    init(availableFunds : Decimal) {
        self.availableFunds = availableFunds
    }
    
    func addItem(_ cost: Decimal){
        transactionTotal += cost
    }
    
    func acceptCashPayment(_ cash: Decimal) {
      transactionTotal -= cash
      availableFunds += cash
    }
    
}

class CashRegisterTests: XCTestCase {
    
    var availableFunds:Decimal!
    var itemCost:Decimal!
    var payment: Decimal!
    
    var sut:CashRegister!
    
    override func setUp() {
        super.setUp()
        availableFunds = 100
        itemCost = 42
        payment = 40
        sut = CashRegister(availableFunds: availableFunds)
    }

    override func tearDown() {
        availableFunds = nil
        itemCost = nil
        payment = nil
        sut = nil
        super.tearDown()
    }
    
    func testInitAvailableFunds_setsAvailableFunds() {
        XCTAssertEqual(sut.availableFunds, availableFunds)
    }
    
    func testAddItem_oneItem_addsCostToTransactionTotal(){
        // when
        sut.addItem(itemCost)
        // then
        XCTAssertEqual(sut.transactionTotal, itemCost)
    }
    
    func testAddItem_twoItems_addCostsToTransactionTotal(){
        // given
        let itemCost2 = Decimal(20)
        let expectedTotal = itemCost + itemCost2
        
        // when
        sut.addItem(itemCost)
        sut.addItem(itemCost2)
        
        // then
        XCTAssertEqual(sut.transactionTotal, expectedTotal)
    }
    
    func testAcceptCashPayment_subtractsPaymentFromTransactionTotal() {
      // given
      givenTransactionInProgress()
      let expected = sut.transactionTotal - payment
        
      // when
      sut.acceptCashPayment(payment)
      
      // then
      XCTAssertEqual(sut.transactionTotal, expected)
    }
    
    
    func testAcceptCashPayment_addsPaymentToAvailableFunds() {
      // given
      givenTransactionInProgress()
      let expected = sut.availableFunds + payment
      
      // when
      sut.acceptCashPayment(payment)
      
      // then
      XCTAssertEqual(sut.availableFunds, expected)
    }

    func givenTransactionInProgress() {
      sut.addItem(50)
      sut.addItem(100)
    }
    
}

CashRegisterTests.defaultTestSuite.run()

```

1. testAcceptCashPayment_subtractsPaymentFromTransactionTotal(), testAcceptCashPayment_addsPaymentToAvailableFunds()
> 이름만 보고는 해석이 좀 애매하죠? 풀어보면 앞에는 수납테스트_총거래액으로부터의 차감지불액()? 뒤에는 수납테스트_이용가능금액에지불금액더하기()?로 볼 수 있겠네요. 쉽게 이해하려면 전자는 거스름돈(transactionTotal)을 계산하는 테스트, 후자는 시재 혹은 POS기내의 이용가능한 현금(availableFunds)를 계산하는 테스트라고 이해하면 되겠습니다.        
> givenTransactionInProgress() 메소드를 보면 내부에 addItem의 매개변수로 50과 100을 두번 실행합니다.            
> 이게 무슨 상황이냐면, 고객에게 현금을 받은거에요. 얼마를? 150원을요. 그래서 나는 지금 POS기에 받은 금액을 표시해야하니까 50원버튼과 100원버튼을 각각 눌러준거에요. 그러면 POS기에는 두 개의 합산금액이 150원이 표시되겠죠.      
2. acceptCashPayment(_ cash: Decimal)
> 수납을 위한 acceptCashPayment 메소드를 지정해줍니다. 매개변수로는 물건값(payment)이 들어가네요. 여기서 어떻게 해야 할까요? 저희가 계산해야 할 것은 transactionTotal과 availableFunds입니다. 150원을 받았으니 transactionTotal은 150이고, availableFunds은 setUp에서 지정해준 100이겠네요. 그리고 우리는 이제 팔아야 될 물건을 바코더로 찍을겁니다. 이 물건값은 payment죠. 그러면 거스름돈(transactionTotal)에서 payment는 차감해주면 되겠고, 현재 100이 있었는데 물건값 40을 받을거니까 시재에 40을 더해주면 되겠네요.       
> 결론적으로 시재는 140이 되고, 거스름돈은 110이 됩니다.        
        
### _Key points_
        
이 장에서 TDD주기에 대해 배웠습니다. 여기에는 4 단계가 있습니다.     
        
- Red : 실패한 테스트를 작성합니다.     
- Green : 테스트를 통과했습니다.      
- ReFactoring : 앱과 테스트 코드를 모두 정리합니다.        
- Repeat : 모든 기능이 구현 될 때까지 다시 수행합니다.        
        
        
