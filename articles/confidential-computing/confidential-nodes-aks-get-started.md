---
title: 'Quickstart: Een Azure Kubernetes Service-cluster (AKS) implementeren met behulp van Azure CLI met vertrouwelijke rekenknooppunten'
description: Meer informatie over het maken van een AKS-cluster met vertrouwelijke knooppunten en het implementeren van een Hello World-app met behulp van de Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 95626836afb09ada286cf7e171f97db450167999
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564341"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-using-azure-cli-preview"></a>Quickstart: Een Azure Kubernetes Service-cluster (AKS) implementeren met behulp van Azure CLI met vertrouwelijke rekenknooppunten (preview)

Deze quickstart is bedoeld voor ontwikkelaars of clusteroperators die snel een AKS-cluster willen maken en een app implementeren om apps te bewaken die gebruikmaken van de beheerde Kubernetes-service in Azure.

## <a name="overview"></a>Overzicht

In deze quickstart leert u hoe u een Azure Kubernetes service-cluster (AKS) kunt implementeren met vertrouwelijke rekenknooppunten met behulp van Azure CLI en een Hello World-toepassing kunt uitvoeren in een enclave. Azure is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. [Hier](../aks/intro-kubernetes.md) vindt u meer informatie over AKS.

> [!NOTE]
> Vertrouwelijke DCsv2-VM's maken gebruik van gespecialiseerde hardware waarvoor hogere prijzen en regionale beschikbaarheid gelden. Zie de pagina Virtuele machines over [beschikbare SKU's en ondersteunde regio's](virtual-machine-solutions.md) voor meer informatie.

### <a name="deployment-pre-requisites"></a>Vereisten voor implementatie

1. U moet een actief Azure-abonnement hebben. Als u geen Azure-abonnement hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint
1. De Azure CLI-versie 2.0.64 of hoger moet zijn geïnstalleerd en geconfigureerd op uw implementatiemachine (voer `az --version` uit om de versie te achterhalen). Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren](../container-registry/container-registry-get-started-azure-cli.md).
1. [AKS-preview-extensie](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) minimale versie 0.4.62 
1. U hebt minimaal zes **DC<x>s-v2**-kernen voor gebruik beschikbaar in uw abonnement. Standaard is het quotum van de VM-kernen voor vertrouwelijke machines 8 kernen per Azure-abonnement. Als u van plan bent een cluster in te richten waarvoor meer dan 8 kerngeheugens zijn vereist, volgt u [deze](../azure-portal/supportability/per-vm-quota-requests.md) instructies om een quotumverhogingsticket te genereren

### <a name="confidential-computing-node-features-dcxs-v2"></a>Kenmerken van vertrouwelijke rekenknooppunten (DC<x>s-v2)

1. Linux-werkknooppunten bieden alleen ondersteuning voor Linux-containers
1. Virtuele machines van Ubuntu Generatie 2 18.04
1. Intel SGX-gebaseerde CPU met versleutelde paginageheugencache (EPC). [Hier](./faq.md) vindt u meer informatie
1. Kubernetes versie 1.16+
1. Vooraf geïnstalleerd Intel SGX DCAP-stuurprogramma. [Hier](./faq.md) vindt u meer informatie
1. CLI geïmplementeerd tijdens preview


## <a name="installing-the-cli-pre-requisites"></a>De CLI-vereisten installeren

Gebruik de volgende Azure CLI-opdrachten om de 0.4.62-uitbreiding (of hoger) van de AKS-preview te installeren:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Gebruik de volgende Azure CLI-opdrachten om de CLI-extensie AKS-preview te installeren:

```azurecli-interactive
az extension update --name aks-preview
```

Registreer de Gen2VMPreview:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Het kan enkele minuten duren voordat de status Geregistreerd wordt weergegeven. U kunt de registratiestatus controleren met behulp van de opdracht 'az feature list':

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Wanneer de status weergegeven wordt als geregistreerd, vernieuw dan de registratie van de resourceprovider Microsoft.ContainerService met behulp van de opdracht 'az provider register':

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Een AKS-cluster maken

Als u al een AKS-cluster hebt dat voldoet aan de bovenstaande vereisten, [gaat u naar de sectie Bestaand cluster](#existing-cluster) om een nieuwe pool met vertrouwelijke knooppunten toe te voegen.

Maak eerst een resourcegroep voor het cluster met de opdracht 'az group create'. In het volgende voorbeeld wordt een resourcegroep met de naam *mijnResourcegroep* gemaakt in de regio *West US 2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Gebruik nu de opdracht 'az aks create' om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster gemaakt met een enkel knooppunt van het formaat `Standard_DC2s_v2`. U kunt [hier](../virtual-machines/dcv2-series.md) een andere ondersteunde lijst met DCsv2-SKU's kiezen:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_DC2s_v2 \
    --node-count 3 \
    --enable-addon confcom \
    --network-plugin azure \
    --vm-set-type VirtualMachineScaleSets \
    --aks-custom-headers usegen2vm=true
```
Met de bovenstaande opdracht richt u een nieuw AKS-cluster in met **DC<x>s-v2**-knooppuntpools en installeert u automatisch twee daemon-sets - ([SGX Device Plugin](confidential-nodes-aks-overview.md#sgx-plugin) & [SGX Quote Helper](confidential-nodes-aks-overview.md#sgx-quote))

Haal de referenties voor uw AKS-cluster op met de opdracht 'az aks get-credentials':

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Controleer of de knooppunten correct zijn gemaakt en of de SGX-gerelateerde daemon-sets worden uitgevoerd op **DC<x>s-v2**-knooppuntpools met behulp van de opdracht 'kubectl get pods & nodes', zoals hieronder wordt weergegeven:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Als de uitvoer overeenkomt met het bovenstaande, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren.

Ga naar de implementatiesectie [Hallo wereld van Enclave](#hello-world) om een app te testen in een enclave. U kunt ook de onderstaande instructies volgen voor het toevoegen van extra knooppuntpools op AKS (AKS ondersteunt het mixen van SGX-knooppuntpools en niet-SGX-knooppuntpools)

>Als de SGX-gerelateerde daemon-sets niet zijn geïnstalleerd in uw DCSv2-knooppuntpools, voert u de onderstaande stappen uit.

```azurecli-interactive
az aks update --enable-addons confcom --resource-group myResourceGroup --name myAKSCluster
```

![DCSv2 AKS-cluster maken](./media/confidential-nodes-aks-overview/CLIAKSProvisioning.gif)

## <a name="adding-confidential-computing-node-to-existing-aks-cluster"></a>Het knooppunt voor vertrouwelijke knooppunten toevoegen aan een bestaand AKS-cluster<a id="existing-cluster"></a>

In deze sectie wordt ervan uitgegaan dat er al een AKS-cluster wordt uitgevoerd dat voldoet aan de criteria die worden vermeld in de sectie met vereisten.

Laten we eerst de vertrouwelijke AKS-invoegtoepassingen op het bestaande cluster inschakelen:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Voeg nu een **DC<x>s-v2**-knooppuntpool aan het cluster toe
    
> [!NOTE]
> Als u de vertrouwelijke rekenfunctie wilt gebruiken, moet uw bestaande AKS-cluster beschikken over ten minste één **DC<x>s-v2**  knooppuntpool op basis van een VM SKU. Meer informatie over DCsv2 VMs SKU's voor vertrouwelijk rekenen vindt u hier: [beschikbare SKU's en ondersteunde regio's](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
De uitvoer zou de zojuist toegevoegde 'confcompool1' in het AKS-cluster weer moeten geven.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Als de uitvoer overeenkomt met het bovenstaande, is uw AKS-cluster nu klaar om vertrouwelijke toepassingen uit te voeren.

## <a name="hello-world-from-isolated-enclave-application"></a>'Hallo wereld' van de geïsoleerde enclavetoepassing <a id="hello-world"></a>
Maak een bestand met de naam *hello-world-enclave.yaml* en plak het volgende YAML-manifest. Deze op Open Enclave-gebaseerde voorbeeldtoepassingscode is te vinden in het [Open Enclave-project](https://github.com/openenclave/openenclave/tree/master/samples/helloworld).

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