---
title: Problemen oplossen in ITSMC
description: Meer informatie over het oplossen van veelvoorkomende problemen in IT Service Management-connector.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e8ae306a4900bc6e5815f6fc251dfa1b8b22964d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492413"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Problemen in IT Service Management-connector oplossen

In dit artikel worden veelvoorkomende problemen in IT Service Management-connector beschreven (ITSMC) en hoe u deze problemen kunt oplossen.

Azure Monitor u proactief op de hoogte wordt gesteld wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Deze waarschuwingen helpen u bij het identificeren en oplossen van problemen voordat de gebruikers van uw systeem ze merken.

U kunt selecteren hoe u waarschuwingen wilt ontvangen. U kunt e-mail, SMS of webhook kiezen of zelfs een oplossing automatiseren. 

U kunt ook een melding ontvangen via ITSMC. ITSMC biedt u de mogelijkheid om waarschuwingen te verzenden naar een extern ticket systeem zoals ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Het dash board gebruiken voor het analyseren van incident-en wijzigings aanvraag gegevens

Afhankelijk van uw configuratie wanneer u een verbinding instelt, kan ITSMC tot 120 dagen aan incidenten en wijzigings aanvraag gegevens worden gesynchroniseerd. Als u het logboek record schema voor deze gegevens wilt ophalen, raadpleegt u de gegevens die zijn [gesynchroniseerd met uw ITSM-product](./itsmc-synced-data.md) artikel.

U kunt het incident visualiseren en gegevens wijzigen met behulp van het ITSMC-dash board:

![Scherm opname van het ITSMC-dash board.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Het dash board bevat ook informatie over de status van de connector. U kunt deze informatie gebruiken als uitgangs punt voor het analyseren van problemen met de verbindingen. Zie voor meer informatie [fout onderzoek met behulp van het dash board](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Servicetoewijzing gebruiken om incidenten te visualiseren

U kunt ook visualiseren van de incidenten die zijn gesynchroniseerd met de betrokken computers in Servicetoewijzing.

Servicetoewijzing detecteert automatisch de toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. Zo kunt u uw servers bekijken zoals u dat wilt: als onderling verbonden systemen die essentiële services leveren. 

Servicetoewijzing toont verbindingen tussen servers, processen en poorten via elke met TCP verbonden architectuur. Met uitzonde ring van de installatie van een agent is geen configuratie vereist. Zie [servicetoewijzing gebruiken](../insights/service-map.md)voor meer informatie.

Als u Servicetoewijzing gebruikt, kunt u de Service Desk-items weer geven die zijn gemaakt in ITSM-oplossingen (IT Service Management), zoals wordt weer gegeven in dit voor beeld:

![Scherm opname van de Log Analytics scherm.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Problemen oplossen

In de volgende secties worden veelvoorkomende symptomen, mogelijke oorzaken en oplossingen beschreven. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Er treedt een verbinding met het ITSM-systeem op en het bericht ' fout bij het opslaan van de verbinding ' verschijnt

**Oorzaak**: de oorzaak kan een van de volgende opties zijn:

* De referenties zijn onjuist.
* De bevoegdheden zijn onvoldoende.
* De web-app is onjuist geïmplementeerd.

**Oplossing**:

* Voor ServiceNow-, Cher well-en Provance-verbindingen:
  * Zorg ervoor dat u de gebruikers naam, het wacht woord, de client-ID en het client geheim juist hebt ingevoerd voor elk van de verbindingen.  
  * Controleer voor ServiceNow of u [voldoende bevoegdheden](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) hebt in het bijbehorende ITSM-product.

* Voor Service Manager verbindingen:  
  * Zorg ervoor dat de web-app is geïmplementeerd en dat de hybride verbinding is gemaakt. Als u wilt controleren of de verbinding tot stand is gebracht met de on-premises Service Manager computer, gaat u naar de URL van de web-app zoals beschreven in de [documentatie voor het maken van een hybride verbinding](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>Er zijn dubbele werk items gemaakt

**Oorzaak**: de oorzaak kan een van de volgende twee opties zijn:

* Er is meer dan één ITSM-actie gedefinieerd voor de waarschuwing.
* De waarschuwing is opgelost.

**Oplossing**: er kunnen twee oplossingen zijn:

* Zorg ervoor dat u per waarschuwing één ITSM-actie groep hebt.
* ITSMC biedt geen ondersteuning voor de status updates van overeenkomende werk items wanneer een waarschuwing wordt opgelost. Maak een nieuw opgelost werk item.

### <a name="work-items-are-not-created"></a>Er zijn geen werk items gemaakt

**Oorzaak**: er kunnen verschillende redenen zijn voor dit symptoom:

* Code is gewijzigd aan de kant van de ServiceNow.
* De machtigingen zijn onjuist geconfigureerd.
* De ServiceNow-frequentie limieten zijn te hoog of te laag.
* Een vernieuwings token is verlopen.
* ITSMC is verwijderd.

**Oplossing**: Controleer het [dash board](itsmc-dashboard.md) en Bekijk de fouten in de sectie voor connector status. Bekijk vervolgens de [veelvoorkomende fouten en hun oplossingen](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>U kunt geen ITSM-actie maken voor een actie groep

**Oorzaak**: een nieuw gemaakt ITSMC-exemplaar heeft nog de eerste synchronisatie voltooid.

**Oplossing**: Controleer de [veelvoorkomende fouten en hun oplossingen](itsmc-dashboard-errors.md).