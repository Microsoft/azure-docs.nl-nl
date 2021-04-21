---
title: Hostgebaseerde versleuteling inschakelen op Azure Kubernetes Service (AKS)
description: Meer informatie over het configureren van een versleuteling op basis van een host in Azure Kubernetes Service AKS-cluster (AKS)
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772915"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Hostgebaseerde versleuteling op Azure Kubernetes Service (AKS) (preview)

Met versleuteling op basis van een host worden de gegevens die zijn opgeslagen op de VM-host van de VM-knooppunten van uw AKS-agentknooppunten in rust versleuteld en worden ze versleuteld naar de Storage-service verzonden. Dit betekent dat de tijdelijke schijven at-rest worden versleuteld met door het platform beheerde sleutels. De cache van besturingssysteem- en gegevensschijven wordt in rust versleuteld met door het platform beheerde sleutels of door de klant beheerde sleutels, afhankelijk van het versleutelingstype dat op deze schijven is ingesteld. Wanneer u AKS gebruikt, worden besturingssysteem- en gegevensschijven standaard in rust versleuteld met door het platform beheerde sleutels. Dit betekent dat de caches voor deze schijven standaard ook at-rest worden versleuteld met door het platform beheerde sleutels.  U kunt uw eigen beheerde sleutels opgeven aan de hand [van BYOK (Bring Your Own Keys) met Azure-schijven in Azure Kubernetes Service](azure-disk-customer-managed-keys.md). De cache voor deze schijven wordt vervolgens ook versleuteld met behulp van de sleutel die u in deze stap opgeeft.


## <a name="before-you-begin"></a>Voordat u begint

Deze functie kan alleen worden ingesteld tijdens het maken van het cluster of het maken van een knooppuntgroep.

> [!NOTE]
> Hostversleuteling is [][supported-regions] beschikbaar in Azure-regio's die ondersteuning bieden voor versleuteling aan de serverzijde van beheerde Azure-schijven en alleen met specifieke [ondersteunde VM-grootten.][supported-sizes]

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de `aks-preview` CLI-extensie v0.4.73 of hoger is geïnstalleerd.
- Zorg ervoor dat de `EnableEncryptionAtHostPreview` functievlag `Microsoft.ContainerService` is ingeschakeld.

U moet de functie voor uw abonnement inschakelen voordat u de eigenschap EncryptionAtHost gebruikt voor uw Azure Kubernetes Service cluster. Volg de onderstaande stappen om de functie voor uw abonnement in te stellen:

1. Voer de volgende opdracht uit om de functie voor uw abonnement te registreren

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. Controleer of de registratie status Geregistreerd is (duurt enkele minuten) met behulp van de onderstaande opdracht voordat u de functie uitproprompt.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster wilt maken dat op host gebaseerde versleuteling gebruikt, hebt u de nieuwste *CLI-extensie aks-preview* nodig. Installeer de *Azure CLI-extensie aks-preview* met de opdracht [az extension add][az-extension-add] of controleer op beschikbare updates met de opdracht az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Beperkingen

- Kan alleen worden ingeschakeld voor nieuwe knooppuntgroepen.
- Kan alleen worden [][supported-regions] ingeschakeld in Azure-regio's die ondersteuning bieden voor versleuteling aan de serverzijde van beheerde Azure-schijven en alleen met specifieke [ondersteunde VM-grootten.][supported-sizes]
- Vereist een AKS-cluster en knooppuntgroep op basis van Virtual Machine Scale Sets (VMSS) als *VM-settype.*

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Op host gebaseerde versleuteling gebruiken op nieuwe clusters (preview)

Configureer de clusteragentknooppunten voor het gebruik van hostversleuteling wanneer het cluster wordt gemaakt. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Als u clusters wilt maken zonder versleuteling op basis van een host, kunt u dit doen door de parameter weg te `--enable-encryption-at-host` laten.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Op host gebaseerde versleuteling gebruiken voor bestaande clusters (preview)

U kunt hostgebaseerde versleuteling inschakelen voor bestaande clusters door een nieuwe knooppuntgroep toe te voegen aan uw cluster. Configureer een nieuwe knooppuntgroep voor het gebruik van versleuteling op basis van een host met behulp van de `--enable-encryption-at-host` parameter .

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Als u nieuwe knooppuntgroepen wilt maken zonder de functie voor hostversleuteling, kunt u dit doen door de parameter weg te `--enable-encryption-at-host` laten.

## <a name="next-steps"></a>Volgende stappen

Bekijk [de best practices voor AKS-clusterbeveiliging][best-practices-security] Meer informatie over [versleuteling op basis van een host.](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
