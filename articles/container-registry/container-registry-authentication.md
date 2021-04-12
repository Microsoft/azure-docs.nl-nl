---
title: Opties voor register verificatie
description: Verificatie opties voor een persoonlijk Azure container Registry, met inbegrip van het aanmelden met een Azure Active Directory identiteit, het gebruik van service-principals en het gebruik van optionele beheerders referenties.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: d12895502ecd30991fbef836903a8ceea445b770
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285498"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Verifiëren met een Azure container Registry

Er zijn verschillende manieren om te verifiëren met een Azure container Registry, die allemaal van toepassing zijn op een of meer scenario's voor het gebruik van het REGI ster.

De aanbevolen manieren zijn verificatie naar een REGI ster rechtstreeks via [afzonderlijke aanmelding](#individual-login-with-azure-ad), of uw toepassingen en container-Orchestrator kunnen zonder toezicht of ' headless ' verificatie uitvoeren met behulp van een [Service-Principal](#service-principal)voor Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Verificatieopties

De volgende tabel bevat een lijst met beschik bare verificatie methoden en typische scenario's. Zie gekoppelde inhoud voor meer informatie.

| Methode                               | Verificatie uitvoeren                                           | Scenario's                                                            | Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)                             | Beperkingen                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuele AD-identiteit](#individual-login-with-azure-ad)                | `az acr login` in azure CLI                             | Interactieve push/pull door ontwikkel aars, testers                                    | Ja                              | AD-token moet elke 3 uur worden vernieuwd     |
| [AD-Service-Principal](#service-principal)                  | `docker login`<br/><br/>`az acr login` in azure CLI<br/><br/> Aanmeldings instellingen voor het REGI ster in Api's of hulpprogram ma's<br/><br/> [Kubernetes pull Secret](container-registry-auth-kubernetes.md)                                           | Push installatie zonder toezicht van CI/CD-pijp lijn<br/><br/> Pull-bewerking zonder toezicht naar Azure of externe services  | Ja                              | Standaard verval van het SP-wacht woord is 1 jaar       |                                                           
| [Integreren met AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | REGI ster koppelen wanneer AKS-cluster is gemaakt of bijgewerkt  | Pull naar AKS-cluster zonder toezicht                                                  | Nee, alleen pull-toegang             | Alleen beschikbaar met AKS-cluster            |
| [Beheerde identiteit voor Azure-resources](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` in azure CLI                                       | Push installatie zonder toezicht van Azure CI/CD-pijp lijn<br/><br/> Pull-bewerking zonder toezicht naar Azure-Services<br/><br/>   | Ja                              | Alleen gebruiken van Select Azure-Services die [beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Gebruiker met beheerdersrechten](#admin-account)                            | `docker login`                                          | Interactieve push/pull door afzonderlijke ontwikkelaar of tester<br/><br/>Portal-implementatie van de installatie kopie van het REGI ster naar Azure App Service of Azure Container Instances                      | Nee, altijd pull-en push-toegang  | Eén account per REGI ster, niet aanbevolen voor meerdere gebruikers         |
| [Toegangs token in opslag plaats bereik](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` in azure CLI   | Interactieve push/pull naar opslag plaats door afzonderlijke ontwikkelaar of tester<br/><br/> Pushen zonder toezicht/pull-naar opslag plaats per afzonderlijk systeem of extern apparaat                  | Ja                              | Momenteel niet geïntegreerd met AD-identiteit  |

## <a name="individual-login-with-azure-ad"></a>Afzonderlijke aanmelding met Azure AD

Wanneer u rechtstreeks met uw REGI ster werkt, zoals het verzamelen van installatie kopieën naar en het pushen van installatie kopieën van een ontwikkel werkstation naar een REGI ster dat u hebt gemaakt, kunt u verifiëren met behulp van uw individuele Azure-identiteit. Meld u aan bij de [Azure cli](/cli/azure/install-azure-cli) met [AZ login](/cli/azure/reference-index#az-login)en voer vervolgens de opdracht [AZ ACR login](/cli/azure/acr#az-acr-login) :

```azurecli
az login
az acr login --name <acrName>
```

Wanneer u zich aanmeldt met `az acr login` , gebruikt de CLI het token dat u hebt gemaakt `az login` om uw sessie naadloos te verifiëren met het REGI ster. Voor het volt ooien van de verificatie stroom moet de docker CLI en docker daemon zijn geïnstalleerd en worden uitgevoerd in uw omgeving. `az acr login` maakt gebruik van de docker-client om een Azure Active Directory-token in het bestand in te stellen `docker.config` . Zodra u op deze manier bent aangemeld, worden uw referenties in de cache opgeslagen en zijn `docker` voor volgende opdrachten in uw sessie geen gebruikers naam of wacht woord vereist.

> [!TIP]
> U kunt ook gebruiken `az acr login` om een afzonderlijke identiteit te verifiëren wanneer u andere artefacten dan docker-installatie kopieën wilt pushen naar uw REGI ster, zoals [OCI-artefacten](container-registry-oci-artifacts.md).  

Voor toegang tot het REGI ster is het token dat wordt gebruikt door `az acr login` **drie uur** geldig. Daarom raden we u aan altijd aan te melden bij het REGI ster voordat u een `docker` opdracht uitvoert. Als uw token verloopt, kunt u het vernieuwen met behulp van de `az acr login` opdracht opnieuw om opnieuw te verifiëren. 

`az acr login`Het gebruik van met Azure-identiteiten biedt [op rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Voor sommige scenario's wilt u zich mogelijk aanmelden bij een REGI ster met uw eigen identiteit in azure AD of andere Azure-gebruikers met specifieke [Azure-rollen en-machtigingen](container-registry-roles.md)configureren. Voor scenario's met meerdere services of voor het afhandelen van de behoeften van een werk groep of een ontwikkel werk stroom waarbij u geen individuele toegang wilt beheren, kunt u zich ook aanmelden met een [beheerde identiteit voor Azure-resources](container-registry-authentication-managed-identity.md).

### <a name="az-acr-login-with---expose-token"></a>AZ ACR login with--Expo-token

In sommige gevallen moet u zich mogelijk verifiëren met `az acr login` wanneer de docker-daemon niet in uw omgeving wordt uitgevoerd. U moet bijvoorbeeld `az acr login` in een script in azure Cloud shell worden uitgevoerd. Dit biedt de docker-CLI, maar voert de docker-daemon niet uit.

Voor dit scenario voert u `az acr login` eerst uit met de `--expose-token` para meter. Met deze optie wordt een toegangs token weer gegeven in plaats van u aan te melden via de docker-CLI.

```azurecli
az acr login --name <acrName> --expose-token
```

In uitvoer wordt het toegangs token weer gegeven, dat hier wordt afgekort:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
Voor register verificatie wordt u aangeraden de token referentie op te slaan op een veilige locatie en de aanbevolen procedures voor het beheren van [docker-aanmeldings](https://docs.docker.com/engine/reference/commandline/login/)referenties te volgen. Sla de token waarde bijvoorbeeld op in een omgevings variabele:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Vervolgens voert u uit `docker login` `00000000-0000-0000-0000-000000000000` als de gebruikers naam en gebruikt u het toegangs token als wacht woord:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Service-principal

Als u een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md) aan uw REGI ster toewijst, kan uw toepassing of service deze gebruiken voor headless-verificatie. Met Service-principals is [Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/role-assignments-portal.md) in een REGI ster toegestaan en kunt u meerdere service-principals aan een REGI ster toewijzen. Met meerdere service-principals kunt u verschillende toegangs rechten definiëren voor verschillende toepassingen.

De beschik bare rollen voor een container register zijn onder andere:

* **AcrPull**: pull

* **AcrPush**: pull en push

* **Eigenaar**: rollen pullen, pushen en toewijzen aan andere gebruikers

Zie [Azure container Registry rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met rollen.

Zie [Azure container Registry verificatie met Service-principals](container-registry-auth-service-principal.md)voor CLI-scripts voor het maken van een service-principal voor verificatie met een Azure container Registry en meer informatie.

## <a name="admin-account"></a>Beheerdersaccount

Elk container register bevat een beheer gebruikers account dat standaard is uitgeschakeld. U kunt de gebruiker beheerder inschakelen en de referenties beheren in de Azure Portal, of door gebruik te maken van de Azure CLI-of andere Azure-hulpprogram ma's. Het beheerders account heeft volledige machtigingen voor het REGI ster.

Het beheerders account is momenteel vereist voor bepaalde scenario's voor het implementeren van een installatie kopie vanuit een container register naar bepaalde Azure-Services. Het beheerders account is bijvoorbeeld nodig wanneer u de Azure Portal gebruikt om een container installatie kopie vanuit een REGI ster rechtstreeks te implementeren in [Azure container instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) of [Azure web apps voor containers](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> Het beheerders account is ontworpen voor één gebruiker voor toegang tot het REGI ster, voornamelijk voor test doeleinden. Het is niet raadzaam om de referenties van het beheerders account te delen met meerdere gebruikers. Alle gebruikers die worden geverifieerd met het beheerders account, worden weer gegeven als één gebruiker met push-en pull-toegang tot het REGI ster. Als u dit account wijzigt of uitschakelt, wordt de toegang tot het REGI ster uitgeschakeld voor alle gebruikers die de referenties gebruiken. Individuele identiteiten worden aanbevolen voor gebruikers en service-principals voor headless scenario's.
>

Het beheerders account wordt weer gegeven met twee wacht woorden, die beide opnieuw kunnen worden gegenereerd. Met twee wacht woorden kunt u de verbinding met het REGI ster onderhouden door één wacht woord te gebruiken terwijl u het andere opnieuw genereert. Als het beheerders account is ingeschakeld, kunt u de gebruikers naam en het wacht woord door geven aan de `docker login` opdracht wanneer u wordt gevraagd om basis verificatie naar het REGI ster. Bijvoorbeeld:

```
docker login myregistry.azurecr.io 
```

Zie voor aanbevolen procedures voor het beheren van aanmeldings referenties de naslag informatie voor het [Aanmelden bij docker](https://docs.docker.com/engine/reference/commandline/login/) -opdrachten.

Als u de gebruiker met beheerders rechten wilt inschakelen voor een bestaand REGI ster, kunt u de `--admin-enabled` para meter van de opdracht [AZ ACR update](/cli/azure/acr#az-acr-update) gebruiken in de Azure cli:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

U kunt de gebruiker beheerder in de Azure Portal inschakelen door te navigeren door uw REGI ster, **toegangs sleutels** te selecteren onder **instellingen** en vervolgens in te **scha kelen** onder **gebruiker met beheerders** rechten.

![Gebruikers interface van de beheerder inschakelen in de Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste installatie kopie pushen met behulp van Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
