---
title: Algemene fouten
description: Dit document bevat informatie over algemene fouten die zich in het dash board bevinden
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 7240c1b0f19dc49ab4130c5ee2516dcfefb2e2c2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602194"
---
# <a name="errors-in-the-connector-status"></a>Fouten in de connector status

In de lijst connector status vindt u fouten die u kunnen helpen bij het oplossen van problemen in uw ITSM-connector.

## <a name="status-common-errors"></a>Status veelvoorkomende fouten

in deze sectie vindt u de algemene fouten die worden weer gegeven in de sectie connector status en hoe u deze kunt oplossen:

* **Fout: er** is een onverwachte reactie van ServiceNow samen met de status code voor geslaagd. Antwoord: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultaat": [{"transform_map": "OMS-incident", "tabel": "incident", "status": "Error", "error_message": "{target record is niet gevonden | Ongeldige tabel | Ongeldige faserings tabel}

    **Oorzaak**: een dergelijke fout wordt geretourneerd vanuit ServiceNow wanneer:
  * Een aangepast script dat in ServiceNow-exemplaar is geïmplementeerd, zorgt ervoor dat incidenten worden genegeerd.
  * De code van de OMS integrator-app is gewijzigd op de ServiceNow-kant, bijvoorbeeld het script onBefore.

    **Oplossing**: alle aangepaste scripts of code wijzigingen van het pad voor het importeren van gegevens uitschakelen.

* **Fout**: "{" Error ": {" bericht ":" de bewerking is mislukt "," detail ":" de uitzonde ring van de toegangs beheer lijst is mislukt vanwege beveiligings beperkingen "}"

    **Oorzaak**: onjuiste configuratie van ServiceNow-machtigingen

    **Oplossing**: Controleer of alle functies juist zijn toegewezen zoals [opgegeven](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

* **Fout**: er is een fout opgetreden tijdens het verzenden van de aanvraag.

    **Oorzaak**: "ServiceNow-instantie niet beschikbaar"

    **Oplossing**: Controleer uw exemplaar in ServiceNow het mogelijk is verwijderd of niet beschikbaar is.

* **Fout**: "ServiceDeskHttpBadRequestException: status code = 429"

    **Oorzaak**: de limieten voor de ServiceNow-frequentie zijn te hoog/laag.

    **Oplossing**: Verhoog of annuleer de frequentie limieten in het ServiceNow-exemplaar, zoals [hier](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)wordt uitgelegd.

* **Fout**: AccessToken en RefreshToken zijn ongeldig. De gebruiker moet zich opnieuw verifiëren. "

    **Oorzaak**: het vernieuwings token is verlopen.

    **Oplossing**: synchroniseer de ITSM-connector om een nieuw vernieuwings token te genereren, zoals [hier](./itsmc-resync-servicenow.md)wordt uitgelegd.

* **Fout**: kan werk item niet maken/bijwerken voor waarschuwing {alertnaam}. ITSM-connector {connectionIdentifier} bestaat niet of is verwijderd. "

    **Oorzaak**: ITSM-connector is verwijderd.

    **Oplossing**: de ITSM-connector is verwijderd, maar er zijn nog steeds ITSM-actie groepen gekoppeld. Er zijn twee opties om dit probleem op te lossen:
  * Deze actie zoeken en uitschakelen of verwijderen
  * [Configureer de actie groep opnieuw voor het](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) gebruik van een bestaande ITSM-connector.
  * [Maak een nieuwe ITSM-connector](./itsmc-definition.md#create-an-itsm-connection) en [Configureer de actie groep opnieuw om deze te gebruiken](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Veelvoorkomende fouten in gebruikers interface

* **Fout**: er is iets verkeerd gegaan. Kan geen verbindings gegevens ophalen. " Deze fout wordt weer gegeven wanneer de klant ITSM-actie groep definieert.

    **Oorzaak**: nieuw gemaakte ITSM-connector heeft nog de eerste synchronisatie voltooid.

    **Oplossing**: wanneer er een nieuwe ITSM-connector wordt gemaakt, wordt ITSM-connector de synchronisatie van gegevens vanuit ITSM-systeem gestart, zoals werk item sjablonen en werk items. Synchroniseer de ITSM-connector om een nieuw vernieuwings token te genereren, zoals [hier](./itsmc-resync-servicenow.md)wordt uitgelegd.
