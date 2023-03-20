# neo4j-graph-algorithm

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


... 추후 마저 정리 ^ㅠ^

- Programmatic API
- Nonfunctional Characteristics
- [링크](https://neo4j.com/docs/graph-data-science/current/introduction/) : Predictive Analysis with Graph Theory
  - DFS
  - Dijkstra
  - A*
- Graph Theory and Predictive Modeling
  - Triadic Closures
  - Structural Balance
  - Local Bridges



