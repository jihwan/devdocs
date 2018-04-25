class: center, middle, inverse

# SPC / RMS 개발 계획

.footnote[
  `2018. 04. 25.` 반도체솔루션그룹 황지환
]

---

# SK 서산 1공장 Overview
전극, 조립, 화성 3대 공정으로 나뉘어 있고,
각 공정내 data source에 따라, 수집하는 데이터(lot, machine, product)가 다릅니다.
화성 공정은 SPC 보다는 Report성격이 강합니다.
결과적으로 3대 공정을 다른 fab으로 인식 하여도 무방할 정도 입니다.
1공장 일부 요구사항은 2공장 프로젝트로 연기 되었습니다.


---

## 1. database
  rSPC db schema를 base로 하였습니다만,
  자주 변하는 요구사항을 설계 없이 그때 그때 마다 반영하다 보니,
  table간 관계가 없고, table내 특정 field 값을 이용하여 로직을 구성하였습니다.
  분석이 난해 합니다.

## 2. sever
  모듈화 및 용도별 서버 구성이 되어있지 않아, 복잡도가 높고 의존성이 높습니다.

## 3. web ui
  database 와 server 쪽에서 데이터 관리가 안되다 보니, 그 처리를 화면에서 수행하고 있습니다.
  여러개의 기능을 한 화면에서 처리 하여 side effect가 높고, 유지 보수성이 떨어집니다.

---

# 2. SPC 개발 구현 리스트
  1. database는 oracle 사용, schema는 CSOT t3 기반.
  2. SPC Modeling (13개)
  3. 분석 (3개)
    - cp/cpk report
    - spc chart
    - spc simulation

# 3. SPC 신규 구현 리스트
  1. dashboard (alarm top 10, cp/cpk  제품별/machine 별, SI 필요)
  2. excel import를 통한 SPC TEST 기능 (선택에 따른 저장 기능)

---

# 4. 기타
  1. fab login/logout 모듈 연동 or 단독 기능
  2. email server
  3. loader (real time rule check)
  4. off line spc를 위한 차트 이미지 생성 기법 (R 고려 중)
  5. 시공간을 초월한 데모 환경 준비 구축

# 5. 주요 요소 기술
  1. JDK8, spring boot 2.X 기반
  2. oracle GTT, IOT 고려
  3. tibrv 사용 하지 않을 경우 server LB,HA 확보 방안은??

---

# 6. 이슈
  1. 서산의 경험으로, 웹개발 프로젝트 수행시 개발생산성 저하가 있습니다.
  2. client platform 결정이 어렵습니다.

# 7. RMS 계획
1. backup
2. db schema, RMS 시나리오 분석 업무

위 항목건 수행.
