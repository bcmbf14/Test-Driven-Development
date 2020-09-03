# _Chapter 12: Dependency Maps_

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
    
코드를 변경하기 전에 첫 번째 단계는 새로운 동작이 무엇인지 식별하는 것입니다.    
이 프로젝트의 경우에는 MyBiz의 로그인 기능을 별도의 모듈로 이동해야합니다. 장기적으로는 여러 앱에서 로그인 모듈을 사용하는 것입니다.       
로그인을 별도의 모듈로 이동하면 더 빠른 증분 컴파일 시간, 단위 테스트 분리 등의 부가적인 이점도 있습니다.       
단순히 LoginViewController 및 관련 유형을 새 모듈로 이동 하고 작동하도록 할 수 있다면 멋지지 않을까요?     
따라서 이를 가능하게하려면 종속성을 분리해야합니다. 이것은 종속성 맵이 해결하는 데 도움이 될 수있는 완벽한 문제입니다.   

### _Finding problematic dependencies_
![image](https://user-images.githubusercontent.com/60660894/92084861-f2daeb00-ee02-11ea-945a-31ec0d3bee73.png)

로그인 기능을 따로 모듈로 분리하려고 합니다. 따라서 다른 것들은 제외하고 로그인 뷰컨트롤러를 기준으로 설명합니다.


    
AppDelegate에 대한 종속성은 지양합니다.
> AppDelegate를 모듈로 가져올 수 없으므로 일반적으로 문제가됩니다.

순환 종속성이 있습니까? 네, 그것들도 있습니다.
LoginViewController는 AppDelegate에서 가져 오는 API에 의존합니다.
차례로 AppDelegate는 LoginViewController에 의존합니다.
이미 LoginViewController-to-AppDelegate 관계를 문제로 식별했습니다.
AppDelegate-to LoginViewController 관계는 어떻습니까? 이렇게하면 로그인을 별도의 모듈로 가져 오지 못합니까?
아니, 사실. AppDelegate는 새 로그인 모듈에 의존 할 수 있으며, 차례로 LoginViewController를 설정할 수 있습니다.
따라서 목표 측면에서 문제가되지 않습니다.


LoginViewController-to-API 관계는 어떻습니까? 예, 이것은 두 가지 이유로 문제입니다.
1. API는 앱 전체의 다른 곳에서 사용되므로 로그인 모듈로 가져 오기가 어렵습니다.
2. API는 로그인 모듈에서 개념적으로 의미가 없습니다.
앱 내의 모든 모델 및 네트워킹 호출에 대해 알고 있습니다.
이것은 로그인이 알아야 할 범위를 벗어난 것입니다.
따라서 LoginViewController-to-API 화살표와 API 상자를 빨간색으로 강조 표시하여 문제가 있음을 표시하십시오.


LoginViewController에 많은 2 차 종속성이있는 종속성이 있습니까? 예, APIDelegate는 많은 모델에 의존합니다.
로그인 모듈이 실제로 이러한 모델에 대해 알아야합니까?
로그인과 관련된 두 가지 APIDelegate 메소드는 loginFailed (error :) 및 loginSucceeded (userId :)입니다.
둘 다 실제로 이러한 모델을 사용하지 않습니다!
결과적으로 LoginViewController-to-APIDelegate 관계, LoginViewController-to-Models 관계 및 APIDelegate 상자 자체는 모두 문제가 있습니다.
이들 각각을 빨간색으로 강조 표시하십시오.
LoginViewController의 세 가지 직접적인 종속성은 Skin, Validators 및 UIViewController + Alert입니다.
로그인과 동일한 모듈로 가져 오는 것이 합리적입니까?
Skin이 LoginViewController에 의해서만 사용 되었다면 같은 모듈로 끌어들이는 것이 좋습니다.
그러나 ErrorViewController에서도 사용되므로이 작업을 수행하는 것은 좋지 않습니다.
LoginViewController-to-Skin 관계와 Skin 상자 자체를 빨간색으로 강조 표시합니다.
유효성 검사기를 동일한 로그인 모듈로 이동해야합니까? 네, 실제로! LoginViewController에서만 사용되며 해당 메서드는 로그인 유효성 검사와 명시 적으로 관련됩니다.
이 관계와 유효성 검사기 상자를 녹색으로 강조 표시하여 이동해도됨을 나타냅니다.
UIViewController + Alert가 동일한 로그인 모듈에있는 것이 합리적입니까?
아니요, 일반 구성 요소이며 앱 전체의 여러 위치에서 사용됩니다.
이것은 실제로 별도의 모듈 자체에있는 것이 합리적 일 수 있지만 로그인 모듈에는 속하지 않습니다.
따라서 LoginViewController-to- UIViewController + Alert 관계와 UIViewController + Alert 상자 자체를 빨간색으로 강조 표시합니다.
궁극적으로 종속성 맵은 다음과 같아야합니다.

