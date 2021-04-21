---
title: Problemen met aanmelden bij register oplossen
description: Symptomen, oorzaken en oplossing van veelvoorkomende problemen bij het aanmelden bij een Azure-containerregister
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 47186cc8256836e5367ecee520787b67662eb42f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780727"
---
# <a name="troubleshoot-registry-login"></a>Problemen met aanmelden bij register oplossen

Dit artikel helpt u bij het oplossen van problemen die kunnen ontstaan bij het aanmelden bij een Azure-containerregister. 

## <a name="symptoms"></a>Symptomen

Kan een of meer van de volgende omvatten:

* Kan niet aanmelden bij het register met `docker login` behulp van , of `az acr login` beide
* Kan niet aanmelden bij het register en u krijgt een `unauthorized: authentication required` foutmelding of `unauthorized: Application not registered with AAD`
* Kan niet aanmelden bij het register en u ontvangt een Azure CLI-fout `Could not connect to the registry login server`
* Kan geen push- of pull-afbeeldingen maken en u ontvangt een Docker-fout `unauthorized: authentication required`
* Kan geen toegang krijgen tot het register Azure Kubernetes Service, Azure DevOps of een andere Azure-service
* Kan geen toegang krijgen tot register en u ontvangt een `Error response from daemon: login attempt failed with status: 403 Forbidden` foutmelding: zie [Problemen met het netwerk met het register oplossen](container-registry-troubleshoot-access.md)
* Kan geen toegang krijgen tot registerinstellingen of deze weergeven in Azure Portal register beheren met behulp van de Azure CLI

## <a name="causes"></a>Oorzaken

* Docker is niet goed geconfigureerd in uw omgeving - [oplossing](#check-docker-configuration)
* Het register bestaat niet of de naam is onjuist - [oplossing](#specify-correct-registry-name)
* De registerreferenties zijn niet geldig - [oplossing](#confirm-credentials-to-access-registry)
* De referenties zijn niet gemachtigd voor push-, pull- of Azure Resource Manager - [oplossing](#confirm-credentials-are-authorized-to-access-registry)
* De referenties zijn verlopen - [oplossing](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Verdere diagnose 

Voer de [opdracht az acr check-health](/cli/azure/acr#az_acr_check_health) uit voor meer informatie over de status van de registeromgeving en optioneel toegang tot een doelregister. U kunt bijvoorbeeld docker-configuratiefouten vaststellen of Azure Active Directory problemen met aanmelding oplossen. 

Zie [De status van een Azure-containerregister controleren voor](container-registry-check-health.md) opdrachtvoorbeelden. Als er fouten worden gerapporteerd, bekijkt u [de foutverwijzing](container-registry-health-error-reference.md) en de volgende secties voor aanbevolen oplossingen.

Als u problemen ondervindt met het gebruik van het register wih Azure Kubernetes Service, voer dan de opdracht [az aks check-acr](/cli/azure/aks#az_aks_check_acr) uit om te controleren of het register toegankelijk is vanuit het AKS-cluster.

> [!NOTE]
> Er kunnen ook verificatie- of autorisatiefouten optreden als er firewall- of netwerkconfiguraties zijn die registertoegang verhinderen. Zie [Netwerkproblemen met register oplossen.](container-registry-troubleshoot-access.md)

## <a name="potential-solutions"></a>Mogelijke oplossingen

### <a name="check-docker-configuration"></a>Docker-configuratie controleren

Voor Azure Container Registry verificatiestromen is een lokale Docker-installatie vereist, zodat u zich bij uw register kunt verifiëren voor bewerkingen zoals het pushen en binnenhalen van installatiegegevens. Controleer of de Docker CLI-client en -daemon (Docker Engine) worden uitgevoerd in uw omgeving. U hebt Docker-clientversie 18.03 of hoger nodig.

Gerelateerde links:

* [Verificatieoverzicht](container-registry-authentication.md#authentication-options)
* [Veelgestelde vragen over containerregisters](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>De juiste registernaam opgeven

Wanneer u `docker login` gebruikt, geeft u de volledige aanmeldingsservernaam van het register op, *zoals myregistry.azurecr.io*. Zorg ervoor dat u alleen kleine letters gebruikt. Voorbeeld:

```console
docker login myregistry.azurecr.io
```

Wanneer u [az acr login gebruikt](/cli/azure/acr#az_acr_login) met een Azure Active Directory-identiteit, moet u zich eerst aanmelden bij de Azure [CLI](/cli/azure/authenticate-azure-cli)en vervolgens de Azure-resourcenaam van het register opgeven. De resourcenaam is de naam die is opgegeven tijdens het maken van het register, zoals *myregistry* (zonder domeinachtervoegsel). Voorbeeld:

```azurecli
az acr login --name myregistry
```

Gerelateerde links:

* [az acr login succeeds but docker fails with error: unauthorized: authentication required](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>Referenties voor toegang tot register bevestigen

Controleer de geldigheid van de referenties die u voor uw scenario gebruikt of die u hebt van een registereigenaar. Enkele mogelijke problemen:

* Als u een Active Directory-service-principal gebruikt, moet u ervoor zorgen dat u de juiste referenties gebruikt in de Active Directory-tenant:
  * Gebruikersnaam: toepassings-id van service-principal (ook wel *client-id genoemd)*
  * Wachtwoord : service-principal-wachtwoord (ook wel *clientgeheim genoemd)*
* Als u een Azure-service zoals Azure Kubernetes Service of Azure DevOps gebruikt om toegang te krijgen tot het register, bevestigt u de registerconfiguratie voor uw service. 
* Als u de optie hebt gebruikt, waarmee registerregistratie mogelijk is zonder de `az acr login` `--expose-token` Docker-daemon te gebruiken, moet u zich verifiëren met de gebruikersnaam `00000000-0000-0000-0000-000000000000` .
* Als uw register is geconfigureerd voor anonieme [pull-toegang,](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)kunnen bestaande Docker-referenties die zijn opgeslagen in een eerdere Docker-aanmelding anonieme toegang verhinderen. Voer `docker logout` uit voordat u een anonieme pull-bewerking op het register probeert uit te voeren.

Gerelateerde links:

* [Verificatieoverzicht](container-registry-authentication.md#authentication-options)
* [Afzonderlijke aanmelding met Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Aanmelden met service-principal](container-registry-auth-service-principal.md)
* [Aanmelden met beheerde identiteit](container-registry-authentication-managed-identity.md)
* [Aanmelden met token binnen het bereik van de opslagplaats](container-registry-repository-scoped-permissions.md)
* [Aanmelden met beheerdersaccount](container-registry-authentication.md#admin-account)
* [Foutcodes voor Azure AD-verificatie en -autorisatie](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az_acr_login) reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Bevestigen dat referenties zijn gemachtigd voor toegang tot het register

Bevestig de registermachtigingen die aan de referenties zijn gekoppeld, zoals de Azure-rol voor het pullen van afbeeldingen uit het register of de rol voor het `AcrPull` `AcrPush` pushen van afbeeldingen. 

Voor toegang tot een register in de portal of registerbeheer met behulp van de Azure CLI zijn ten minste de rol of gelijkwaardige machtigingen vereist om de `Reader` Azure Resource Manager uitvoeren.

Als uw machtigingen onlangs zijn gewijzigd om registertoegang via de portal toe te staan, moet u mogelijk een incognito- of privésessie in uw browser proberen om verouderde browsercache of cookies te voorkomen.

U of een registereigenaar moet voldoende bevoegdheden hebben in het abonnement om roltoewijzingen toe te voegen of te verwijderen.

Gerelateerde links:

* [Azure-rollen en -machtigingen - Azure Container Registry](container-registry-roles.md)
* [Aanmelden met token binnen het bereik van de opslagplaats](container-registry-repository-scoped-permissions.md)
* [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](../role-based-access-control/role-assignments-portal.md)
* [Gebruik de portal voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md)
* [Een nieuw toepassingsgeheim maken](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD-verificatie en autorisatiecodes](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Controleer of de referenties niet zijn verlopen

Tokens en Active Directory-referenties kunnen verlopen na gedefinieerde perioden, waardoor registertoegang wordt voorkomen. Als u toegang wilt inschakelen, moeten referenties mogelijk opnieuw worden ingesteld of opnieuw worden ge regenereerd.

* Als u een afzonderlijke AD-identiteit, een beheerde identiteit of service-principal gebruikt voor registratie in het register, verloopt het AD-token na 3 uur. Meld u opnieuw aan bij het register.  
* Als u een AD-service-principal met een verlopen clientgeheim gebruikt, moet een abonnementseigenaar of accountbeheerder referenties opnieuw instellen of een nieuwe service-principal genereren.
* Als u een [token binnen het bereik van](container-registry-repository-scoped-permissions.md)een opslagplaats gebruikt, moet een registereigenaar mogelijk een wachtwoord opnieuw instellen of een nieuw token genereren.

Gerelateerde links:

* [Referenties voor service-principal opnieuw instellen](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [Tokenwachtwoorden opnieuw genereren](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Afzonderlijke aanmelding met Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Geavanceerde probleemoplossing

Als [het verzamelen van resourcelogboeken](container-registry-diagnostics-audit-logs.md) is ingeschakeld in het register, controleert u het logboek ContainterRegistryLoginEvents. Dit logboek slaat verificatiegebeurtenissen en -status op, met inbegrip van de binnenkomende identiteit en het IP-adres. Zoek in het logboek naar [mislukte registerverificaties.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Gerelateerde links:

* [Logboeken voor diagnostische evaluatie en controle](container-registry-diagnostics-audit-logs.md)
* [Veelgestelde vragen over containerregisters](container-registry-faq.md)
* [Aanbevolen procedures voor Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende opties als u het probleem hier niet kunt oplossen.

* Andere onderwerpen over het oplossen van problemen met het register zijn:
  * [Netwerkproblemen met het register oplossen](container-registry-troubleshoot-access.md)
  * [Problemen met registerprestaties oplossen](container-registry-troubleshoot-performance.md)
* [Ondersteuningsopties van de](https://azure.microsoft.com/support/community/) community
* [Microsoft Q&A](/answers/products/)
* [Open een ondersteuningsticket.](https://azure.microsoft.com/support/create-ticket/) Op basis van de informatie die u op geeft, kan er een snelle diagnose worden uitgevoerd voor verificatiefouten in uw register