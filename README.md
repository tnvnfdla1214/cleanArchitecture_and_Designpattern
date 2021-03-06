# 클린아키텍쳐와 디자인 패턴

### Clean Architecture란
먼저 제가 이해한 방향은 디자인 패턴에서 시작되었습니다. 먼저 "디자인 패턴?" 좋다 이겁니다. 그런데 그 디자인 패턴마저 ViewModel이 해야하는 양이 많이지고 너무 비대해지며 예를 들면 BD의 변경시 등 변화에 잘 대응하지 못한다는 이해에서 시작되었습니다. 그랬을때 변화에 잘 대응 할 수있고 의존성의 규칙을 두어 체계적인 구조화를 목표로 CleanArchitecture와 MVVM을 합친 저의 결론을 이번 블로그를 통해 작성할 것 입니다.

### 변화에 잘 대응할 수 있는 코드
변화에 잘 대응하는 것의 결과는 변화에 따른 코드의 변경이 적다는 것이겠죠. 즉, 변화가 일어나는 곳에서만 코드의 변화가 일어나야 합니다. 만약 데이터베이스에서 변화가 일어났는데 액티비티에서 변화가 일어나는 것은 좋지 않을 겁니다. 그러나 코드가 잘 분리돼 있어야 합니다.

그러면 코드를 어떻게 잘 분리해야 할까요?

코드가 본질에 맞게 설계돼 있어야 합니다. 예를 들어 데이터베이스의 테이블을 레이아웃을 구성하는 부분에서 사용한다면 본질에 맞지 않게 설계된 겁니다.그렇다면 모호할 수 있는 본질에 대해 생각해보겠습니다. 먼저 도메인의 본질이 있습니다. 기획자나 실제 사용자가 받아들이는 형태 그 자체를 코드로 작성할 수 있어야 합니다. 뱅크샐러드에서 제공하는 가계부 서비스를 생각해보면, 소비자가 생각하는 지출의 본질과 개념은 바뀌지 않지만, **서비스에서 어떻게 보여줄 것인지만이 바뀔 뿐이죠.** 그러므로 개발은 그 본질을 잘 나타낼 수 있는 방향으로 이뤄져야 합니다. 만약 데이터의 테이블뷰를 뷰모델로 사용한다면, 사용자는 개발자가 테이블 단위로 생각하지 않고 자신이 생각하는 집합 내지 개념 단위로 생각하므로, 변화가 필연적으로 일어납니다.

다음으로 **개발 구조 상의 본질**이 있습니다. 데이터라는 하나의 레벨로 추상화를 했는데, 실제로는 내부에 SharedPreferences, Realm, SQLite를 모두 포함하고 있다면, 구조 상으로 변화가 일어날 수 있는 지점에 대해 생각하지 않고 분리하지 않아 문제가 발생할 수 있습니다. 가령 SharedPreferences를 바꿨는데 Realm을 불러오는 코드에 영향을 미칠 수도 있다면 변화가 일어나는 위치에 대한 고민이 부족했다고 할 수 있습니다.안드로이드 구조 상의 본질도 생각해야 합니다. 만약 화면에 보여주는 뷰의 생명주기를 액티비티에서 관리하므로 이런 부분에 맞게 코드 구조가 설계돼 있어야 합니다.

### Clean Architecture

<img src="https://user-images.githubusercontent.com/48902047/147200319-87188776-8476-476c-8652-4b6308f451d4.png" width="50%" height="50%"></img>

Uncle Bob으로 유명한 Robert Martin의 [Clean Architecture 블로그](http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)에서 시작되었습니다. 양파 모양의 레이어 가장 바깥 쪽이 사용자와의 접접에 있는 Presentation 이고 가장 안쪽의 Entities가 사용자가 실제로 생각하는 개념 단위입니다. Clean Architecture에서는 서버 쪽 내용이지만 안드로이드에서도 이 원리를 적용시켜서 UI를 독립시키고 Database를 분리시키고, 외부적인 설정에 독립적인 구조를 적용하면 프레임워크에 의존적이지 않은 코드를 짤 수 있고, 테스트가 가능한 코드를 짤 수 있는 원리가 될 수 있습니다.

<img src="https://user-images.githubusercontent.com/48902047/147200512-ae27d295-36b4-4102-9c01-b30efb2d759e.png" width="50%" height="50%"></img>

Android에서도 Clean Architecture를 적용하려는 시도가 많습니다. 그 중 가장 유명한 글에서 위 그림처럼 레이어를 네 개로 분리했는데 이에 따른 의존성이 클린 아키텍쳐의 근간입니다.

**클린 아키텍처의 목표는 의존성 규칙을 통한 계층 분리**

<img src="https://user-images.githubusercontent.com/48902047/147200950-e28803fa-1107-42b3-ad4b-4cea7d5b8adf.png"></img>
<img src="https://user-images.githubusercontent.com/48902047/147201071-11e917da-ab69-4f38-add6-11c68ab0c67c.png"></img>


이렇게 계층을 나누는 이유는 계층을 분리하여 관심사를 분리시키기 위해서이며 이런 아키텍처가 동작하기 위해서는 **의존성 규칙**을 지켜야 합니다. 한마디로 **각 분리된 클래스가 한가지 역할만 하고 서로 의존을 어떻게 할지 규칙이 정해져있고 지켜야한다는 말**입니다.

여기서 **의존성 규칙은 모든 소스코드 의존성은 반드시 외부에서 내부로, 고수준 정책을 향해야 합니다.** 즉 안드로이드를 예로들면 비즈니스 로직을 담당하는 ViewModel과 같은 코드들이 DB 또는 Web 같이 구체적인 세부 사항에 의존하지 않아야 합니다. 이를 통해 비즈니스 로직(고수준 정책)은 세부 사항들(저수준 정책)의 변경에 영향을 받지 않도록 할 수 있습니다.

이렇게 나눔으로써 얻는 이점들은 다음과 같습니다. 가장 중요한건 Testable과 유지보수 및 협업이라 볼 수 있겠습니다.

+ 코트 테스트 커버리지 증대
+ 쉽게 패키지 구조 탐색 가능
+ 집중화된 클래스에 따른 프로젝트 유지 관리 증대
+ 새 기능을 빠르게 적용 가능
+ 이후의 개발에도 안정적인 구현
+ 명확한 규율로 전반적으로 따라야 할 베스트 프랙티스

위와 같은 설명이 있지만 가장 와닿는것은 **ViewModel의 부담**을 줄어들 수 있는 점입니다.

MVVM, MVP 구분없이 많은 프로젝트에서 위와 같은 구조로 이루어져 있을 것이라 생각됩니다. 복잡하지 않은 화면에서는 위 구조도 충분히 역할을 다할 수 있습니다. 문제는 스펙 자체가 복잡하거나 Main flow 에 속한 화면일수록 수많은 비즈니스 로직이 존재하거나 늘어나가고 있습니다. 즉, ViewModel 이 집중적으로 비대해지게 되되어 무슨일을 수행하는지 파악하기가 어려워지곤 합니다.

ViewModel 을 만드는 기준을 Activity 가 아닌 개별 View 를 기준으로 만들어보기도 했으나 근본적인 해결책은 되지 못했습니다. 이에 클린 아키텍처에서 주장하는 **도메인 계층**의 필요성이 대두 되습니다. 도메인 계층을 통해 ViewModel 에서 수행되는 비즈니스 로직을 분리하는 것이 목표입니다.

안드로이드 개발을 위한 더 이해하기 쉬운 클린아키텍처 구조 그림도가 있지만 뒤에서 살펴보도록 하고 먼저 오리지널 클린아키텍처의 각 계층의 역할에 대해 설명하면 다음과 같습니다.

### 4 Layers
<img src = "https://user-images.githubusercontent.com/48902047/146732189-bbbaa693-fe81-4363-8ab2-c34cbce3c98f.png">

 이 네 레이어 간의 의존성은 안쪽으로만 발생해야 합니다. 즉, 가장 하단부의 레이어일 수록 가장 의존성이 낮아야 합니다. 가량 프리젠테이션 레이어는 데이터 레이어를 알지만 데이터는 프리젠테이션을 몰라야 하며, 이 덕분에 맨 아래의 엔티티는 순수한 Java 내지는 Kotlin 모듈이 될 수 있습니다.

이런 레이어의 분리 덕분에 본질을 정의할 때 어떤 데이터베이스에 저장될지, 어떤 뷰에서 보일지 고민하지 않고 Entity를 작성할 수 있고, 이에 대한 유스 케이스로 Domain 레이어를 작성할 수 있습니다. 또한 트랜잭션을 가져오는 것을 Data에서, 어떻게 보여줄 것인지를 Presentation에서 고민하면 됩니다.

이제 각각의 레이어에 대해 더 자세히 살펴보겠습니다.

#### 1. 엔티티 레이어
엔티티는 순수한 Java나 Kotlin 모듈로 안드로이드 모듈이 아니므로 안드로이드와의 의존성이 없습니다. 만약 to do 앱을 만든다고 할 때 아이템을 하나 눌러서 넘길 때 사용자는 Parcelable을 생각하지 않겠죠. 따라서 넘기는 데이터를 **Parcelable로 정의하지 않아야 안드로이드와 독립적일 수 있습니다.** 즉, 사용자가 생각하는 형태대로 도메인(비즈니스 로직)에서 파생되는 개념을 표현합니다. 만약 같은 서비스를 만든다면 Android, iOS, 서버 모두 같은 이름과 타입을 사용하는 동일한 형태여야 합니다. 코틀린으로 된 코드를 보여드리겠습니다.

<img src = "https://user-images.githubusercontent.com/48902047/145938704-551d226c-bc91-4201-a4ee-6d58d7f966f9.png" width="50%" height="50%">

소비 내역에 카테고리라는 엔티티를 적용했습니다. 엔티티 레이어에 존재하는 개념으로, 어떤 테이블과 테이블을 조인하는 SQLite의 개념이 여기서는 들어가서는 안됩니다. 만약 그렇게 하면 객체 간의 관계를 지원하는 Realm으로 데이터 레이어를 바꾸는 순간 엔티티 레이어에 영향을 주기 때문입니다.

#### (상식) Model과 entity의 차이
1. model(DTO) : Data Transfer Object)로서 계층(Layer)간 데이터 교환을 위해 사용하는 객체입니다. 데이터 교환만을 위해 사용하므로 로직을 갖지 않고, getter/setter 메소드만 갖습니다.

```kotlin
class RGBColorDto {
   private int red;
   private int green;
   private int blue;
  
   public RGBColor(int red, int green, int blue) {
      this.red = red;
      this.green = green;
      this.blue = blue;
   }
  
   public int getRed() {
      return red;
   }
  
   public int setRed(int red) {
      this.red = red;
   }
   ...
}
```
로직은 없고, 데이터를 담고, 꺼내는 getter/setter 메소드만 담습니다.

2. Entity : 실제 DB의 테이블과 매핑되는 객체입니다. id를 통해 각각의 Entity를 구분합니다.
```kotlin
@Entity
public class User {
   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   privae Long id;
  
   @Column(nullable = false)
   private String name;
  
   @Column(nullable = false)
   private String email;
  
   @Builder
   public User(String name, String email) {
      this.name = name;
      this.email = email;
   }
  
   public User update(String name, String email) {
      this.name = name;
      this.email = email;
      return this;
   }
}
```
해당 예시는 이해를 돕기 위해 BD와 연결 되게끔 작성하였으나 이 포스터인 CleanArtichecture는 직접적인 join은 하면 안됩니다. CleanArtichecture에서는 Repository의 mapper를 이용하였습니다.

#### 2. 도메인 레이어
도메인 레이어도 순수한 Java나 Kotlin 모듈입니다. 그 이유는 도메인 레이어에서 일어나는 이유는 실제로 사용자가 하는 일련의 행동들 즉 유스 케이스를 적용하는 것인데 이 역시 안드로이드에 의존할 필요가 없기 때문입니다. 가령 소비 내역을 불러오는 유스 케이스는 *getTransactions* 함수의 리턴 타입을 *List\<Transaction>* 과 같이 정의하는 인터페이스만 있으면 됩니다.
 
<img src = "https://user-images.githubusercontent.com/48902047/146888735-d170d0b8-d598-4433-8137-c779a58a0fcc.png">

뱅크샐러드에서 지원하는 은행의 목록을 가져오는 유스 케이스입니다. 인터페이스로 정의된 Repository를 주입받습니다. BanksRepository는 도메인 레이어에 존재하는 인터페이스입니다. 유스 케이스를 구성할 때는 데이터베이스가 뭔지 고민하지 않고 도메인에서 정의한 적당한 리파지토리를 이용하서 구축하므로 코드가 사고의 흐름처럼 구성될 수 있습니다. domain 모듈은 다음과 같은 코드를 포함합니다.

+ Entity  : 특정 영역을 표현하는 객체. ex) Pojo, VO, DTO 등
+ UseCase : Use case란 내가 만들고자하는 시스템(혹은 서비스라고 하자)을 사용하는 클라이언트가 **그 시스템을 통해 하고자 하는 것**입니다. 예를 들어, '영화관' 이라는 서비스가 있다고 가정해봅시다. 영화관에서 손님(클라이언트)는 '영화 예매'를 할 수도 있고, '예매 취소'를 할 수도 있고, '환불', 심지어 '팝콘 사기'를 할 수도 있을 것입니다. 이 때, 이런 '영화 예매', '예매 취소', '환불', '팝콘 사기' 등등이, '영화관'이라는 시스템에 사용자가 요청할 수 있는, '영화관'의 Use case이다. Use Case는 이름만 보고 이게 무슨 기능을 가졌을지 짐작하고 구분할 수 있어야합니다. Entity와 함께 비즈니스 로직을 수행합니다.
 
#### 3. 데이터 레이어 
데이터 레이어에서 하는 한 가지 일을 고른다면 도메인 레이어를 알고 있으므로 도메인 레이어에 정의된 [Repository 페턴](https://github.com/tnvnfdla1214/Repository_-pattern) 으로 설계된 Repository를 실제로 구현을 하는 것입니다. 또한 여기에서는 Data Source에의 의존성이 생기므로 안드로이드 의존성이 생길 수 있습니다.
 
 <img src = "https://user-images.githubusercontent.com/48902047/146889483-ecb50d48-3864-47c5-a0d4-c17f211c6be3.png">

BanksRepository를 상속받아 정의한 모습입니다. 저희는 데이터베이스로 Realm을 사용하므로, Realm의 인스턴스를 가져오고 Realm에 들어갈 데이터 모델을 가지고 모두 넣었습니다. 여기서 데이터 모델은 데이터 레이어에서 정의한 Realm 오브젝트로 명확히 데이터베이스에 저장할 것을 내포하고 있습니다.

<img src = "https://user-images.githubusercontent.com/48902047/146889764-9ffe9a54-76e8-4dc2-9998-a79b99ecc11b.png">
 
도메인 레이어에서 데이터 모델을 모르므로 리턴 타입은 엔티티에 맞게 돌려줘야 합니다. 이를 위해 BankEntityMapper을 이용했습니다. Bank 데이터 모델을 데이터 엔티티로 바꿔줍니다.

이런 구조를 통해 데이터베이스로 Realm을 사용하다가 SQLite로 변환한 경우 데이터의 테이블을 정의한 클래스와, 데이터를 가져오던 Repository만이 바뀌게 됩니다. 즉, 데이터가 바뀌는 위치에 따른 데이터의 변화만 강제할 수 있습니다.

 <img src = "https://user-images.githubusercontent.com/48902047/146889899-a4ec29c1-01ab-4732-8809-feb23ed348d8.png">

앞서는 로컬 데이터베이스에 저장된 은행 엔티티를 가져오는 코드였는데, 서버에서 최초로 받아오는 경우에도 도메인에 존재하는 인터페이스를 그대로 유지할 수 있습니다. Retrofit으로 네트워크에서 값을 가져오며, 여기서의 Bank는 서버에서 JSON으로 보내오는 응답을 정의한 것입니다.

하나의 유스 케이스를 사용할 때 앱 초기에 서버에서 데이터를 받아와야 하는지, 로컬 데이터베이스에서 받아와야 하는지 하는 부분은 프리젠테이션 레벨에서 어떤 Repository를 주입할 지에 따라 결정됩니다.

#### 4. 프리젠테이션 레이어
 마지막 레이어인 프리젠테이션 레이어는 UI 레벨에서의 처리이고, Android 의존성이 높습니다. 저희는 MVP 패턴에 따라 View와 Presenter 모습을 사용했습니다.
 
<img src = "https://user-images.githubusercontent.com/48902047/146890082-75bfbaf3-fd2b-44c4-841e-388af172ee07.png">

프래그먼트의 타입을 BaseFragment로 정의해서 타입을 강제하고 이 프래그먼트가 특정 OrganizationView를 구현하게 하고, View가 presenter를 가집니다. 생명주기는 뷰가 생성됐을 때 presenter에 onViewCreated 함수를 만들었는데, presenter에 어떤 함수를 호출하는지는 뷰 레벨에서 담당하므로 네이밍을 뷰에서 알 수 있는 것으로 만들었습니다. 버튼이 눌릴 때도 의미상 맞는 onNextButtonClicked로 네이밍했습니다. 이런 네이밍으로 더 나은 코드 구조를 만들 수 있다고 생각합니다. 만약 레이아웃에 버튼이 여러 개 필요한 경우 presenter에 일련의 함수를 호출하는 식으로 구성하면 이 변화 때문에 presenter 코드가 다시 변화해야 하는 불상사가 발생할 수도 있습니다.
 
 ![image](https://user-images.githubusercontent.com/48902047/146890221-3a7a9a0d-3d21-4640-969c-b29ade419d8d.png)

 따라서 위 코드처럼 presenter가 생성되는 시점에서 유스 케이스를 주입받을 수 있는 구조를 만들었습니다. 그 이유는 테스트가 가능한 코드를 만들기 위해서입니다. GetBanks에서 데이터 레이어 중 맥락에 맞는 BanksNetworkRepository를 주입했고, onDestroy일 때 unsubscribe를 했습니다. 또한 onViewCreated일 때 getBanks를 이용해서 유스 케이스의 값을 가져오고 view.showBanks 처럼 코드를 작성해서 내부 로직이 바뀌더라도 뷰는 은행의 리스트를 보여줄 수 있는 구조를 만들었습니다. 따라서 이 구조를 따르면 뷰의 코드는 바뀔 필요가 없습니다.
 
 ![image](https://user-images.githubusercontent.com/48902047/146891656-a658acf5-f5fb-4a61-aee6-4324ec802918.png)

앞서 말씀드린 Parcelable의 예로 돌아가서, 데이터 레이어에도 모델이 존재하고, 엔티티 레벨에서는 개념에 대한 모델이, 프리젠테이션 레벨에서도 모델이 존재합니다. 위 코드는 뷰모델인데, 가끔 Parcelable로 넘길 필요가 있거나, 사용자의 개념 단위는 아니지만 개발 측면에서 화면에 보여줄때 데이터를 이런 형태로 조합하면 편하겠다고 생각할 때 만들어서 사용하고 있습니다. 뷰모델의 매퍼를 만들어서 엔티티로부터 변환하거나 엔티티로 변환하고 있습니다.

### 결론
저희는 좋은 코드의 구조가 좋은 제품으로 이어진다고 믿기에 이런 아키텍처를 학습하였습니다. 그리고 이런 복잡하다면 복잡한 아키텍처 도입의 진입 장벽보다 변화에 적응할 수 있음에서 오는 유연함이 더 값지다고 생각해서 이런 아키텍처를 학습하였습니다. 다음은 실제 [어플리케이션의 사용 예시](https://github.com/tnvnfdla1214/cleanArchitecture_and_Designpattern_2)를 통해 알아보겠습니다.
