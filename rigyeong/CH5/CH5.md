# 스프링 데이터 JPA를 이용한 조회 기능


## 5.1 시작에 앞서

* CQRS : 명령 모델과 조회 모델 분리 패턴
	* 명령 모델 : 상태 변경 기능 구현시 사용   ex. 회원 가입, 암호 변경, 주문 취소 
	* 조회 모델 : 데이터 조회 기능 구현시 사용   ex. 주문 목록, 주문 상세

* 도메인 모델은 명령 모델로 주로 사용.

## 5.2 검색을 위한 스펙 

* 스펙 : 애그리거트가 특정 조건 충족하는지 검사할 때 사용하는 **인터페이스**. 
	* 목록 조회 같이 다양한 검색 조건을 조합해야할 때 사용.
	```java
	public interface Speficiation<T> {
		public boolean isSatisfiedBy(T agg);
	}
	```
	* agg 파라미터 : 검사 대상인 객체
		ex1) 스펙을 리포지터리(도메인 모델 속한) 사용시 : agg 는 애그리거트 루트
		ex2) 스펙을 DAO(데이터 접근)에 사용시 : agg는 검색 결과로 리턴할 데이터 객체


* 예시) 특정 고객의 주문인지 확인하기 위해 주문자 ID를 기준
```java
public class OrdererSpec implements Specification<Order> { 
	private String ordererId; 
	
	public OrdererIdSpec(String ordererId) { // 생성자에서 주문자ID 받아 저장
		this.ordererId = ordererId; 
	} 
	
	public boolean isSatisfiedBy(Order agg) { 
		return agg.getOrdererId().getMemberId().getId().equals(orderId);
	}  
}
```