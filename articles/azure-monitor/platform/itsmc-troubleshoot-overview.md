---
title: Problemen oplossen in ITSM-connector
description: Problemen met IT Service Management-connector oplossen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: fce20626d5e000c08b8a057671c06a3084534187
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896033"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Problemen oplossen in ITSM-connector

In dit artikel worden veelvoorkomende problemen in ITSM-connector beschreven en vindt u informatie over het oplossen van problemen.

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken.
De klant kan selecteren hoe ze op de waarschuwing moeten worden gewaarschuwd, ongeacht of het gaat om e-mail, SMS, webhook of zelfs om een oplossing te automatiseren. Een andere optie om op de hoogte te worden gesteld, is het gebruik van ITSM.
ITSM biedt u de mogelijkheid om de waarschuwingen te verzenden naar het externe ticket systeem, zoals ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Het incident en de gegevens van de wijzigings aanvraag visualiseren en analyseren

Afhankelijk van uw configuratie wanneer u een verbinding instelt, kan ITSMC tot 120 dagen aan incidenten en wijzigings aanvraag gegevens worden gesynchroniseerd. Het logboek record schema voor deze gegevens vindt u in de [sectie aanvullende informatie](./itsmc-synced-data.md) van dit artikel.

U kunt het incident visualiseren en gegevens wijzigen met behulp van het ITSMC-dash board:

![Scherm opname van het ITSMC-dash board.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Het dash board bevat ook informatie over de status van de connector, die u als uitgangs punt kunt gebruiken om problemen met de verbindingen te analyseren.

Zie [fout onderzoek met behulp van het dash board](./itsmc-dashboard.md)voor meer informatie over het dashboard onderzoek.

### <a name="service-map"></a>Service overzicht

U kunt ook visualiseren van de incidenten die zijn gesynchroniseerd met de betrokken computers in Servicetoewijzing.

Servicetoewijzing detecteert automatisch de toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. Zo kunt u uw servers bekijken zoals u dat wilt: als onderling verbonden systemen die essentiële services leveren. Servicetoewijzing toont verbindingen tussen servers, processen en poorten via elke met TCP verbonden architectuur. Met uitzonde ring van de installatie van een agent is geen configuratie vereist. Zie [servicetoewijzing gebruiken](../insights/service-map.md)voor meer informatie.

Als u Servicetoewijzing gebruikt, kunt u de Service Desk-items weer geven die zijn gemaakt in ITSM-oplossingen, zoals hier wordt weer gegeven:

![Scherm opname van de Log Analytics scherm.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="common-symptoms---how-should-it-be-resolved"></a>Veelvoorkomende symptomen: hoe moet het worden opgelost?

De onderstaande lijst bevat algemene symptomen en hoe moet deze worden opgelost:

* **Symptoom**: als een verbinding geen verbinding kan maken met het ITSM systeem en er een **fout optreedt bij het opslaan van het verbindings** bericht.

    **Oorzaak**: de oorzaak kan een van de volgende opties zijn:
    * Onjuiste referenties
     * Onvoldoende bevoegdheden
     * De web-app moet correct worden geïmplementeerd

    **Oplossing**:
    * Voor ServiceNow-, Cher well-en Provance-verbindingen:
        * Zorg ervoor dat u de gebruikers naam, het wacht woord, de client-ID en het client geheim juist hebt ingevoerd voor elk van de verbindingen.  
        * Voor ServiceNow: Zorg ervoor dat u voldoende bevoegdheden hebt in het bijbehorende ITSM-product om de verbinding op te [geven.](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)
  * Voor Service Manager verbindingen:  
      * Zorg ervoor dat de web-app is geïmplementeerd en dat de hybride verbinding is gemaakt. Als u wilt controleren of de verbinding tot stand is gebracht met de on-premises Service Manager computer, gaat u naar de URL van de web-app zoals beschreven in de documentatie voor het maken van de [hybride verbinding](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  
* **Symptoom**: er zijn dubbele werk items gemaakt

    **Oorzaak**: de oorzaak kan een van de volgende twee opties zijn:
    * Er zijn meer dan één ITSM-actie gedefinieerd voor de waarschuwing.
    * De waarschuwing is opgelost.

    **Oplossing**: er kunnen twee oplossingen zijn:
    * Zorg ervoor dat u per waarschuwing één ITSM-actie groep hebt.
    * ITSM-connector ondersteunt geen overeenkomende status update van werk items wanneer een waarschuwing wordt opgelost. Er wordt een nieuw, opgelost werk item gemaakt.
* **Symptoom**: er zijn geen werk items gemaakt

    **Oorzaak**: er kunnen een aantal redenen zijn voor dit symptoom:
    * Code wijzigen in de ServiceNow-zijde
    * Onjuiste configuratie van machtigingen
    * De ServiceNow-frequentie limieten zijn te hoog/laag
    * Het vernieuwings token is verlopen
    * ITSM-connector is verwijderd

    **Oplossing**: u kunt het [dash board](itsmc-dashboard.md) controleren en de fouten bekijken in de sectie connector status. Bekijk de [algemene fouten](itsmc-dashboard-errors.md) en ontdek hoe u de fout kunt oplossen.

* **Symptoom**: kan geen ITSM-actie maken voor de actie groep

    **Oorzaak**: nieuw gemaakte ITSM-connector heeft nog de eerste synchronisatie voltooid.

    **Oplossing**: u kunt de [algemene UI-fouten](itsmc-dashboard-errors.md#ui-common-errors) bekijken en ontdekken hoe u de fout oplost.