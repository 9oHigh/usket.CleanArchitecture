# MVVM Clean Architecture & Modular Architecture

iOS에서의 MVVM Clean Architecture와 Modular Architecture를 학습하는 공간 🔥

### 목차
 **[1. MVVM이란](#1-mvvm이란)**
  
 **[2. Clean Architecture란](#2-clean-architecture란)**
 
 **[3. MVVM Clean Architecture란](#3-mvvm-clean-architecture란)**

### 1. MVVM이란
  * Model - View - ViewModel로 이루어진 Architecture
    
    <div>
      <img src="https://user-images.githubusercontent.com/53691249/169693805-624069bf-cb52-4100-ab44-0b8548b6e8bd.png"  width="400" height="300"/>                                                                                                                                </div>

  * Model 
    * Model은 데이터와 관련된 코드를 담고 있음
    * 데이터를 담아두기 위한 구조체, 네트워크 로직, JSON parsing 코드 등이 담겨있음
    * Model은 View와 ViewModel을 전혀 신경쓰지 않아도 됨. 어떻게 데이터를 가지고 있을지에만 초점을 두고 있으면 됨
    * 예시 - Person이라는 구조체에 init 함수를 이용해 네트워크로 부터 가지고온 JSON 정보를 이용해 파싱할 수 있음
    
      ```swift
      struct Person {
          let name: String
          var age: Int

          init(json: JSON) {
          name = json["name"].stringValue
          age = json["age"].intValue
          }
       }
      ```
    
  * View ( ViewController )
    * UI를 담당하는 부분
    * ViewModel로부터 바인딩된 데이터를 어떻게 배치할지 혹은 특정 상황에 따라 어떤 ViewModel의 메서드를 이용할지 담겨있음
  
  * ViewModel
    * 앱의 핵심적인 비즈니스 로직을 담고있는 계층
    * View로부터 전달받은 요청을 받고 이에 따른 비즈니스 로직을 수행 및 Model의 변화에 따라 View를 갱신하는 역할을 하고 있음
    * UI와 관련된 코드로부터 독립적

  * MVVM Architecture의 동작원리
    * 데이터 흐름
      1. Action들이 View를 통해 들어오게 됨
      2. View는 ViewModel에게 Action을 전달
      3. ViewModel이 Model에게 Data를 요청
      4. Model은 ViewModel에게 요청받은 데이터로 응답
      5. ViewModel은 응답받은 데이터를 가공하고 저장
      6. View에서는 ViewModel과의 Binding을 통해 자동으로 갱신

  * MVVM의 장단점

    * 장점
      * 뷰 로직과 비즈니스 로직을 분리하여 생산성을 높힐 수 있음. (UI가 존재하지 않은 상태에서도 개발이 가능)
      * 테스트가 용이해짐 ( 의존성이 없기 때문 )
      * 뷰와 뷰모델이 1:n 관계이기 때문에 중복되는 로직을 모듈화해 여러 뷰에 적용할 수 있음 ( 코드 재사용 가능 )

    * 단점
      * 설계가 다소 어려움 ( RxSwift, 데이터 바인딩 등에 대한 지식이 필요 )
      * MVC에서 Controller가 비대해진 것처럼 ViewModel이 비대해질 수 있음
      * 데이터 바인딩으로 인한 메모리 소모가 심한편

### 2. Clean Architecture란
  * 기존의 아키텍처들

    * 공통점
      * 같은 목표를 가지고 있음 ( 가독성, 재사용성, 독립적 등 )
      * 소프트웨어를 계층(Layer)으로 나누어 관심사를 분리하고자 함

    * 아키텍처로부터 원했던 것
      * Independent of Frameworks - 아키텍처는 소프으웨터 라이브러리의 존재에 의존하지 않아야함
      * Testable - 비즈니스 로직은 UI, DB, 웹 서버 또는 기타 외부 소요 없이 테스트 가능해야함
      * Independent of UI - UI는 시스템을 변경하지 않고도 쉽게 변경 가능해야함
      * Independent of Database - 비즈니스 로직이 DB에 바인딩 되지 않아야함
      * Independent of any external agency - 비즈니스 로직은 자신의 외부에 대해 전혀 알지 못해야함

  * 위의 사항들을 고려하여 나오게 된것이 마틴의 클린 아키텍처

  * 클린 아키텍처의 다이어그램 

     ![image](https://user-images.githubusercontent.com/53691249/169694887-35c422e6-7ccf-49d3-9fd4-5a2bb36ebf69.png)

  * Dependency Rule
    * 소스코드 종속성은 항상 안쪽으로만 향해야 함
    * Inner Circles 안에 존재하는 것들은 Outer Circles에 대해 아무것도 알고 있어서는 안됨
    * 특히, Outer Circles에 선언되어 있는 함수, 클래스 등은 Inner Circles에서 언급해서는 안됨
  
      ![image](https://user-images.githubusercontent.com/53691249/169695405-44d33c41-e525-49b0-96d2-182529e1b652.png)
  
  * 설명
    * Entities
      * Enterprise wide business rules를 캡슐화 한 것
      * 외부의 변화가 있을 때 변경될 가능성이 가장 적거나 없는 것
      * 가장 높은 수준의 규칙을 캡슐화 
      * 예시 - [Oleh Kundinov님의 블로그](https://tech.olx.com/clean-architecture-and-mvvm-on-ios-c9d167d9f5b3) ( 앞으로의 예시는 모두 해당 프로젝트를 기준으로 함)

          ```swift
          struct Movie: Equatable, Identifiable {
              typealias Identifier = String 
              enum Genre {
                  case adventure
                  case scienceFiction
              } 
              let id: Identifier 
              let title: String? 
              let genre: Genre?
              let posterPath: String?
              let overview: String?
              let releaseDate: Date?
          }
          
          struct MoviesPage: Equatable {
              let page: Int
              let totalPages: Int
              let movies: [Movie]
          }
          ```
          * Movie라는 데이터 구조가 Entities가 된다. 간단하게 데이터 구조 및 함수 집합이라고 생각하면 됨. 가령 영화 검색 애플리케이션에서 무엇이 바뀌던 간에 영화에 대한 정보는 가장 높은 수준의 규칙이라고 할 수 있음. 

    * Use cases
      * 시스템의 동작을 사용자의 입장에서 표현한 시나리오
      * 시스템의 모든 Use cases를 캡슐화 및 구현
      * Entities와의 데이터 흐름을 조정 및 해당 Entities가 Use cases의 목표를 달성하도록 지시하는 역할
      * 즉, Use case는 Entities를 알고 있지만 Entities는 Use cases를 알지 못하고 Use cases의 변경이 Entities에 영향을 미쳐서는 안됨
      

    * Interface Adapters
      * (View)Controllers, Gateways, Presenters가 속하는 곳
      * Presentation Layer라고도 함
      * Entities, Use cases에 가장 편리한 format에서 DB 등과 같은 외부 프레임워크에 가장 편리한 format으로 변환되는 곳

    * Frameworks and Drivers
      *  DB, 프레임워크 같은 것들로 구성

    * Dependency Rule
      *  DB -> Adapter -> Use Cases -> Entities

    * 원의 개수
      * Dependency Rule이 안쪽을 향하고 갈수록 추상화 수준이 증가하면 개수는 상관없음

### 3. MVVM Clean Architecture란
  * MVVM과 Clean Architecture
    * Presentation Layer : View + ViewModel
    * Domain Layer : Use case + Model
    * (Repository) : Domain과 Data의 인터페이스 역할 (protocol)
    * Data Layer: Datastore + Entities

      ![image](https://user-images.githubusercontent.com/53691249/169697738-8ec5d877-c3f3-41b6-bf67-c42283bf2304.png)

    * Presenter(ViewModel) vs Use cases
      * ViewModel에 비즈니스 로직을 넣는 경우는 잘못된 접근
        * 비즈니스 로직은 오로지 Use cases에 존재해야함
      * ViewModel의 역할은 UI 이벤트들이 발생하면 '무엇'을 해야하는지 알고 있는 것
        * '무엇'을 해야하는지 알고 있기 때문에 Use case를 실행시키고 UI에 업데이트를 알리는 것
      * 비즈니스 로직은 앱에서 사용자와의 상호작용이 아닌 업무 요구사항을 담고 있는 것

    * Models vs Entities
      * Models은 Entities로 부터 애플리케이션에서 사용하고자 하는 모델로 변경한 것을 말함
        * Entities는 Codable과 같은 JSON에서 파싱되는 모델
  
    * 데이터 흐름
      1. View에서 이벤트가 발생 -> Presenter(ViewModel)에서 '무엇'인지 판단
      2. Presenter는 Use case를 실행
      3. Use case는 User와 Repository를 결합
      4. 각 Repository는 network,DB관련 Store에서 데이터를 반환
      5. UI에 업데이트 : Store -> Repository -> Use case -> ViewModel -> View(UI)

### Module & Pods

### Modular Architecture


#### 참고 URL
* Clean Architecture: [Zedd0202님의 블로그](https://zeddios.tistory.com/1065)
* MVVM Clean Architecture : [Oleh Kundinov님의 블로그](https://tech.olx.com/clean-architecture-and-mvvm-on-ios-c9d167d9f5b3)
* Modular Clean Architecture : [Oleh Kundinov님의 ](https://github.com/kudoleh/iOS-Modular-Architecture)
