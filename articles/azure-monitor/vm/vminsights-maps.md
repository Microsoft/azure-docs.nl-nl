---
title: App-afhankelijkheden weer geven met VM Insights
description: De kaart is een functie van VM Insights. Het detecteert automatisch toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. In dit artikel vindt u informatie over het gebruik van de kaart functie in verschillende scenario's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: 19da3e3e02581ce9fad080bb23bc48dcb9a3ceb3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719641"
---
# <a name="use-the-map-feature-of-vm-insights-to-understand-application-components"></a>De toewijzings functie van VM Insights gebruiken om inzicht te krijgen in toepassings onderdelen
In VM Insights kunt u gedetecteerde toepassings onderdelen weer geven op virtuele Windows-en Linux-machines (Vm's) die worden uitgevoerd in azure of in uw omgeving. U kunt de virtuele machines op twee manieren bekijken. Een kaart rechtstreeks vanuit een virtuele machine weer geven of een kaart van Azure Monitor weer geven om de onderdelen van verschillende Vm's te bekijken. In dit artikel vindt u meer informatie over deze twee weergave methoden en over het gebruik van de kaart functie. 

Zie voor meer informatie over het configureren van VM Insights [VM Insights inschakelen](./vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Inleiding tot de kaart ervaring
Voordat u aan de kaart ervaring gaat, moet u weten hoe de informatie wordt gepresenteerd en gevisualiseerd. Of u de kaart functie rechtstreeks vanuit een virtuele machine of van Azure Monitor selecteert, de kaart functie biedt een consistente ervaring. Het enige verschil is dat van Azure Monitor, één kaart toont alle leden van een toepassing of cluster met meerdere lagen.

De functie map visualiseert de VM-afhankelijkheden door actieve processen te detecteren die het volgende hebben: 

- Actieve netwerk verbindingen tussen servers.
- Binnenkomende en uitgaande verbindings latentie.
- Poorten via elke met TCP verbonden architectuur gedurende een opgegeven periode.  
 
Vouw een VM uit om proces details weer te geven en alleen de processen die communiceren met de virtuele machine. De client groep toont het aantal front-end-clients dat verbinding maakt met de virtuele machine. De server poort groepen tonen het aantal back-endservers waarmee de virtuele machine verbinding maakt. Vouw een server-poort groep uit om de gedetailleerde lijst met servers te zien die via die poort verbinding maken.  

Wanneer u de virtuele machine selecteert, worden in het deel venster met **Eigenschappen** aan de rechter kant de eigenschappen van de virtuele machine weer gegeven. Eigenschappen bevatten systeem informatie die is gerapporteerd door het besturings systeem, eigenschappen van de virtuele machine van Azure en een ring diagram met een overzicht van de gedetecteerde verbindingen. 

![Het deel venster Eigenschappen](./media/vminsights-maps/properties-pane-01.png)

Selecteer aan de rechter kant van het deel venster **logboek gebeurtenissen** om een lijst met gegevens weer te geven die de VM naar Azure monitor heeft verzonden. Deze gegevens zijn beschikbaar voor het uitvoeren van query's.  Selecteer een record type om de pagina **Logboeken** te openen, waar u de resultaten voor het record type ziet. U ziet ook een vooraf geconfigureerde query die wordt gefilterd op de virtuele machine.  

![Het deel venster logboek gebeurtenissen](./media/vminsights-maps/properties-pane-logs-01.png)

Sluit de **Logboeken** pagina en ga terug naar het deel venster **Eigenschappen** . Hier selecteert u **waarschuwingen** om waarschuwingen over de status van de virtuele machine te bekijken. De kaart functie integreert met Azure-waarschuwingen om waarschuwingen weer te geven voor de geselecteerde server in het geselecteerde tijds bereik. Op de server wordt een pictogram voor de huidige waarschuwingen weer gegeven en in het deel venster **computer waarschuwingen** staan de waarschuwingen. 

![Het deel venster waarschuwingen](./media/vminsights-maps/properties-pane-alerts-01.png)

Als u de kaart functie relevante waarschuwingen wilt laten weer geven, maakt u een waarschuwings regel die van toepassing is op een specifieke computer:

- Neem een component op voor het groeperen van waarschuwingen per computer (bijvoorbeeld **per computer interval van 1 minuut**).
- De waarschuwing baseren op een metrieke waarde.

Zie [Unified Alerts in azure monitor](../alerts/alerts-overview.md)voor meer informatie over Azure-waarschuwingen en het maken van waarschuwings regels.

In de rechter bovenhoek worden de symbolen en rollen op de kaart in de **legenda** -optie beschreven. Voor een beter overzicht van uw kaart en om deze te verplaatsen, gebruikt u de Zoom knoppen in de rechter benedenhoek. U kunt het zoom niveau instellen en de kaart passend maken voor de grootte van de pagina.  

## <a name="connection-metrics"></a>Verbindings gegevens
In het deel venster **verbindingen** worden standaard metrische gegevens weer gegeven voor de geselecteerde verbinding van de virtuele machine via de TCP-poort. De metrische gegevens bevatten de reactie tijd, aanvragen per minuut, verkeers doorvoer en koppelingen.  

![Netwerk verbindings grafieken in het deel venster verbindingen](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Mislukte verbindingen
De kaart toont mislukte verbindingen voor processen en computers. Een stippel rode lijn geeft aan dat een client systeem een proces of poort niet kan bereiken. Voor systemen die gebruikmaken van de afhankelijkheids agent rapporteert de agent over mislukte Verbindings pogingen. De kaart functie bewaakt een proces door TCP-sockets te gebruiken die geen verbinding tot stand brengen. Deze fout kan het gevolg zijn van een firewall, een onjuiste configuratie in de client of server of een niet-beschik bare externe service.

![Een mislukte verbinding op de kaart](./media/vminsights-maps/map-group-failed-connection-01.png)

Inzicht in mislukte verbindingen kan u helpen bij het oplossen van problemen, het valideren van de migratie, het analyseren van de beveiliging en het begrijpen van de algehele architectuur van de service. Mislukte verbindingen zijn soms onschadelijk, maar ze verwijzen vaak naar een probleem. Verbindingen kunnen mislukken, bijvoorbeeld wanneer een failover-omgeving plotseling onbereikbaar wordt of wanneer twee toepassings lagen niet met elkaar kunnen communiceren na een Cloud migratie.

### <a name="client-groups"></a>Client groepen
Op de kaart vertegenwoordigen client groepen client computers die verbinding maken met de toegewezen computer. Eén client groep vertegenwoordigt de clients voor een afzonderlijk proces of een afzonderlijke computer.

![Een client groep op de kaart](./media/vminsights-maps/map-group-client-groups-01.png)

Als u de bewaakte clients en IP-adressen van de systemen in een client groep wilt weer geven, selecteert u de groep. De inhoud van de groep wordt hieronder weer gegeven.  

![De lijst met IP-adressen van een client groep op de kaart](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Als de groep bewaakte en niet-bewaakte clients bevat, kunt u de juiste sectie van het ring diagram van de groep selecteren om de clients te filteren.

### <a name="server-port-groups"></a>Server poort groepen
Server-poort groepen vertegenwoordigen poorten op servers die binnenkomende verbindingen van de toegewezen computer hebben. De groep bevat de server poort en een telling van het aantal servers die verbindingen met die poort hebben. Selecteer de groep om de afzonderlijke servers en verbindingen weer te geven. 

![Een server poort groep op de kaart](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Als de groep bewaakte en niet-bewaakte servers bevat, kunt u de juiste sectie van het ring diagram van de groep selecteren om de servers te filteren.

## <a name="view-a-map-from-a-vm"></a>Een kaart van een virtuele machine weer geven 

Voor toegang tot de VM-inzichten rechtstreeks vanuit een VM:

1. Selecteer **virtual machines** In het Azure Portal. 
2. Kies een virtuele machine in de lijst. Kies **inzichten** in het gedeelte **bewaking** .  
3. Selecteer het tabblad **map** .

De kaart visualiseert de afhankelijkheden van de virtuele machine door het detecteren van actieve proces groepen en processen die actieve netwerk verbindingen hebben gedurende een opgegeven tijds bereik.  

Standaard toont de kaart de laatste 30 minuten. Als u wilt zien hoe afhankelijkheden in het verleden worden weer geven, kunt u een query uitvoeren voor historische Peri Oden van Maxi maal één uur. Als u de query wilt uitvoeren, gebruikt u de **time Range** selector in de linkerbovenhoek. U kunt een query uitvoeren, bijvoorbeeld tijdens een incident of de status voor een wijziging bekijken.  

![Scherm afbeelding van het tabblad map in het gedeelte bewakings inzichten van Azure Portal een diagram van de afhankelijkheden tussen virtuele machines weer te geven.](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Een kaart uit een schaalset voor virtuele machines weer geven

Voor toegang tot de VM-inzichten rechtstreeks vanuit een schaalset voor virtuele machines:

1. Selecteer in de Azure Portal **virtuele-machine schaal sets**.
2. Kies een virtuele machine in de lijst. Klik vervolgens in de sectie **bewaking** op **inzichten**.  
3. Selecteer het tabblad **map** .

De kaart visualeert alle instanties in de schaalset als groeps knooppunt samen met de afhankelijkheden van de groep. In het uitgevouwen knoop punt worden de exemplaren in de schaalset weer gegeven. U kunt 10 per keer door deze instanties bladeren. 

Als u een kaart voor een specifiek exemplaar wilt laden, selecteert u eerst dat exemplaar op de kaart. Selecteer vervolgens de knop met het **weglatings teken** (...) aan de rechter kant en kies **Server toewijzing laden**. In de kaart die wordt weer gegeven, ziet u proces groepen en processen die actieve netwerk verbindingen hebben gedurende een opgegeven tijds bereik. 

Standaard toont de kaart de laatste 30 minuten. Als u wilt zien hoe afhankelijkheden in het verleden worden weer geven, kunt u een query uitvoeren voor historische Peri Oden van Maxi maal één uur. Als u de query wilt uitvoeren, gebruikt u de **time Range** kiezer. U kunt een query uitvoeren, bijvoorbeeld tijdens een incident of de status voor een wijziging bekijken.

![Scherm afbeelding van het tabblad map in het gedeelte bewakings inzichten van Azure Portal een diagram met afhankelijkheden tussen schaal sets voor virtuele machines weer geven.](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>U kunt ook toegang krijgen tot een kaart voor een specifiek exemplaar vanuit de weer gave **instanties** voor de schaalset van de virtuele machine. Ga in de sectie **instellingen** naar **instances**  >  **Insights**.

## <a name="view-a-map-from-azure-monitor"></a>Een kaart van Azure Monitor weer geven

In Azure Monitor biedt de kaart functie een globaal overzicht van uw Vm's en de bijbehorende afhankelijkheden. Voor toegang tot de kaart functie in Azure Monitor:

1. Selecteer in de Azure Portal **monitor**. 
2. Kies in het gedeelte **insightss** de optie **virtual machines**.
3. Selecteer het tabblad **map** .

   ![Overzichts toewijzing van meerdere Vm's Azure Monitor](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Kies een werk ruimte met behulp van de **werkruimte** kiezer boven aan de pagina. Als u meer dan één Log Analytics-werk ruimte hebt, kiest u de werk ruimte die is ingeschakeld met de oplossing en waar u Vm's aan rapporteert. 

De **groeps** kiezer retourneert abonnementen, resource groepen, [computer groepen](../logs/computer-groups.md)en virtuele-machine schaal sets van computers die zijn gerelateerd aan de geselecteerde werk ruimte. Uw selectie is alleen van toepassing op de kaart functie en heeft geen betrekking op de prestaties of de status.

Standaard toont de kaart de laatste 30 minuten. Als u wilt zien hoe afhankelijkheden in het verleden worden weer geven, kunt u een query uitvoeren voor historische Peri Oden van Maxi maal één uur. Als u de query wilt uitvoeren, gebruikt u de **time Range** kiezer. U kunt een query uitvoeren, bijvoorbeeld tijdens een incident of de status voor een wijziging bekijken.  

## <a name="next-steps"></a>Volgende stappen

Zie prestaties van de [virtuele machine bekijken voor](vminsights-performance.md)meer informatie over het identificeren van knel punten, het controleren van de prestaties en het algehele gebruik van uw vm's.
