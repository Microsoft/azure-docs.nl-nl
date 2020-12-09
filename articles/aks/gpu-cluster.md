---
title: Gpu's gebruiken op Azure Kubernetes service (AKS)
description: Meer informatie over het gebruik van Gpu's voor high performance Compute of grafisch intensieve workloads op Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929137"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Gebruik Gpu's voor computerintensieve werk belastingen op Azure Kubernetes service (AKS)

Grafische verwerkings eenheden (Gpu's) worden vaak gebruikt voor computerintensieve werk belastingen, zoals grafische werk belastingen en visualisaties. AKS biedt ondersteuning voor het maken van knooppunt groepen met GPU-functionaliteit voor het uitvoeren van deze reken intensief werk belastingen in Kubernetes. Zie voor meer informatie over beschik bare virtuele machines met GPU voor [GPU geoptimaliseerde VM-grootten in azure][gpu-skus]. Voor AKS-knoop punten wordt een minimale grootte van *Standard_NC6* aangeraden.

> [!NOTE]
> Virtuele machines met GPU bevatten gespecialiseerde hardware waarvoor hogere prijzen en beschik baarheid van de regio gelden. Zie de [prijs][azure-pricing] informatie en [Beschik baarheid van regio's][azure-availability]voor meer informatie.

Momenteel is het gebruik van knooppunt Pools met GPU ingeschakeld alleen beschikbaar voor Linux-knooppunt groepen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster met knoop punten die Gpu's ondersteunen. Uw AKS-cluster moet Kubernetes 1,10 of hoger uitvoeren. Als u een AKS-cluster nodig hebt dat aan deze vereisten voldoet, raadpleegt u de eerste sectie van dit artikel om [een AKS-cluster te maken](#create-an-aks-cluster).

Ook moet de Azure CLI-versie 2.0.64 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Als u een AKS-cluster nodig hebt dat voldoet aan de minimale vereisten (GPU-ingeschakeld knoop punt en Kubernetes versie 1,10 of hoger), voert u de volgende stappen uit. Als u al een AKS-cluster hebt dat aan deze vereisten voldoet, [gaat u verder met de volgende sectie](#confirm-that-gpus-are-schedulable).

Maak eerst een resource groep voor het cluster met behulp van de opdracht [AZ Group Create][az-group-create] . In het volgende voor beeld wordt de naam van een resource groep *myResourceGroup* in de regio *oostus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een AKS-cluster met behulp van de opdracht [AZ AKS Create][az-aks-create] . In het volgende voor beeld wordt een cluster gemaakt met één knoop punt van grootte `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Haal de referenties voor uw AKS-cluster op met de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>De invoeg toepassing voor NVIDIA-apparaten installeren

Voordat de Gpu's in de knoop punten kunnen worden gebruikt, moet u een Daemonset voor de invoeg toepassing voor NVIDIA-apparaten implementeren. Deze Daemonset voert een pod uit op elk knoop punt om de vereiste Stuur Programma's voor de Gpu's op te geven.

Maak eerst een naam ruimte met behulp van de kubectl-opdracht [naam ruimte maken][kubectl-create] , zoals *GPU-resources*:

```console
kubectl create namespace gpu-resources
```

Maak een bestand met de naam *NVIDIA-apparaat-plugin-DS. yaml* en plak het volgende YAML-manifest. Dit manifest wordt meegeleverd als onderdeel van de [NVIDIA-apparaat-invoeg toepassing voor Kubernetes-project][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Gebruik nu de opdracht [kubectl apply][kubectl-apply] om de daemonset te maken en bevestig dat de invoeg toepassing voor nvidia-apparaten is gemaakt, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>De AKS Special GPU-installatie kopie gebruiken (preview-versie)

Als alternatief voor deze stappen biedt AKS een volledig geconfigureerde AKS-installatie kopie die al de [invoeg toepassing voor nvidia-apparaten bevat voor Kubernetes][nvidia-github].

> [!WARNING]
> U moet niet hand matig de NVIDIA-apparaat-daemon voor de invoeg toepassing installeren voor clusters met behulp van de nieuwe AKS specialistische GPU-installatie kopie.


De `GPUDedicatedVHDPreview` functie registreren:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status als **geregistreerd** wordt weer gegeven. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt u de registratie van de `Microsoft.ContainerService` resource provider met behulp van de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Als u de AKS-preview CLI-extensie wilt installeren, gebruikt u de volgende Azure CLI-opdrachten:

```azurecli
az extension add --name aks-preview
```

Gebruik de volgende Azure CLI-opdrachten om de CLI-extensie AKS-preview te installeren:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>De AKS specialistische GPU-installatie kopie gebruiken in nieuwe clusters (preview-versie)    

Configureer het cluster voor het gebruik van de AKS specialistische GPU-installatie kopie wanneer het cluster wordt gemaakt. Gebruik de `--aks-custom-headers` vlag voor de GPU-agent knooppunten op uw nieuwe cluster om de AKS specialistische GPU-afbeelding te gebruiken.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Als u een cluster wilt maken met behulp van de gewone AKS-installatie kopieën, kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` . U kunt er ook voor kiezen om meer gespecialiseerde GPU-knooppunt groepen toe te voegen aan de hand van hieronder.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>De AKS specialistische GPU-installatie kopie gebruiken in bestaande clusters (preview-versie)

Configureer een nieuwe knooppunt groep om de AKS Special GPU-installatie kopie te gebruiken. Gebruik de `--aks-custom-headers` vlag Flag voor de GPU-agent knooppunten in de nieuwe knooppunt groep om de AKS Special GPU-installatie kopie te gebruiken.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Als u een knooppunt groep wilt maken met behulp van de gewone AKS-installatie kopieën, kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` . 

> [!NOTE]
> Als uw GPU-SKU generatie 2 virtuele machines vereist, kunt u het volgende doen:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Controleer of de Gpu's Schedulable zijn

Als uw AKS-cluster is gemaakt, controleert u of de Gpu's Schedulable zijn in Kubernetes. Vermeld eerst de knoop punten in uw cluster met behulp van de kubectl-opdracht [knoop punten ophalen][kubectl-get] :

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Gebruik nu de opdracht [kubectl beschrijven knoop punt][kubectl-describe] om te bevestigen dat de gpu's Schedulable zijn. Onder het gedeelte *capaciteit* moet de GPU worden weer geven als `nvidia.com/gpu:  1` .

In het volgende verkorte voor beeld ziet u dat er een GPU beschikbaar is op het knoop punt met de naam *AKS-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Een werk belasting met GPU uitvoeren

Als u de GPU in actie wilt zien, moet u een werk belasting met GPU plannen met de juiste resource aanvraag. In dit voor beeld wordt een [tensor flow](https://www.tensorflow.org/) -taak uitgevoerd op basis van de [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/).

Maak een bestand met de naam *samples-TF-mnist-demo. yaml* en plak het volgende YAML-manifest. Het volgende taak manifest bevat een resource limiet van `nvidia.com/gpu: 1` :

> [!NOTE]
> Als er een fout is opgetreden die niet overeenkomt bij het aanroepen van Stuur Programma's, zoals de versie van het CUDA-stuur programma is niet voldoende voor de CUDA-runtime versie, raadpleegt u de compatibiliteits grafiek voor NVIDIA-Stuur [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Gebruik de opdracht [kubectl Toep assen][kubectl-apply] om de taak uit te voeren. Deze opdracht parseert het manifest bestand en maakt de gedefinieerde Kubernetes-objecten:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Bekijk de status en de uitvoer van de GPU-ingeschakelde werk belasting

Bewaak de voortgang van de taak met behulp van de opdracht [kubectl Get Jobs][kubectl-get] with het `--watch` argument. Het kan een paar minuten duren voordat de installatie kopie is opgehaald en de gegevensset wordt verwerkt. Wanneer in de kolom *voltooiings* de *1/1* wordt weer gegeven, is de taak voltooid. Sluit de `kubetctl --watch` opdracht af met *CTRL-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Als u de uitvoer van de GPU-ingeschakelde werk belasting wilt bekijken, moet u eerst de naam van de pod ophalen met de opdracht [kubectl Get peul][kubectl-get] :

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Gebruik nu de [kubectl-logboeken][kubectl-logs] opdracht om de pod-logboeken weer te geven. In het volgende voor beeld worden pod-logboeken gecontroleerd of het juiste GPU-apparaat is gedetecteerd `Tesla K80` . Geef de naam op voor uw eigen Pod:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gekoppelde Kubernetes-objecten die in dit artikel zijn gemaakt wilt verwijderen, gebruikt u de opdracht [kubectl verwijderen][kubectl delete] als volgt:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Volgende stappen

Zie [Apache Spark-taken uitvoeren op AKS][aks-spark]om Apache Spark taken uit te voeren.

Zie [Kubeflow Labs][kubeflow-labs](Engelstalig) voor meer informatie over het uitvoeren van machine learning (ml) workloads op Kubernetes.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
