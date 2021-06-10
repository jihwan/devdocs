class: center, middle, inverse

# ETOS Manager DEMO

.footnote[
  `2021. 06. 10.` PNAT 황지환
]

---

# ETOS Manager Overview
Etos manager는 Etos Box 모니터링, 관리를 하는 시스템입니다.
본 시스템은 시스템 IC 중국 우시 공장(s1) 타겟이며, 모니터링(Edge 상태, 에러, Device 연결 상태)과
기준 정보 관리 기능이 제공됩니다.

.footnote[
  Box와 edge는 같은 표현임
]
---

# Main function
- Dashboard
- Inquery (history)
- 기준정보 (edge, device)
- User

---

## Technical Overview 
BackEnd
 - JDK 11, spring boot 2.5.0
 - REST API
 - Security(Authentication, Authorization), JWT(Json Web Token)
 - STOMP(Simple Text Oriented Messaging Protocol)
 - JPA

FrontEnd
  - VUE 3
  - Bootstrap

---
## Future plan
BackEnd
  - 관리기능 (patch, start/stop)
  - 다양한 모니터링 (simple SPC)
  - 쉬운 customizing (알람, 이상발생통보)
  - various channel

FrontEnd
  - main component 결정
  - 다국어
  - various chart

---

## DEMO
