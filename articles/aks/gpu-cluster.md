---
title: GPU's op Azure Kubernetes Service (AKS) gebruiken
description: Meer informatie over het gebruik van GPU's voor high performance reken- of grafisch-intensieve workloads op Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5e36465c307443c8e6f135c5937bddbbb079b60e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783157"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>GPU's gebruiken voor rekenintensieve workloads op Azure Kubernetes Service (AKS)

Grafische verwerkingseenheden (GPU's) worden vaak gebruikt voor rekenintensieve workloads, zoals grafische en visualisatieworkloads. AKS ondersteunt het maken van knooppuntgroepen met GPU om deze rekenintensieve workloads in Kubernetes uit te voeren. Zie Voor GPU geoptimaliseerde VM-grootten in Azure voor meer informatie over beschikbare [VM's][gpu-skus]met GPU. Voor AKS-knooppunten wordt een minimale grootte *van* Standard_NC6.

> [!NOTE]
> VM's met GPU bevatten gespecialiseerde hardware die onderhevig is aan hogere prijzen en beschikbaarheid in regio's. Zie het prijsprogramma en de [beschikbaarheid][azure-pricing] in [regio's voor meer informatie.][azure-availability]

Op dit moment is het gebruik van knooppuntgroepen met GPU alleen beschikbaar voor Linux-knooppuntgroepen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt met knooppunten die GPU's ondersteunen. Op uw AKS-cluster moet Kubernetes 1.10 of hoger worden uitgevoerd. Als u een AKS-cluster nodig hebt dat aan deze vereisten voldoet, bekijkt u de eerste sectie van dit artikel om [een AKS-cluster te maken.](#create-an-aks-cluster)

Ook moet Azure CLI versie 2.0.64 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Als u een AKS-cluster nodig hebt dat voldoet aan de minimale vereisten (knooppunt met GPU en Kubernetes versie 1.10 of hoger), moet u de volgende stappen voltooien. Als u al een AKS-cluster hebt dat aan deze vereisten voldoet, [gaat u verder met de volgende sectie](#confirm-that-gpus-are-schedulable).

Maak eerst een resourcegroep voor het cluster met behulp van [de opdracht az group create.][az-group-create] In het volgende voorbeeld wordt een resourcegroep met de *naam myResourceGroup* gemaakt in de *regio eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een AKS-cluster met de [opdracht az aks create.][az-aks-create] In het volgende voorbeeld wordt een cluster gemaakt met één knooppunt met de grootte `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Haal de referenties voor uw AKS-cluster op met de [opdracht az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>NVIDIA-apparaatinvoeging installeren

Voordat de GPU's in de knooppunten kunnen worden gebruikt, moet u een DaemonSet implementeren voor de NVIDIA-apparaatinvoeging. Met deze DaemonSet wordt op elk knooppunt een pod uitgevoerd om de vereiste stuurprogramma's voor de GPU's op te geven.

Maak eerst een naamruimte met behulp van de [opdracht kubectl create namespace,][kubectl-create] zoals *gpu-resources:*

```console
kubectl create namespace gpu-resources
```

Maak een bestand met de *naam nvidia-device-plugin-ds.yaml* en plak het volgende YAML-manifest. Dit manifest wordt geleverd als onderdeel van de [NVIDIA-apparaatinvoegsel voor Kubernetes-project][nvidia-github].

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

Gebruik nu de [opdracht kubectl apply][kubectl-apply] om de DaemonSet te maken en te bevestigen dat de NVIDIA-apparaatinvoeggebruiker is gemaakt, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>De gespecialiseerde GPU-afbeelding van AKS gebruiken (preview)

Als alternatief voor deze stappen biedt AKS een volledig geconfigureerde AKS-afbeelding die al de NVIDIA-apparaatinvoegsel voor [Kubernetes bevat.][nvidia-github]

> [!WARNING]
> U moet de NVIDIA-apparaatinvoegings-daemonset voor clusters niet handmatig installeren met behulp van de nieuwe gespecialiseerde AKS GPU-installatie afbeelding.


Registreer de `GPUDedicatedVHDPreview` functie:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status wordt weergeven als **Geregistreerd.** U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](/cli/azure/feature#az_feature_list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Wanneer de status geregistreerd is, vernieuwt u de registratie van de `Microsoft.ContainerService` resourceprovider met behulp van [de opdracht az provider register:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Gebruik de volgende Azure CLI-opdrachten om de CLI-extensie aks-preview te installeren:

```azurecli
az extension add --name aks-preview
```

Gebruik de volgende Azure CLI-opdrachten om de CLI-extensie AKS-preview te installeren:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>De gespecialiseerde GPU-afbeelding van AKS gebruiken op nieuwe clusters (preview)    

Configureer het cluster voor het gebruik van de gespecialiseerde GPU-afbeelding van AKS wanneer het cluster wordt gemaakt. Gebruik de `--aks-custom-headers` vlag voor de GPU-agentknooppunten op uw nieuwe cluster om de gespecialiseerde GPU-afbeelding van AKS te gebruiken.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Als u een cluster wilt maken met behulp van de reguliere AKS-afbeeldingen, kunt u dit doen door de aangepaste tag weg te `--aks-custom-headers` laten. U kunt er ook voor kiezen om meer gespecialiseerde GPU-knooppuntgroepen toe te voegen zoals hieronder wordt weergegeven.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>De gespecialiseerde AKS-GPU-afbeelding gebruiken op bestaande clusters (preview)

Configureer een nieuwe knooppuntgroep voor het gebruik van de gespecialiseerde GPU-afbeelding van AKS. Gebruik de `--aks-custom-headers` vlag voor de GPU-agentknooppunten in uw nieuwe knooppuntgroep om de gespecialiseerde AKS-GPU-afbeelding te gebruiken.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Als u een knooppuntgroep wilt maken met behulp van de reguliere AKS-afbeeldingen, kunt u dit doen door de aangepaste tag weg te `--aks-custom-headers` laten. 

> [!NOTE]
> Als uw GPU-SKU virtuele machines van de tweede generatie vereist, kunt u het volgende maken:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Controleer of GPU's kunnen worden geseuleerd

Nu uw AKS-cluster is gemaakt, controleert u of GPU's kunnen worden gesluisd in Kubernetes. Vermeld eerst de knooppunten in uw cluster met behulp van de [opdracht kubectl get nodes:][kubectl-get]

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Gebruik nu de [opdracht kubectl describe node][kubectl-describe] om te bevestigen dat de GPU's schedulable zijn. In de *sectie Capaciteit* moet de GPU worden vermeld als `nvidia.com/gpu:  1` .

In het volgende verkorte voorbeeld ziet u dat er een GPU beschikbaar is op het knooppunt met de naam *aks-nodepool1-18821093-0:*

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

## <a name="run-a-gpu-enabled-workload"></a>Een workload met GPU uitvoeren

Als u de GPU in actie wilt zien, moet u een workload met GPU plannen met de juiste resourceaanvraag. In dit voorbeeld voeren we een [Tensorflow-taak](https://www.tensorflow.org/) uit op de [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/).

Maak een bestand met de *naam samples-tf-mnist-demo.yaml* en plak het volgende YAML-manifest. Het volgende taakmanifest bevat een resourcelimiet van `nvidia.com/gpu: 1` :

> [!NOTE]
> Als er een fout wordt weergegeven over niet-overeenkomende versies bij het aanroepen van stuurprogramma's, zoals de versie van het CUDA-stuurprogramma is onvoldoende voor de CUDA-runtimeversie, bekijkt u de compatibiliteitsgrafiek van de NVIDIA-stuurprogrammamatrix: [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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

Gebruik de [opdracht kubectl apply][kubectl-apply] om de taak uit te voeren. Met deze opdracht wordt het manifestbestand geparseerd en worden de gedefinieerde Kubernetes-objecten gemaakt:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>De status en uitvoer van de gpu-workload weergeven

Controleer de voortgang van de taak met behulp van de [opdracht kubectl get jobs][kubectl-get] met het argument `--watch` . Het kan enkele minuten duren om eerst de afbeelding op te halen en de gegevensset te verwerken. Wanneer de *kolom COMPLETIONS* *1/1 toont,* is de taak voltooid. Sluit de `kubetctl --watch` opdracht af met *Ctrl+C:*

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Als u de uitvoer van de workload met GPU wilt bekijken, moet u eerst de naam van de pod op halen met de [opdracht kubectl get pods:][kubectl-get]

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Gebruik nu de [opdracht kubectl logs][kubectl-logs] om de podlogboeken weer te geven. In de volgende voorbeeldpodlogboeken wordt bevestigd dat het juiste GPU-apparaat is ontdekt, `Tesla K80` . Geef de naam op voor uw eigen pod:

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

Als u de gekoppelde Kubernetes-objecten wilt verwijderen die in dit artikel zijn gemaakt, gebruikt u de opdracht [kubectl delete][kubectl delete] job als volgt:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Volgende stappen

Zie Taken Apache Spark uitvoeren op [AKS om Apache Spark uit te voeren.][aks-spark]

Zie [Kubeflow Labs][kubeflow-labs]voor meer machine learning het uitvoeren van ml-workloads op Kubernetes.

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
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
