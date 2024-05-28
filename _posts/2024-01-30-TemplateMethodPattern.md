---
layout: post
title: 템플릿 메서드 패턴으로 첫결제 로직 개선해보기
tags:
  - [템플릿 메서드 패턴, 회사생활, 결제]
---

<br>

### 우리 서비스의 현재 결제 로직

현재 내가 개발하는 서비스는 정기구독 서비스로, 첫결제(카드결제 또는 ARS 결제) 와 매달 도는 빌링으로 결제가 이루어진다. 

결제를 담당하시던 분이 퇴사를 하게 되면서 내가 첫결제 로직을 담당하게 되었다. 운이 좋게도 이번에 결제 로직을 수정해야하는 프로젝트 진행건이 있었고, 이번 기회에 개선해보고 싶었던 결제 로직을 수정해보기로 했다. 

현재 우리 서비스의 첫결제는 카드결제, ARS결제 이렇게 두가지 타입의 결제를 지원한다. 각각의 결제수단은 70%가 동일한 공통 로직이고 30%는 결제 수단별로 약간 다른 처리가 필요하다. 대부분의 로직이 공통 로직임에도 불구하고 결제 수단에 따라 결제 흐름에 약간의 차이가 있다보니, 결제 수단에 따라 분기처리가 되어있고 그 안에 중복된 코드가 많이 작성되어 있었다.

한눈에 결제 흐름을 파악하기 쉬우면서도 결제 수단별로 다른 로직을 깔끔하게 처리할 수 있는 방법이 없을까 고민하다가 GOF 디자인 패턴 중 템플릿 메소드 패턴을 적용해보기로 했다. 

<br><br>

### Template Method Pattern 

먼저 간단하게 템플릿 메소드 패턴에 대해 정리해보자. 템플릿 메소드 패턴은 상위 클래스에서 템플릿 메소드을 만들어서 알고리즘의 뼈대를 만든다. 상위 클래스에 정의된 알고리즘 흐름 내에서 개별 로직 처리가 필요한 경우 상위 클래스를 상속받고 필요한 메서드를 오버라이딩한다. 즉 알고리즘 전체의 구조는 상위 클래스의 템플릿 메소드에서 정의하고, 하위 클래스에서 메서드 오버라이딩을 통해 알고리즘의 특정 단계를 구현할 수 있다. 

> The template method is a method in a superclass, usually an abstract superclass, and defines the skeleton of an operation in terms of a number of high-level steps. These steps are themselves implemented by additional *helper methods* in the same class as the *template method*.
>
> The *helper methods* may be either *[abstract methods](https://en.wikipedia.org/wiki/Abstract_method)*, in which case subclasses are required to provide concrete implementations, or *[hook methods](https://en.wikipedia.org/w/index.php?title=Hook_methods&action=edit&redlink=1),* which have empty bodies in the superclass. [Subclasses](https://en.wikipedia.org/wiki/Subclass_(computer_science)) can (but are not required to) customize the operation by [overriding](https://en.wikipedia.org/wiki/Method_overriding) the hook methods. The intent of the template method is to define the overall structure of the operation, while allowing subclasses to refine, or redefine, certain steps. (출처 위키피디아)

![class diagram](https://upload.wikimedia.org/wikipedia/commons/2/2a/W3sDesign_Template_Method_Design_Pattern_UML.jpg)

<br>

이렇게 템플릿 메소드 패턴을 사용하면 상위 클래스의 템플릿 메소드만 봐도 알고리즘 흐름을 쉽게 파악할 수 있다. 또 필요한 경우에는 상속받는 클래스에서 특정 메서드만 오버라이딩하면 중복코드 없이 개별적인 알고리즘을 적용할 수 있다. 

사실 이 템플릿 메소드 패턴은 어딘가 낯이 익었는데, 우리팀의 백오피스 개발시에 사용하는 프레임워크에서도 이 패턴을 이미 사용하고 있었다. 프레임워크에서 상위 클래스의 템플릿 메소드를 미리 구현해둔다. (=프레임워크 흐름의 뼈대를 설계하고 통제한다) 개발자들이 이 클래스를 상속받는 하위 클래스를 생성해 특정 메소드를 오버라이딩하면 프레임워크의 흐름 안에서 개발자가 작성한 코드가 특정 시점에 호출되는 방식이다.

<br><br>

### 결제로직에 Template Method Pattern 적용해보기

그럼 이제 우리 서비스에서 상품을 구매할 경우 실행되는 결제전 프로세스에 템플릿 메서드 패턴을 적용해보자. 대략적인 결제 흐름은 다음과 같다.

1. 사용자가 결제 정보를 입력한 후 결제하기 버튼 클릭
2. <b>사용자가 입력한 정보를 받아서 결제전 프로세스 실행</b>
   1. 유효한 상품인지 체크
   2. 사용자 정보 조회
   3. 상품 정보 조회
   4. 주문번호 생성
   5. (ARS 결제인 경우만) ARS예약번호 생성하고 PG사로 전송
   6. 첫결제 데이터, 약정 데이터 디비 저장
   7. (ARS 결제인 경우만) ARS 예약번호 알림톡 발송
   8. (카드결제인 경우만) 카드결제 팝업창에서 결제 진행 완료후 PG사에 전송할 데이터 셋팅
3. 2번 프로세스 실행 후 결제수단에 따라 다음 진행 페이지 리턴 
4. 사용자가 다음 결제 단계 진행 (전화로 ARS 결제 진행 또는 카드결제 팝업창에서 카드결제 진행)
5. 결제 완료후 PG사에서 우리 서버의 return url 호출, 결제 결과 디비저장 

<br>

위 흐름 중 <b>2번 결제전 프로세스에 템플릿 메서드 패턴을 적용해 개선해보았다</b>. 먼저 상위 클래스인 PaymentTemplate을 만들고 그 안에 결제전 공통 로직을 정의하는 prePayProcess() 템플릿 메서드를 정의했다. 결제 수단에 상관없이 실행되는 공통 로직인 1,2,3,4,6번은 이 클래스 내부에 private 메서드로 구현하고 prePayProcess() 에서 호출했다. 결제 수단별로 따로 처리해야하는 5,7,8번 로직은 PaymentTemplate 클래스 내부에 virtural 메서드로 정의하고 구현부는 비워둔 후, 상속받는 하위 클래스에서 오버라이딩 하도록 구현했다. 

<br>

템플릿 메소드 패턴을 적용한 대략적인 코드는 아래와 같다. (간단하게 흐름만 살펴보기 위해 메서드 리턴 타입은 void로 단순화했다. )

```c#
public abstract class PaymentTemplate{
    
    protected readonly PaymentType paymentType;
    protected readonly PrePayRegisterModel prePayRegisterModel;
    
    public PaymentTemplate(PrePayRegisterModel prePayRegisterModel, PaymentType paymentType)		{
        this.prePayRegisterModel = prePayRegisterModel;
        this.paymentType = paymentType;
    }
    
    public void prePayProcess(){
        
        try{
            
            //유효한 결제인지 체크
            checkValidPayment();

            //상품정보 조회
            getProductInfo();

            //사용자 정보조회 
            getUserInfo();

            //주문번호 생성
            createOrderID();

            //ARS - 예약번호 생성
            if(paymentType == PaymentType.ARS){
                setArsReserveNum();
            }

            //첫결제 데이터 저장
            saveFirstPaymentData();

            //약정 데이터 저장
            saveAgreementData();

            //ARS - 예약번호 전송
            if(paymentType == PaymentType.ARS){
                sendSMS();
            }
          
          	//Card - PG전송 데이터 셋팅
          	if(paymentType == PaymentType.Card){
              setPgSendData();
            }
            
            //..생략

        }catch(Exception ex){
           logging.Write(Log.ERROR, ex.Message, userId);
        }
            
    }
    
   
    private void checkValidPayment(){
     	//유효한 결제인지 체크 (유효한 상품코드인지, 장바구니에 담긴 상품인지)   
    }
   	
    private void saveFirstPaymentData(){
        //첫결제 데이터 임시 저장
    }
    
    
    //..생략
    
  
    protected virtual void sendSMS(){  //hook() 메서드 : ARS 결제일 경우에만 구현
    }
    
    protected virtual void setArsReserveNum(){ //hook() 메서드 : ARS 결제일 경우에만 구현
    }
  
    protected virtual void setPgSendData(){ //hook() 메서드 : 카드결제일 경우에만 구현
    }
}
```

```c#
public class ARSPayment : PaymentTemplate{
    
    public ARSPayment(UserPaymentRegisterModel userPaymentModel) 
        								: base (userPaymentModel, PaymentType.ARS){
    }
    
    protected ovveride void setArsReserveNum(){
        //ARS 예약번호 생성 & PG사 전송
    }
    
    protected ovveride void sendSMS(){
        //ARS 예약번호 결제자에게 전송
    }
    
}
```

```c#
public class CardPayment : PaymentTemplate{        
    public CardPayment(UserPaymentRegisterModel userPaymentModel) 
        								: base (userPaymentModel, PaymentType.Card){
    }       
  
   	protected ovveride void setPgSendData(){
      	// PG사 전송 데이터 셋팅 
    }
}
```

<br><br>

### Template Method Pattern 적용시 장점

템플릿 메소드 패턴 적용시 다음과 같은 장점이 있었다.

1. prePayProcess() 메서드만 보고도 결제흐름을 한눈에 파악하기 쉬워졌다.
2. 공통로직에 수정이 있을 경우 상위 클래스인 PaymentTemplate 클래스만 수정하면 되기 때문에 유지보수가 쉬워졌다.
3. 추후에 새로운 결제수단이 추가될 때에도 공통로직은 중복해서 작성할 필요가 없어졌다. 
4. 모든 결제수단 하위 클래스에서 공통로직을 위반하지 않음을 보장할 수 있게되었다. 

<br>

### C#의 Virtual Method와 Abstract Method

C#에서 Virtual Method 또는 Abstract Method만 하위 클래스에서 오버라이딩 할 수 있다. Abstract Method는 구현부가 없는 메서드로 상속받는 non-abstract 클래스에서는 반드시 구현해야하지만, Virtual Method는 구현부가 존재하고 상속받는 클래스에서 선택적으로 오버라이딩 할 수 있다. 

| Feature                   | Virtual Method                                       | Abstract Method                                              |
| :------------------------ | :--------------------------------------------------- | ------------------------------------------------------------ |
| **Implementation**        | Has a default implemenation                          | No implementation                                            |
| **Overriding**            | Optional                                             | Mandatory for non-abstract classes                           |
| **Location**              | Can be declared in abstract and non-abstract classes | Can only be declared in abstract classes                     |
| **Purpose**               | Provides a default behavior that can be overridden   | Mandatory for the derived classes to override the abstract methods |
| **Incompatible keywords** | static, abstract, private, and override              | static and virtual                                           |
| **Flexibility**           | Offers greater flexibility for customization         | Encourages consistency and uniformity                        |

출처 : [code-maze](https://code-maze.com/csharp-differences-between-a-virtual-method-and-an-abstract-method/)

위 코드에서 볼 수 있듯이 sendSMS() 와 setARSReserveNum() 메서드는 ARS결제일 경우에만 실행되는 hook() 메서드이기 때문에 virtual 키워드를 사용해 구현부는 비워두었다. 

<br>

<br>

---

- https://www.csharptutorial.net/csharp-design-patterns/csharp-template-method-pattern/
- https://engineering.linecorp.com/ko/blog/templete-method-pattern

































































