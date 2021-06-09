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

---

# Main function
- Dashboard
- Inquery (history)
- 기준정보 (edge, device)
- User

---

## Technical Overview 
BackEnd
 - JDK 11, spring boot 2.5
 - Web, STOMP(Simple Text Oriented Messaging Protocol)
 - Security, JWT(Json Web Token)
 - JPA

FrontEnd
  - Vue3
  - Bootstrap

---
## Future plan
BackEnd
  - 이발통 기능
  - simple SPC
  - various channel

FrontEnd
  - main component 결정
  - 다국어
  - various chart

---

## DEMO

---
