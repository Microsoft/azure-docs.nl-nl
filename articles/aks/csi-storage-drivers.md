---
title: De Stuur Programma's van de container Storage-interface (CSI) inschakelen op de Azure Kubernetes-service (AKS)
description: Meer informatie over het inschakelen van de container Storage interface (CSI)-Stuur Programma's voor Azure-schijven en Azure Files in een AKS-cluster (Azure Kubernetes service).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: fa40ab22f0c1ebf47bb490a50f782a848d1441e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182108"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>De Stuur Programma's van de container Storage-interface (CSI) inschakelen voor Azure-schijven en Azure Files op Azure Kubernetes service (AKS) (preview)

De container Storage-interface (CSI) is een standaard voor het beschikbaar maken van wille keurige blok-en opslag systemen in containers op Kubernetes. Door het gebruik van CSI, Azure Kubernetes service (AKS) kan invoeg toepassingen schrijven, implementeren en laten lopen om nieuwe of bestaande opslag systemen in Kubernetes beschikbaar te maken, zonder dat u de kern Kubernetes hoeft aan te raken en de release cycli moet wachten.

Met de ondersteuning van de CSI-opslag Stuur Programma's op AKS kunt u native gebruiken:
- [*Azure-schijven*](azure-disk-csi.md), die kunnen worden gebruikt voor het maken van een Kubernetes *DataDisk* -resource. Schijven kunnen gebruikmaken van Azure Premium Storage, ondersteund door Ssd's met hoge prestaties of Azure Standard-opslag, ondersteund door gewone Hdd's of standaard Ssd's. Gebruik Premium Storage voor de meeste werk belastingen voor productie en ontwikkeling. Azure-schijven zijn gekoppeld als *ReadWriteOnce*, dus zijn alleen beschikbaar voor één pod. Gebruik Azure Files voor opslag volumes die gelijktijdig toegankelijk zijn voor meerdere peulen.
- [*Azure files*](azure-files-csi.md), dat kan worden gebruikt voor het koppelen van een SMB 3,0-share die wordt ondersteund door een Azure Storage-account tot een Peul. Met Azure Files kunt u gegevens delen op meerdere knoop punten en een Peul. Azure Files kunt Azure Standard-opslag gebruiken die wordt ondersteund door gewone Hdd's of Azure Premium Storage die worden ondersteund door Ssd's met hoge prestaties.

> [!IMPORTANT]
> Met ingang van Kubernetes versie 1,21 worden alleen de CSI-Stuur Programma's en standaard de Kubernetes gebruikt. Deze Stuur Programma's zijn de toekomst van opslag ondersteuning in Kubernetes.
>
> *In-structuur Stuur Programma's* verwijzen naar de huidige opslag Stuur Programma's die deel uitmaken van de kern Kubernetes-code versus de nieuwe CSI-Stuur Programma's, die invoeg toepassingen zijn.

## <a name="limitations"></a>Beperkingen

- Deze functie kan alleen worden ingesteld tijdens het maken van het cluster.
- De minimale Kubernetes-secundaire versie die ondersteuning biedt voor CSI-Stuur Programma's is v 1.17.
- Tijdens de preview-periode is de standaard opslag klasse nog steeds [dezelfde in-structuur opslag klasse](concepts-storage.md#storage-classes). Nadat deze functie algemeen beschikbaar is, wordt de standaard-opslag klasse de opslag `managed-csi` klasse en de opslag klassen in de boom structuur verwijderd.
- Tijdens de eerste preview-fase wordt alleen Azure CLI ondersteund.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>De `EnableAzureDiskFileCSIDriver` Preview-functie registreren

Als u een AKS-cluster wilt maken dat CSI-Stuur Programma's voor Azure-schijven en Azure Files kan gebruiken, moet u de `EnableAzureDiskFileCSIDriver` functie vlag voor uw abonnement inschakelen.

Registreer de `EnableAzureDiskFileCSIDriver` functie vlag met behulp van de opdracht [AZ feature REGI ster][az-feature-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Het duurt enkele minuten voordat de status is *geregistreerd*. Controleer de registratie status met behulp van de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster of een knooppunt groep wilt maken die de CSI-opslag Stuur Programma's kan gebruiken, hebt u de meest recente *AKS-preview* Azure cli-extensie nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] . Of installeer alle beschik bare updates met behulp van de opdracht [AZ extension update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Een nieuw cluster maken dat CSI-opslag Stuur Programma's kan gebruiken

Maak een nieuw cluster dat CSI-opslag Stuur Programma's voor Azure-schijven en Azure Files kan gebruiken met behulp van de volgende CLI-opdrachten. Gebruik de `--aks-custom-headers` markering om de functie in te stellen `EnableAzureDiskFileCSIDriver` .

Een Azure-resource groep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Maak het AKS-cluster met ondersteuning voor CSI-opslag Stuur Programma's:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Als u clusters wilt maken in structuur opslag Stuur Programma's in plaats van CSI-opslag Stuur Programma's, kunt u dit doen door de aangepaste para meter weg te laten `--aks-custom-headers` .


Controleren hoeveel Azure disk-based volumes u aan dit knoop punt kunt koppelen door uit te voeren:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Volgende stappen

- Als u het CSI-station voor Azure-schijven wilt gebruiken, raadpleegt u [Azure schijven gebruiken met CSI-Stuur Programma's](azure-disk-csi.md).
- Als u het CSI-station voor Azure Files wilt gebruiken, raadpleegt [u Azure files gebruiken met CSI-Stuur Programma's](azure-files-csi.md).
- Zie [Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service][operator-best-practices-storage]voor meer informatie over aanbevolen procedures voor opslag.

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register