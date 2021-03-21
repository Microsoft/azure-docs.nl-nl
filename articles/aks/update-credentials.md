---
title: De referenties voor een cluster opnieuw instellen
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het bijwerken of opnieuw instellen van de referenties van de service-principal of AAD-toepassing voor een Azure Kubernetes service-cluster (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 84cadc54a11c2e57d73fd1f06e98deb3d599d648
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501172"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>De referenties voor de Azure Kubernetes-service bijwerken of draaien (AKS)

AKS-clusters die zijn gemaakt met een Service-Principal, hebben een verval tijd van één jaar. Bij de voor de verloop datum kunt u de referenties opnieuw instellen om de service-principal voor een extra periode uit te breiden. Het is ook mogelijk dat u de referenties wilt bijwerken of draaien, als onderdeel van een gedefinieerd beveiligings beleid. In dit artikel wordt beschreven hoe u deze referenties voor een AKS-cluster bijwerkt.

Mogelijk hebt u ook [uw AKS-cluster geïntegreerd met Azure Active Directory][aad-integration]en gebruikt u dit als een verificatie provider voor uw cluster. In dat geval hebt u nog twee identiteiten gemaakt voor uw cluster, de AAD-server-app en de AAD-client-app. u kunt deze referenties ook opnieuw instellen.

U kunt ook een beheerde identiteit voor machtigingen gebruiken in plaats van een service-principal. Beheerde identiteiten zijn eenvoudiger te beheren dan service-principals en vereisen geen updates of draaiingen. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.65 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Een nieuwe service-principal voor uw AKS-cluster bijwerken of maken

Wanneer u de referenties voor een AKS-cluster wilt bijwerken, kunt u kiezen uit:

* Werk de referenties voor de bestaande service-principal bij.
* Maak een nieuwe Service-Principal en werk het cluster bij om deze nieuwe referenties te gebruiken. 

> [!WARNING]
> Als u ervoor kiest om een *nieuwe* Service-Principal te maken, kan het enige tijd duren om een groot AKS-cluster bij te werken voor het gebruik van deze referenties.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Controleer de verval datum van de Service-Principal

Als u de verval datum van de Service-Principal wilt controleren, gebruikt u de opdracht [AZ AD SP Credential List][az-ad-sp-credential-list] . In het volgende voor beeld wordt de Service-Principal-ID voor het cluster met de naam *myAKSCluster* in de resource groep *myResourceGroup* opgehaald met de opdracht [AZ AKS show][az-aks-show] . De Service-Principal-ID wordt ingesteld als een variabele met de naam *SP_ID* voor gebruik met de opdracht [AZ AD SP Credential List][az-ad-sp-credential-list] .

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>De bestaande Service-Principal-referenties opnieuw instellen

Als u de referenties voor de bestaande Service-Principal wilt bijwerken, haalt u de Service-Principal-ID van uw cluster op met de opdracht [AZ AKS show][az-aks-show] . In het volgende voor beeld wordt de ID van het cluster met de naam *myAKSCluster* in de resource groep *myResourceGroup* opgehaald. De Service-Principal-ID wordt ingesteld als een variabele met de naam *SP_ID* voor gebruik in een extra opdracht. Deze opdrachten gebruiken de bash-syntaxis.

> [!WARNING]
> Wanneer u uw cluster referenties opnieuw instelt op een AKS-cluster dat gebruikmaakt van Azure Virtual Machine Scale Sets, wordt een upgrade van een [knooppunt installatie kopie][node-image-upgrade] uitgevoerd om uw knoop punten bij te werken met de nieuwe referentie gegevens.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Stel, met een variabelenset die de Service-Principal-ID bevat, de referenties nu opnieuw in met [AZ AD SP-referentie opnieuw instellen][az-ad-sp-credential-reset]. In het volgende voor beeld kan het Azure-platform een nieuw beveiligd geheim genereren voor de Service-Principal. Dit nieuwe beveiligde geheim wordt ook opgeslagen als een variabele.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Ga nu verder met het [bijwerken van AKS-cluster met nieuwe Service-Principal-referenties](#update-aks-cluster-with-new-service-principal-credentials). Deze stap is nodig om de service-principal te wijzigen in overeenstemming met het AKS-cluster.

### <a name="create-a-new-service-principal"></a>Een nieuwe service-principal maken

Als u ervoor hebt gekozen om de bestaande Service-Principal-referenties in de vorige sectie bij te werken, slaat u deze stap over. Ga door met het [bijwerken van AKS-cluster met nieuwe Service-Principal-referenties](#update-aks-cluster-with-new-service-principal-credentials).

Als u een Service-Principal wilt maken en vervolgens het AKS-cluster voor het gebruik van deze nieuwe referenties wilt bijwerken, gebruikt u de opdracht [AZ AD SP create-for-RBAC][az-ad-sp-create] . In het volgende voorbeeld wordt met de parameter `--skip-assignment` voorkomen dat eventuele extra standaardtoewijzingen worden toegewezen:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer uw eigen `appId` en `password`. Deze waarden worden in de volgende stap gebruikt.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Definieer nu variabelen voor de Service-Principal-ID en het client geheim met behulp van de uitvoer van uw eigen [AZ AD SP create-for-RBAC-][az-ad-sp-create] opdracht, zoals wordt weer gegeven in het volgende voor beeld. De *SP_ID* is uw *appId* en de *SP_SECRET* is uw *wacht woord*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Ga nu verder met het [bijwerken van AKS-cluster met nieuwe Service-Principal-referenties](#update-aks-cluster-with-new-service-principal-credentials). Deze stap is nodig om de service-principal te wijzigen in overeenstemming met het AKS-cluster.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>AKS-cluster bijwerken met nieuwe Service-Principal-referenties

> [!IMPORTANT]
> Voor grote clusters kan het enige tijd duren voordat het AKS-cluster wordt bijgewerkt met een nieuwe service-principal.

Ongeacht of u ervoor hebt gekozen om de referenties voor de bestaande service-principal bij te werken of een service-principal te maken, werkt u nu het AKS-cluster bij met uw nieuwe referenties met behulp van de opdracht [AZ AKS update-credentials][az-aks-update-credentials] . De variabelen voor de *Service-Principal* en *--client-Secret* worden gebruikt:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Voor kleine en middel grote clusters duurt het enkele minuten voordat de referenties van de service-principal worden bijgewerkt in de AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>AKS-cluster bijwerken met nieuwe AAD-toepassings referenties

U kunt nieuwe AAD-server-en client toepassingen maken door de [Aad-integratie stappen][create-aad-app]te volgen. Of stel uw bestaande AAD-toepassingen opnieuw [in volgens dezelfde methode als voor Service Principal reset](#reset-the-existing-service-principal-credential). Nadat u de referenties voor de AAD-toepassing voor het cluster hoeft bij te werken met dezelfde opdracht [AZ AKS update-credentials][az-aks-update-credentials] , maar met behulp van de *---Reset-Aad-* variabelen.

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

In dit artikel is de service-principal voor het AKS-cluster zelf en de AAD-integratie toepassingen bijgewerkt. Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][best-practices-identity]voor meer informatie over het beheren van identiteit voor werk belastingen binnen een cluster.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[node-image-upgrade]: ./node-image-upgrade.md
