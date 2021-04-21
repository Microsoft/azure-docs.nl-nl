---
title: Opties voor registerverificatie
description: Verificatieopties voor een persoonlijk Azure-containerregister, waaronder aanmelden met een Azure Active Directory-identiteit, service-principals gebruiken en optionele beheerdersreferenties gebruiken.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 7ff55d569e2659262ce9f323e4db2ea7ed671d20
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784277"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Verifiëren met een Azure-containerregister

Er zijn verschillende manieren om te verifiëren met een Azure-containerregister, die allemaal van toepassing zijn op een of meer scenario's voor registergebruik.

Aanbevolen manieren zijn verificatie bij een register rechtstreeks [via](#individual-login-with-azure-ad)afzonderlijke aanmelding, of uw toepassingen en container-orchestrators kunnen verificatie zonder toezicht of headless uitvoeren met behulp van een [service-principal](#service-principal)van Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Verificatieopties

De volgende tabel bevat beschikbare verificatiemethoden en typische scenario's. Zie gekoppelde inhoud voor meer informatie.

| Methode                               | Verificatie                                           | Scenario's                                                            | Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)                             | Beperkingen                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Afzonderlijke AD-identiteit](#individual-login-with-azure-ad)                | `az acr login` in Azure CLI                             | Interactieve push/pull door ontwikkelaars, testers                                    | Yes                              | AD-token moet elke 3 uur worden vernieuwd     |
| [AD-service-principal](#service-principal)                  | `docker login`<br/><br/>`az acr login` in Azure CLI<br/><br/> Aanmeldingsinstellingen voor register in API's of hulpprogramma's<br/><br/> [Pull-geheim van Kubernetes](container-registry-auth-kubernetes.md)                                           | Pushen zonder toezicht vanuit CI/CD-pijplijn<br/><br/> Pull zonder toezicht naar Azure of externe services  | Yes                              | De standaardwaarde voor SP-wachtwoord verloopt 1 jaar       |                                                           
| [Integreren met AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Register koppelen wanneer AKS-cluster wordt gemaakt of bijgewerkt  | Pull zonder toezicht naar AKS-cluster                                                  | Nee, alleen pull-toegang             | Alleen beschikbaar met AKS-cluster            |
| [Beheerde identiteit voor Azure-resources](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` in Azure CLI                                       | Pushen zonder toezicht vanuit Azure CI/CD-pijplijn<br/><br/> Pull zonder toezicht naar Azure-services<br/><br/>   | Yes                              | Alleen gebruiken bij geselecteerde Azure-services die [beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Gebruiker met beheerdersrechten](#admin-account)                            | `docker login`                                          | Interactieve push/pull door afzonderlijke ontwikkelaars of tester<br/><br/>Portalimplementatie van installatie afbeelding van register naar Azure App Service of Azure Container Instances                      | Nee, altijd pull- en pushtoegang  | Eén account per register, niet aanbevolen voor meerdere gebruikers         |
| [Toegangs token binnen het bereik van de opslagplaats](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` in Azure CLI   | Interactieve push/pull naar opslagplaats door individuele ontwikkelaar of tester<br/><br/> Push/pull zonder toezicht naar opslagplaats door afzonderlijk systeem of extern apparaat                  | Yes                              | Momenteel niet geïntegreerd met AD-identiteit  |

## <a name="individual-login-with-azure-ad"></a>Afzonderlijke aanmelding met Azure AD

Wanneer u rechtstreeks met uw register werkt, zoals het binnenhalen van afbeeldingen naar en het pushen van afbeeldingen van een ontwikkelwerkstation naar een register dat u hebt gemaakt, moet u zich verifiëren met behulp van uw afzonderlijke Azure-identiteit. Meld u aan bij [de Azure CLI](/cli/azure/install-azure-cli) met az [login](/cli/azure/reference-index#az_login)en voer vervolgens de opdracht [az acr login](/cli/azure/acr#az_acr_login) uit:

```azurecli
az login
az acr login --name <acrName>
```

Wanneer u zich aanmeldt met , gebruikt de CLI het token dat is gemaakt tijdens de uitvoering om uw sessie naadloos te verifiëren `az acr login` `az login` bij uw register. Om de verificatiestroom te voltooien, moeten de Docker CLI en Docker-daemon zijn geïnstalleerd en worden uitgevoerd in uw omgeving. `az acr login` gebruikt de Docker-client om een Azure Active Directory in het bestand in te `docker.config` stellen. Zodra u op deze manier hebt aangemeld, worden uw referenties in de cache opgeslagen en is voor volgende opdrachten in uw sessie geen gebruikersnaam of `docker` wachtwoord vereist.

> [!TIP]
> Gebruik ook om een afzonderlijke identiteit te verifiëren wanneer u andere artefacten dan Docker-afbeeldingen naar uw register wilt pushen of pullen, zoals `az acr login` [OCI-artefacten.](container-registry-oci-artifacts.md)  

Voor registertoegang is het token dat wordt gebruikt door 3 uur geldig. Daarom raden we u aan u altijd aan te melden bij het register voordat u `az acr login` een opdracht gaat  `docker` uitvoeren. Als uw token is verlopen, kunt u het vernieuwen door de opdracht opnieuw te `az acr login` gebruiken om opnieuw te worden geauthenticeerd. 

Het `az acr login` gebruik van met Azure-identiteiten biedt op rollen gebaseerd [toegangsbeheer van Azure (Azure RBAC).](../role-based-access-control/role-assignments-portal.md) In sommige scenario's kunt u zich aanmelden bij een register met uw eigen afzonderlijke identiteit in Azure AD of andere Azure-gebruikers configureren met specifieke [Azure-rollen en -machtigingen.](container-registry-roles.md) Voor scenario's met meerdere service-omgevingen of voor het afhandelen van de behoeften van een werkgroep of een ontwikkelingswerkstroom waarbij u geen afzonderlijke toegang wilt beheren, kunt u zich ook aanmelden met een beheerde identiteit voor [Azure-resources.](container-registry-authentication-managed-identity.md)

### <a name="az-acr-login-with---expose-token"></a>az acr login with --expose-token

In sommige gevallen moet u mogelijk verifiëren met `az acr login` wanneer de Docker-daemon niet wordt uitgevoerd in uw omgeving. U moet bijvoorbeeld uitvoeren in een script in Azure Cloud Shell, dat de Docker CLI levert, maar de `az acr login` Docker-daemon niet wordt uitgevoerd.

Voer voor dit scenario eerst `az acr login` uit met de parameter `--expose-token` . Met deze optie wordt een toegangs token beschikbaar in plaats van u aan te melden via de Docker CLI.

```azurecli
az acr login --name <acrName> --expose-token
```

In de uitvoer wordt het toegangsken weergegeven, dat hier wordt afgekort:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
Voor registerverificatie raden we u aan de tokenreferenties op een veilige locatie op te slaan en de aanbevolen procedures voor het beheren van [docker-aanmeldingsreferenties](https://docs.docker.com/engine/reference/commandline/login/)te volgen. Sla de tokenwaarde bijvoorbeeld op in een omgevingsvariabele:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Voer vervolgens `docker login` uit, door `00000000-0000-0000-0000-000000000000` te geven als de gebruikersnaam en het toegangs token als wachtwoord te gebruiken:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Service-principal

Als u een [service-principal aan](../active-directory/develop/app-objects-and-service-principals.md) uw register toewijst, kan uw toepassing of service deze gebruiken voor headless verificatie. Service-principals staan op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) toe aan een register en u kunt meerdere service-principals toewijzen aan een register. Met meerdere service-principals kunt u verschillende toegangsmogelijkheden voor verschillende toepassingen definiëren.

De beschikbare rollen voor een containerregister zijn onder andere:

* **AcrPull:** pull

* **AcrPush:** pull en push

* **Eigenaar:** rollen pullen, pushen en toewijzen aan andere gebruikers

Zie rollen en machtigingen voor een [volledige Azure Container Registry rollen en machtigingen.](container-registry-roles.md)

Zie verificatie met service-principals voor CLI-scripts voor het maken van een service-principal voor verificatie met een Azure [Azure Container Registry-containerregister](container-registry-auth-service-principal.md)en meer richtlijnen.

## <a name="admin-account"></a>Beheerdersaccount

Elk containerregister bevat een beheerdersaccount, dat standaard is uitgeschakeld. U kunt de gebruiker met beheerdersrechten inschakelen en de referenties beheren in de Azure Portal of met behulp van de Azure CLI of andere Azure-hulpprogramma's. Het beheerdersaccount heeft volledige machtigingen voor het register.

Het beheerdersaccount is momenteel vereist voor sommige scenario's voor het implementeren van een -afbeelding vanuit een containerregister naar bepaalde Azure-services. Het beheerdersaccount is bijvoorbeeld nodig wanneer u de Azure Portal gebruikt om een containerafbeelding rechtstreeks vanuit een register te implementeren [naar Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) of [Azure Web Apps for Containers.](container-registry-tutorial-deploy-app.md)

> [!IMPORTANT]
> Het beheerdersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. U wordt aangeraden de referenties voor het beheerdersaccount niet te delen met meerdere gebruikers. Alle gebruikers die zich bij het beheerdersaccount authenticeren, worden weergegeven als één gebruiker met push- en pull-toegang tot het register. Als u dit account wilt wijzigen of uitschakelen, wordt registertoegang uitgeschakeld voor alle gebruikers die de referenties ervan gebruiken. Afzonderlijke identiteiten worden aanbevolen voor gebruikers en service-principals voor headless scenario's.
>

Het beheerdersaccount wordt voorzien van twee wachtwoorden, die beide opnieuw kunnen worden ge regenereerd. Met twee wachtwoorden kunt u verbinding met het register houden door één wachtwoord te gebruiken terwijl u het andere wachtwoord opnieuw maakt. Als het beheerdersaccount is ingeschakeld, kunt u de gebruikersnaam en het wachtwoord doorgeven aan de opdracht wanneer u wordt gevraagd om `docker login` basisverificatie voor het register. Bijvoorbeeld:

```
docker login myregistry.azurecr.io 
```

Zie de naslag voor de opdracht [docker](https://docs.docker.com/engine/reference/commandline/login/) login voor aanbevolen procedures voor het beheren van aanmeldingsreferenties.

Als u de gebruiker met beheerdersrechten wilt inschakelen voor een bestaand register, kunt u de parameter van de `--admin-enabled` [opdracht az acr update](/cli/azure/acr#az_acr_update) in de Azure CLI gebruiken:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

U kunt de gebruiker met beheerdersrechten inschakelen in Azure Portal  door in het register te navigeren, Toegangssleutels te selecteren onder **INSTELLINGEN** en vervolgens Inschakelen **onder** **Gebruiker met beheerdersrechten.**

![De gebruikersinterface van de gebruiker met beheerdersrechten inschakelen in Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste afbeelding pushen met de Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
