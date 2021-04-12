---
title: 'Snelstartgids: een AKS-cluster met vertrouwelijke computing knooppunten implementeren met behulp van de Azure CLI'
description: Meer informatie over het maken van een Azure Kubernetes service-cluster (AKS) met vertrouwelijke knoop punten en het implementeren van een Hallo wereld-app met behulp van de Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: b012a8a5856b344b366f1ddd89fc5059a6f3c8ae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283521"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Snelstartgids: een AKS-cluster met vertrouwelijke computing knooppunten implementeren met behulp van de Azure CLI

In deze Quick Start gebruikt u de Azure CLI om een Azure Kubernetes service (AKS)-cluster te implementeren met een DCsv2-knoop punt (vertrouwelijk Computing). Vervolgens voert u een eenvoudige Hallo wereld-toepassing uit in een enclave. U kunt ook een cluster inrichten en vertrouwelijke computer knooppunten toevoegen vanuit de Azure Portal, maar deze Snelstartgids is gericht op de Azure CLI.

AKS is een beheerde Kubernetes-service waarmee ontwikkel aars of cluster operators snel clusters kunnen implementeren en beheren. Lees voor meer informatie de [AKS-Inleiding](../aks/intro-kubernetes.md) en het [overzicht van AKS vertrouwelijke knoop punten](confidential-nodes-aks-overview.md).

Functies van vertrouwelijke computing knooppunten zijn onder andere:

- Linux-werk knooppunten die Linux-containers ondersteunen.
- 2e generatie virtuele machine (VM) met Ubuntu 18,04 VM-knoop punten.
- Intel SGX-compatibele CPU voor het uitvoeren van uw containers in vertrouwelijk beveiligde enclave met encrypted page cache memory (EPC). Zie [Veelgestelde vragen over Azure vertrouwelijk computing](./faq.md)voor meer informatie.
- Intel SGX DCAP-stuur programma vooraf geïnstalleerd op de vertrouwelijke computer knooppunten. Zie [Veelgestelde vragen over Azure vertrouwelijk computing](./faq.md)voor meer informatie.

> [!NOTE]
> DCsv2 Vm's gebruiken speciale hardware die is onderworpen aan hogere prijzen en beschik baarheid van de regio. Zie de [beschik bare sku's en ondersteunde regio's](virtual-machine-solutions.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

- Een actief Azure-abonnement. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- De Azure CLI-versie 2.0.64 of hoger is geïnstalleerd en geconfigureerd op uw implementatie computer. 

  Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](../container-registry/container-registry-get-started-azure-cli.md).
- Ten minste zes DCsv2 kernen beschikbaar in uw abonnement. 

  Het quotum voor vertrouwelijke computing per Azure-abonnement is standaard acht VM-kernen. Als u van plan bent een cluster in te richten waarvoor meer dan acht kernen nodig zijn, volgt u [deze instructies](../azure-portal/supportability/per-vm-quota-requests.md) om een quotum voor quota verhoging te genereren.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Een AKS-cluster maken met vertrouwelijke computer knooppunten en-invoeg toepassingen

Gebruik de volgende instructies om een AKS-cluster te maken met de functie voor het toevoegen van vertrouwelijke computers, het toevoegen van een knooppunt groep aan het cluster en het controleren van wat u hebt gemaakt.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Een AKS-cluster met een systeem knooppunt groep maken

> [!NOTE]
> Als u al een AKS-cluster hebt dat voldoet aan de hierboven vermelde vereisten criteria, [gaat u naar de volgende sectie](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) om een vertrouwelijk computing-knooppunt groep toe te voegen.

Maak eerst een resource groep voor het cluster met behulp van de opdracht [AZ Group Create][az-group-create] . In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt in de regio *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Maak nu een AKS-cluster, waarbij de add-on van de functie voor vertrouwelijke computers is ingeschakeld, met behulp van de opdracht [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Een groep met gebruikers knooppunten met vertrouwelijke computer mogelijkheden toevoegen aan het AKS-cluster 

Voer de volgende opdracht uit om een gebruikers knooppunt groep van `Standard_DC2s_v2` grootte met drie knoop punten toe te voegen aan het AKS-cluster. U kunt in de [lijst met ondersteunde DCsv2 sku's en regio's](../virtual-machines/dcv2-series.md)een andere SKU kiezen.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Nadat u de opdracht hebt uitgevoerd, moet u een nieuwe knooppunt groep met DCsv2 zichtbaar maken met de vertrouwelijke computer add-on DaemonSets ([SGX-apparaat-invoeg toepassing](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)).

### <a name="verify-the-node-pool-and-add-on"></a>De knooppunt groep en-invoeg toepassing controleren

Haal de referenties voor uw AKS-cluster op met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Gebruik de `kubectl get pods` opdracht om te controleren of de knoop punten correct zijn gemaakt en de SGX-gerelateerde DaemonSets worden uitgevoerd op DCsv2-knooppunt groepen:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Als de uitvoer overeenkomt met de voor gaande code, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren.

U kunt in deze Quick Start naar het gedeelte [Hallo wereld implementeren vanuit een geïsoleerde enclave-toepassing](#hello-world) gaan om een app in een enclave te testen. U kunt ook de volgende instructies gebruiken om meer knooppunt groepen toe te voegen op AKS. (AKS ondersteunt het mixen van SGX-knooppunt Pools en niet-SGX-knooppunt Pools.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Een groep met een vertrouwelijk computer knooppunt toevoegen aan een bestaand AKS-cluster<a id="existing-cluster"></a>

In deze sectie wordt ervan uitgegaan dat u al een AKS-cluster uitvoert dat voldoet aan de vereiste criteria die eerder in deze Quick start zijn vermeld.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>De AKS-invoeg toepassing voor vertrouwelijke computing inschakelen op het bestaande cluster

Voer de volgende opdracht uit om de add-on van de vertrouwelijk computing in te scha kelen:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Een DCsv2 toevoegen aan het cluster

> [!NOTE]
> Als u de functie voor vertrouwelijke computing wilt gebruiken, moet uw bestaande AKS-cluster mini maal één knooppunt groep hebben die is gebaseerd op een DCsv2 VM-SKU. Zie de [beschik bare sku's en ondersteunde regio's](virtual-machine-solutions.md)voor meer informatie over de vm's van de DC-v2 voor vertrouwelijke computing.

Voer de volgende opdracht uit om een knooppunt groep te maken:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Controleer of de nieuwe knooppunt groep met de naam *confcompool1* is gemaakt:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Controleren of DaemonSets worden uitgevoerd op groepen met een vertrouwelijk knoop punt

Meld u aan bij uw bestaande AKS-cluster om de volgende verificatie uit te voeren:

```console
kubectl get nodes
```

De uitvoer moet de zojuist toegevoegde *confcompool1* -groep weer geven op het AKS-cluster. Mogelijk ziet u ook andere DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Als de uitvoer overeenkomt met de voor gaande code, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Hallo wereld implementeren vanuit een geïsoleerde enclave-toepassing <a id="hello-world"></a>
U bent nu klaar om een test toepassing te implementeren. 

Maak een bestand met de naam *Hello-World-enclave. yaml* en plak het volgende YAML-manifest. U kunt deze voorbeeld toepassings code vinden in het [project enclave openen](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Bij deze implementatie wordt ervan uitgegaan dat u de *confcom* -invoeg toepassing hebt geïmplementeerd.

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
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Gebruik nu de `kubectl apply` opdracht voor het maken van een voorbeeld taak die in een beveiligde enclave wordt geopend, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

U kunt controleren of de werk belasting een enclave (Trusted Execution Environment) heeft gemaakt door de volgende opdrachten uit te voeren:

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

Gebruik de volgende opdracht om de groep met vertrouwelijke computing-knoop punten die u in deze Quick Start hebt gemaakt, te verwijderen: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Als u het AKS-cluster wilt verwijderen, gebruikt u de volgende opdracht: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Volgende stappen

* Voer python, node of andere toepassingen uit via vertrouwelijke containers met behulp van de voor beelden van de [vertrouwelijke container in github](https://github.com/Azure-Samples/confidential-container-samples).

* Voer enclave-toepassingen uit met behulp van de [enclave-bewuste Azure container-voor beelden in github](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
