---
title: Problemen met aanmelding bij REGI ster oplossen
description: Symptomen, oorzaken en oplossingen voor veelvoorkomende problemen bij het aanmelden bij een Azure container Registry
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5deb1717cf3886d8ea9c021d92afa358946b16dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99052075"
---
# <a name="troubleshoot-registry-login"></a>Problemen met register aanmelding oplossen

Dit artikel helpt u bij het oplossen van problemen die zich kunnen voordoen wanneer u zich aanmeldt bij een Azure container Registry. 

## <a name="symptoms"></a>Symptomen

Dit kan een of meer van de volgende zijn:

* Kan niet aanmelden bij het REGI ster met `docker login` , `az acr login` of beide
* Kan niet aanmelden bij het REGI ster en er wordt een fout bericht weer gegeven `unauthorized: authentication required` of `unauthorized: Application not registered with AAD`
* U kunt zich niet aanmelden bij het REGI ster en u ontvangt een Azure CLI-fout `Could not connect to the registry login server`
* Kan geen installatie kopieën pushen of ophalen en u ontvangt een docker-fout `unauthorized: authentication required`
* Kan geen toegang krijgen tot het REGI ster vanuit de Azure Kubernetes-service, Azure DevOps of een andere Azure-service
* Kan geen toegang krijgen tot het REGI ster en er treedt een fout `Error response from daemon: login attempt failed with status: 403 Forbidden` op. Zie problemen met het [netwerk oplossen met het REGI ster](container-registry-troubleshoot-access.md)
* Kan geen toegang krijgen tot of register instellingen weer geven in Azure Portal of het REGI ster niet beheren met de Azure CLI

## <a name="causes"></a>Oorzaken

* Docker is niet juist geconfigureerd in uw omgeving- [oplossing](#check-docker-configuration)
* Het REGI ster bestaat niet of de naam is onjuist- [oplossing](#specify-correct-registry-name)
* De register referenties zijn niet geldig- [oplossing](#confirm-credentials-to-access-registry)
* De referenties zijn niet geautoriseerd voor push-, pull-of Azure Resource Manager- [oplossingen](#confirm-credentials-are-authorized-to-access-registry) .
* De referenties zijn verlopen- [oplossing](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Verdere diagnose 

Voer de opdracht [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) uit voor meer informatie over de status van de register omgeving en eventueel toegang tot een doel register. U kunt bijvoorbeeld configuratie fouten van docker diagnosticeren of Azure Active Directory aanmeldings problemen oplossen. 

Zie [de status van een Azure container Registry controleren](container-registry-check-health.md) op opdracht voorbeelden. Als er fouten worden gerapporteerd, raadpleegt u de [fout referentie](container-registry-health-error-reference.md) en de volgende secties voor de aanbevolen oplossingen.

Als u problemen ondervindt met het gebruik van de register wih Azure Kubernetes-service, voert u de opdracht [AZ AKS check-ACR](/cli/azure/aks#az_aks_check_acr) uit om te controleren of het REGI ster toegankelijk is vanuit het AKS-cluster.

> [!NOTE]
> Sommige verificatie-of autorisatie fouten kunnen zich ook voordoen als er firewall-of netwerk configuraties zijn die geen toegang tot het REGI ster verhinderen. Zie [problemen met het netwerk oplossen met het REGI ster](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Mogelijke oplossingen

### <a name="check-docker-configuration"></a>Docker-configuratie controleren

Voor de meeste Azure Container Registry-verificatie stromen is een lokale docker-installatie vereist, zodat u met uw REGI ster kunt verifiëren voor bewerkingen zoals het pushen en uitpakken van installatie kopieën. Controleer of de docker CLI-client en-daemon (docker-Engine) worden uitgevoerd in uw omgeving. U hebt docker client versie 18,03 of hoger nodig.

Gerelateerde links:

* [Verificatieoverzicht](container-registry-authentication.md#authentication-options)
* [Veelgestelde vragen over container Registry](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>De juiste register naam opgeven

Wanneer u gebruikt `docker login` , geeft u de volledige naam van de aanmeldings server van het REGI ster op, bijvoorbeeld *myregistry.azurecr.io*. Zorg ervoor dat u alleen kleine letters gebruikt. Voorbeeld:

```console
docker login myregistry.azurecr.io
```

Wanneer u [AZ ACR login](/cli/azure/acr#az-acr-login) met een Azure Active Directory-identiteit gebruikt, meldt u zich eerst aan bij [de Azure cli](/cli/azure/authenticate-azure-cli)en geeft u vervolgens de naam van de Azure-resource van het REGI ster op. De resource naam is de naam die wordt gegeven wanneer het REGI ster is gemaakt, zoals *myregistry* (zonder domein achtervoegsel). Voorbeeld:

```azurecli
az acr login --name myregistry
```

Gerelateerde links:

* [AZ ACR login slaagt maar docker mislukt met fout: niet toegestaan: verificatie vereist](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Referenties voor toegang tot het REGI ster bevestigen

Controleer de geldigheid van de referenties die u gebruikt voor uw scenario of die aan u zijn geleverd door een eigenaar van het REGI ster. Enkele mogelijke problemen:

* Als u een Active Directory Service-Principal gebruikt, moet u ervoor zorgen dat u de juiste referenties gebruikt in de Active Directory Tenant:
  * Gebruikers naam-ID van de Service-Principal-toepassing (ook wel *client-id* genoemd)
  * Wacht woord-Service-Principal-wacht woord (ook wel *client geheim* genoemd)
* Als u een Azure-service gebruikt, zoals Azure Kubernetes service of Azure DevOps voor toegang tot het REGI ster, bevestigt u de Register configuratie voor uw service. 
* Als u `az acr login` met de optie hebt uitgevoerd `--expose-token` , waarbij u zich aanmeldt zonder de docker-daemon te gebruiken, moet u verifiëren met de gebruikers naam `00000000-0000-0000-0000-000000000000` .
* Als uw REGI ster is geconfigureerd voor [anonieme pull-toegang](container-registry-faq.md#how-do-i-enable-anonymous-pull-access), kunnen bestaande docker-referenties die zijn opgeslagen van een eerdere docker-aanmelding, anonieme toegang verhinderen. Voer uit `docker logout` voordat u een anonieme pull-bewerking in het REGI ster probeert uit te voeren.

Gerelateerde links:

* [Verificatieoverzicht](container-registry-authentication.md#authentication-options)
* [Afzonderlijke aanmelding met Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Aanmelden met Service-Principal](container-registry-auth-service-principal.md)
* [Aanmelden met beheerde identiteit](container-registry-authentication-managed-identity.md)
* [Aanmelden met een token uit de opslag plaats](container-registry-repository-scoped-permissions.md)
* [Aanmelden met beheerders account](container-registry-authentication.md#admin-account)
* [Fout codes voor Azure AD-verificatie en-autorisatie](../active-directory/develop/reference-aadsts-error-codes.md)
* [AZ ACR login](/cli/azure/acr#az-acr-login) -Naslag informatie

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Bevestigen dat referenties zijn geautoriseerd voor toegang tot het REGI ster

Bevestig de register machtigingen die zijn gekoppeld aan de referenties, zoals de `AcrPull` Azure-rol voor het ophalen van installatie kopieën uit het REGI ster of de `AcrPush` rol voor het pushen van installatie kopieën. 

Toegang tot een REGI ster in de portal of het register beheer met behulp van Azure CLI vereist ten minste de `Reader` rol of gelijkwaardige machtigingen om Azure Resource Manager bewerkingen uit te voeren.

Als uw machtigingen recent zijn gewijzigd om toegang tot het REGI ster mogelijk te maken via de portal, moet u mogelijk een incognito of een persoonlijke sessie in uw browser proberen om te voor komen dat er verouderde browser cache of cookies worden geopend.

U of een register eigenaar moet voldoende bevoegdheden hebben in het abonnement om roltoewijzingen toe te voegen of te verwijderen.

Gerelateerde links:

* [Azure-rollen en-machtigingen-Azure Container Registry](container-registry-roles.md)
* [Aanmelden met een token uit de opslag plaats](container-registry-repository-scoped-permissions.md)
* [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](../role-based-access-control/role-assignments-portal.md)
* [Gebruik de portal voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md)
* [Een nieuw toepassingsgeheim maken](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Verificatie-en autorisatie codes voor Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Controleren of de referenties zijn verlopen

Tokens en Active Directory referenties kunnen verlopen na gedefinieerde Peri Oden, waardoor toegang tot het REGI ster wordt voor komen. Als u toegang wilt inschakelen, moeten referenties mogelijk opnieuw worden ingesteld of opnieuw worden gegenereerd.

* Als u een individuele AD-identiteit, een beheerde identiteit of Service-Principal gebruikt voor aanmelding bij een REGI ster, verloopt het AD-token na 3 uur. Meld u opnieuw aan bij het REGI ster.  
* Als u een AD-Service-Principal gebruikt met een verlopen client geheim, moet een eigenaar van het abonnement of account beheerder referenties opnieuw instellen of een nieuwe Service-Principal genereren.
* Als u een [token van een opslag plaats bereik](container-registry-repository-scoped-permissions.md)gebruikt, moet de eigenaar van het REGI ster mogelijk een wacht woord opnieuw instellen of een nieuw token genereren.

Gerelateerde links:

* [De Service-Principal-referenties opnieuw instellen](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Token wachtwoorden opnieuw genereren](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Afzonderlijke aanmelding met Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Geavanceerde probleemoplossing

Als het [verzamelen van bron logboeken](container-registry-diagnostics-audit-logs.md) is ingeschakeld in het REGI ster, raadpleegt u het ContainterRegistryLoginEvents-logboek. In dit logboek worden verificatie gebeurtenissen en status opgeslagen, met inbegrip van de binnenkomende identiteit en het IP-adres. Zoek het logboek op voor [register verificatie fouten](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Gerelateerde links:

* [Logboeken voor diagnostische evaluaties en controles](container-registry-diagnostics-audit-logs.md)
* [Veelgestelde vragen over container Registry](container-registry-faq.md)
* [Aanbevolen procedures voor Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Volgende stappen

Als u uw probleem hier niet kunt oplossen, raadpleegt u de volgende opties.

* Andere onderwerpen over het oplossen van problemen met het REGI ster zijn:
  * [Problemen met het netwerk oplossen met het REGI ster](container-registry-troubleshoot-access.md)
  * [Problemen met het REGI ster oplossen](container-registry-troubleshoot-performance.md)
* Opties voor [Community-ondersteuning](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A](/answers/products/)
* [Open een ondersteunings ticket](https://azure.microsoft.com/support/create-ticket/) op basis van de gegevens die u verstrekt, een snelle diagnose kan worden uitgevoerd voor verificatie fouten in uw REGI ster