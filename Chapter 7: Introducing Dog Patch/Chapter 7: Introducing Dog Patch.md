## Chapter 7: Introducing Dog Patch 


TDD의 기본 사항을 배웠으므로 TDD에 익숙해지기 시작해야합니다. 그러나 대부분의 앱에서 매우 중요한 부분 인 네트워킹에 대해 TDD를 수행하는 방법을 배웠습니다.
다음 몇 장에서 테스트 코드 방식으로 네트워킹 코드 작성에 대한 정보를 얻을 수 있습니다. 이 장의 목표는이 섹션의 샘플 프로젝트를 소개하고 완료해야 할 작업을 강조하는 것입니다.

#### Getting started
이 섹션에서 Dog Patch라는 강아지 입양 앱을 완성하게됩니다. 이 응용 프로그램은 자신의 꿈의 강아지를 찾기 위해 종류, 전문 브리더와 개 애호가를 연결합니다.
이것을 가능하게하기 위해해야 할 일을 살펴 보자.

#### Networking client
8 장에서는 RESTful 네트워킹을위한 TDD를 시작하는 방법에 대해 설명합니다. 먼저 시작 프로젝트를 살펴보고 ListingsViewController에 항상 오류가 있음을 발견하십시오.

![image](https://user-images.githubusercontent.com/60660894/88854006-9bf25c80-d22b-11ea-818b-1d2137175f84.png)

이것은 앱이 실제로 네트워킹을 수행하지 않기 때문입니다. 네트워킹 클라이언트를 생성하고이를 해결하기위한 첫 번째 단계로 원격 서버에서 Dog 모델을 가져 오기위한 GET 요청을 작성합니다.

#### Using networking client

9 장에서는 TDD에 따라 뷰 컨트롤러에서 네트워킹 클라이언트를 사용합니다. 궁극적으로 앱은 이미지를 제외하고 사용자에게 네트워킹 결과를 표시 할 수 있습니다.

![image](https://user-images.githubusercontent.com/60660894/88854131-d1974580-d22b-11ea-82bc-3684b4a8efaa.png)

특히 마지막 장에서 만든 네트워킹 클라이언트 인 DogPatchClient를 사용하도록 ListingsViewController를 업데이트합니다.

#### Image client

10 장에서는 이미지 클라이언트를 만들고 ListingsViewController를 사용하여 이미지를 표시하는 방법을 설명합니다.

![image](https://user-images.githubusercontent.com/60660894/88854302-0acfb580-d22c-11ea-84a1-0f55ae2697c5.png)



