---
title: Replicatie status van Active Directory controleren
description: Het Active Directory-replicatiestatus Solution Pack bewaakt uw Active Directory omgeving regel matig voor replicatie fouten.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: c99ad16e119c4262aa6d9d645b2457cdd46061b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700674"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Active Directory replicatie status controleren met Azure Monitor

![AD-replicatiestatus-symbool](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory is een belang rijk onderdeel van een IT-omgeving van een onderneming. Om hoge Beschik baarheid en hoge prestaties te garanderen, heeft elke domein controller een eigen kopie van de Active Directory-Data Base. Domein controllers repliceren met elkaar om wijzigingen door te geven aan de hele onderneming. Fouten in dit replicatie proces kunnen diverse problemen in de hele onderneming veroorzaken.

De AD-replicatiestatus-oplossing bewaakt de Active Directory omgeving regel matig voor replicatie fouten.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende informatie om de oplossing te installeren en configureren.

### <a name="prerequisites"></a>Vereisten

* Voor de AD-replicatiestatus oplossing is een ondersteunde versie van .NET Framework 4.6.2 of hoger vereist die is geïnstalleerd op elke computer met de Log Analytics-agent voor Windows (ook wel micro soft Monitoring Agent (MMA)) geïnstalleerd.  De agent wordt gebruikt door System Center 2016-Operations Manager, Operations Manager 2012 R2 en Azure Monitor.
* De oplossing biedt ondersteuning voor domein controllers met Windows Server 2008 en 2008 R2, Windows Server 2012 en 2012 R2 en Windows Server 2016.
* Een Log Analytics-werk ruimte om de Active Directory Health Check-oplossing toe te voegen vanuit de Azure Marketplace in de Azure Portal. Er is geen aanvullende configuratie vereist.


### <a name="install-agents-on-domain-controllers"></a>Agents installeren op domein controllers
U moet agents installeren op domein controllers die lid zijn van het domein om te kunnen worden geëvalueerd. Of u moet agents op lidservers installeren en de agents configureren voor het verzenden van AD-replicatie gegevens naar Azure Monitor. Zie [Windows-computers verbinden met Azure monitor](../agents/agent-windows.md)als u wilt weten hoe u Windows-computers verbindt met Azure monitor. Zie [Operations Manager verbinden om te Azure monitor](../agents/om-agents.md)als uw domein controller al deel uitmaakt van een bestaande System Center Operations Manager omgeving die u wilt verbinden met Azure monitor.

### <a name="enable-non-domain-controller"></a>Niet-domein controller inschakelen
Als u niet rechtstreeks verbinding wilt maken met een van uw domein controllers met Azure Monitor, kunt u een andere computer in uw domein gebruiken die verbonden is met Azure Monitor om gegevens te verzamelen voor het AD-replicatiestatus Solution Pack en de gegevens te verzenden.

1. Controleer of de computer lid is van het domein dat u wilt bewaken met behulp van de AD-replicatiestatus-oplossing.
2. [Verbind de Windows-computer met Azure monitor](../agents/om-agents.md) of [Maak verbinding met uw bestaande Operations Manager omgeving om Azure monitor](../agents/om-agents.md)als deze nog niet is verbonden.
3. Stel op die computer de volgende register sleutel in:<br>Sleutel: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName> \Solutions\ADReplication**<br>Waarde: **IsTarget**<br>Waardegegevens: **True**

   > [!NOTE]
   > Deze wijzigingen worden pas van kracht nadat u de micro soft Monitoring Agent-service (HealthService.exe) opnieuw hebt gestart.
   > ### <a name="install-solution"></a>Oplossing installeren
   > Volg het proces dat wordt beschreven in [een bewakings oplossing installeren](solutions.md#install-a-monitoring-solution) om de **Active Directory-replicatiestatus** oplossing toe te voegen aan uw log Analytics-werk ruimte. Er is geen verdere configuratie nodig.


## <a name="ad-replication-status-data-collection-details"></a>Details van het verzamelen van AD-replicatiestatus gegevens
De volgende tabel toont methoden voor gegevens verzameling en andere informatie over hoe gegevens worden verzameld voor AD-replicatiestatus.

| platform | Directe agent | SCOM-agent | Azure Storage | SCOM vereist? | SCOM-agent gegevens die via een beheer groep zijn verzonden | verzamelings frequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |elke vijf dagen |



## <a name="understanding-replication-errors"></a>Wat zijn replicatie fouten?

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Op de tegel AD-replicatiestatus wordt weer gegeven hoeveel replicatie fouten er momenteel zijn. **Kritieke replicatie fouten** zijn fouten die gelijk zijn aan of hoger 75% van de [tombstone-levens duur](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10)) voor uw Active Directory-forest.

![AD-replicatiestatus tegel](./media/ad-replication-status/oms-ad-replication-tile.png)

Wanneer u op de tegel klikt, kunt u meer informatie over de fouten weer geven.
![AD-replicatiestatus dash board](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status van de doel server en de status van de bron server
In deze kolommen ziet u de status van de doel servers en bron servers waarop replicatie fouten zich voordoen. Het nummer na elke domein controller geeft het aantal replicatie fouten op die domein controller.

De fouten voor beide doel servers en bron servers worden weer gegeven, omdat sommige problemen eenvoudiger kunnen worden opgelost vanuit het oogpunt van de bron server en andere van het perspectief van de doel server.

In dit voor beeld ziet u dat veel doel servers ongeveer hetzelfde aantal fouten hebben, maar er is één bron server (ADDC35) met veel meer fouten dan alle andere. Het is waarschijnlijk dat er een probleem is met ADDC35 dat ervoor zorgt dat het verzenden van gegevens naar de replicatie partners mislukt. Bij het oplossen van de problemen met ADDC35 kunnen veel van de fouten die worden weer gegeven in het gebied van de doel server, worden opgelost.

### <a name="replication-error-types"></a>Replicatie fout typen
Dit gedeelte bevat informatie over de typen fouten die in uw hele onderneming zijn gedetecteerd. Elke fout heeft een unieke numerieke code en een bericht dat u kan helpen om de hoofd oorzaak van de fout te bepalen.

De ring bovenaan geeft u een idee van de fouten die meer en minder vaak in uw omgeving worden weer gegeven.

U ziet wanneer meerdere domein controllers dezelfde replicatie fout ervaren. In dit geval kunt u mogelijk een oplossing op één domein controller detecteren of identificeren. Herhaal deze vervolgens op andere domein controllers waarop dezelfde fout van toepassing is.

### <a name="tombstone-lifetime"></a>Tombstone-levens duur
De tombstone-levens duur bepaalt hoe lang een verwijderd object, aangeduid als tombstone, wordt bewaard in de Active Directory-Data Base. Wanneer een verwijderd object de tombstone-levens duur doorgeeft, wordt deze automatisch uit de Active Directory Data Base verwijderd.

De standaard-tombstone-levens duur is 180 dagen voor de meest recente versies van Windows, maar is 60 dagen voor oudere versies en kan expliciet worden gewijzigd door een Active Directory beheerder.

Het is belang rijk te weten of u replicatie fouten ondervindt die bijna de tombstone-levens duur hebben. Als twee domein controllers een replicatie fout ondervinden die de tombstone-levens duur persistent maakt, wordt replicatie tussen deze twee domein controllers uitgeschakeld, zelfs als de onderliggende replicatie fout is opgelost.

Het gebied tombstone-levens duur helpt u bij het identificeren van locaties waar uitgeschakelde replicatie zich in gevaar bevindt. Elke fout in de categorie **meer dan 100% TSL** vertegenwoordigt een partitie die niet is gerepliceerd tussen de bron-en doel server ten minste de tombstone-levens duur voor het forest.

In dit geval is het niet voldoende om de replicatie fout te verhelpen. U moet ten minste hand matig onderzoeken om achtergebleven objecten te identificeren en op te schonen voordat u de replicatie opnieuw kunt starten. Mogelijk moet u ook een domein controller buiten gebruik stellen.

Naast het identificeren van replicatie fouten die zich na de tombstone-levens duur bevindt, moet u ook aandacht best Eden aan eventuele fouten in de categorieën **50-75% TSL** of **75-100% TSL** .

Dit zijn fouten die duidelijk achterblijvend zijn, niet tijdelijk, waardoor de interventie waarschijnlijk nodig is om te kunnen oplossen. Het goede nieuws is dat de tombstone-levens duur nog niet is bereikt. Als u deze problemen onmiddellijk corrigeert en *voordat* de tombstone-levens duur is bereikt, kan de replicatie opnieuw worden gestart met minimale hand matige tussen komst.

Zoals eerder is opgemerkt, toont de dashboard tegel voor de AD-replicatiestatus oplossing het aantal *kritieke* replicatie fouten in uw omgeving, dat is gedefinieerd als fouten die groter zijn dan 75% van de tombstone-levens duur (inclusief fouten die groter zijn dan 100% van TSL). Streven om dit getal te blijven bij 0.

> [!NOTE]
> Alle berekeningen van de tombstone-levens duur zijn gebaseerd op de werkelijke tombstone-levens duur van uw Active Directory-forest, zodat u kunt vertrouwen dat deze percentages nauw keurig zijn, zelfs als u een aangepaste waarde voor de tombstone-levens duur hebt ingesteld.
>
>

### <a name="ad-replication-status-details"></a>Details van AD-replicatie status
Wanneer u op een item in een van de lijsten klikt, ziet u aanvullende informatie hierover met een logboek query. De resultaten worden gefilterd zodat alleen de fouten die betrekking hebben op dat item worden weer gegeven. Als u bijvoorbeeld klikt op de eerste domein controller die wordt weer gegeven onder status van de **doel server (ADDC02)**, ziet u query resultaten gefilterd om fouten weer te geven met die domein controller vermeld als de doel server:

![AD-replicatie status fouten in query resultaten](./media/ad-replication-status/oms-ad-replication-search-details.png)

Hier kunt u verder filteren, de logboek query wijzigen, enzovoort. Zie [logboek gegevens analyseren in azure monitor](../logs/log-query-overview.md)voor meer informatie over het gebruik van de logboek query's in azure monitor.

In het veld **helpLink** wordt de URL van een TechNet-pagina weer gegeven met aanvullende informatie over die specifieke fout. U kunt deze koppeling kopiëren en plakken in uw browser venster om informatie over het oplossen van problemen te bekijken en de fout te verhelpen.

U kunt ook klikken op **exporteren** om de resultaten naar Excel te exporteren. Het exporteren van de gegevens kan u helpen de gegevens van de replicatie fout op elke gewenste manier te visualiseren.

![AD-replicatie status fouten in Excel geëxporteerd](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Veelgestelde vragen over AD-replicatiestatus
**V: hoe vaak worden de gegevens van de AD-replicatie status bijgewerkt?**
A: de gegevens worden elke vijf dagen bijgewerkt.

**V: is er een manier om te configureren hoe vaak deze gegevens worden bijgewerkt?**
A: Momenteel niet.

**V: moet ik al mijn domein controllers toevoegen aan mijn Log Analytics-werk ruimte om de replicatie status weer te geven?**
A: Nee, er moet slechts één domein controller worden toegevoegd. Als u meerdere domein controllers in uw Log Analytics-werk ruimte hebt, worden gegevens van al deze in Azure Monitor verzonden.

**V: Ik wil geen domein controllers toevoegen aan mijn Log Analytics-werk ruimte. Kan ik de AD-replicatiestatus-oplossing nog steeds gebruiken?**

A: Ja. U kunt de waarde van een register sleutel instellen om deze in te scha kelen. Zie [niet-domein controller inschakelen](#enable-non-domain-controller).

**V: wat is de naam van het proces dat het verzamelen van gegevens doet?**
A: AdvisorAssessment.exe

**V: hoe lang duurt het voordat gegevens worden verzameld?**
A: de tijd voor het verzamelen van gegevens is afhankelijk van de grootte van de Active Directory omgeving, maar duurt meestal minder dan 15 minuten.

**V: welk type gegevens er worden verzameld?**
A: de replicatie gegevens worden verzameld via LDAP.

**V: is er een manier om te configureren wanneer gegevens worden verzameld?**
A: Momenteel niet.

**V: welke machtigingen heb ik nodig om gegevens te verzamelen?**
A: normale gebruikers machtigingen voor Active Directory zijn voldoende.

## <a name="troubleshoot-data-collection-problems"></a>Problemen met gegevens verzameling oplossen
Voor het verzamelen van gegevens moet voor het AD-replicatiestatus Solution Pack ten minste één domein controller zijn verbonden met uw Log Analytics-werk ruimte. Totdat u verbinding maakt met een domein controller, wordt er een bericht weer gegeven dat aangeeft dat de **gegevens nog worden verzameld**.

Als u hulp nodig hebt bij het verbinden van een van uw domein controllers, kunt u de documentatie weer geven op [Windows-computers verbinden met Azure monitor](../agents/om-agents.md). Als uw domein controller al is verbonden met een bestaande System Center Operations Manager omgeving, kunt u ook de documentatie bekijken in [Connect System Center Operations Manager om Azure monitor](../agents/om-agents.md).

Zie [niet-domein controller inschakelen](#enable-non-domain-controller)als u niet rechtstreeks verbinding wilt maken met een van uw domein controllers met Azure Monitor of System Center Operations Manager.

## <a name="next-steps"></a>Volgende stappen
* Gebruik [logboek query's in azure monitor](../logs/log-query-overview.md) om gedetailleerde Active Directory replicatie status gegevens weer te geven.

