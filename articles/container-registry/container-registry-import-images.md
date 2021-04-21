---
title: Containerinstallatiekopieën importeren
description: Importeer containerafbeeldingen naar een Azure-containerregister met behulp van Azure-API's, zonder dat u Docker-opdrachten hoeft uit te voeren.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e7becadab7f23acd7b85d6d82fd8abbfa7608add
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781519"
---
# <a name="import-container-images-to-a-container-registry"></a>Containerafbeeldingen importeren in een containerregister

U kunt eenvoudig containerafbeeldingen importeren (kopiëren) naar een Azure-containerregister, zonder Docker-opdrachten te gebruiken. Importeer bijvoorbeeld afbeeldingen uit een ontwikkelregister naar een productieregister of kopieer basisafbeeldingen uit een openbaar register.

Azure Container Registry een aantal veelvoorkomende scenario's voor het kopiëren van afbeeldingen en andere artefacten uit een bestaand register:

* Afbeeldingen importeren uit een openbaar register

* Importeer afbeeldingen of OCI-artefacten, waaronder Helm 3-grafieken uit een ander Azure-containerregister, in hetzelfde of een ander Azure-abonnement of -tenant

* Importeren vanuit een niet-Azure-privécontainerregister

Het importeren van een afbeelding in een Azure-containerregister heeft de volgende voordelen ten opzichte van het gebruik van Docker CLI-opdrachten:

* Omdat uw clientomgeving geen lokale Docker-installatie nodig heeft, importeert u een containerinstallatie installatie, ongeacht het ondersteunde type besturingssysteem.

* Wanneer u meerdere architectuur-afbeeldingen (zoals officiële Docker-afbeeldingen) importeert, worden de afbeeldingen voor alle architecturen en platformen die zijn opgegeven in de manifestlijst gekopieerd.

* Toegang tot het doelregister hoeft niet het openbare eindpunt van het register te gebruiken.

Voor het importeren van containerafbeeldingen moet u voor dit artikel de Azure CLI uitvoeren in Azure Cloud Shell of lokaal (versie 2.0.55 of hoger wordt aanbevolen). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!NOTE]
> Als u identieke containerafbeeldingen over meerdere Azure-regio's wilt distribueren, Azure Container Registry ook [geo-replicatie.](container-registry-geo-replication.md) Door geo-replicatie van een register (Premium-servicelaag vereist) kunt u meerdere regio's met identieke namen van afbeeldingen en tags uit één register leveren.
>

> [!IMPORTANT]
> Wijzigingen in het importeren van afbeeldingen tussen twee Azure-containerregisters zijn geïntroduceerd vanaf januari 2021:
> * Voor importeren naar of uit een Azure-containerregister met beperkte netwerktoegang is het beperkte register vereist om toegang door vertrouwde [**services**](allow-access-trusted-services.md) toe te staan om het netwerk te omzeilen. De instelling is standaard ingeschakeld, waardoor importeren is ingeschakeld. Als de instelling niet is ingeschakeld in een nieuw gemaakt register met een privé-eindpunt of met firewallregels voor het register, mislukt het importeren. 
> * In een bestaand Azure-containerregister met beperkte netwerkbeperking dat wordt gebruikt als een importbron of -doel, is het inschakelen van deze netwerkbeveiligingsfunctie optioneel, maar wordt aanbevolen.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-containerregister hebt, maakt u een register. Zie [Quickstart: Een privécontainerregister maken](container-registry-get-started-azure-cli.md)met behulp van de Azure CLI voor stappen.

Als u een afbeelding wilt importeren in een Azure-containerregister, moet uw identiteit schrijfmachtigingen hebben voor het doelregister (ten minste de rol Inzender of een aangepaste rol die de importImage-actie toestaat). Zie [Azure Container Registry en machtigingen .](container-registry-roles.md#custom-roles) 

## <a name="import-from-a-public-registry"></a>Importeren vanuit een openbaar register

### <a name="import-from-docker-hub"></a>Importeren vanuit Docker Hub

Gebruik bijvoorbeeld de opdracht [az acr import][az-acr-import] om de afbeelding met meerdere architectuur te importeren uit Docker Hub naar een register met de naam `hello-world:latest` *myregistry*. Omdat `hello-world` een officiële afbeelding is van Docker Hub, staat deze afbeelding in de standaardopslagplaats. `library` Neem de naam van de opslagplaats en eventueel een tag op in de waarde van de `--source` afbeeldingsparameter. (U kunt eventueel een afbeelding identificeren aan de basis van de samenvatting van het manifest in plaats van met een tag, waardoor een bepaalde versie van een afbeelding wordt gegarandeerd.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

U kunt controleren of er meerdere manifesten zijn gekoppeld aan deze afbeelding door de opdracht uit te `az acr repository show-manifests` voeren:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Als u een Docker Hub [hebt,](https://www.docker.com/pricing)raden we u aan de referenties te gebruiken bij het importeren van een afbeelding uit Docker Hub. Geef de Docker Hub gebruikersnaam en het wachtwoord of een [persoonlijk toegangs token](https://docs.docker.com/docker-hub/access-tokens/) als parameters door aan `az acr import` . In het volgende voorbeeld wordt een openbare afbeelding uit de opslagplaats `tensorflow` in Docker Hub geïmporteerd met behulp Docker Hub referenties:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importeren vanuit Microsoft Container Registry

Importeer bijvoorbeeld de `ltsc2019` Windows Server Core-afbeelding uit de `windows` opslagplaats in Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importeren vanuit een Azure-containerregister in dezelfde AD-tenant

U kunt een afbeelding importeren uit een Azure-containerregister in dezelfde AD-tenant met behulp van geïntegreerde Azure Active Directory machtigingen.

* Uw identiteit moet over Azure Active Directory machtigingen voor het lezen uit het bronregister (rol lezer) en [](container-registry-roles.md#custom-roles) voor het importeren naar het doelregister (de rol Inzender of een aangepaste rol waarmee de importImage-actie is mogelijk).

* Het register kan zich in hetzelfde of een ander Azure-abonnement in dezelfde Active Directory-tenant.

* [Openbare toegang](container-registry-access-selected-networks.md#disable-public-network-access) tot het bronregister is mogelijk uitgeschakeld. Als openbare toegang is uitgeschakeld, geeft u het bronregister op resource-id op in plaats van op de naam van de aanmeldingsserver voor het register.

* Als het bronregister en/of het doelregister een privé-eindpunt of registerfirewallregels heeft, moet u ervoor zorgen dat het beperkte register vertrouwde [services](allow-access-trusted-services.md) toegang geeft tot het netwerk.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importeren vanuit een register in hetzelfde abonnement

Importeer bijvoorbeeld de afbeelding `aci-helloworld:latest` uit een bronregister *mysourceregistry* naar *myregistry* in hetzelfde Azure-abonnement.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

In het volgende voorbeeld wordt de afbeelding geïmporteerd naar myregistry vanuit een bronregister `aci-helloworld:latest` *mysourceregistry* waarin toegang tot het openbare eindpunt van het register is uitgeschakeld.  Gebruik de parameter om de resource-id van het bronregister op te `--registry` geven. U ziet dat met `--source` de parameter alleen de bronopslagplaats en -tag worden opgegeven, niet de naam van de aanmeldingsserver voor het register.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

In het volgende voorbeeld wordt een afbeelding geïmporteerd via manifest digest (SHA-256 hash, vertegenwoordigd als ) in `sha256:...` plaats van met een tag:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importeren vanuit een register in een ander abonnement

In het volgende voorbeeld *maakt mysourceregistry* deel uit van een ander abonnement dan *myregistry* in dezelfde Active Directory-tenant. Gebruik de parameter om de resource-id van het bronregister op te `--registry` geven. U ziet dat met `--source` de parameter alleen de bronopslagplaats en -tag worden opgegeven, niet de naam van de aanmeldingsserver voor het register.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importeren vanuit een register met behulp van referenties voor de service-principal

Als u wilt importeren uit een register dat u niet kunt openen met behulp van geïntegreerde Active Directory-machtigingen, kunt u service-principalreferenties (indien beschikbaar) gebruiken voor het bronregister. De appID en het wachtwoord opgeven van een Active [Directory-service-principal](container-registry-auth-service-principal.md) die ACRPull-toegang heeft tot het bronregister. Het gebruik van een service-principal is handig voor het bouwen van systemen en andere systemen zonder toezicht die afbeeldingen in uw register moeten importeren.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importeren vanuit een Azure-containerregister in een andere AD-tenant

Als u wilt importeren vanuit een Azure-containerregister in een andere Azure Active Directory-tenant, geeft u het bronregister op op basis van de naam van de aanmeldingsserver en geeft u de gebruikersnaam en wachtwoordreferenties op waarmee pull-toegang tot het register mogelijk is. Gebruik bijvoorbeeld een token en wachtwoord binnen het bereik van de opslagplaats, of de appID en het wachtwoord van een Active [Directory-service-principal](container-registry-repository-scoped-permissions.md) met ACRPull-toegang tot het bronregister. [](container-registry-auth-service-principal.md) 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importeren vanuit een niet-Azure-privécontainerregister

Importeer een afbeelding uit een niet-Azure-privéregister door referenties op te geven waarmee pull-toegang tot het register mogelijk is. Haal bijvoorbeeld een afbeelding op uit een privé-Docker-register: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het importeren van containerafbeeldingen naar een Azure-containerregister vanuit een openbaar register of een ander privéregister. Zie de naslag voor de opdracht [az acr import][az-acr-import] voor aanvullende opties voor het importeren van afbeeldingen. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
