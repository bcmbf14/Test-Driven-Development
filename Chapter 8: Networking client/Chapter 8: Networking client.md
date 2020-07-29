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
더 나은 옵션은 모든 네트워킹 논리를 처리하는 🔆별도의 네트워킹 클라이언트를 만드는 것🔆입니다. 이것이 이장의 초점입니다!       
