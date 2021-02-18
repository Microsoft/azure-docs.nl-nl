---
title: 'Quickstart: Een Azure Kubernetes Service-cluster (AKS) implementeren met behulp van Azure CLI met vertrouwelijke rekenknooppunten'
description: Meer informatie over het maken van een AKS-cluster met vertrouwelijke knooppunten en het implementeren van een Hello World-app met behulp van de Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/8/2020
ms.author: amgowda
ms.openlocfilehash: 866c8340cf9c16d768f4035326aa2ec52dbf1401
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653360"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Snelstartgids: een Azure Kubernetes service (AKS)-cluster implementeren met vertrouwelijke computing nodes (DCsv2) met behulp van Azure CLI

Deze quickstart is bedoeld voor ontwikkelaars of clusteroperators die snel een AKS-cluster willen maken en een app implementeren om apps te bewaken die gebruikmaken van de beheerde Kubernetes-service in Azure. U kunt ook het cluster inrichten en vertrouwelijke computer knooppunten van Azure Portal toevoegen.

## <a name="overview"></a>Overzicht

In deze Quick Start leert u hoe u een Azure Kubernetes service-cluster (AKS) kunt implementeren met vertrouwelijke computing knoop punten met behulp van Azure CLI en een eenvoudige Hello World-toepassing kunt uitvoeren in een enclave. Azure is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. [Hier](../aks/intro-kubernetes.md) vindt u meer informatie over AKS.

> [!NOTE]
> Vertrouwelijke DCsv2-VM's maken gebruik van gespecialiseerde hardware waarvoor hogere prijzen en regionale beschikbaarheid gelden. Zie de pagina Virtuele machines over [beschikbare SKU's en ondersteunde regio's](virtual-machine-solutions.md) voor meer informatie.

### <a name="confidential-computing-node-features-dcxs-v2"></a>Kenmerken van vertrouwelijke rekenknooppunten (DC<x>s-v2)

1. Linux-werkknooppunten bieden alleen ondersteuning voor Linux-containers
1. 2e generatie VM met Ubuntu 18.04 Virtual Machines-knooppunten
1. Intel SGX-gebaseerde CPU met versleutelde paginageheugencache (EPC). [Hier](./faq.md) vindt u meer informatie
1. Ondersteunende Kubernetes-versie 1.16+
1. Intel SGX DCAP-stuurprogramma vooraf geïnstalleerd op de AKS-knooppunten. [Hier](./faq.md) vindt u meer informatie

## <a name="deployment-prerequisites"></a>Vereisten voor implementatie
Voor de implementatie-zelf studie hebt u het volgende nodig:

1. Een actief Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint
1. Azure CLI-versie 2.0.64 of hoger geïnstalleerd en geconfigureerd op uw implementatie computer (Voer uit `az --version` om de versie te vinden. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren](../container-registry/container-registry-get-started-azure-cli.md).
1. Azure [AKS-preview-uitbrei ding](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) minimum versie 0.5.0
1. Mini maal zes **DC <x> s-v2** cores die beschikbaar zijn in uw abonnement voor gebruik. Standaard is het quotum van de VM-kernen voor vertrouwelijke machines 8 kernen per Azure-abonnement. Als u van plan bent een cluster in te richten waarvoor meer dan 8 kerngeheugens zijn vereist, volgt u [deze](../azure-portal/supportability/per-vm-quota-requests.md) instructies om een quotumverhogingsticket te genereren

## <a name="cli-based-preparation-steps-required-for-add-on-in-preview---optional-but-recommended"></a>Voorbereidings stappen op basis van CLI (vereist voor invoeg toepassing in Preview-optioneel, maar aanbevolen)
Volg de onderstaande instructies voor het inschakelen van de add-on voor vertrouwelijke computer op AKS.

### <a name="step-1-installing-the-cli-prerequisites"></a>Stap 1: de CLI-vereisten installeren

Gebruik de volgende Azure CLI-opdrachten om de 0.5.0-uitbrei ding AKS-preview of hoger te installeren:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Gebruik de volgende Azure CLI-opdrachten om de CLI-extensie AKS-preview te installeren:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="step-2-azure-confidential-computing-addon-feature-registration-on-azure"></a>Stap 2: registratie van invoeg toepassingen voor Azure vertrouwelijk Computing op Azure
De AKS-ConfidentialComputingAddon registreren op het Azure-abonnement. Met deze functie wordt het stuur programma voor de invoeg toepassing voor SGX-apparaten toegevoegd, zoals [hier](./confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)wordt beschreven:
1. Invoegtoepassing SGX-apparaatstuurprogramma
```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Het kan enkele minuten duren voordat de status Geregistreerd wordt weergegeven. U kunt de registratiestatus controleren met behulp van de opdracht az feature list. Deze functieregistratie wordt slechts eenmaal per abonnement uitgevoerd. Als deze eerder al is geregistreerd, kunt u de bovenstaande stap overslaan:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
```
Wanneer de status weergegeven wordt als geregistreerd, vernieuw dan de registratie van de resourceprovider Microsoft.ContainerService met behulp van de opdracht 'az provider register':

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```
## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Een nieuw AKS-cluster maken met vertrouwelijke computer knooppunten en-invoeg toepassingen
Volg de onderstaande instructies voor het toevoegen van voor vertrouwelijke computer geschikte knoop punten met invoeg toepassing.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>Stap 1: een AKS-cluster maken met een systeem knooppunt groep

Als u al een AKS-cluster hebt dat voldoet aan de bovenstaande vereisten, [gaat u naar de sectie Bestaand cluster](#existing-cluster) om een nieuwe pool met vertrouwelijke knooppunten toe te voegen.

Maak eerst een resourcegroep voor het cluster met de opdracht 'az group create'. In het volgende voorbeeld wordt een resourcegroep met de naam *mijnResourcegroep* gemaakt in de regio *West US 2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Gebruik nu de opdracht 'az aks create' om een AKS-cluster te maken.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
In het bovenstaande maakt u een nieuw AKS-cluster met een systeem knooppunt groep waarvoor de invoeg toepassing is ingeschakeld. Ga nu verder met het toevoegen van een gebruikersknooppunt van het type pool met vertrouwelijke knooppunten op AKS (DCsv2)

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>Stap 2: een groep voor vertrouwelijke computing toevoegen aan het AKS-cluster 

Voer de onderstaande opdracht uit voor een gebruiker nodepool van `Standard_DC2s_v2` grootte met 3 knoop punten. U kunt [hier](../virtual-machines/dcv2-series.md) een andere ondersteunde lijst met DCsv2-SKU's en regio‘s kiezen:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
De bovenstaande opdracht is voltooid een nieuwe knooppunt groep met **DC <x> s-v2** moet zichtbaar zijn met de Daemonsets-invoeg toepassing voor het toevoegen van vertrouwelijke computers ([SGX-apparaat-plugin](confidential-nodes-aks-overview.md#sgx-plugin) )
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>Stap 3: de knooppunt groep en-invoeg toepassing controleren
Haal de referenties voor uw AKS-cluster op met de opdracht 'az aks get-credentials':

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Controleer of de knooppunten correct zijn gemaakt en of de SGX-gerelateerde daemonsets worden uitgevoerd op **DC<x>s-v2**-knooppuntpools met behulp van de opdracht kubectl get pods & nodes, zoals hieronder wordt weergegeven:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Als de uitvoer overeenkomt met het bovenstaande, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren.

Ga naar de implementatiesectie [Hallo wereld van Enclave](#hello-world) om een app te testen in een enclave. U kunt ook de onderstaande instructies volgen voor het toevoegen van extra knooppunt groepen op AKS (AKS ondersteunt het mixen van SGX-knooppunt Pools en niet-SGX-knooppunt groepen)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Het knooppunt voor vertrouwelijk knooppuntpool toevoegen aan een bestaand AKS-cluster<a id="existing-cluster"></a>

In deze sectie wordt ervan uitgegaan dat er al een AKS-cluster wordt uitgevoerd dat voldoet aan de criteria die worden vermeld in de sectie prerequisites (van toepassing op add-on).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Stap 1: de AKS-invoeg toepassing voor vertrouwelijke computing inschakelen in het bestaande cluster

Voer de onderstaande opdracht uit om de add-on van de vertrouwelijk computing in te scha kelen

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>Stap 2: **DC <x> s-v2** gebruikers knooppunt groep toevoegen aan het cluster
    
> [!NOTE]
> Als u de vertrouwelijke rekenfunctie wilt gebruiken, moet uw bestaande AKS-cluster beschikken over ten minste één **DC<x>s-v2**  knooppuntpool op basis van een VM SKU. Meer informatie over DCsv2 VMs SKU's voor vertrouwelijk rekenen vindt u hier: [beschikbare SKU's en ondersteunde regio's](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
de bovenstaande opdracht moet een lijst met de recente knooppunt groep zijn die u hebt toegevoegd met de naam confcompool1.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Stap 3: controleren of daemonsets worden uitgevoerd op vertrouwelijke knooppunt groepen

Meld u aan bij uw bestaande AKS-cluster om de onderstaande verificatie uit te voeren. 

```console
kubectl get nodes
```
De uitvoer zou de zojuist toegevoegde 'confcompool1' in het AKS-cluster weer moeten geven.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Als de uitvoer overeenkomt met het bovenstaande, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren. Volg de onderstaande implementatie van de toepassing testen.

## <a name="hello-world-from-isolated-enclave-application"></a>'Hallo wereld' van de geïsoleerde enclavetoepassing <a id="hello-world"></a>
Maak een bestand met de naam *hello-world-enclave.yaml* en plak het volgende YAML-manifest. Deze op Open Enclave-gebaseerde voorbeeldtoepassingscode is te vinden in het [Open Enclave-project](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Bij de onderstaande implementatie wordt ervan uitgegaan dat u de invoegtoepassing 'confcom' hebt geïmplementeerd.

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
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gekoppelde knooppuntpools wilt verwijderen of het AKS-cluster wilt verwijderen, gebruikt u de onderstaande opdrachten:

Het AKS-cluster verwijderen
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Volgende stappen

Python, Node, enzovoort uitvoeren. Toepassingen gaan vertrouwelijk door vertrouwelijke containers door het bezoeken van [voorbeelden van vertrouwelijke containers](https://github.com/Azure-Samples/confidential-container-samples).

Voer Enclave-compatibele toepassingen uit door [Enclave Aware Azure-containervoorbeelden](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/) te bezoeken.
