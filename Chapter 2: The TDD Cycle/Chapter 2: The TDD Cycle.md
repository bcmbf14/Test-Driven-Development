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
        




    
    
  
  

        



    

    

### _Getting started_
### _Getting started_
### _Getting started_
