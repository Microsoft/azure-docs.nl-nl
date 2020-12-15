---
title: Problemen met ServiceNow-synchronisatie hand matig oplossen
description: Stel de verbinding opnieuw in op ServiceNow zodat waarschuwingen in Microsoft Azure opnieuw kunnen worden aangeroepen ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 3e836873219bde3836f2863e328b0b6f5b89addc
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507282"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Problemen oplossen in ITSM-connector

In dit artikel worden veelvoorkomende problemen in ITSM-connector beschreven en vindt u informatie over het oplossen van problemen.

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Zie overzicht van waarschuwingen in Microsoft Azure voor meer informatie over waarschuwingen.
De klant kan selecteren hoe ze op de waarschuwing moeten worden gewaarschuwd, ongeacht of het gaat om e-mail, SMS, webhook of zelfs om een oplossing te automatiseren. Een andere optie om op de hoogte te worden gesteld, is het gebruik van ITSM.
ITSM biedt u de mogelijkheid om de waarschuwingen te verzenden naar het externe ticket systeem, zoals ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Het incident en de gegevens van de wijzigings aanvraag visualiseren en analyseren

Afhankelijk van uw configuratie wanneer u een verbinding instelt, kan ITSMC tot 120 dagen aan incidenten en wijzigings aanvraag gegevens worden gesynchroniseerd. Het logboek record schema voor deze gegevens vindt u in de [sectie aanvullende informatie](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#additional-information) van dit artikel.

U kunt het incident visualiseren en gegevens wijzigen met behulp van het ITSMC-dash board:

![Scherm opname van het ITSMC-dash board.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Het dash board bevat ook informatie over de status van de connector, die u als uitgangs punt kunt gebruiken om problemen met de verbindingen te analyseren.

U kunt ook visualiseren van de incidenten die zijn gesynchroniseerd met de betrokken computers in Servicetoewijzing.

Servicetoewijzing detecteert automatisch de toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. Zo kunt u uw servers bekijken zoals u dat wilt: als onderling verbonden systemen die essentiële services leveren. Servicetoewijzing toont verbindingen tussen servers, processen en poorten via elke met TCP verbonden architectuur. Met uitzonde ring van de installatie van een agent is geen configuratie vereist. Zie [servicetoewijzing gebruiken](../insights/service-map.md)voor meer informatie.

Als u Servicetoewijzing gebruikt, kunt u de Service Desk-items weer geven die zijn gemaakt in ITSM-oplossingen, zoals hier wordt weer gegeven:

![Scherm opname van de Log Analytics scherm.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>Problemen met ServiceNow-synchronisatie hand matig oplossen

Azure Monitor kan verbinding maken met ITSM-providers (IT Service Management) van derden. ServiceNow is een van deze providers.

Uit veiligheids overwegingen moet u mogelijk het verificatie token vernieuwen dat voor uw verbinding wordt gebruikt met ServiceNow.
Gebruik het volgende synchronisatie proces om de verbinding opnieuw te activeren en het token te vernieuwen:


1. Zoek naar de oplossing in de bovenste Zoek banner en selecteer vervolgens de relevante oplossingen

    ![Scherm afbeelding met de bovenste Zoek banner en waar u de relevante oplossingen kunt selecteren.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Kies in het scherm oplossing de optie Alles selecteren in het abonnements filter en filter vervolgens op ' Service Desk '

    ![Scherm afbeelding die laat zien waar u selecteert selecteren en waar u wilt filteren op Service Desk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecteer de oplossing van uw ITSM-verbinding.
1. Selecteer ITSM-verbinding in het linkerdeel blad.

    ![Scherm afbeelding die laat zien waar u ITSM-verbindingen selecteert.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecteer elke connector in de lijst. 
    1. Klik op de naam van de connector om deze te configureren
    1. Alle connectors verwijderen die niet meer worden gebruikt

    1. De velden bijwerken volgens [deze definities](./itsmc-connections.md) op basis van uw partner type

    1. Klik op synchroniseren

       ![Scherm opname van de knop synchroniseren.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klik op opslaan

        ![Nieuwe verbinding](media/itsmc-resync-servicenow/save-8bit.png)

f.    Bekijk de meldingen om na te gaan of het proces is voltooid

## <a name="troubleshoot-itsm-connections"></a>Problemen met ITSM-verbindingen oplossen

- Voer de volgende stappen uit als er een **fout optreedt bij het opslaan** van een verbindings bericht in de gebruikers interface van de verbonden Bron:
   - Voor ServiceNow-, Cher well-en Provance-verbindingen:  
     - Zorg ervoor dat u de gebruikers naam, het wacht woord, de client-ID en het client geheim juist hebt ingevoerd voor elk van de verbindingen.  
     - Zorg ervoor dat u voldoende bevoegdheden hebt in het bijbehorende ITSM-product om de verbinding tot stand te brengen.  
   - Voor Service Manager verbindingen:  
     - Zorg ervoor dat de web-app is geïmplementeerd en dat de hybride verbinding is gemaakt. Als u wilt controleren of de verbinding tot stand is gebracht met de on-premises Service Manager computer, gaat u naar de URL van de web-app zoals beschreven in de documentatie voor het maken van de [hybride verbinding](./itsmc-connections.md#configure-the-hybrid-connection).  

- Als gegevens van ServiceNow niet worden gesynchroniseerd met Log Analytics, moet u ervoor zorgen dat het ServiceNow-exemplaar niet in de slaap stand staat. ServiceNow dev-instanties gaan soms naar de slaap stand wanneer ze gedurende een lange periode niet actief zijn. Als dat niet het geval is, meldt u het probleem.
- Als Log Analytics waarschuwingen geactiveerd, maar er worden geen werk items gemaakt in het ITSM-product, als configuratie-items niet zijn gemaakt/gekoppeld aan werk items, of als u andere informatie wilt, raadpleegt u deze bronnen:
   -  ITSMC: de oplossing toont een samen vatting van verbindingen, werk items, computers en meer. Selecteer de tegel met het label **status** van de connector. Hiermee gaat u zoeken naar **Logboeken** met de relevante query. Bekijk logboek records met een `LogType_S` van `ERROR` voor meer informatie.
   - **Zoek pagina voor logboeken** : de fouten en gerelateerde informatie rechtstreeks weer geven met behulp van de query `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Problemen met de implementatie van Service Manager web-app oplossen

-   Als u problemen ondervindt met het implementeren van web-apps, moet u ervoor zorgen dat u gemachtigd bent om resources in het abonnement te maken of te implementeren.
-   Als u een **object verwijzing krijgt die niet is ingesteld op een object** fout wanneer u het [script](itsmc-service-manager-script.md)uitvoert, moet u ervoor zorgen dat u geldige waarden hebt opgegeven in de sectie **gebruikers configuratie** .
-   Als u de service bus relay-naam ruimte niet hebt gemaakt, moet u ervoor zorgen dat de vereiste resource provider is geregistreerd in het abonnement. Als het niet is geregistreerd, maakt u de service bus relay-naam ruimte hand matig via de Azure Portal. U kunt deze ook maken wanneer u [de hybride verbinding maakt](./itsmc-connections.md#configure-the-hybrid-connection) in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [IT Service Management-verbindingen](itsmc-connections.md)
