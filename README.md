# Samsung CMM-D for Red Hat Openshift Operator
[![Go Report Card](https://goreportcard.com/badge/github.com/kubeflow/spark-operator)](https://goreportcard.com/report/github.com/kubeflow/spark-operator)
[![License](http://img.shields.io/:license-apache-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.html)

Samsung Operator for CMM-D는 PasS 환경에서 삼성이 제조한 CXL* Memory Module Device를 통합 활용하는 방법을 제공합니다.
이는 [CMM-D 메모리 계층화에서 Linux NUMA를 활용하는 기술](https://semiconductor.samsung.com/news-events/tech-blog/utilizing-linux-numa-in-cmm-d-memory-tiering/)과 RedHat에서 제공하는 CoreOs 및 Openshift Operator 기술을 활용하여 구현 되었습니다.
<br>   
> __Compute Express Link(CXL)__: 고성능 데이터 센터 컴퓨터용으로 설계된 고속, 고용량 중항 처리 장치(CPU)-장치 및 CPU-메모리 연결을 위한 개방형 표준 입니다.   
   
<br>   

## Introduction

컨테이너 기반의 애플리케이션을 배포 및 관리하는 플랫폼인 Red Hat Openshift 환경에서 서버내의 Local Memory(DRAM)와   
[Samsung CMM-D](https://semiconductor.samsung.com/news-events/tech-blog/worlds-first-cmm-d-technology-leading-the-ai-era/) 를 통합하고, 통합된 이종 Memory를 필요에 따라 효율적으로 활용할 수 있는 Golang 기반의   
Operator 입니다.   
이 Operator는 사용자가 필요에 따라 Local Memory 또는 확장된 Samsung CMM-D 를 선택 및 할당하여 활용할 수 있는 기능을   
제공하고, Red Hat의 Openshift Container Platform의 통제하에 안전하고, 효율적으로 활용할 수 있는 방법을 제공합니다.   
그리고, CoreOS(커널)과 Openshift 수정이 필요 없어 Openshift에 대한 Redhat 지원에 문제가 없는 제품 입니다.

Rad Hat Openshift Operator에 대한 상세한 내용은 [여기](https://docs.openshift.com/container-platform/4.15/operators/index.html) 를 참조하세요.
   
<br>   

## Supported Features

Operator가 지원하는 기능은 다음과 같습니다.

- 서버에서 Local Memory 이외에 설치된 CMM-D의 인식과 Memory의 자동 통합/확장 기능
- CPU Core 와 Memory NUMA를 설정을 통해 사용가 필요한 Resource를 지정할 수 있는 기능
- 신규 Pod 생성시 최적의 리소스 할당이 가능한 서버 노드에 생성되도록 하는 Scheduling 기능
- Red Hat OCP Web Console을 통한 간편한 Install 기능 제공
- 오류상황에 대한 OCP Event log 연계 기능
- 다음과 같은 Kubernetes 기반 객체가 지원됩니다.
	- CRD(Custom Resource Definition)
	- Controller
	- API
	- NodeSelector
	- Resource and Limits
	- Taint/Tolerations   
	- Config Map
	- DaemonSet
   
<br>   	
	
## Support Matrix
Samsung Operator for CMM-D는 Openshift 및 Kubernetes에서 테스트 검증 되었습니다.  
지원되는 버전은 다음과 같습니다.  

|쿠버네티스 버젼|Openshift 버전| RHEL 버전|
|-------------------|-------------------|-------------------|
|v1.27.10+c79e5e2|4.14.16|9.2
   
또한 현재 CXL Extend Memory를 지원하는 System 으로 검증 되었으며, 이에 대한 상세한 내용은 [System Requirment](./document/CXL_requiements.md) 문서를 참조하세요.

<br>   

## Quick Start Guide
Samsung CMM-D for Operator를 Install, Configuration, 활용하기 위한 Hardware 및 Software Requirment와 설치 및   
사용방법은 [Install & User Guide](./document/Quick_Start_Guide.md) 문서를 참조하세요.   

<br>

## E2E Test
Samsung CMM-D for Operator의 End To End Test는 사용자가 필요에 따라 Manualy 한 설정으로 CMM-D Memory를 할당하는 Pod를 생성 하거나, CMM-D 가 장착된 여러개 Node 중 Operator가 알아서 가장 적합한 Node에 Pod를 생성하는 테스트를 진항 합니다.

E2E test는 실제 Samsung CMM-D 가 세팅되어 있는 Samsung 환경에 대서 진행 하였으며, Test에 대한 상세한 내용은 [E2E Test Document를](./document/E2E_Test_Guide.md) 참조하세요.



