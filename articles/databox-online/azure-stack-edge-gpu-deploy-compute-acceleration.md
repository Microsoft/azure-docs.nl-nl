---
title: Compute Acceleration GPU of VPU op Azure Stack edge-apparaten gebruiken voor Kubernetes-implementaties | Microsoft Docs
description: Hierin wordt beschreven hoe u GPU of VPU van Compute Acceleration kunt gebruiken op uw Azure Stack Edge Pro GPU, Azure Stack Edge Pro R of Azure Stack Edge-minitower voor Kubernetes-implementaties.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 3eb648af60a7be62d08f6b172347778d2358643c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440119"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Berekenings versnelling gebruiken op Azure Stack Edge Pro GPU voor Kubernetes-implementatie

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u de verwerkings versnelling kunt gebruiken op Azure Stack edge-apparaten wanneer u Kubernetes-implementaties gebruikt. Het artikel is van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten.


## <a name="about-compute-acceleration"></a>Over Compute Acceleration 

De CPU (Central Processing Unit) is uw standaard Compute voor algemeen gebruik voor de meeste processen die op een computer worden uitgevoerd. Vaak wordt een gespecialiseerde computerhardware gebruikt om bepaalde functies efficiënter uit te voeren dan het uitvoeren van die in de software in een CPU. Een GPU (graphics processing unit) kan bijvoorbeeld worden gebruikt om de verwerking van pixel gegevens te versnellen.  

Berekenings versnelling is een term die specifiek wordt gebruikt voor Azure Stack edge-apparaten, waarbij een GPU (grafische verwerkings eenheid), een Vision processing unit (VPU) of een veld Programmeer bare poort matrix (FPGA) wordt gebruikt voor hardwareversnelling. De meeste workloads die op uw Azure Stack edge-apparaat zijn geïmplementeerd, zijn kritiek, meerdere camera stromen en/of hoge frame snelheden, die allemaal specifieke hardwareversnelling nodig hebben.

In het artikel wordt Compute Acceleration alleen beschreven met behulp van GPU of VPU voor de volgende apparaten:

- **Azure stack Edge Pro GPU** : deze apparaten kunnen 1 of 2 NVIDIA T4 TENSOR core GPU hebben. Zie [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)voor meer informatie.
- **Azure stack Edge Pro R** : deze apparaten hebben één nVidia T4 TENSOR core GPU. Zie [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)voor meer informatie.
- **Azure stack Edge mini maal R** : deze apparaten hebben 1 Intel Movidius TALLOZE X VPU. Zie [Intel Movidius talloze X VPU](https://www.movidius.com/MyriadX)voor meer informatie.


## <a name="use-gpu-for-kubernetes-deployment"></a>GPU gebruiken voor Kubernetes-implementatie

Het volgende voor beeld `yaml` kan worden gebruikt voor een Azure stack Edge Pro GPU of een Azure stack Edge Pro R-apparaat met een GPU.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>VPU gebruiken voor Kubernetes-implementatie

Het volgende voor beeld `yaml` kan worden gebruikt voor een Azure stack Edge mini-R-apparaat met een VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [gebruik van kubectl voor het uitvoeren van een Kubernetes stateful-toepassing met een PersistentVolume op uw Azure stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
