---
title: Containerinstallatiekopieën importeren
description: Container installatie kopieën importeren in een Azure container Registry met behulp van Azure Api's, zonder dat u docker-opdrachten hoeft uit te voeren.
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 3950b9fb24b80db4d9654a615521c0eb82914499
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019970"
---
# <a name="import-container-images-to-a-container-registry"></a>Container installatie kopieën importeren in een container register

U kunt container installatie kopieën eenvoudig importeren (kopiëren) naar een Azure container Registry zonder gebruik te maken van docker-opdrachten. Importeer bijvoorbeeld installatie kopieën van een ontwikkelings register naar een productie register of kopieer basis installatie kopieën uit een openbaar REGI ster.

Azure Container Registry verwerkt een aantal algemene scenario's voor het kopiëren van installatie kopieën uit een bestaand REGI ster:

* Importeren uit een openbaar REGI ster

* Importeren uit een ander Azure container Registry, in hetzelfde of een ander Azure-abonnement of een andere Tenant

* Importeren uit een persoonlijk niet-Azure-container register

Het importeren van afbeeldingen in een Azure container Registry heeft de volgende voor delen ten opzichte van het gebruik van docker CLI-opdrachten:

* Omdat uw client omgeving geen lokale docker-installatie nodig heeft, importeert u een container installatie kopie, ongeacht het ondersteunde type besturings systeem.

* Bij het importeren van installatie kopieën met meerdere architecturen (zoals officiële docker-installatie kopieën), worden installatie kopieën voor alle architecturen en platformen die zijn opgegeven in de manifest lijst gekopieerd.

* Toegang tot het doel register heeft geen gebruik van het open bare eind punt van het REGI ster.

Als u container installatie kopieën wilt importeren, moet u de Azure CLI in Azure Cloud Shell of lokaal uitvoeren (versie 2.0.55 of hoger aanbevolen). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!NOTE]
> Als u identieke container installatie kopieën wilt distribueren over meerdere Azure-regio's, ondersteunt Azure Container Registry ook [geo-replicatie](container-registry-geo-replication.md). Door Geo-replicatie van een REGI ster (Premium-servicelaag vereist), kunt u meerdere regio's met identieke installatie kopieën en label namen uit één REGI ster gebruiken.
>

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure container Registry hebt, maakt u een REGI ster. Zie [Quick Start: een persoonlijk container register maken met behulp van de Azure cli](container-registry-get-started-azure-cli.md)voor stappen.

Voor het importeren van een installatie kopie naar een Azure container Registry moet uw identiteit schrijf machtigingen hebben voor het doel register (ten minste de rol van Inzender) of een aangepaste rol die de actie importImage toestaat). Zie [Azure container Registry-rollen en-machtigingen](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importeren uit een openbaar REGI ster

### <a name="import-from-docker-hub"></a>Importeren uit docker hub

Gebruik bijvoorbeeld de opdracht [AZ ACR import][az-acr-import] om de installatie kopie met meerdere architecturen te importeren `hello-world:latest` vanuit docker hub naar een REGI ster met de naam *myregistry*. Omdat `hello-world` een officiële installatie kopie van docker hub is, bevindt deze installatie kopie zich in de standaard `library` opslagplaats. Neem de naam van de opslag plaats en eventueel een tag op in de waarde van de `--source` para meter image. (U kunt een installatie kopie eventueel identificeren met de samen vatting van het manifest in plaats van met een tag, waarmee een bepaalde versie van een installatie kopie wordt gegarandeerd.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

U kunt controleren of er meerdere manifesten zijn gekoppeld aan deze installatie kopie door de opdracht uit te voeren `az acr repository show-manifests` :

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

In het volgende voor beeld wordt een open bare installatie kopie uit de `tensorflow` opslag plaats in docker hub geïmporteerd:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importeren uit micro soft Container Registry

Importeer bijvoorbeeld de `ltsc2019` Windows Server Core-installatie kopie uit de `windows` opslag plaats in micro soft container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importeren uit een Azure container registry in dezelfde AD-Tenant

U kunt een installatie kopie importeren uit een Azure container registry in dezelfde AD-Tenant met behulp van geïntegreerde Azure Active Directory-machtigingen.

* Uw identiteit moet Azure Active Directory machtigingen hebben voor het lezen van het bron register (rol van lezer) en voor het importeren naar het doel register (rol Inzender of een [aangepaste rol](container-registry-roles.md#custom-roles) die de actie importImage toestaat).

* Het REGI ster kan zich in hetzelfde of een ander Azure-abonnement bevindt als de Tenant van hetzelfde Active Directory.

* [Open bare toegang](container-registry-access-selected-networks.md#disable-public-network-access) tot het bron register is mogelijk uitgeschakeld. Als open bare toegang is uitgeschakeld, geeft u het bron register op Resource-ID op in plaats van de naam van de aanmeldings server van het REGI ster.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importeren uit een REGI ster in hetzelfde abonnement

Importeer bijvoorbeeld de `aci-helloworld:latest` installatie kopie van een bron register *mysourceregistry* naar *myregistry* in hetzelfde Azure-abonnement.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

In het volgende voor beeld `aci-helloworld:latest` wordt de installatie kopie geïmporteerd naar *myregistry* vanuit een bron register *mysourceregistry* waarin toegang tot het open bare eind punt van het REGI ster is uitgeschakeld. Geef de bron-ID van het bron register op met de `--registry` para meter. U ziet dat de `--source` para meter alleen de bron opslagplaats en-tag opgeeft, niet de naam van de aanmeldings server van het REGI ster.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

In het volgende voor beeld wordt een installatie kopie geïmporteerd door de manifest Digest (SHA-256-Hash, weer gegeven als `sha256:...` ) in plaats van op label:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importeren uit een REGI ster in een ander abonnement

In het volgende voor beeld bevindt *mysourceregistry* zich in een ander abonnement dan *myregistry* in dezelfde Active Directory Tenant. Geef de bron-ID van het bron register op met de `--registry` para meter. U ziet dat de `--source` para meter alleen de bron opslagplaats en-tag opgeeft, niet de naam van de aanmeldings server van het REGI ster.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importeren uit een REGI ster met de referenties van de Service-Principal

Als u wilt importeren uit een REGI ster waartoe u geen toegang hebt met behulp van geïntegreerde Active Directory-machtigingen, kunt u de referenties van de Service-Principal (indien beschikbaar) gebruiken in het bron register. Geef de appID en het wacht woord op van een Active Directory [Service-Principal](container-registry-auth-service-principal.md) die toegang heeft tot het bron register ACRPull. Het gebruik van een Service-Principal is handig voor het bouwen van systemen en andere systemen zonder toezicht die installatie kopieën naar uw REGI ster moeten importeren.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importeren uit een Azure container registry in een andere AD-Tenant

Als u wilt importeren uit een Azure container registry in een andere Azure Active Directory Tenant, geeft u het bron register op bij de naam van de aanmeldings server en geeft u de gebruikers naam en het wacht woord op waarmee pull-toegang tot het REGI ster mogelijk wordt. U kunt bijvoorbeeld een token en wacht woord van een [opslag plaats bereiken](container-registry-repository-scoped-permissions.md) , of de appID en het wacht woord van een Active Directory [Service-Principal](container-registry-auth-service-principal.md) met ACRPull toegang tot het bron register. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importeren uit een persoonlijk niet-Azure-container register

Importeer een installatie kopie uit een persoonlijk niet-Azure-REGI ster door referenties op te geven waarmee pull-toegang tot het REGI ster mogelijk wordt. U kunt bijvoorbeeld een installatie kopie uit een privé-docker-REGI ster halen: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het importeren van container installatie kopieën naar een Azure container Registry vanuit een openbaar REGI ster of een ander privé register. Zie voor aanvullende opties voor het importeren van installatie kopieën de verwijzing [AZ ACR import][az-acr-import] Command. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
