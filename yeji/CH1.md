# 1장 도메인 모델 시작하기

## 1.1 도메인이란?

- 도메인 : 소프트웨어로 해결하고자 하는 문제영역 → 온라인 서점
- 한 도메인은 여러 하위 도메인으로 구성된다 → 주문,배송,결제
- 도메인은 다른 도메인과 연동하여 완전한 기능을 제공한다
- 개발적 측면에서는 도메인이 제공해야할 모든 기능을 직접 구현해야하는 것은 아니고, 외부 시스템을 사용하기도 한다
- 도메인은 고정된 하위 도메인이 존재하는것이 아닌 대상,상황에 따라 달라진다

![image](https://github.com/GetMapping/FunDDD/assets/26589901/c3bebf02-052e-4112-9b0c-7b1495ab0ee0)

 



## 1.2 도메인 전문가와 개발자간 지식 공유

- 각 도메인 영역의 전문가는 지식과 경험을 바탕으로 기능개발을 요구

⇒ 도메인 전문가가 전문가로 있는 역역을 도메인으로 정의 할 정도로 도메인 전문가가 중요!

- 요구사항을 이해하는 것이 개발에 있어서 중요 → 직접 대화를 통해 이해

⇒ garbage in, garbage out = 잘못된 요구 사항이 들어가면 잘못된 제품이 나온다.

도메인 전문가의 요구사항이 항상 올바른것은 아님. 기존의 소프트웨어를 기준으로 맞출때가 있기 때문에 대화를 통해 진정한 요구사항을 파악 

- 이해관계자나 개발자도 도메인 지식을 갖추고 대화해야 원하는 제품을 만들 수 있다!

## 1.3 도메인 모델

- 도메인 모델 :특정 도메인을 개념적으로 표현한것 → 도메인을 이해하기 위한 개념 모델 → 구현기술에 맞는 구현모델이 필요
- 도메인 모델을 사용하면 여러 관계자들이 동일한 모습으로 도메인을 이해하고 도메인 지식을 공유하는데 도움이 됨
- 도메인 모델은 객체상태 다이어그램 등이나 uml이 아닌 그래프, 수학공식 등 다양한 방식 사용 가능
- 구현 모델이 개념모델을 따르도록 할수는 있다 (객체기반모델→객체지향언어/수학모델→함수)
- 도메인은 여러 하위도메인으로 구성되어있는데, 도메인마다 용어의 의미가 달라질 수 있음으로 하나의 다이어그램에 모델링 하지 않고 , 각 하위 도메인마다 별도로 모델을 만들어야한다
![image](https://github.com/GetMapping/FunDDD/assets/26589901/2b591a10-8407-493b-b0f8-215c6ada138b)

## 1.4 도메인 모델 패턴

어플리케이션 아키텍쳐를 4개의 영역으로 구성

도메인 모델 : 도메인 자체를 표현하는 개념적 모델 (개념,개념모델) or 도메인 계층을 구현할 때 사용하는 객체 모델 (개발,구현모델)

→ 처음부터 완벽한 개면 모델을 만들기 보다는 전반적인 개요를 알 수 있는 수준의 개념 모델을 작성하고, 프로젝트가 진행되며 지식이 쌓이고 구현 하는 과정에서 구현모델로 점진적으로 발전시켜 나가야 한다.

- Presentation계층(사용자,인터페이스,표현 계층) : 사용자(외부시스템)의 요청을 처리하고 정보를 보여줌
- Application계층(응용계층) : 요청한 기능을 실행, 로직을 직접 구현하지 않으며 도메인 계층을 조합하여 기능 실핼
- Domain : 시스템이 제공할 도메인 규칙 구현(비즈니스 로직?)
- infrastructure : 데이터베이스나 메시징시스템같은 외부 시스템과의 연동

- 도메인 모델 패턴 : 아키텍처상의 도메인 계층을 객체 지향 기법으로 구현하는 패턴

⇒ 해당 도메인과 관련된 중요 업무 규칙을 해당 도메인 모델에서 구현(order,orderstate)

```java
//도메인 모델 패턴 - 도메인 규칙을 구현
//orderstate 도 order에 속한 데이터이므로 order 클래스에서 구현가능
//(state뿐만 아니라 다른 정보도 필요한 경우 등)
public class Order {
	private OrderState state;
	private ShippingInfo shippingInfo;

	public void changeShippingInfo(ShippingInfo newShippingInfo) { 
		if (!state.isShippingChangeable()) {
		 throw new IllegalStateException(“can’t change shipping in “ + state);
		}
	this.shippingInfo = newShippingInfo;
	} 

}
public enum OrderState { 
	PAYMENT_WAITING {
		public boolean isShippingChangeable() { 
			return true;
		}
	},
	SHIPPED, DELIVERING, DELIVERY_COMPLETED;

	public boolean isShippingChangeable() { 
		return false;
	}
}
```

## 1.5 도메인 모델 도출

- 도메인에 대한 이해를 바탕으로 도메인 모델 초안을 만들어야 코딩 시작가능~
- 도메인 모델링시 모델을 구성하는 핵심 구성요소, 규칙, 기능을 요구사항에서 찾아야함 → 메서드와 필드 추출 및 제약 사항 파악 가능

```java
public class Order {
	private OrderState state;
	
public void changeShippingInfo(ShippingInfo newShippingInfo) {
		verifyNotYetShipped();
		setShippingInfo(newShippingInfo);
	}
	public void cancel() { 
		verifyNotYetShipped();
		this.state = OrderState.CANCELED;
	}
	private void verifyNotYetShipped() { 
	if (state != OrderState.PAYMENT_WAITING && state != OrderState.PREPARING)
		 throw new IllegalStateException(“aleady shipped”);
	}
}

//verifyNotYetShipped 해당이름은 어떤 상태일때 배송이 가능한지 알아야하는느낌?
// ischangeable보다 이런 정보를 넣는게 더 좋은 작명인가? 
```

- 문서화 → 코드는 상세한 내용이기때문에 상위 수준에서 정리한 문서를 참고하는 것이 빠른 파악에 도움이 된다. (전반적인 기능 목록, 모듈 구조, 빌드과정 등)

## 1.6 엔티티와 밸류

- 엔티티 + 밸류 = 도메인 모델
- 엔티티 타입: 식별자를 가진다
    - 식별자: 다른 요소들이 바뀌어도 바뀌지 않는 고유한 값
    - 식별자 생성은 사용 기술에 따라 특정규칙,uuid/nanoid,일련번호, 값입력 등의 방식을 따름
- 밸류 타입 : 개념적으로 완전한 하나를 표한할때 사용
    - 필드 묶어서 하나의 개념을 나타내거나(주소 등) 개념을 명확히 함(돈)
    - 밸류타입을 위한 기능을 추가 할 수 있다 (의미 부여,돈-계산)
    - 밸류 객체의데이터 변경 시, 기존 데이터 변경보다는 새로운 밸류 객체 생성 방식이 선호된다(= 데이터 변경 기능을 제공하지 않는타입인 불변객체 사용)→ 안전한 코드를 위해!(파라미터로 받은 객체가 다른곳에서 변경되면 문제 발생 등)
    - 밸류 객체 비교시 모든 속성을 비교해야 한다
- 식별자도 보통 의미를 가지기 때문에 밸류타입으로 만들면 좋다
    - Q orderNO라는 이름이 어때서?
- 도메인 모델에는 set메서드를 넣지 않는다(public)
    - 도메인의 핵심 개념이나 의도가 코드에서 사라지게 된다.
    - 도메인 객체 생성 시점에 온전하지 않을 수 있다 → 생성자를 통해 필요한 데이터를 모두 받아야 한다
    - 필요시 내부에서 데이터를 변경하는 private set은 사용 가능
    - 밸류 타입은 set이 꼭 필요한 경우가 아니라면 set없이 불변으로 구현
- DTO : 프리젠테이션 계층과 도메인 쳬층이 데이터를 주고받을때 사용하는 구조체 .
    - set을 써도 상관없으나 프레임워크에서 직접 할당을 제공하는 경우 set을 사용하지 않고 불변객체로 사용하자. → Q어차피 직접할당이 되는경우에는 불변객체가 의미가 있는가…? 어떤게 있지?

## 1.7 도메인 용어와 유비쿼터스 용어

- 코드에 사용되는 용어를 도메인 용어로 적어 해석하는 과정을 줄인다.
- 유비쿼터스 언어 : 전문가 관계자 개발자가 도메인과 관련된 공통의 언어를 만들고 모든 곳에서 같은 용어 사용
