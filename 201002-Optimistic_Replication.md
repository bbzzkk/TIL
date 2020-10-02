# Optimistic Replication (낙관적 복제)

## Optimistic Replication 이란

복제가 분기될 수 있는 복제 전략이며, Lazy Replication 이라고도 한다.

> 복제본의 내용이 잠시 달라지는 것을 허용  
> document 들은 분산 복제되어 있으며 로컬에서 먼저 실행하고 나중에 싱크를 맞춤  
  > => 클라이언트의 높은 응답성을 보장할 수 있음

기존의 비관적인 복제 시스템은 마치 복제본이 하나인 것처럼 모든 복제본이 서로 동일하다는 것을 보장하려고 한다.

**Optimistic Replication**은 **Eventual Consistency**(최종적 일관성) 을 노려 시스템이 일정 기간 동안 정지된 경우에만 복제본이 **Convergence**(수렴)되도록 보장한다.

> Eventual Consistency: 최종적으로 어떤 결과를 같게 하겠다.  

- 장점: 결과적으로 데이터를 업데이트 할 때 모든 복사본이 동기화 될 때까지 기다릴 필요가 없게되며, 이것은 동시성과 병렬처리에 도움이 된다.

- 단점: 다른 복제본이 나중에 명시적으로 조정될 수 있다. -> 처리하기 매우 어려움

## Convergence 문제를 해결하는 방법들

### 1. Operational Transformation(OT)

- 1989년 Ellis/Gibbs에 의해 collaborative editing 연구가 시작

- P2P방식으로 operation delivery 하여 사용자가 모든 다른 순서로 operation들을 실행

  > Operation ex) abcd의 문자열에 대해  
  > Insert(1, "y") = ybcd  
  > Delete(4) = abc  
  > Update(2, "x") = axcd  

- Operation들간의 causality(인과성) / concurrency(동시성) 이 존재하며 각각의 operation들은 intention(의도)를 가지고 있음
  - Integer index 때문에 operation의 의도가 왜곡되며 의도를 보존하기 위해 concurrent operation들의 integer index를 변환(transformation)

- OT 알고리즘이 진화되어 Client/Server 기반으로 한 OT의 변형이 Google docs에서 쓰이는 것으로 알려짐
  - Server가 client의 operation들을 serialization 함
  - 클라이언트에서 operation을 서버로 보내면 서버는 그 operation을 받아 해당 operation에 대한 transform을 적용하는 단순한 절차의 연속

#### OT 단점

- 동시에 발생하는 operation의 수가 증가하면 급격히 성능이 저하되며 알고리즘의 복잡성으로 인해 성능 및 확장성에서 심각한 한계를 가지고 있음
  > 일부 OT 알고리즘은 논문 발표 이후에 convergence를 만족하지 못한다는 것이 알려지기도 함
- OT는 upstream의 빠른 응답성이 장점이지만 각 레플리카의 히스토리를 재정렬하고 operation을 변환해야 하므로 downstream의 처리 속도가 매우 느림

  > upstream : 로컬 operation을 처리  
  > downstream : 다른 사용자가 전송한 원격 operation을 처리

#### > [OT 알고리즘 원리](http://www.secmem.org/blog/2019/01/09/operational-transform/)

### 2. CRDT(Conflict-free Replicated Data Type)

- Optimistic replication of abstract data types
  - Real-time collaboration을 위해 분산 data structure를 제공

- Commutative(교환 법칙)의 특성을 이용해서 concurrent한 operation 경우 순서가 뒤바뀌어서 실행되어도 동일한 결과를 얻을 수 있도록 하는 전략
  - 모든 경우에 OP1 -> OP2 = OP2 -> OP1
  - Commutative이 적용되기 때문에 downstream 실행 시 리플리카의 일관성을 위해 operation을 별도로 transform 하거나 정렬할 필요가 없음
  
- Integer index 대신에 unique ID

- Conflict를 해결할 수 있는 meta data를 object와 함께 저장하여 operation이 commutative를 만족하도록 함

- Upstream 처리 시 고유한 identifier를 생성하거나 조회되어야 하므로 upstream 처리가 느린 경향이 있음

### 주요 CRDTs

- Linked list: 주로 insert & delete operation like OT algorithm
  - WOOT, Treedoc, Logoot, RGA

- Array, Set, Hash table, Trees, Graph
  - Array, Set, Hash table 등은 last write win(LWW) 기법
  - Tree나 Graph 등에서 children 간의 순서가 존재하면 Linked list의 해결 기법이 쓰일 수 있음

#### > [CRDT로 실시간 협업 텍스트 편집기를 구축하는 간단한 접근 방식](https://digitalfreepen.com/2017/10/06/simple-real-time-collaborative-text-editor.html)

## Reference

- [실시간 웹 협업도구 만들기](https://deview.kr/2013/detail.nhn?topicSeq=4)
- [Towards collaboration system: OT 알고리즘에서 CRDT 시스템으로](https://deview.kr/2013/detail.nhn?topicSeq=66)
- [Operational tranformation - Wikipedia](https://en.wikipedia.org/wiki/Operational_transformation)
- [실시간 문서 협업은 어떻게 동작할까](http://www.secmem.org/blog/2019/01/09/operational-transform/)
- [Google Docs 같은 실시간 협업 에디터를 만드는 방법](https://hackerwins.github.io/2019-04-16/co-editor)
- [A Conflict-Free Replicated JSON Datatype 요약](https://hackerwins.github.io/2018-10-26/a-conflict-free-replicated-json-datatype)
- [High Responsiveness for Group Editing CRDTs 요약](https://hackerwins.github.io/2018-09-14/high-responsiveness-for-group-editing-crdts)
- [A SIMPLE APPROACH TO BUILDING A REAL-TIME COLLABOATIVE TEXT EDITOR](https://digitalfreepen.com/2017/10/06/simple-real-time-collaborative-text-editor.html)
- [Differences between OT and CRDT - stackoverflow](https://stackoverflow.com/questions/26694359/differences-between-ot-and-crdt)
- [Conflict-free replicated data type - Wikipedia](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type)