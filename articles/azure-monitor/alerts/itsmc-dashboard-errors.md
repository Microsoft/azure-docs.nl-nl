---
title: Connector status fouten in het ITSMC-dash board
description: Meer informatie over veelvoorkomende fouten in het dash board van IT Service Management-connector.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 5cc3c4a07cc698f3592a2ff2fd76e9f4bbef441b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036449"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Connector status fouten in het ITSMC-dash board

Het ITSMC-dash board (IT Service Management-connector) bevat fouten die u kunnen helpen bij het oplossen van problemen in uw connector.

In de volgende secties worden veelvoorkomende fouten beschreven die worden weer gegeven in de sectie connector status van het dash board en hoe u deze kunt oplossen.

## <a name="unexpected-response"></a>Onverwachte reactie

**Fout: er** is een onverwachte reactie van ServiceNow samen met de status code voor geslaagd. Antwoord: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultaat": [{"transform_map": "OMS-incident", "tabel": "incident", "status": "Error", "error_message": "{target record is niet gevonden | Ongeldige tabel | Ongeldige faserings tabel}

**Oorzaak**: ServiceNow retourneert deze fout wanneer:

* Een aangepast script dat in een ServiceNow-exemplaar wordt geïmplementeerd, zorgt ervoor dat incidenten worden genegeerd.
* De code van de OMS integrator-app is gewijzigd aan de kant van de ServiceNow (bijvoorbeeld door middel van het `onBefore` script).

**Oplossing**: Schakel alle aangepaste scripts of code wijzigingen uit.

## <a name="exception-update-failure"></a>Fout bij het bijwerken van uitzonde ring

**Fout**: "{" Error ": {" bericht ":" de bewerking is mislukt "," detail ":" de uitzonde ring van de toegangs beheer lijst is mislukt vanwege beveiligings beperkingen "}"

**Oorzaak**: ServiceNow-machtigingen zijn onjuist geconfigureerd.

**Oplossing**: Controleer of alle functies op de juiste wijze zijn toegewezen als [opgegeven](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

## <a name="problem-sending-a-request"></a>Probleem bij het verzenden van een aanvraag

**Fout**: er is een fout opgetreden tijdens het verzenden van de aanvraag.

**Oorzaak**: een ServiceNow-exemplaar is niet beschikbaar.

**Oplossing**: Controleer uw exemplaar in ServiceNow. Deze is mogelijk verwijderd of niet beschikbaar.

## <a name="servicenow-rate-problem"></a>ServiceNow-rate probleem

**Fout**: "ServiceDeskHttpBadRequestException: status code = 429"

**Oorzaak**: de limieten voor de ServiceNow-frequentie zijn te hoog of te laag.

**Oplossing**: Verhoog of annuleer de frequentie limieten in het ServiceNow-exemplaar, zoals wordt uitgelegd in de [ServiceNow-documentatie](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## <a name="invalid-refresh-token"></a>Ongeldig vernieuwings token

**Fout**: 
  * AccessToken en RefreshToken zijn ongeldig. De gebruiker moet zich opnieuw verifiëren. "
  * "Kan de configuratie van de sjablonen niet synchroniseren voor gebeurtenis, waarschuwing, incident. Zie het uitzonderings bericht voor meer informatie.

**Oorzaak**: een vernieuwings token is verlopen.

**Oplossing**: Synchroniseer ITSMC om een nieuw vernieuwings token te genereren, zoals wordt beschreven in [hand matig synchronisatie problemen oplossen](./itsmc-resync-servicenow.md).

## <a name="missing-connector"></a>Connector ontbreekt

**Fout**: kan werk item niet maken/bijwerken voor waarschuwing {alertnaam}. ITSM-connector {connectionIdentifier} bestaat niet of is verwijderd. "

**Oorzaak**: ITSMC is verwijderd.

**Oplossing**: ITSMC is verwijderd, maar de gedefinieerde ITSM-actie groepen (IT Service Management) zijn nog steeds gekoppeld. Er zijn drie opties om dit probleem op te lossen:

* Dergelijke actie groepen zoeken en uitschakelen of verwijderen.
* [Configureer de actie groepen opnieuw](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) om een bestaand ITSMC-exemplaar te gebruiken.
* [Maak een nieuw ITSMC-exemplaar](./itsmc-definition.md#create-an-itsm-connection) en [Configureer de actie groepen opnieuw om het te gebruiken](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Geen verbindings Details

**Fout**: er is iets verkeerd gegaan. Kan geen verbindings gegevens ophalen. " Deze fout wordt weer gegeven wanneer u een ITSM-actie groep definieert.

**Oorzaak**: een dergelijke fout wordt weer gegeven in een van de volgende situaties:

* Een nieuw gemaakt ITSM-connector-exemplaar heeft nog de eerste synchronisatie voltooid.
* De connector is niet op de juiste wijze gedefinieerd.

**Oplossing**: 

* Wanneer een nieuw ITSMC-exemplaar wordt gemaakt, wordt de synchronisatie van gegevens van het ITSM-systeem gestart, zoals werk [SYNCHRONISEER ITSMC om een nieuw vernieuwings token te genereren](./itsmc-resync-servicenow.md).
* [Controleer uw verbindings gegevens in ITSMC](./itsmc-connections-servicenow.md#create-a-connection) en controleer of de ITSMC kan worden [gesynchroniseerd](./itsmc-resync-servicenow.md).
