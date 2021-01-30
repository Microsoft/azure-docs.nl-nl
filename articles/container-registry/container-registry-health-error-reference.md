---
title: Fout referentie voor register status controles
description: Fout codes en mogelijke oplossingen voor problemen die worden gevonden door de opdracht AZ ACR check-Health Diagnostic uit te voeren in Azure Container Registry
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: 05ae5a7ac19bb7748d5313ccb4974b639ab52d9c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061865"
---
# <a name="health-check-error-reference"></a>Naslag informatie voor status controle fout

Hieronder vindt u informatie over de fout codes die worden geretourneerd door de opdracht [AZ ACR check-Health][az-acr-check-health] . Voor elke fout worden mogelijke oplossingen weer gegeven.

`az acr check-healh`Zie [de status van een Azure container Registry controleren](container-registry-check-health.md)voor meer informatie over het uitvoeren van.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Deze fout houdt in dat docker-client voor CLI niet kan worden gevonden. Als gevolg hiervan worden de volgende aanvullende controles niet uitgevoerd: een docker-versie vinden, de status van docker daemon evalueren en een docker-pull-opdracht uitvoeren.

*Mogelijke oplossingen*: docker-client installeren; Docker-pad toevoegen aan de systeem variabelen.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Deze fout betekent dat de status van de docker-daemon niet beschikbaar is of niet kan worden bereikt met de CLI. Als gevolg hiervan zijn docker-bewerkingen (zoals `docker login` en `docker pull` ) niet beschikbaar via de cli.

*Mogelijke oplossingen*: start docker daemon opnieuw of Controleer of deze juist is geïnstalleerd.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Deze fout betekent dat CLI de opdracht niet kan uitvoeren `docker --version` .

*Mogelijke oplossingen*: Probeer de opdracht hand matig uit te voeren, zorg ervoor dat u over de meest recente CLI-versie beschikt en onderzoek het fout bericht.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Deze fout betekent dat de CLI geen voorbeeld installatie kopie in uw omgeving kan ophalen.

*Mogelijke oplossingen*: Controleer of alle onderdelen die nodig zijn voor het ophalen van een installatie kopie correct worden uitgevoerd.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Deze fout betekent dat de helm-client niet kan worden gevonden door de CLI, waardoor andere helm-bewerkingen worden uitgesloten.

*Mogelijke oplossingen*: Controleer of de helm-client is geïnstalleerd en of het pad wordt toegevoegd aan de omgevings variabelen van het systeem.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Deze fout betekent dat de CLI niet kan bepalen welke helm-versie is geïnstalleerd. Dit kan gebeuren als de Azure CLI-versie (of de versie van de helm) die wordt gebruikt, verouderd is.

*Mogelijke oplossingen*: werk bij naar de nieuwste versie van Azure CLI of naar de aanbevolen helm-versie. Voer de opdracht hand matig uit en onderzoek het fout bericht.

## <a name="cmk_error"></a>CMK_ERROR

Deze fout houdt in dat het REGI ster geen toegang kan krijgen tot de door de gebruiker toegewezen of sysem toegewezen beheerde identiteit die wordt gebruikt voor het configureren van register versleuteling met een door de klant beheerde sleutel. De beheerde identiteit is mogelijk verwijderd.  

*Mogelijke oplossing*: Zie stappen voor het oplossen van de door [de gebruiker toegewezen identiteit](container-registry-customer-managed-keys.md#troubleshoot)om het probleem op te lossen en de sleutel te roteren met een andere beheerde identiteit.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Deze fout betekent dat de DNS voor de opgegeven register aanmeldings server is gepingd, maar niet reageert, wat betekent dat deze niet beschikbaar is. Dit kan duiden op problemen met de verbinding. Het is ook mogelijk dat het REGI ster niet bestaat. de gebruiker heeft mogelijk niet de juiste machtigingen voor het REGI ster (om de aanmeldings server correct op te halen) of het doel register bevindt zich in een andere Cloud dan het REGI ster dat wordt gebruikt in de Azure CLI.

*Mogelijke oplossingen*: connectiviteit valideren; Controleer de spelling van het REGI ster en het REGI ster bestaat. Controleer of de gebruiker de juiste machtigingen heeft en of de cloud van het REGI ster hetzelfde is als in de Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Deze fout houdt in dat het controle-eind punt voor het opgegeven REGI ster heeft gereageerd met een 403 verboden HTTP-status. Deze fout betekent dat gebruikers geen toegang hebben tot het REGI ster, waarschijnlijk vanwege een configuratie van een virtueel netwerk of omdat toegang tot het open bare eind punt van het REGI ster niet is toegestaan. Als u de momenteel geconfigureerde firewall regels wilt zien, voert u uit `az acr show --query networkRuleSet --name <registry>` .

*Mogelijke oplossingen*: Verwijder de regels voor het virtuele netwerk of Voeg het huidige client-IP-adres toe aan de lijst met toegestane toepassingen.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Deze fout houdt in dat het controle-eind punt van het doel register geen uitdaging heeft uitgegeven.

*Mogelijke oplossingen*: Probeer het na enige tijd opnieuw. Als de fout zich blijft voordoen, opent u een probleem op https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Deze fout houdt in dat het controle-eind punt van het doel register een Challenge heeft uitgegeven, maar het REGI ster biedt geen ondersteuning voor Azure Active Directory-verificatie.

*Mogelijke oplossingen*: probeer op een andere manier te verifiëren, bijvoorbeeld met beheerders referenties. Als gebruikers moeten worden geverifieerd met behulp van Azure Active Directory, open dan een probleem op https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Deze fout betekent dat de aanmeldings server van het REGI ster niet met een vernieuwings token heeft gereageerd, zodat de toegang tot het doel register is geweigerd. Deze fout kan optreden als de gebruiker niet over de juiste machtigingen beschikt voor het REGI ster of als de gebruikers referenties voor de Azure CLI verouderd zijn.

*Mogelijke oplossingen*: Controleer of de gebruiker de juiste machtigingen heeft voor het REGI ster. uitvoeren `az login` om machtigingen, tokens en referenties te vernieuwen.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Deze fout betekent dat de aanmeldings server van het REGI ster niet met een toegangs token heeft gereageerd, zodat de toegang tot het doel register is geweigerd. Deze fout kan optreden als de gebruiker niet over de juiste machtigingen beschikt voor het REGI ster of als de gebruikers referenties voor de Azure CLI verouderd zijn.

*Mogelijke oplossingen*: Controleer of de gebruiker de juiste machtigingen heeft voor het REGI ster. uitvoeren `az login` om machtigingen, tokens en referenties te vernieuwen.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Deze fout betekent dat de client geen beveiligde verbinding kan maken met het container register. Deze fout treedt doorgaans op als u een proxy server uitvoert of gebruikt.

*Mogelijke oplossingen*: [hier](/cli/azure/use-cli-effectively)vindt u meer informatie over het werken achter een proxy.

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Deze fout betekent dat de CLI de aanmeldings server van het opgegeven REGI ster niet kan vinden en dat er geen standaard achtervoegsel is gevonden voor de huidige Cloud. Deze fout kan optreden als het REGI ster niet bestaat, als de gebruiker niet over de juiste machtigingen beschikt voor het REGI ster, als de cloud van het REGI ster en de huidige Azure CLI-Cloud niet overeenkomen of als de Azure CLI-versie verouderd is.

*Mogelijke oplossingen*: Controleer of de spelling juist is en of het REGI ster bestaat. Controleer of de gebruiker over de juiste machtigingen beschikt voor het REGI ster en of de Clouds van het REGI ster en de CLI-omgeving overeenkomen. werk Azure CLI bij naar de nieuwste versie.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Deze fout betekent dat de CLI niet compatibel is met de momenteel geïnstalleerde versie van docker/notaris. Probeer uw notary.exe-versie naar een eerdere versie dan 0.6.0 te downgrade door de notaris-client van de docker-installatie hand matig te vervangen om dit probleem op te lossen.

## <a name="next-steps"></a>Volgende stappen

Zie [de status van een Azure container Registry controleren](container-registry-check-health.md)voor opties voor het controleren van de status van een REGI ster.

Raadpleeg de [Veelgestelde](container-registry-faq.md) vragen over Azure container Registry en andere bekende problemen met betrekking tot de beveiliging.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
