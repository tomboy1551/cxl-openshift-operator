# Operator`s Quick User Guide for Samsung CMM-D
Samsung CMM-D for Operator를 사용하기 위한 Hardware와 Software 환경 구성과 Operator 설치 및 활용 방법에 대한 빠른 가이드 내용을 설명 합니다. 상세한 Hardware 사양과 BIOS 세팅 사항은 [System Requirment](./CXL_requierments.md) 문서를 참조하세요.
   


<br>

## Hardware 구성
|Node|H/W|Usage|CPU|Local Memory|CXL|etc|
|-----|----------|-----|-----|---------|----------|-----------|
|node #1|Dell|KVM|Intel <br> 96 Core|512 GB|NA|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|node #2|SMC|OCP Worker #1|Intel <br> 96 Core|64 GB|512 GB <br> 128 GB * 4ea|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
 node #3|SMC|OCP Worker #2|AMD <br> 96 Core|128 GB|512 GB <br> 128 GB * 4ea|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
 node #4|SMC|OCP Worker #3|AMD <br> 96 Core|64 GB|512 GB <br> 128 GB * 4ea|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   
<br>   

## Software 구성
 Software 들의 버젼정보는 아래와 같습니다.

```bash
$ oc version
Client Version: 4.14.16
Kustomize Version: v5.0.1
Server Version: 4.14.16
Kubernetes Version: v1.27.10+c79e5e2

$ oc debug node/cmmd1
sh-4.4# chroot /host
sh-5.1# grep -e "OPENSHIFT_VERSION" -e "RHEL_VERSION" -e "^VERSION=" /etc/os-release
VERSION="414.92.202403051622-0"
OPENSHIFT_VERSION="4.14"
RHEL_VERSION="9.2"
```
<br>
   

## Operator 설치 및 사용법
Samsung CMM-D Operator를 사용하는 방법에 대한 설명 입니다.  
<br>
   
1. Openshift Cluster 설치
- CMM-D Operator 를 테스트 진행 하기 위해 위에서 기술 하였던 H/W, S/W 환경과 같이 CMM-D 가 세팅 되어 있는 서버 환경에 Openshift Cluster를 설치 합니다.   
  설치에 대한 자세한 방법은 [Redhat Openshift 설치방법을](https://docs.redhat.com/ko/documentation/openshift_container_platform/4.9/html-single/installing/index) 참조 바랍니다.
2. Redhat Operator Hub Web Console을 통한 Operator 설치   <br><br>
	1) Redhat OCP Web Console Operator 검색   <br>  
	   아래 그림은 Redhat OCP Web Console 접속시 첫 화면 입니다.   

	   ![image 1 - Web Console Main Screen](./image/User_guide/webconsole_main.png)   
	   <br>

	   아래 그림은 Redhat OCP Web Console에서 Operator를 검색 하기위해 OperatorHub 메뉴를 클릭 했을 때의 화면 입니다.   

	   ![image 2 - Web Console Operator Search](./image/User_guide/webconsole_operator_select.png)   
	   <br>

	   아래 그림은 Samsung CMM-D for Operator를 검색하기위해 검색 하는 화면 입니다. 검색어를 입력 하면 해당 검색를   
	   만족하는 Operator들이 검색 됩니다.   

	   ![image 3 - Web Console Operator Search2](./image/User_guide/webconsole_operator_search.png)   
	   <br>

	2) Operator 선택 및 설치 옵션 지정   
	
	   아래 그림은 1)번에서 검색한 Operator를 클릭 했을 때 설치 화면 입니다.   
	   추가적으로 설치 시 필요한 옵션을 선택한 후 "Install" 버튼을 눌러 설치를 진행 합니다.   

	   ![image 4 - Web Console Operator ](./image/User_guide/webconsole_operator_install_2.png)   
	   <br>

	3) Operator 설치   
	
	   아래 그림은 2)번 그림에서 Install 버튼을 눌렀을때 설치 중 화면 입니다.   <br>

	   ![image 5 - Web Console Operator Install](./image/User_guide/webconsole_operator_install_3.png)   
	   <br>

	   아래 그림은 Operator 설치가 완료 되었을 때의 화면 입니다.   

	   ![image 6 - Web Console Operator Install 2](./image/User_guide/operator_install_success.png)   
	   <br><br>   

3. Samsung CMM-D for Operator 사용예   
다음은 Samsung CMM-D for Operator를 사용하기 위해 필요한 CRD에 대한 설명과 CRD를 기본으로 한 CR을 제출 하는 방법과 CR을 통해 생성된 Pod를 확인 하고 삭제 하는 방식을 2가지 예를 통해 설명 합니다.

	1) CRD (Custom Resource Definition)   
		- CRD는 Operator를 통해 Pod를 생성하는데 필수적인 정의 내용입니다. 아래 내용에서 설명합니다.
		   
```yaml
apiVersion: apiextensions.k8s.io/v1					<1>
kind: CustomResourceDefinition						<2>   
metadata:
  annotations:
    controller-gen.kubebuilder.io/version: v0.11.1
  creationTimestamp: null
  name: cxls.cxl.antline.com
spec:
  group: cxl.antline.com
  names:
    kind: CXL
    listKind: CXLList
    plural: cxls
    singular: cxl
  scope: Namespaced
  versions:											<3>
  - name: v1										<4>
    schema:											<5>
    openAPIV3Schema:
      description: CXL is the Schema for the cxls API
      properties:
        apiVersion:
          description: 'APIVersion defines the versioned schema of this representation
            of an object. Servers should convert recognized schemas to the latest
            internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources'
          type: string
        kind:
          description: 'Kind is a string value representing the REST resource this
            object represents. Servers may infer this from the endpoint the client
            submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
          type: string
        metadata:
          type: object

```		
 
   <b>&#10102; <span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;API version을 정의 합니다</span></b><br>
   <b>&#10103; <span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;해당 YAML 문서의 종류를 정의 하는 항목으로 Custom Resource Definition 을 정의 합니다</span></b><br>
   <b>&#10104;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CRD가 지원하는 version 목록을 정의합니다</span></b><br>
   <b>&#10105; <span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;해당 CRD version의 이름을 정의 합니다</span></b><br>
   <b>&#10106;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Resource의 스키마를 정의 합니다</span></b><br>
<br>   
   

```yaml
          spec:					<1>
            description: CXLSpec defines the desired state of CXL
            properties:			<2>
              allocate:			<3>
                description: Specify resource allocation manually
                properties:
                  cpu:			<4>
                    description: // +kubebuilder:validation:XIntOrString
                    type: string
                  memory:		<5>
                    description: //+kubebuilder:validation:XValidation:message="wrong
                      value type",rule="^([+-]?[0-9.]+)$"
                    type: string
                  nodeName:		<6>
                    type: string
                type: object
              allocateMode:		<7>
                description: Specify resource allocation mode
                enum:
                - auto
                - manual
                type: string
              enable:			<8>
                description: Whether use of CXL
                type: boolean
              payload:			<9>
                description: Specify user resource manifest. e.g.) Pod, Deployment,
                  Replicaset
                type: object
                x-kubernetes-embedded-resource: true
                x-kubernetes-preserve-unknown-fields: true
            required:			<10>
            - allocateMode
            - enable
            - payload
            type: object

```		

   <b>&#10102;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;원하는 CXL의 Spec을 정의 합니다</span></b><br>
   <b>&#10103;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spec의 속성을 정의 합니다</span></b><br>
   <b>&#10104;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;자원 할당을 수동으로 할당하는 속성을 정의 합니다. 여기서 CPU, Memory, nodeName을 정의 합니다</span></b><br>
   <b>&#10105;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CPU 자원 할당을 String 형으로 지정합니다.  (cpuset.cpu 값을 세팅 합니다.)</span></b><br>
   <b>&#10106;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Memory에 대한 자원 할당을 숫자를 사용한 String 형으로 지정합니다. (cpuset.mems 값을 세팅 합니다.)</span></b><br>
   <b>&#10107;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;자원을 할당할 node의 이름을 String 형으로 지정 합니다</span></b><br>
   <b>&#10108;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;자원할당 Mode로 auto (자동 자원할당) 와 manual (수동자원할당) 중 하나를 지정합니다</span></b><br>
   <b>&#10109;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CXL 사용여부를 정의 합니다</span></b><br>
   <b>&#10110;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CR의 매니 페스트를 지정 합니다</span></b><br>
   <b>&#10111;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spec 속성 중 필수 fields 목록을 나열합니다. 여기서는 ‘allocateMode’, ‘enable’, ‘payload’ 가 필수 입니다</span></b><br>   	
<br>   

```yaml
          status:				<1>
            description: CXLStatus defines the observed state of CXL
            properties:			<2>
              allocateMode:
                type: string
              amountMemory:
                format: int64
                type: integer
              amountMemoryStr:
                type: string
              cpu:
                type: string
              enable:
                type: boolean
              kind:
                type: string
              memory:
                type: string
              name:
                type: string
              namespace:
                type: string
              nodeName:
                type: string
            type: object
        type: object
    served: true				<3>
    storage: true				<4>
    subresources:
      status: {}
status:
  acceptedNames:
    kind: ""
    plural: ""
  conditions: null
  storedVersions: null

```		  


   <b>&#10102;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;원하는 CXL의 Spec을 정의 합니다</span></b><br>
   <b>&#10103;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spec의 속성을 정의 합니다</span></b><br>
   <b>&#10104;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;자원 할당을 수동으로 할당하는 속성을 정의 합니다. 여기서 CPU, Memory, nodeName을 정의 합니다</span></b><br>
   <b>&#10105;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CPU 자원 할당을 String 형으로 지정합니다.  (cpuset.cpu 값을 세팅 합니다.)</span></b><br>

<br>



2) CR(Custom Resource) 제출 Sample 1 - Manual mode   
 	- 사용자가 CR 에서 제공하는 Properties를 manual로 지정하여 제출하는 방식에 대한 예시 입니다.   
   
<br>
> A. CR 제출 시나리오   

- CMM-D 가 세팅된 삼성 환경의 node (cmmd1, cmmd2, cmmd3) 에 __Manual__ 모드로 CPU, Memory NUMA 지정을 통해   
 지정된 CPU와 Memory가 잘 활용 되는지를 테스트 합니다   
<br>		

> B. 실행 내용   

- 대상 노드 : cmmd1 (intel cpu node)
- allocationMode : manual
- memory : 2 (CXL Memory NUMA)
- cpu : 0-23 (cpu 0)
- 리소스 할당량 : 100 GB
- Pod 생성 및 삭제를 통한 리소스 회수   
<br>

> C. CR (Custom Resource)   

```yaml
apiVersion: cxl.antline.com/v1
kind: CXL
metadata:
  name: scenario1001
  namespace: cxl-operator
spec:
  allocateMode: manual					<1>
  allocate:
    cpu: "0-23"							<2>
    memory: "2"							<3>
    nodeName: "cmmd1"					<4>
  enable: true							<5>
  payload:
    apiVersion: v1
    kind: Pod
    metadata:
      namespace: cxl-operator
      name: scenario1001
      labels:
        name: senario
    spec:
      terminationGracePeriodSeconds: 0
      containers:
      - name: stress
        image: quay.io/rasen708/stress:antline
        args: ["tail", "-f", "/dev/null"]
        resources:						<6>
          requests:
            memory: 100Gi
          limits:
            memory: 100Gi

```
   
   <b>&#10102;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Resource를 지정하는 mode에 대한 기술사항 입니다. (manual : Resource를 할당하는 mode, auto : 가장 효율적인 node를 선택 하여 자동으로 할당 하는 mode)</span></b><br>
   <b>&#10102;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;manual mode 일때 지정될 CPU Core 정의</span></b><br>
   <b>&#10103;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;manual mode 일때 지정될 Memory NUMA 정의</span></b><br>
   <b>&#10104;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;생성할 Pod의 대상 node 이름 정의</span></b><br>
   <b>&#10105;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CXL Device 활용 여부 (true : 사용, false : 비 사용)</span></b><br>
   <b>&#10106;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;요청/제한 Resource를 기술 합니다.</span></b><br>
<br>   

> D. CR 제출 (Pod 생성)   
   
   ![image 1 - Manual Mode CR 제출 ](./image/User_guide/cr_creation_pod.png)   
<br>   

> E. 생성된 Pod 확인   
   
   ![image 1 - Manual Mode Pod 확인 ](./image/User_guide/cr_create_check_pod.png)   
<br>   

> F. 생성된 Pod 삭제   
   
   ![image 1 - Manual Mode Pod 삭제 ](./image/User_guide/cr_delete_pod.png)   
<br>   

> G. 삭제된 Pod 확인   
   
   ![image 1 - Manual Mode Pod 삭제 확인](./image/User_guide/cr_delete_check_pod.png)   
<br>   



3)  CR(Custom Resource) 제출 Sample 2 - Auto mode   
  - 사용자가 CR 에서 제공하는 Properties를 auto로 지정하여 제출하는 방식에 대한 예시 입니다. auto mode는 생성할 Pod가 가장 효율적인 node를 자동으로 지정 하도록 스케쥴링 하는 기능을 제공합니다.   
   
<br>
> A. CR 제출 시나리오   

- CMM-D 가 세팅된 삼성 환경의 nodes (cmmd1, cmmd2, cmmd3) 중 cmmd2 node에 400GB 의 Pod가 할당되어 있는 상태에서 Auto mode로 200GB 의 Pod 생성요청을 제출 했을때, 3개 node 중 Pressure가 가장 적은 node에 자동으로 Pod가 생성 되는지 테스트 합니다   
<br>		

> B. 사전 세팅 사항   

- 대상 노드 : cmmd2
- 권한 생성 : ServiceAccount 및 권한 생성 작업 진행
- 리소스 할당량 : cmmd2 node CMM-D에 400 GB Pod 할당
<br>

> C. 실행 내용   

- 대상 노드 : 모든 nodes (cmmd1, cmmd2, cmmd3)
- allocationMode : auto
- 리소스 할당량 : 200 GB
- Pod 생성 및 삭제를 통한 리소스 회수   
<br>

> D. CR (Custom Resource)   

```yaml
apiVersion: cxl.antline.com/v1
kind: CXL
metadata:
  name: scenario1003
  namespace: cxl-operator
spec:
  allocateMode: auto					<1>
  enable: true							<2>
  payload:
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: scenario1003
      namespace: cxl-operator
    spec:
      replicas: 1
      selector:
        matchLabels:
          name: scenario1003
      template:
        metadata: 
          labels:
            name: scenario1003
        spec:
          terminationGracePeriodSeconds: 0
          nodeSelector:
            node-role.kubernetes.io/cmmd: ""
          containers:
          - name: stress01
            image: quay.io/rasen708/stress:antline
            args: ["tail", "-f", "/dev/null"]
            resources:					<3>
              requests:
                memory: 200Gi
              limits:
                memory: 200Gi
          serviceAccount: stress01
          serviceAccountName: stress01

```
   


   <b>&#10102;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Resource를 지정하는 mode에 대한 기술사항 입니다. (manual : Resource를 할당하는 mode, auto : 가장 효율적인 node를 선택 하여 자동으로 할당 하는 mode)</span></b><br>
   <b>&#10103;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CXL Device 활용 여부 (true : 사용, false : 비 사용)</span></b><br>
   <b>&#10104;<span style="color:blue">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;manual mode 일때 지정될 Memory NUMA 정의</span></b><br>
<br>   

> E. CR 제출 (Pod 생성)   
   
   ![image 1 - Manual Mode CR 제출 ](./image/User_guide/cr_creation_pod.png)   
<br>   

> F. 생성된 Pod 확인   
   
   ![image 1 - Manual Mode Pod 확인 ](./image/User_guide/cr_create_check_pod.png)   
<br>   

> G. 생성된 Pod 삭제   
   
   ![image 1 - Manual Mode Pod 삭제 ](./image/User_guide/cr_delete_pod.png)   
<br>   

> H. 삭제된 Pod 확인   
   
   ![image 1 - Manual Mode Pod 삭제 확인](./image/User_guide/cr_delete_check_pod.png)   
<br>
