# Transation

### 트랜잭션이란?

데이터베이스의 상태를 변화(sql을 이용하여 db에 접근하는 것)시키기 해서 수행하는 작업의 단위이다. 하나의 sql문이 하나의 트랜잭션을 의미하지 않는다. 

ex. 게시판 게시글 작성 후 업데이트 확인

​	insert & select 문이 모두 필요. 

​	-> 개발자가 하나의 트랜잭션 설계를 잘해야 한다.

### 트랜잭션의 성질

- 원자성(Atomicity)

  트랜잭션이 db에 모두 반영되거나 전혀 반영되지 않아야 한다.

- 일관성(Consistency)

  트랜잭션의 작업처리 결과가 항상 일관성이 있어야 한다. 

  ​	-> 트랜잭션 진행 중 db가 변경되더라도 업데이트된 db로 트랜잭션이 진행되는 것이 아니라 처음 트랜잭션 시 참조한 db로 진행된다.

- 독립성(Isolation)

  둘 이상의 트랜잭션 실행 시 하나의 트랜잭션이 완료될 때까지 다른 트랜잭션이 특정 트랜잭션의 결과를 참조할 수 없다.

- 지속성(Durability)

  트랜잭션 성공적으로 완료 시 결과가 영구적으로 반영되어야 한다.

***

### Spirng에서의 Transaction

일반적으로 Service Layer에서 `@Transaction`을 추가하여 Transaction 처리(퀴리문을 처리하는 과정에서 에러가 났을 경우 자동으로 Rollback 처리)를 한다.

```java
@RequiredArgsConstructor
@Service

public class PostsService {

    private final PostsRepository postsRepository;

    @Transactional
    public Long save(PostsSaveReqestDto requestDto){
        return postsRepository.save(requestDto.toEntity()).getId();
    }
}
```

***

- Spring 웹 계층

  ![](.\assets\SpringWebLayer.PNG)



- Web Layer
  - Controller, View Templates(ex. jsp, thymeleaf)
  - 외부 요청과 응답에 대한 전반적 영역
- Service Layer
  - @Service에 사용.
  - Controller와 Dao의 중간 여역에서 사용.
  - @Transactional이 사용되어야 하는 영역 (트랜잭션, 도메인간 순서 보장)
- Repository Lyaer
  - DB 접근 영역(DAO)
- Dtos
  - 계층 간 데이터 교환을 위한 객체
  - ex. 뷰 템플릿 엔진에서 사용될 객체나 Repository Layer에서 결과로 넘겨준 객체

- Domain Model
  - 비즈니스 로직을 처리하는 영역
  - @Entity가 사용된 영역