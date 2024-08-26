# Samsung CMM-D for Red Hat Openshift Operator
[![Go Report Card](https://goreportcard.com/badge/github.com/kubeflow/spark-operator)](https://goreportcard.com/report/github.com/kubeflow/spark-operator)
[![License](http://img.shields.io/:license-apache-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.html)

Samsung Operator for CMM-D provides a way to integrate and utilize CXL* Memory Module Device manufactured by Samsung in PasS environment.
This is implemented by utilizing [technology that utilizes Linux NUMA in CMM-D memory hierarchy](https://semiconductor.samsung.com/news-events/tech-blog/utilizing-linux-numa-in-cmm-d-memory-tiering/) and CoreOs and Openshift Operator technology provided by RedHat.
<br>   
> __Compute Express Link(CXL)__: An open standard for high-speed, high-capacity central processing unit (CPU)-to-device and CPU-to-memory connections designed for high-performance data center computing.   
   
<br>   

## Introduction

The Samsung CMM-D Operator integrates the server's Local Memory (DRAM) and [Samsung CMM-D](https://semiconductor.samsung.com/news-events/tech-blog/worlds-first-cmm-d-technology-leading-the-ai-era/) devices within the Red Hat OpenShift environment. This Golang-based operator enables users to efficiently utilize integrated heterogeneous memory as needed. It provides the ability to select and allocate local memory or expanded Samsung CMM-D resources, ensuring safe and efficient usage under the control of Red Hat's OpenShift Container Platform. Additionally, there is no need to modify CoreOS (kernels) or OpenShift, so Red Hat's support for OpenShift remains unaffected.   

Read more about the Red Hat OpenShift Operator [here](https://docs.openshift.com/container-platform/4.15/operators/index.html).
 
<br>   

## Supported Features

The operator supports the following features:

- Recognition of CMM-D installed alongside local memory on the server, with automatic memory integration and expansion.
- Ability to specify the required resources by configuring CPU core and memory NUMA settings.
- Scheduling functionality that ensures new pods are created on server nodes with optimal resource allocation.
- Easy installation through the Red Hat OpenShift Container Platform (OCP) Web Console.
- Integration with OCP event logs for error handling.
- Support for the following Kubernetes-based objects:
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
The Samsung Operator for CMM-D has been tested and verified on OpenShift and Kubernetes.   
Supported versions include:   

|Kubernetes version|Openshift version| RHEL version|
|-------------------|-------------------|-------------------|
|v1.27.10+c79e5e2|4.14.16|9.2
 
For information on the environment in which the Samsung Operator for CMM-D was tested and verified, please refer to the document [here](./document/CXL_requiements.md)

<br>   

## Quick Start Guide
Hardware and software requirements for installing, configuring, and utilizing the Samsung CMM-D Operator can be found in the document [here](./document/Quick_StartGuid.md).   
For instructions on how to use it, please refer to the document [here](./document/Quick_StartGuid.md)  


<br>

## E2E Test
The End-to-End Test of the Samsung CMM-D Operator included various use case scenarios involving the Samsung CMM-D equipment:   
- Creation and recovery of pods by manually setting resources (CPU, memory) as needed.
- Automatic generation and allocation of pod resources (CPU, memory) from the target node to the most efficient node.
- Configuration of pod resources to utilize local memory.
  
For detailed information about this test, please refer to the document [E2E Test Document](./document/E2E_Test_Guide.md).
