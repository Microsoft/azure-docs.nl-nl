---
title: 'Quickstart: Een AKS-cluster met Confidential Computing-knooppunten implementeren met behulp van de Azure CLI'
description: Informatie over het maken van een AKS Azure Kubernetes Service cluster met vertrouwelijke knooppunten en het implementeren van een Hallo wereld-app met behulp van de Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 261deb0c4f5f28be51e806ab76261278709efc3b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482871"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Quickstart: Een AKS-cluster met Confidential Computing-knooppunten implementeren met behulp van de Azure CLI

In deze quickstart gebruikt u de Azure CLI om een AKS-cluster (Azure Kubernetes Service) te implementeren met DCsv2-knooppunten (Confidential Computing). Vervolgens gaat u een eenvoudige toepassing Hallo wereld in een enclave. U kunt ook een cluster inrichten en Confidential Computing-knooppunten toevoegen vanuit de Azure Portal, maar deze quickstart is gericht op de Azure CLI.

AKS is een beheerde Kubernetes-service waarmee ontwikkelaars of clusteroperators snel clusters kunnen implementeren en beheren. Lees de inleiding tot [AKS](../aks/intro-kubernetes.md) en het overzicht van vertrouwelijke [AKS-knooppunten](confidential-nodes-aks-overview.md)voor meer informatie.

Functies van confidential computing-knooppunten zijn onder andere:

- Linux-werkknooppunten die Linux-containers ondersteunen.
- Virtuele machine (VM) van de tweede generatie met Ubuntu 18.04 VM-knooppunten.
- Intel SGX-geschikte CPU om uw containers uit te voeren in een met vertrouwelijkheid beveiligde enclave die gebruik maakt van encrypted Page Cache Memory (EPC). Zie Veelgestelde vragen [over Azure Confidential Computing voor meer informatie.](./faq.md)
- Intel SGX DCAP-stuurprogramma vooraf geïnstalleerd op de confidential computing-knooppunten. Zie Veelgestelde vragen [over Azure Confidential Computing voor meer informatie.](./faq.md)

> [!NOTE]
> Virtuele DCsv2-VM's maken gebruik van gespecialiseerde hardware die onderhevig is aan hogere prijzen en beschikbaarheid in regio's. Zie de beschikbare SKU's en [ondersteunde regio's voor meer informatie.](virtual-machine-solutions.md)

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

- Een actief Azure-abonnement. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI versie 2.0.64 of hoger is geïnstalleerd en geconfigureerd op uw implementatiemachine. 

  Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](../container-registry/container-registry-get-started-azure-cli.md).
- Er zijn minimaal zes DCsv2-kernen beschikbaar in uw abonnement. 

  Het quotum voor confidential computing per Azure-abonnement is standaard acht VM-kernen. Als u een cluster wilt inrichten dat meer [](../azure-portal/supportability/per-vm-quota-requests.md) dan acht kernen nodig heeft, volgt u deze instructies om een ticket voor quotumverhoging te maken.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Een AKS-cluster maken met confidential computing-knooppunten en een invoeg-on

Volg de volgende instructies om een AKS-cluster te maken waarvoor de invoeggebruiker confidential computing is ingeschakeld, voeg een knooppuntgroep toe aan het cluster en controleer wat u hebt gemaakt.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Een AKS-cluster maken met een systeemknooppuntgroep

> [!NOTE]
> Als u al een AKS-cluster hebt dat voldoet aan de vereisten die eerder zijn [vermeld,](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) gaat u naar de volgende sectie om een confidential computing-knooppuntgroep toe te voegen.

Maak eerst een resourcegroep voor het cluster met behulp van [de opdracht az group create.][az-group-create] In het volgende voorbeeld wordt een resourcegroep met de *naam myResourceGroup* gemaakt in de *regio westus2:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Maak nu een AKS-cluster, met de invoegversie confidential computing ingeschakeld, met behulp van de [opdracht az aks create:][az-aks-create]

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Een gebruikersknooppuntgroep met confidential computing-mogelijkheden toevoegen aan het AKS-cluster 

Voer de volgende opdracht uit om een gebruikersknooppuntgroep van grootte met drie knooppunten toe te voegen `Standard_DC2s_v2` aan het AKS-cluster. U kunt een andere SKU kiezen in de [lijst met ondersteunde DCsv2-SKU's en -regio's.](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Nadat u de opdracht hebt uitgevoerd, moet er een nieuwe knooppuntgroep met DCsv2 worden weergegeven met confidential computing-invoeggebruiker DaemonSets ([SGX-apparaatinvoeging](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)).

### <a name="verify-the-node-pool-and-add-on"></a>De knooppuntgroep en invoeg-on controleren

Haal de referenties voor uw AKS-cluster op met behulp van [de opdracht az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Gebruik de opdracht om te controleren of de knooppunten correct zijn gemaakt en of `kubectl get pods` de SGX-gerelateerde DaemonSets worden uitgevoerd op DCsv2-knooppuntgroepen:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Als de uitvoer overeenkomt met de voorgaande code, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren.

U kunt in deze quickstart naar de sectie [Deploy Hallo wereld from an isolated enclave application](#hello-world) (Een app implementeren vanuit een geïsoleerde enclavetoepassing) gaan om een app in een enclave te testen. Of gebruik de volgende instructies om meer knooppuntgroepen toe te voegen aan AKS. (AKS ondersteunt het combineren van SGX-knooppuntgroepen en niet-SGX-knooppuntgroepen.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Een Confidential Computing-knooppuntgroep toevoegen aan een bestaand AKS-cluster<a id="existing-cluster"></a>

In deze sectie wordt ervan uitgenomen dat u al een AKS-cluster hebt dat voldoet aan de vereisten die eerder in deze quickstart zijn vermeld.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>De AKS-invoegversie van Confidential Computing inschakelen voor het bestaande cluster

Voer de volgende opdracht uit om de invoeg-on confidential computing in teschakelen:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Een DCsv2-gebruikersknooppuntgroep toevoegen aan het cluster

> [!NOTE]
> Als u de confidential computing-functie wilt gebruiken, moet uw bestaande AKS-cluster minimaal één knooppuntgroep hebben die is gebaseerd op een DCsv2 VM-SKU. Zie de beschikbare SKU's en ondersteunde regio's voor meer informatie over SKU's voor DCs-v2-VM's voor confidential [computing.](virtual-machine-solutions.md)

Voer de volgende opdracht uit om een knooppuntgroep te maken:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Controleer of de nieuwe knooppuntgroep met de naam *confcompool1* is gemaakt:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Controleren of DaemonSets worden uitgevoerd op vertrouwelijke knooppuntgroepen

Meld u aan bij uw bestaande AKS-cluster om de volgende verificatie uit te voeren:

```console
kubectl get nodes
```

De uitvoer moet de zojuist toegevoegde *confcompool1-pool* op het AKS-cluster tonen. Mogelijk ziet u ook andere DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Als de uitvoer overeenkomt met de voorgaande code, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Een Hallo wereld implementeren vanuit een geïsoleerde enclavetoepassing <a id="hello-world"></a>
U bent nu klaar om een testtoepassing te implementeren. 

Maak een bestand met de *naam hello-world-enclave.yaml* en plak het volgende YAML-manifest. U vindt deze voorbeeldtoepassingscode in het [Open Enclave-project](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Bij deze implementatie wordt ervan uitgenomen dat u de confcom-invoegvoeggebruiker hebt geïmplementeerd. 

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

Gebruik nu de opdracht om een voorbeeld van een taak te maken die wordt geopend in een `kubectl apply` beveiligde enclave, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

U kunt controleren of de workload een Trusted Execution Environment (enclave) heeft gemaakt door de volgende opdrachten uit te voeren:

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

Als u de confidential computing-knooppuntgroep wilt verwijderen die u in deze quickstart hebt gemaakt, gebruikt u de volgende opdracht: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Gebruik de volgende opdracht om het AKS-cluster te verwijderen: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Volgende stappen

* Voer Python, Node of andere toepassingen uit via vertrouwelijke containers met behulp van de voorbeelden [van vertrouwelijke containers in GitHub](https://github.com/Azure-Samples/confidential-container-samples).

* Voer enclave-aware toepassingen uit met behulp van [de enclave-aware Azure-containervoorbeelden in GitHub](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
