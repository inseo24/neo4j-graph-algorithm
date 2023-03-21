# neo4j-graph-algorithm

<details>
  <summary>Neo4j 기본 지식</summary>
  
- Neo4j Docs Example

  <img width="656" alt="image" src="https://user-images.githubusercontent.com/84627144/226246411-cc6f1e21-5b5a-4c0e-bcab-5c10bd1000eb.png">
  
  - Cypher
    ```cypher
    CREATE
    (charlie:Person {name: 'Charlie Sheen', bornIn: 'New York', chauffeurName: 'John Brown'}),
    (martin:Person {name: 'Martin Sheen', bornIn: 'Ohio', chauffeurName: 'Bob Brown'}),
    (michael:Person {name: 'Michael Douglas', bornIn: 'New Jersey', chauffeurName: 'John Brown'}),
    (oliver:Person {name: 'Oliver Stone', bornIn: 'New York', chauffeurName: 'Bill White'}),
    (rob:Person {name: 'Rob Reiner', bornIn: 'New York', chauffeurName: 'Ted Green'}),
    (wallStreet:Movie {title: 'Wall Street'}),
    (theAmericanPresident:Movie {title: 'The American President'}),
    (charlie)-[:ACTED_IN]->(wallStreet),
    (martin)-[:ACTED_IN]->(wallStreet),
    (michael)-[:ACTED_IN]->(wallStreet),
    (martin)-[:ACTED_IN]->(theAmericanPresident),
    (michael)-[:ACTED_IN]->(theAmericanPresident),
    (oliver)-[:DIRECTED]->(wallStreet),
    (rob)-[:DIRECTED]->(theAmericanPresident)
    ```

- Native Graph Storage
  
  - 특징
    - 고정 크기 레코드 저장 방식을 사용하여 노드를 빠르게 조회
    - 노드, 관계, 속성은 각각의 저장소에 저장됨
    <br/>
    <img width="640" alt="image" src="https://user-images.githubusercontent.com/84627144/226246547-011b9b8a-6fe6-4000-879b-7d522b2bec8a.png">
  
  - 노드 저장소
    - 고정된 15Byte
    - isInUse(1), 첫번째 관계 id(1), node id(4), 첫번째 속성 id(1), 레이블 저장소(5), extra(1) 

  - 관계 저장소
    - 고정된 34Byte
    - 시작 노드 id, 끝 노드 id, 관계 유형에 대한 포인터, 각 시작 노드와 끝 노드에 대한 다음 및 이전 관계 레코드에 대한 포인터

  - 속성 저장소
    - Key-Value 
    - 노드, 관계 모두 속성 참조

- Programmatic API
  - Kernel : 데이터 CRUD, 탐색 등 작업 제공
  - Core API(GraphDB 기능 제공하는 인터페이스)
  - Traversal Framework : 노드와 관계 구조를 활용해 그래프 내의 경로 탐색, 필요한 데이터 추출
- Nonfunctional Characteristics
  - Transaction : ACID, Active transaction log, commit transaction log ...
  - Recoverability : replay, 복구 기능 등
  - Availabitliy : Master-Slave Cluster, Traversal Query(하나 이상의 지정된 위치에서 시작해 하위 그래프를 탐색하는 쿼리. 시작점이 다르면 쿼리가 분산되어 전체 로드가 낮은 경합으로 실행되어 가용성이 높아짐)
  
</details>


<details>
  <summary>Neo4j에서 사용되는 그래프 알고리즘</summary>


- [링크](https://neo4j.com/docs/graph-data-science/current/introduction/) :  Neo4j Graph Data Science(GDS)

  <details>
    <summary>Syntax Overview</summary>


  - Syntax overview
    - 일반적인 알고리즘의 syntax는 이전에 로드한 그래프를 참조하는 걸 포함
    - 또한, 다음과 같은 다양한 execution modes가 제공된다.
      - stream : 알고리즘 결과를 스트림으로 반환
      - stats : 통계 정보를 단일 레코드로 반환하나 Neo4j DB에 저장하진 않음
      - mutate : 알고리즘의 결과를 예상 그래프에 기록하고 요약, 통계 정보 반환
      - write : 알고리즘 결과를 DB에 저장.
      - 마지막으로 execution mode는 명령어 끝에 estimate를 붙이면 메모리 요구 사항도 추정 가능함. 
        - 일부 알고리즘은 대규모 그래프를 써 수십 기가바이트 이상의 메모리가 필요할 수 있음. 따라서 미리 추정하는게 실행 전에 메모리 부족 문제를 방지할 수 있다.
        - 물론 추정은 실제 실행 환경에서의 정확한 메모리 요구 사항을 예측할 수는 없음.(무료와 production에 따라 달라지기도 함)

    - Syntax Composition
      ```cypher
      CALL gds[.<tier>].<algorithm>.<execution-mode>[.<estimate>](
        graphName: String,
        configuration: Map
      )
      ```

    - estimation mode도 가능
      ```cypher
      CALL gds[.<tier>].<algorithm>.<execution-mode>.estimate(
        configuration: Map
      )
      ```

  </details>

</details>




