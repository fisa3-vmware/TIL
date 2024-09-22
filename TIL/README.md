# <p align="center">⛳WooriFISA TechSeminar - VMware vShpere HA Deep Dive

<h2 style="font-size: 25px;"> 👨‍👨‍👧‍👦💻 개발 팀원 <br>
<br>
    
|<img src="https://avatars.githubusercontent.com/u/64997345?v=4" width="120" height="120"/>|<img src="https://avatars.githubusercontent.com/u/86272865?v=4" width="120" height="120"/>|<img src="https://avatars.githubusercontent.com/u/90691610?v=4" width="120" height="120"/>|<img src="https://avatars.githubusercontent.com/u/127727927?v=4" width="120" height="120"/>
|:-:|:-:|:-:|:-:|
|[@최영하](https://github.com/ChoiYoungha)|[@최수연](https://github.com/lotuxsoo)|[@이석철](https://github.com/SeokCheol-Lee)|[@부준혁](https://github.com/BooJunhyuk) 



<br>

# 🙆프로젝트 개요
Enterprise 환경에서 가장 중요하게 생각되는 High Availability 즉 고가용성을 보장하기 위해 필요한 개념 학습을 목표로 합니다.<br>
On-premise 환경에서 가장 많이 사용되는 솔루션인 VMWare vShpere의 핵심원리와 Hands-On-Lab 실습 내용을 정리하여 우리FIS 아카데미 기술세미나에서 발표하였습니다.

1. VMware [공식문서 vSphere Availability](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.avail.doc/GUID-33A65FF7-DA22-4DC5-8B18-5A7F97CCA536.html) 정독
2. VMware HOL - [Virtualization 101 (HOL-2535-01-VCF-L)](https://labs.hol.vmware.com/HOL/catalog/lab/13928) 실습
3. 기타문서 - VMware vSphere 67_ Clustering Deep Dive, VCP-DCV for vSphere 8.x Official Cert Guide
<br>

# ⛷ 학습 목적
- VMware vShpere Cluster 작동원리와 HA 기능 및 세부옵션 이해
- 자료수집 및 검증 후 지식 체계화
- 발표력 향상
<br>

# 🛠 목차
- 주제 선정 이유 및 HA 개념설명
  - 고가용성을 보장하지 못한 사례
  - 서비스 지속성
  - vSphere HA
- HA 작동 원리 및 장애 대응 방식
  - Primary Host 선정 및 Secondary Host 역할
  - 네트워크 격리, 파티셔닝, 응답옵션
  - NIC 카드 이중화
  - 데이터스토어 하트비트
  - 스플릿 브레인 현상 대응
- HA 설정 및 최적화 방법
  - Priority 
  - Admission Controll
  - 데이터스토어 하트비트 정책
  - Advancd Setting
- 결론 및 고찰
  - 워크로드에 따른 Admission Controll 정책설정
  - vSphere Cluster 기능 적극적으로 활용하기
  - 3Tier 구조에서 HCI로 전환하기
<br>

## 🥾 주제 선정 이유 및 HA 개념설명
### 1. 고가용성을 보장하지 못한 사례
