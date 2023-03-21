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
  
   <details>
    <summary>PageRank</summary>

    - PageRank
      - 각 노드의 중요성을 그래프 내에서 측정
      - 중요도는 해당 노드로 들어오는 관게 수와 해당 관계의 source node의 중요도에 따라 결정된다.
      - 식
        
        <img width="410" alt="image" src="https://user-images.githubusercontent.com/84627144/226495874-820511eb-6169-437c-bc16-1c875440b0a1.png">

        - PR(A) : 현재 노드의 PageRank 값
        - T1~Tn은 현재 노드로 들어오는 노드
          - 예를 들어, 현재 노드 A로 들어오는 모든 노드를 말하는데 A와 직접적으로 연결된 노드가 아니라, A를 통해 다른 노드들과 연결되 노드들을 말한다.
        - PR(T1) ... PR(Tn) 은 각각 T1 ... Tn 노드의 PageRank 값을 의미 
        - C(A)는 현재 노드에서 출발하는 관계의 수를 의미한다.
        - d는 damping factor로 0부터 1사이 값인데 보통 0.85 정도로 세팅된다.
          - web sufer가 현재 페이지에서 다른 페이지로 이동하는 것이 아닌, 어떤 페이지에서든 랜덤하게 이동하는 확률을 나타낸다.
          - 1에 가까울수록 랜덤 이동 확률이 높음을 의미하고 페이지 간의 상호 연결성이 낮아진다. 
          - 0에 가까울수록 랜덤 이동 확률이 낮고 페이지 간의 상호 연결성이 높아진다. 
          - 이 값은 구글이 PageRank 알고리즘을 처음 발표했을 때 사용한 값. damping factor 값이 높을수록 PageRank 값이 높은 노드들은 더 높은 값을 갖게 된다. 
          
      - 예시 
        - A 노드로 들어오는 T1, T2, T3 노드가 있고, 각각 노드에서 출발하는 관계의 수가 2, 1, 3이라고 가정.
        - d 값은 0.85로 가정
        - T1, T2, T3 노드의 PageRank 값을 0.1, 0.3, 0.6으로 설정하면, A노드의 PageRank 값은 아래와 같이 구할 수 있다.
          ```
          PR(A) = (1-0.85) + 0.85 [(0.1/2) + (0.3/1) + (0.6/3)] = 0.115
          ```
        
  </details>

</details>




