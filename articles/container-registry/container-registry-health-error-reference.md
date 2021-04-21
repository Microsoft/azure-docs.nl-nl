---
title: Foutverwijzing voor register statuscontroles
description: Foutcodes en mogelijke oplossingen voor problemen die zijn gevonden door de opdracht az acr check-health diagnostic uit te voeren in Azure Container Registry
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773437"
---
# <a name="health-check-error-reference"></a>Naslag voor statuscontrolefouten

Hieronder ziet u meer informatie over foutcodes die worden geretourneerd [door de opdracht az acr check-health.][az-acr-check-health] Voor elke fout worden mogelijke oplossingen vermeld.

Zie De status van een `az acr check-healh` [Azure-containerregister controleren voor](container-registry-check-health.md)meer informatie over het uitvoeren van .

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Deze fout betekent dat de Docker-client voor CLI niet kan worden gevonden. Als gevolg hiervan worden de volgende extra controles niet uitgevoerd: dockerversie zoeken, de status van de Docker-daemon evalueren en een Docker-pull-opdracht uitvoeren.

*Mogelijke oplossingen:* Docker-client installeren; Docker-pad toevoegen aan de systeemvariabelen.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Deze fout betekent dat de status van de Docker-daemon niet beschikbaar is of dat deze niet kan worden bereikt met behulp van de CLI. Als gevolg hiervan zijn Docker-bewerkingen (zoals `docker login` en ) niet beschikbaar via de `docker pull` CLI.

*Mogelijke oplossingen:* start de Docker-daemon opnieuw op of controleer of deze juist is geïnstalleerd.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Deze fout betekent dat CLI de opdracht niet kan `docker --version` uitvoeren.

*Mogelijke oplossingen:* voer de opdracht handmatig uit, zorg ervoor dat u de nieuwste CLI-versie hebt en onderzoek het foutbericht.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Deze fout betekent dat de CLI geen voorbeeldafbeelding naar uw omgeving heeft kunnen halen.

*Mogelijke oplossingen:* controleer of alle onderdelen die nodig zijn om een afbeelding op te halen, correct worden uitgevoerd.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Deze fout betekent dat de Helm-client niet kan worden gevonden door de CLI, waardoor andere Helm-bewerkingen worden uitgesloten.

*Mogelijke oplossingen:* controleer of de Helm-client is geïnstalleerd en of het pad ervan is toegevoegd aan de systeemomgevingsvariabelen.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Deze fout betekent dat de CLI de geïnstalleerde Helm-versie niet kan bepalen. Dit kan gebeuren als de Azure CLI-versie (of als de Helm-versie) die wordt gebruikt, verouderd is.

*Mogelijke oplossingen:* werk bij naar de nieuwste versie van Azure CLI of naar de aanbevolen Helm-versie; Voer de opdracht handmatig uit en onderzoek het foutbericht.

## <a name="cmk_error"></a>CMK_ERROR

Deze fout betekent dat het register geen toegang heeft tot de door de gebruiker toegewezen of door sysem toegewezen beheerde identiteit die wordt gebruikt om registerversleuteling te configureren met een door de klant beheerde sleutel. De beheerde identiteit is mogelijk verwijderd.  

*Mogelijke oplossing:* als u het probleem wilt oplossen en de sleutel wilt roteren met behulp van een andere beheerde identiteit, bekijkt u de stappen voor het oplossen van problemen met de door de gebruiker [toegewezen identiteit.](container-registry-customer-managed-keys.md#troubleshoot)

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Deze fout betekent dat de DNS voor de opgegeven registeraanmeldingsserver is gepingd, maar niet heeft gereageerd, wat betekent dat deze niet beschikbaar is. Dit kan duiden op een aantal verbindingsproblemen. Het is ook mogelijk dat het register niet bestaat, de gebruiker mogelijk niet over de machtigingen voor het register (om de aanmeldingsserver op de juiste manier op te halen) of het doelregister zich in een andere cloud dan het register dat wordt gebruikt in de Azure CLI.

*Mogelijke oplossingen:* Connectiviteit valideren; controleer de spelling van het register en of het register bestaat; controleer of de gebruiker over de juiste machtigingen beschikt en of de cloud van het register hetzelfde is als die in de Azure CLI wordt gebruikt.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Deze fout betekent dat het eindpunt van de uitdaging voor het opgegeven register heeft gereageerd met de HTTP-status 403 Verboden. Deze fout betekent dat gebruikers geen toegang hebben tot het register, waarschijnlijk vanwege een configuratie van een virtueel netwerk of omdat toegang tot het openbare eindpunt van het register niet is toegestaan. Voer uit om de momenteel geconfigureerde firewallregels te `az acr show --query networkRuleSet --name <registry>` zien.

*Mogelijke oplossingen:* verwijder regels voor virtuele netwerken of voeg het huidige IP-adres van de client toe aan de lijst met toegestane adressen.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Deze fout betekent dat het eindpunt van de uitdaging van het doelregister geen uitdaging heeft veroorzaakt.

*Mogelijke oplossingen:* probeer het na enige tijd opnieuw. Als de fout zich blijft voordoen, opent u een probleem op https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Deze fout betekent dat het eindpunt van de uitdaging van het doelregister een uitdaging heeft uitgegeven, maar dat het register geen ondersteuning biedt Azure Active Directory verificatie.

*Mogelijke oplossingen:* probeer een andere manier om te verifiëren, bijvoorbeeld met beheerdersreferenties. Als gebruikers moeten verifiëren met behulp van Azure Active Directory, opent u een probleem op https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Deze fout betekent dat de aanmeldingsserver van het register niet heeft gereageerd met een vernieuwings token, zodat toegang tot het doelregister is geweigerd. Deze fout kan optreden als de gebruiker niet de juiste machtigingen voor het register heeft of als de gebruikersreferenties voor de Azure CLI zijn verouderd.

*Mogelijke oplossingen:* controleer of de gebruiker de juiste machtigingen heeft voor het register; uitvoeren `az login` om machtigingen, tokens en referenties te vernieuwen.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Deze fout betekent dat de aanmeldingsserver van het register niet heeft gereageerd met een toegangs token, zodat de toegang tot het doelregister is geweigerd. Deze fout kan optreden als de gebruiker niet de juiste machtigingen voor het register heeft of als de gebruikersreferenties voor de Azure CLI zijn verouderd.

*Mogelijke oplossingen:* controleer of de gebruiker de juiste machtigingen heeft voor het register; uitvoeren `az login` om machtigingen, tokens en referenties te vernieuwen.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Deze fout betekent dat de client geen beveiligde verbinding met het containerregister kan maken. Deze fout treedt doorgaans op als u een proxyserver gebruikt of gebruikt.

*Mogelijke oplossingen:* meer informatie over het werken achter een proxy vindt [u hier.](/cli/azure/use-cli-effectively)

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Deze fout betekent dat de CLI de aanmeldingsserver van het opgegeven register niet kan vinden en dat er geen standaardachtervoegsel is gevonden voor de huidige cloud. Deze fout kan optreden als het register niet bestaat, als de gebruiker niet de juiste machtigingen heeft voor het register, als de cloud van het register en de huidige Azure CLI-cloud niet overeenkomen of als de Azure CLI-versie verouderd is.

*Mogelijke oplossingen:* controleer of de spelling juist is en of het register bestaat; controleer of de gebruiker de juiste machtigingen heeft voor het register en of de clouds van het register en de CLI-omgeving overeenkomen; Azure CLI bijwerken naar de nieuwste versie.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Deze fout betekent dat de CLI niet compatibel is met de momenteel geïnstalleerde versie van Docker/Notary. Downgrade uw notary.exe versie naar een eerdere versie dan 0.6.0 door de Notary-client van uw Docker-installatie handmatig te vervangen om dit probleem op te lossen.

## <a name="next-steps"></a>Volgende stappen

Zie De status van een [Azure-containerregister controleren](container-registry-check-health.md)voor opties om de status van een register te controleren.

Zie de [Veelgestelde](container-registry-faq.md) vragen over veelgestelde vragen en andere bekende problemen over Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
