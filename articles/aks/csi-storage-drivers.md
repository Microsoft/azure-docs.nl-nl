---
title: Stuurprogramma'Container Storage Interface (CSI) inschakelen op Azure Kubernetes Service (AKS)
description: Meer informatie over het inschakelen van Container Storage Interface (CSI)-stuurprogramma's voor Azure-schijven en Azure Files in een Azure Kubernetes Service (AKS)-cluster.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c9edfdf1c9740ec1fdaaeeedbc6ba92793eb0b3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779953"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Stuurprogramma'Container Storage Interface (CSI) inschakelen voor Azure-schijven en Azure Files op Azure Kubernetes Service (AKS) (preview)

Het Container Storage Interface (CSI) is een standaard voor het blootstellen van willekeurige blok- en bestandsopslagsystemen aan in containers geplaatste workloads in Kubernetes. Door OVER te nemen en CSI te gebruiken, kan Azure Kubernetes Service (AKS) in plug-ins schrijven, implementeren en itereren om nieuwe of bestaande opslagsystemen in Kubernetes te verbeteren zonder dat de belangrijkste Kubernetes-code moet worden bereikt en moet worden gewacht op de releasecycli.

Met de stuurprogrammaondersteuning voor CSI-opslag in AKS kunt u het volgende systeemeigen gebruiken:
- [*Azure-schijven,*](azure-disk-csi.md)die kunnen worden gebruikt om een Kubernetes *DataDisk-resource te* maken. Schijven kunnen gebruikmaken van Azure Premium Storage, back-by high-performance SSD's of Azure Standard Storage, met ondersteuning van reguliere HDD's of Standard - SSD's. Gebruik voor de meeste productie- en ontwikkelingsworkloads Premium Storage. Azure-schijven worden als *ReadWriteOnce bevestigd* en zijn dus alleen beschikbaar voor één pod. Gebruik voor opslagvolumes die door meerdere pods tegelijk kunnen worden gebruikt, Azure Files.
- [*Azure Files*](azure-files-csi.md), die kan worden gebruikt om een SMB 3.0-share te maken die wordt Azure Storage pods. Met Azure Files kunt u gegevens delen tussen meerdere knooppunten en pods. Azure Files kunnen een Azure Standard Storage door reguliere HDD's of Azure-Premium Storage worden gebruikt door hoogwaardige SSD's.

> [!IMPORTANT]
> Vanaf Kubernetes versie 1.21 gebruikt Kubernetes alleen csi-stuurprogramma's en standaard. Deze stuurprogramma's zijn de toekomst van opslagondersteuning in Kubernetes.
>
> *In-tree stuurprogramma's* verwijst naar de huidige opslagst stuurprogramma's die deel uitmaken van de belangrijkste Kubernetes-code ten opzichte van de nieuwe CSI-stuurprogramma's, die in plug-ins zijn.

## <a name="limitations"></a>Beperkingen

- Deze functie kan alleen worden ingesteld tijdens het maken van het cluster.
- De minimale secundaire Kubernetes-versie die CSI-stuurprogramma's ondersteunt, is v1.17.
- Tijdens de preview is de standaardopslagklasse nog steeds dezelfde [opslagklasse in de structuur.](concepts-storage.md#storage-classes) Nadat deze functie algemeen beschikbaar is, is de standaardopslagklasse de opslagklasse en worden `managed-csi` opslagklassen in de structuur verwijderd.
- Tijdens de eerste preview-fase wordt alleen Azure CLI ondersteund.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>De `EnableAzureDiskFileCSIDriver` preview-functie registreren

Als u een AKS-cluster wilt maken dat CSI-stuurprogramma's voor Azure-schijven en -Azure Files kan gebruiken, moet u de `EnableAzureDiskFileCSIDriver` functievlag inschakelen voor uw abonnement.

Registreer de `EnableAzureDiskFileCSIDriver` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* Controleer de registratiestatus met behulp van [de opdracht az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van de [opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster of een knooppuntgroep wilt maken die gebruik kan maken van de CSI-opslagst stuurprogramma's, hebt u de nieuwste Azure *CLI-extensie aks-preview* nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van [de opdracht az extension add.][az-extension-add] U kunt ook beschikbare updates installeren met behulp van de [opdracht az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Een nieuw cluster maken dat gebruik kan maken van CSI-opslag stuurprogramma's

Maak een nieuw cluster dat CSI-opslag stuurprogramma's voor Azure-schijven kan gebruiken en Azure Files met behulp van de volgende CLI-opdrachten. Gebruik de `--aks-custom-headers` vlag om de functie in te `EnableAzureDiskFileCSIDriver` stellen.

Een Azure-resourcegroep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Maak het AKS-cluster met ondersteuning voor CSI-opslagst stuurprogramma's:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Als u clusters wilt maken in stuurprogramma's voor structuuropslag in plaats van CSI-opslagstuurders, kunt u dit doen door de aangepaste parameter weg te `--aks-custom-headers` laten.


Controleer hoeveel Azure-schijfvolumes u aan dit knooppunt kunt koppelen door het volgende uit te gaan:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Volgende stappen

- Zie Azure-schijven gebruiken met CSI-stuurprogramma's voor het gebruik van het [CSI-station voor Azure-schijven.](azure-disk-csi.md)
- Als u het CSI-station voor Azure Files wilt gebruiken, zie [Azure Files gebruiken met CSI-stuurprogramma's.](azure-files-csi.md)
- Zie Best practices voor opslag en back-ups in Azure Kubernetes Service voor meer [informatie over best practices voor Azure Kubernetes Service.][operator-best-practices-storage]

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
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register