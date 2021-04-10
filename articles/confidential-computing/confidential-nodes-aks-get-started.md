---
title: 'Quickstart: Een Azure Kubernetes Service-cluster (AKS) implementeren met behulp van Azure CLI met vertrouwelijke rekenknooppunten'
description: In deze Quick Start leert u hoe u een AKS-cluster met vertrouwelijke knoop punten maakt en een Hello World-app implementeert met behulp van de Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: 73770acefc8a153e4a2f2fde146f9afd4c319cd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933131"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Snelstartgids: een Azure Kubernetes service (AKS)-cluster implementeren met vertrouwelijke computing nodes (DCsv2) met behulp van Azure CLI

Deze quickstart is bedoeld voor ontwikkelaars of clusteroperators die snel een AKS-cluster willen maken en een app implementeren om apps te bewaken die gebruikmaken van de beheerde Kubernetes-service in Azure. U kunt ook het cluster inrichten en vertrouwelijke computer knooppunten van Azure Portal toevoegen.

## <a name="overview"></a>Overzicht

In deze Quick Start leert u hoe u een Azure Kubernetes service-cluster (AKS) kunt implementeren met vertrouwelijke computing knoop punten met behulp van Azure CLI en een eenvoudige Hello World-toepassing kunt uitvoeren in een enclave. Azure is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. Lees voor meer informatie de [AKS-Inleiding](../aks/intro-kubernetes.md) en het overzicht van de [AKS-vertrouwelijke knoop punten](confidential-nodes-aks-overview.md).

> [!NOTE]
> Vertrouwelijke DCsv2-VM's maken gebruik van gespecialiseerde hardware waarvoor hogere prijzen en regionale beschikbaarheid gelden. Zie de pagina Virtuele machines over [beschikbare SKU's en ondersteunde regio's](virtual-machine-solutions.md) voor meer informatie.

### <a name="confidential-computing-node-features-dcsv2"></a>Functies van het DCsv2 (vertrouwelijk computing node)

1. Linux-werk knooppunten die Linux-containers ondersteunen.
1. VM van de tweede generatie met Ubuntu 18,04 Virtual Machines-knoop punten.
1. Intel SGX-gebaseerde CPU met versleutelde paginageheugencache (EPC). Meer informatie is [hier](./faq.md) beschikbaar.
1. Ondersteuning voor Kubernetes-versie 1.16 +.
1. Intel SGX DCAP-stuur programma vooraf geïnstalleerd op de AKS-knoop punten. Meer informatie is [hier](./faq.md) beschikbaar.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

1. Een actief Azure-abonnement. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
1. Azure CLI-versie 2.0.64 of hoger geïnstalleerd en geconfigureerd op uw implementatie computer (Voer uit `az --version` om de versie te vinden. Zie [Azure CLI installeren](../container-registry/container-registry-get-started-azure-cli.md) als u de CLI wilt installeren of een upgrade wilt uitvoeren.
1. Ten minste zes **DCsv2** kernen beschikbaar in uw abonnement voor gebruik. Het quotum voor VM-kernen voor het vertrouwelijke computing per Azure-abonnement is standaard acht kernen. Als u van plan bent een cluster in te richten waarvoor meer dan acht kernen nodig zijn, volgt u [deze](../azure-portal/supportability/per-vm-quota-requests.md) instructies om een quota verhogings ticket te genereren.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Een nieuw AKS-cluster maken met vertrouwelijke computer knooppunten en-invoeg toepassingen

Volg de onderstaande instructies voor het toevoegen van voor vertrouwelijke computer geschikte knoop punten met invoeg toepassing.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Een AKS-cluster met een systeem knooppunt groep maken

Als u al een AKS-cluster hebt dat voldoet aan de bovenstaande vereisten, [gaat u naar de sectie Bestaand cluster](#existing-cluster) om een nieuwe pool met vertrouwelijke knooppunten toe te voegen.

Maak eerst een resource groep voor het cluster met behulp van de opdracht [AZ Group Create][az-group-create] . In het volgende voorbeeld wordt een resourcegroep met de naam *mijnResourcegroep* gemaakt in de regio *West US 2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Maak nu een AKS-cluster met behulp van de opdracht [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

In het bovenstaande wordt een nieuw AKS-cluster gemaakt met een systeem knooppunt groep waarvoor de invoeg toepassing is ingeschakeld. Voeg vervolgens een gebruikers knooppunt groep met vertrouwelijke computer mogelijkheden toe aan het AKS-cluster.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Een groep met een vertrouwelijk computer knooppunt toevoegen aan het AKS-cluster 

Voer de volgende opdracht uit om een gebruikers knooppunt groep `Standard_DC2s_v2` met drie knoop punten toe te voegen. U kunt een andere SKU kiezen in de lijst met ondersteunde [DCsv2 sku's en regio's](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Na het uitvoeren moet een nieuwe knooppunt groep met **DCsv2** zichtbaar zijn met de vertrouwelijke computer add-on Daemonsets ([SGX-invoeg apparaat](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>De knooppunt groep en-invoeg toepassing controleren

Haal de referenties voor uw AKS-cluster op met de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Controleer of de knoop punten correct zijn gemaakt en of de SGX-gerelateerde daemonsets worden uitgevoerd op **DCsv2** -knooppunt Pools met behulp van de opdracht kubectl ophalen van een Peul & knoop punten, zoals hieronder wordt weer gegeven:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Als de uitvoer overeenkomt met het bovenstaande, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren.

Ga naar het gedeelte [Hallo wereld van de implementatie van enclave](#hello-world) om een app in een enclave te testen. U kunt ook de onderstaande instructies volgen voor het toevoegen van extra knooppunt groepen op AKS (AKS ondersteunt het mixen van SGX-knooppunt Pools en niet-SGX-knooppunt groepen).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Een groep met een vertrouwelijk computer knooppunt toevoegen aan een bestaand AKS-cluster<a id="existing-cluster"></a>

In deze sectie wordt ervan uitgegaan dat er al een AKS-cluster wordt uitgevoerd dat voldoet aan de criteria die worden vermeld in de sectie prerequisites (van toepassing op add-on).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>De AKS-invoeg toepassing voor vertrouwelijke computing inschakelen op het bestaande cluster

Voer de volgende opdracht uit om de add-on van de vertrouwelijk computing in te scha kelen:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Een **DCsv2** toevoegen aan het cluster

> [!NOTE]
> Als u de functie voor vertrouwelijke computing wilt gebruiken, moet uw bestaande AKS-cluster mini maal één op **DCsv2** VM-SKU gebaseerde knooppunt groep hebben. Zie [beschik bare sku's en ondersteunde regio's](virtual-machine-solutions.md)voor meer informatie over de voor delen van vertrouwelijke computing DCS-v2 vm's.

Voer de volgende opdracht uit om een nieuwe knooppunt groep te maken:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Controleer of de nieuwe knooppunt groep met de naam confcompool1 is gemaakt:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Controleren of daemonsets worden uitgevoerd op groepen met een vertrouwelijk knoop punt

Meld u aan bij uw bestaande AKS-cluster om de volgende verificatie uit te voeren.

```console
kubectl get nodes
```

De uitvoer zou de zojuist toegevoegde 'confcompool1' in het AKS-cluster weer moeten geven. Mogelijk ziet u ook andere daemonsets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Als de uitvoer overeenkomt met het bovenstaande, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren. Volg de onderstaande instructies om een test toepassing te implementeren.

## <a name="hello-world-from-isolated-enclave-application"></a>'Hallo wereld' van de geïsoleerde enclavetoepassing <a id="hello-world"></a>
Maak een bestand met de naam *hello-world-enclave.yaml* en plak het volgende YAML-manifest. Deze op Open Enclave-gebaseerde voorbeeldtoepassingscode is te vinden in het [Open Enclave-project](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Bij de volgende implementatie wordt ervan uitgegaan dat u de addon ' confcom ' hebt geïmplementeerd.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Gebruik nu de opdracht 'kubectl apply' om een voorbeeldtaak te maken die wordt gestart in een beveiligde enclave, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

U kunt bevestigen dat de werkbelasting een Trusted Execution Environment (Enclave) heeft gemaakt door de volgende opdrachten uit te voeren:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gekoppelde knooppunt groepen wilt verwijderen of het AKS-cluster wilt verwijderen, gebruikt u de volgende opdrachten:

### <a name="remove-the-confidential-computing-node-pool"></a>De groep voor het vertrouwelijk computing-knoop punt verwijderen

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Het AKS-cluster verwijderen

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Volgende stappen

* Voer python-, node-, enz.-toepassingen uit met behulp van vertrouwelijke containers door de voor [beelden van vertrouwelijke container](https://github.com/Azure-Samples/confidential-container-samples)te bezoeken.

* Voer Enclave-compatibele toepassingen uit door [Enclave Aware Azure-containervoorbeelden](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/) te bezoeken.

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials