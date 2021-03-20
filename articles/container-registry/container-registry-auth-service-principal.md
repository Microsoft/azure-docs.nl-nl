---
title: Verifiëren met service-principal
description: Toegang bieden tot installatie kopieën in uw persoonlijke container register met behulp van een Azure Active Directory Service-Principal.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 8d49628576a1c337efaea3e5286fef00e39def17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259152"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Verificatie Azure Container Registry met Service-principals

U kunt een service-principal voor Azure Active Directory (Azure AD) gebruiken om een container installatie kopie te bieden `docker push` en `pull` toegang te krijgen tot uw container register. Met behulp van een Service-Principal kunt u toegang bieden tot ' headless ' Services en toepassingen.

## <a name="what-is-a-service-principal"></a>Wat is een service-principal?

Azure AD- *service-principals* bieden toegang tot Azure-resources binnen uw abonnement. U kunt een Service-Principal beschouwen als een gebruikers-id voor een service, waarbij ' service ' een toepassing, service of platform is die toegang nodig heeft tot de resources. U kunt een service-principal met toegangs rechten instellen die alleen zijn gericht op de resources die u opgeeft. Configureer vervolgens uw toepassing of service voor het gebruik van de referenties van de service-principal voor toegang tot deze resources.

In de context van Azure Container Registry kunt u een Azure AD-service-principal maken met pull-, push-en pull-toegang of andere machtigingen voor uw persoonlijke REGI ster in Azure. Zie [Azure container Registry rollen en machtigingen](container-registry-roles.md)voor een volledige lijst.

## <a name="why-use-a-service-principal"></a>Waarom een Service-Principal gebruiken?

Als u een service-principal voor Azure AD gebruikt, kunt u toegang tot uw persoonlijke container register verlenen. Maak verschillende service-principals voor elk van uw toepassingen of services, elk met aangepaste toegangs rechten voor uw REGI ster. En omdat u het delen van referenties tussen services en toepassingen kunt voor komen, kunt u referenties draaien of de toegang intrekken voor alleen de Service-Principal (en dus de toepassing) die u kiest.

Configureer bijvoorbeeld uw webtoepassing voor het gebruik van een service-principal die hiermee `pull` alleen toegang geeft tot de installatie kopie, terwijl uw build-systeem een Service-Principal gebruikt die deze zowel heeft `push` als `pull` toegang verschaft. Als de ontwikkeling van uw toepassing wordt gewijzigd, kunt u de referenties van de Service-Principal draaien zonder dat dit van invloed is op het build-systeem.

## <a name="when-to-use-a-service-principal"></a>Wanneer moet u een Service-Principal gebruiken?

U moet een Service-Principal gebruiken om toegang te krijgen tot het REGI ster in **headless scenario's**. Dat wil zeggen, elke toepassing, service of script dat container installatie kopieën moet pushen of ophalen in een geautomatiseerde of op een andere manier zonder toezicht. Bijvoorbeeld:

  * *Pull*: implementeer containers vanuit een REGI ster naar Orchestration-systemen, waaronder KUBERNETES, DC/OS en docker Swarm. U kunt ook uit container registers halen voor gerelateerde Azure-Services, zoals [Azure Kubernetes service (AKS)](../aks/cluster-container-registry-integration.md), [Azure container instances](container-registry-auth-aci.md), [app service](../app-service/index.yml), [batch](../batch/index.yml), [service Fabric](../service-fabric/index.yml)en anderen.

  * *Push*: container installatie kopieën bouwen en deze pushen naar een REGI ster met behulp van doorlopende integratie-en implementatie oplossingen zoals Azure pipelines of Jenkins.

Voor individuele toegang tot een REGI ster, zoals wanneer u hand matig een container installatie kopie naar uw ontwikkel werkstation haalt, wordt u aangeraden uw eigen [Azure AD-identiteit](container-registry-authentication.md#individual-login-with-azure-ad) te gebruiken in plaats van toegang tot het REGI ster (bijvoorbeeld met [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Voorbeeldscripts

U kunt de voor gaande voorbeeld scripts voor Azure CLI vinden op GitHub, evenals versies voor Azure PowerShell:

* [Azure-CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Verifiëren met de Service-Principal

Zodra u een Service-Principal hebt die u toegang tot uw container register hebt verleend, kunt u de referenties voor toegang tot ' headless ' Services en toepassingen configureren of ze invoeren met behulp van de `docker login` opdracht. Gebruik de volgende waarden:

* **Gebruikers naam** -id van de Service-Principal-toepassing (ook wel *client-id* genoemd)
* **Wacht** woord-Service-Principal-wacht woord (ook wel *client geheim* genoemd)

Elke waarde is een GUID van het formulier `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . 

> [!TIP]
> U kunt het wacht woord van een Service-Principal opnieuw genereren door de opdracht [AZ AD SP reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) uit te voeren.
>

### <a name="use-credentials-with-azure-services"></a>Referenties gebruiken met Azure-Services

U kunt de referenties van de Service-Principal gebruiken van elke Azure-service die wordt geverifieerd met een Azure container Registry.  Gebruik de referenties van de Service-Principal in plaats van de beheerders referenties van het REGI ster voor diverse scenario's.

Gebruik bijvoorbeeld de referenties om een installatie kopie van een Azure container Registry naar [Azure container instances](container-registry-auth-aci.md)te halen.

### <a name="use-with-docker-login"></a>Gebruiken met aanmelden bij docker

U kunt uitvoeren `docker login` met behulp van een service-principal. In het volgende voor beeld wordt de toepassings-ID van de Service-Principal door gegeven in de omgevings variabele `$SP_APP_ID` en het wacht woord in de variabele `$SP_PASSWD` . Zie voor aanbevolen procedures voor het beheren van docker-referenties de koppeling naar de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Zodra de gegevens zijn aangemeld, worden deze door docker in de cache opgeslagen.

### <a name="use-with-certificate"></a>Gebruiken met certificaat

Als u een certificaat aan uw Service-Principal hebt toegevoegd, kunt u zich aanmelden bij Azure CLI met verificatie op basis van certificaten en vervolgens de opdracht [AZ ACR login][az-acr-login] gebruiken om toegang te krijgen tot een REGI ster. Het gebruik van een certificaat als geheim in plaats van een wacht woord biedt extra beveiliging wanneer u de CLI gebruikt. 

U kunt een zelfondertekend certificaat maken wanneer u [een Service-Principal maakt](/cli/azure/create-an-azure-service-principal-azure-cli). U kunt ook een of meer certificaten toevoegen aan een bestaande service-principal. Als u bijvoorbeeld een van de scripts in dit artikel gebruikt om een service-principal te maken of bij te werken met rechten om installatie kopieën uit een REGI ster te halen of te pushen, voegt u een certificaat toe met de opdracht [AZ AD SP Credential reset][az-ad-sp-credential-reset] .

Als u de service-principal met het certificaat wilt gebruiken om u aan te [melden bij de Azure cli](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal), moet het certificaat de PEM-indeling hebben en de persoonlijke sleutel bevatten. Als uw certificaat niet de vereiste indeling heeft, gebruikt u een hulp programma zoals `openssl` om het te converteren. Wanneer u [AZ login][az-login] uitvoert om zich aan te melden bij de CLI met behulp van de Service-Principal, geeft u ook de toepassings-id van de Service-Principal en de Active Directory Tenant-id op. In het volgende voor beeld worden deze waarden als omgevings variabelen weer gegeven:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Voer [AZ ACR login][az-acr-login] uit voor verificatie met het REGI ster:

```azurecli
az acr login --name myregistry
```

De CLI gebruikt het token dat is gemaakt toen u de `az login` sessie met het REGI ster hebt geverifieerd.

## <a name="next-steps"></a>Volgende stappen

* Zie het [verificatie overzicht](container-registry-authentication.md) voor andere scenario's voor verificatie met een Azure container Registry.

* Raadpleeg de zelf studie voor het [maken en implementeren van een container installatie kopie met behulp van ACR-taken](container-registry-tutorial-quick-task.md)voor een voor beeld van het gebruik van een Azure-sleutel kluis voor het opslaan en ophalen van referenties voor de service-principal voor een container register.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
