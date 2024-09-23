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
### 고가용성을 보장하지 못한 사례
![2024-09-22 17 40 43](https://github.com/user-attachments/assets/f30c8fa0-cae1-436a-9693-183e30e7dd27)
- DR센터를 따로 두지 않아 신속한 복구가 어려웠다.
### vSphere HA
![2024-09-22 18 08 59](https://github.com/user-attachments/assets/2993f17d-b7d5-4921-a76e-e45e33f7d9ed)

- vSphere HA는 하나의 호스트가 죽으면 해당 호스트의 VM을 다른 Host에 failover 하여 고가용성을 보장
## 🎯 HA 작동 원리 및 장애 대응 방식
### Primary Host 선정 및 Secondary Host 역할
![2024-09-22 18 11 25](https://github.com/user-attachments/assets/28c135a3-17db-4b6f-b04e-fdf1ff70990b)
- vSphere를 통해 host를 cluster로 묶게되면 FDA 에이전트가 설치된다.
    - FDA 에이전트는 Primary host를 선출할 때 데이터스토어를 가장 많이 가진 host를 후보군으로 둔다.
- primary host는 secondary host를 계속 모니터링하며 vCenter와 통신한다.
    - host가 죽게되면 primary host는 이를 감지하여 다른 host에 VM을 failover 시킨다.
    - 만약에 primary host가 죽으면 데이터스토어 개수를 기반으로 primary host를 다시 선출한다.
### 네트워크 격리, 파티셔닝, 응답옵션
![2024-09-22 18 20 02](https://github.com/user-attachments/assets/13aeae30-7e57-4cc4-9470-186897692065)
- 각 호스트들은 Management Network를 통해 Network Heartbeat를 주고받는다.
    - 만약에 외부와 클러스터 내부 모두 네트워크 통신이 끊긴다면 네트워크 격리 상태가된다.
       - 대표적으로 NIC 카드장애, HOST down이 원인
    - 특정 호스트끼리는 통신이 가능하지만 나머지 내부 클러스터간에 통신이 안된다면 네트워크 파티셔닝 상태다.
       - 대표적으로 잘못된 네트워크 설정, 잘못된 방화벽 설정이 원인
![2024-09-22 18 25 28](https://github.com/user-attachments/assets/19f2a86c-4781-4f97-b7da-28fe1401835c)
- 네트워크 격리 또는 파티셔닝 상태가 됐을 때 vSphere에서 응답옵션을 선택할 수 있다.
    - Power off 옵션은 호스트를 강제종료하여 다른 host로 VM을 failover 한다.
    - Shutdown 옵션은 호스트를 정상종료 시키고 다른 host로 VM을 failover 한다.

### NIC 카드 이중화
![2024-09-22 18 29 06](https://github.com/user-attachments/assets/1fa36c7d-deba-49e3-8acd-7def049b7210)

- 네트워크 격리, 파티셔닝 상황을 방지하고자 Management network를 NIC 이중화를 통해 고가용성을 강화한다.

### 데이터스토어 하트비트
![2024-09-22 18 30 07](https://github.com/user-attachments/assets/698bdcf5-cd76-4662-a4ff-040678206a50)
- 네트워크 하트비트를 주고받을 수 없는 상태일 때 데이터스토어 하트비트를 통해 해당 호스트의 네트워크가 격리 또는 파티셔닝 상태가 되었는지 확인할 수 있다.
    - 데이터스토어 하트비트는 스토리지 내부에 존재하는 LUN 이라는 논리적인 공간내에 생성된 데이터스토어에 IO를 보내 Host의 동작여부를 확인한다.
    - 다만 LUN은 NFS에서는 지원하지 않으며 VMFS만 지원한다.
### 스플릿 브레인 현상 대응
![2024-09-22 18 34 29](https://github.com/user-attachments/assets/02ad7b75-1620-489c-8b53-a3294787b819)
- 스플릿 브레인이란 네트워크 격리, 파티셔닝 응답옵션이 설정되어 있지 않았을 경우 vSphere는 해당 호스트를 종료하지 않고 다른 Host에 VM을 failover 한다
    - 때문에 네트워크가 분리, 파티셔닝된 host에도 VM이 살아있고 failover된 host에도 VM이 살아 있는 상황이 발생한다.
    - 같은 VM이 다른 host에 똑같이 올라간 이 상태를 두뇌가 분리된 것 같다해서 스플릿 브레인 현상이라고 부른다.
- 스플릿 브레인 현상을 해결하기 위해서 VMCP를 사용할 수 있다.
    - 같은 데이터스토어에 Lock을 가지지 않은 Host가 데이터스토어에 접근한다면 해당 host를 종료시킴으로써 스플릿 브레인 현상을 해결할 수 있다.

### PDL, APD 현상대응
![2024-09-23 10 05 27](https://github.com/user-attachments/assets/673d98f9-eeae-4220-b364-c49969149ae0)
- PDL : 스토리지 하드웨어가 문제가 생긴 상황
    - 복구될 가능성이 없기에 바로 다른 호스트로 failover 하는 옵션 
- APD : 스토리지와 통신할 수 있는 스토리지 네트워크가 모두 다운된 상황
    - 복구될 가능성이 있기 때문에 보수적인 접근(일정시간 대기하다가 failover)
    - 상황에 따라 즉각적인 failover 옵션도 사용가능



