---
title: De referenties voor een cluster opnieuw instellen
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het bijwerken of opnieuw instellen van de service-principal- of AAD-toepassingsreferenties voor een Azure Kubernetes Service (AKS)-cluster
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 0b750eb9af7dfd7bcbada7500b6ef71b015db11f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767471"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>De referenties bijwerken of roteren voor Azure Kubernetes Service (AKS)

AKS-clusters die zijn gemaakt met een service-principal hebben een verlooptijd van één jaar. Wanneer de vervaldatum bijna is verstreken, kunt u de referenties opnieuw instellen om de service-principal voor een extra periode uit te breiden. Mogelijk wilt u de referenties ook bijwerken of roteren als onderdeel van een gedefinieerd beveiligingsbeleid. In dit artikel wordt beschreven hoe u deze referenties voor een AKS-cluster bij kunt werken.

Mogelijk hebt u uw [AKS-cluster][aad-integration]ook geïntegreerd met Azure Active Directory en gebruikt u dit als verificatieprovider voor uw cluster. In dat geval hebt u nog twee identiteiten die zijn gemaakt voor uw cluster, de AAD Server-app en de AAD-client-app, kunt u deze referenties ook opnieuw instellen.

U kunt ook een beheerde identiteit gebruiken voor machtigingen in plaats van een service-principal. Beheerde identiteiten zijn eenvoudiger te beheren dan service-principals en vereisen geen updates of roulaties. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Azure CLI versie 2.0.65 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Een nieuwe service-principal voor uw AKS-cluster bijwerken of maken

Wanneer u de referenties voor een AKS-cluster wilt bijwerken, kunt u kiezen voor het volgende:

* Werk de referenties voor de bestaande service-principal bij.
* Maak een nieuwe service-principal en werk het cluster bij om deze nieuwe referenties te gebruiken. 

> [!WARNING]
> Als u ervoor kiest om een nieuwe *service-principal* te maken, kan het lang duren om een groot AKS-cluster bij te werken om deze referenties te gebruiken.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Controleer de vervaldatum van uw service-principal

Gebruik de opdracht az ad sp [credential list][az-ad-sp-credential-list] om de vervaldatum van uw service-principal te controleren. In het volgende voorbeeld wordt de service-principal-id voor het cluster met de naam *myAKSCluster* in de resourcegroep *myResourceGroup* met behulp van de [opdracht az aks show.][az-aks-show] De service-principal-id is ingesteld als een variabele met de *SP_ID* voor gebruik met de [opdracht az ad sp credential list.][az-ad-sp-credential-list]

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>De bestaande service-principalreferenties opnieuw instellen

Als u de referenties voor de bestaande service-principal wilt bijwerken, moet u de service-principal-id van uw cluster op halen met de [opdracht az aks show.][az-aks-show] In het volgende voorbeeld wordt de id voor het cluster met de *naam myAKSCluster* in de *resourcegroep myResourceGroup* opgeruimd. De service-principal-id is ingesteld als een variabele met de *naam SP_ID* voor gebruik in de aanvullende opdracht. Voor deze opdrachten wordt de Bash-syntaxis gebruikt.

> [!WARNING]
> Wanneer u uw clusterreferenties opnieuw in stelt op een AKS-cluster dat gebruikmaakt van Azure Virtual Machine Scale Sets, wordt een upgrade van de knooppuntafbeelding uitgevoerd om uw knooppunten bij te werken met de nieuwe referentiegegevens. [][node-image-upgrade]

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Stel nu met een variabelenset die de service-principal-id bevat de referenties opnieuw in [met az ad sp credential reset.][az-ad-sp-credential-reset] In het volgende voorbeeld kan het Azure-platform een nieuw beveiligd geheim genereren voor de service-principal. Dit nieuwe beveiligde geheim wordt ook opgeslagen als een variabele.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Ga nu verder met het [bijwerken van het AKS-cluster met nieuwe referenties voor de service-principal.](#update-aks-cluster-with-new-service-principal-credentials) Deze stap is nodig om de wijzigingen in de service-principal door te voeren op het AKS-cluster.

### <a name="create-a-new-service-principal"></a>Een nieuwe service-principal maken

Als u ervoor hebt gekozen om de bestaande referenties voor de service-principal in de vorige sectie bij te werken, slaat u deze stap over. Blijf het [AKS-cluster bijwerken met nieuwe referenties voor de service-principal.](#update-aks-cluster-with-new-service-principal-credentials)

Als u een service-principal wilt maken en vervolgens het AKS-cluster wilt bijwerken om deze nieuwe referenties te gebruiken, gebruikt u de opdracht [az ad sp create-for-rbac.][az-ad-sp-create] In het volgende voorbeeld wordt met de parameter `--skip-assignment` voorkomen dat eventuele extra standaardtoewijzingen worden toegewezen:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer uw eigen `appId` en `password`. Deze waarden worden gebruikt in de volgende stap.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Definieer nu variabelen voor de service-principal-id en het clientgeheim met behulp van de uitvoer van uw eigen [opdracht az ad sp create-for-rbac,][az-ad-sp-create] zoals wordt weergegeven in het volgende voorbeeld. De *SP_ID* is uw *appId* en de *SP_SECRET* is uw *wachtwoord*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Ga nu verder met het [bijwerken van het AKS-cluster met nieuwe referenties voor de service-principal.](#update-aks-cluster-with-new-service-principal-credentials) Deze stap is nodig om de wijzigingen in de service-principal weer te geven in het AKS-cluster.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>AKS-cluster bijwerken met nieuwe referenties voor service-principal

> [!IMPORTANT]
> Voor grote clusters kan het bijwerken van het AKS-cluster met een nieuwe service-principal lang duren.

Ongeacht of u ervoor hebt gekozen om de referenties voor de bestaande service-principal bij te werken of een service-principal te maken, kunt u nu het AKS-cluster bijwerken met uw nieuwe referenties met behulp van de opdracht [az aks update-credentials.][az-aks-update-credentials] De variabelen voor de *--service-principal en* *--client-secret* worden gebruikt:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Voor kleine en middelgrote clusters duurt het even voordat de referenties van de service-principal in de AKS zijn bijgewerkt.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>AKS-cluster bijwerken met nieuwe AAD-toepassingsreferenties

U kunt nieuwe AAD-server- en clienttoepassingen maken door de stappen voor [AAD-integratie uit te voeren.][create-aad-app] Of stel uw bestaande AAD-toepassingen opnieuw in volgens [dezelfde methode als voor service-principal reset.](#reset-the-existing-service-principal-credential) Daarna hoeft u alleen de AAD-toepassingsreferenties van uw cluster bij te werken met dezelfde [opdracht az aks update-credentials,][az-aks-update-credentials] maar met behulp van de *variabelen --reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Volgende stappen

In dit artikel zijn de service-principal voor het AKS-cluster zelf en de AAD-integratietoepassingen bijgewerkt. Zie Best practices for [authentication and authorization in AKS (Best practices][best-practices-identity]voor verificatie en autorisatie in AKS) voor meer informatie over het beheren van identiteiten voor workloads in een cluster.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-update-credentials]: /cli/azure/aks#az_aks_update_credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az_ad_sp_credential_list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[node-image-upgrade]: ./node-image-upgrade.md
