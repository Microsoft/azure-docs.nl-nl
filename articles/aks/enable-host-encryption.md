---
title: Op hosts gebaseerde versleuteling inschakelen op de Azure Kubernetes-service (AKS)
description: Meer informatie over het configureren van een op een host gebaseerde versleuteling in een Azure Kubernetes service (AKS)-cluster
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: f4e599ae7aa81c15f86d0e8b1c934824010ea45b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430152"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Versleuteling op basis van een host op de Azure Kubernetes-service (AKS) (preview)

Met versleuteling op basis van een host worden de gegevens die zijn opgeslagen op de VM-host van de virtuele machines van uw AKS-agent, versleuteld op rest en stromen die zijn versleuteld met de opslag service. Dit betekent dat de tijdelijke schijven in rust worden versleuteld met sleutels die door het platform worden beheerd. De cache van het besturings systeem en de gegevens schijven is versleuteld met door het platform beheerde sleutels of door de klant beheerde sleutels, afhankelijk van het versleutelings type dat op die schijven is ingesteld. Wanneer u AKS gebruikt, worden besturings systeem-en gegevens schijven standaard versleuteld met door het platform beheerde sleutels, wat inhoudt dat de caches voor deze schijven ook standaard versleuteld zijn met door het platform beheerde sleutels.  U kunt uw eigen beheerde sleutels opgeven na [het maken van uw eigen sleutels (BYOK) met Azure-schijven in de Azure Kubernetes-service](azure-disk-customer-managed-keys.md). De cache voor deze schijven wordt vervolgens ook versleuteld met behulp van de sleutel die u in deze stap opgeeft.


## <a name="before-you-begin"></a>Voordat u begint

Deze functie kan alleen worden ingesteld tijdens het maken van het cluster of het maken van een knooppunt groep.

> [!NOTE]
> Versleuteling op basis van een host is beschikbaar in [Azure-regio's][supported-regions] die ondersteuning bieden voor versleuteling aan de server zijde van Azure Managed disks en alleen met specifieke [ondersteunde VM-grootten][supported-sizes].

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de `aks-preview` cli-uitbrei ding v 0.4.73 of een hogere versie hebt geïnstalleerd.
- Zorg ervoor dat u de `EnableEncryptionAtHostPreview` functie vlag onder `Microsoft.ContainerService` ingeschakeld hebt.

Als u versleuteling wilt gebruiken op de host voor uw Vm's of virtuele-machine schaal sets, moet u de functie inschakelen voor uw abonnement. Ontvang een e-mail **encryptionAtHost@microsoft.com** met uw abonnement-id's om de functie in te scha kelen voor uw abonnementen. 

> [!IMPORTANT]
> U moet **encryptionAtHost@microsoft.com** een e-mail met uw abonnement-id's ontvangen om de functie in te scha kelen voor reken resources. U kunt dit niet zelf inschakelen voor reken resources.


### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster wilt maken dat is gebaseerd op een host-gebaseerde versleuteling, hebt u de meest recente *AKS-preview cli-* extensie nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] of Controleer of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Beperkingen

- Kan alleen worden ingeschakeld voor nieuwe knooppunt groepen.
- Kan alleen worden ingeschakeld in [Azure-regio's][supported-regions] die ondersteuning bieden voor versleuteling aan de server zijde van Azure Managed disks en alleen met specifieke [ondersteunde VM-grootten][supported-sizes].
- Vereist een AKS-cluster en-knooppunt groep op basis van Virtual Machine Scale Sets (VMSS) als *type VM-set*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Op hosts gebaseerde versleuteling gebruiken voor nieuwe clusters (preview-versie)

Configureer de cluster agent-knoop punten voor het gebruik van versleuteling op basis van een host wanneer het cluster wordt gemaakt. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Als u clusters wilt maken zonder versleuteling op basis van een host, kunt u dit doen door de para meter weg te laten `--enable-encryption-at-host` .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Op hosts gebaseerde versleuteling op bestaande clusters gebruiken (preview-versie)

U kunt op een host gebaseerde versleuteling op bestaande clusters inschakelen door een nieuwe knooppunt groep toe te voegen aan uw cluster. Configureer een nieuwe knooppunt groep om versleuteling op basis van een host te gebruiken met behulp van de `--enable-encryption-at-host` para meter.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Als u nieuwe knooppunt groepen wilt maken zonder de op de host gebaseerde versleutelings functie, kunt u dit doen door de para meter weg te laten `--enable-encryption-at-host` .

## <a name="next-steps"></a>Volgende stappen

Lees de [Aanbevolen procedures voor AKS-cluster beveiliging][best-practices-security] meer informatie over [op hosts gebaseerde versleuteling](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
