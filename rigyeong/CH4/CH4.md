# 리포지터리와 모델 구현

## 4.1 JPA를 이용한 리포지터리 구현

* 도메인 모델과 리포지터리 구현시 선호하는 JPA

- 도메인 영역 : 리포지터리 인터페이스, 애그리거트
- 인프라 영역 : 리포지터리 구현 클래스 <-> domain.impl : 의존성 높아짐


* 리포지터리 인터페이스는 애그리거트 루트 기준으로 작성.
```
public interface OrderRepository {
	Order findById(OrderNo no);
	void save(Order order);
}
```
ex. 주문 애그리거트
	- Order 루트 엔티티
	- OrderLine, Orderer, ShippingInfo 등 객체

## 4.2 스프링 데이터 JPA를 이용한 리포지터리 구현

* 스프링 데이터 JPA : 지정 규칙에 맞게 리포지터리 인터페이스 정의시 리포지터리를 구현한 객체 알아서 만들어 스프링 빈으로 등록.
	(해당 책 스프링 데이터 JPA 사용시, 리포지터리 인터페이스 어떻게 정의하는지 공부)

## 4.3 매핑 구현

* 기본 규칙
	* 애그리거트 루트 엔티티이므로 @Entity로 매핑 설정
	* 벨류는 @Embeddable로 매핑
	* 벨류 타입 프로퍼티는 @Embedded로 매핑