---
title: Azure Monitor voor bestaande Operations Manager klanten
description: Richt lijnen voor bestaande gebruikers van Operations Manager om de overgang van bepaalde werk belastingen naar Azure Monitor te maken als onderdeel van een overgang naar de Cloud.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 85172e2430a3e65edb0c5ec119c920e2c7d20217
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234858"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor voor bestaande Operations Manager klanten
Dit artikel bevat richt lijnen voor klanten die momenteel [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/welcome) gebruiken en een overgang plannen naar [Azure monitor](overview.md) wanneer ze bedrijfs toepassingen en andere resources migreren naar Azure. Hierbij wordt ervan uitgegaan dat uw ultieme doel stelling een volledige overgang naar de Cloud is, waarbij u zoveel mogelijk Operations Manager functionaliteit kunt vervangen door Azure Monitor, zonder in te leveren op de operationele vereisten van uw bedrijf en IT. 

De specifieke aanbevelingen in dit artikel worden gewijzigd als Azure Monitor en Operations Manager onderdelen toevoegen. De fundamentele strategie is echter consistent.

> [!IMPORTANT]
> Er zijn kosten verbonden aan het implementeren van verschillende Azure Monitor functies die hier worden beschreven, dus u moet de waarde ervan evalueren voordat u deze in uw hele omgeving implementeert.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u [Operations Manager](https://docs.microsoft.com/system-center/scom) al gebruikt en dat er ten minste een basis memorandum van [Azure monitor](overview.md)is. Zie [overzicht van Cloud monitoring: bewakings platforms](/azure/cloud-adoption-framework/manage/monitor/platform-overview)voor een volledige vergelijking tussen de twee. In dit artikel vindt u meer informatie over de verschillen tussen de twee en enkele van de aanbevelingen die hier worden beschreven. 


## <a name="general-strategy"></a>Algemene strategie
Er zijn geen migratie hulpprogramma's voor het converteren van activa van Operations Manager naar Azure Monitor omdat de platformen fundamenteel verschillen. De migratie is in plaats daarvan een [standaard Azure monitor implementatie](deploy.md) wanneer u Operations Manager blijft gebruiken. Bij het aanpassen van Azure Monitor om te voldoen aan uw vereisten voor verschillende toepassingen en onderdelen, en naarmate er meer functies worden gerealiseerd, kunt u de verschillende Management Packs en agents in Operations Manager buiten gebruik stellen.

De algemene strategie die in dit artikel wordt aanbevolen, is hetzelfde als in de [Cloud monitoring-hand leiding](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/), waarmee een hybride strategie voor [Cloud bewaking](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) wordt aanbevolen waarmee u een geleidelijke overgang naar de cloud kunt uitvoeren. Hoewel sommige functies elkaar kunnen overlappen, biedt deze strategie u de mogelijkheid om uw bestaande bedrijfs processen te onderhouden terwijl u vertrouwd raakt met het nieuwe platform. Verplaats alleen van Operations Manager functionaliteit, omdat u deze kunt vervangen door Azure Monitor. Het gebruik van meerdere controle hulpprogramma's voegt complexiteit toe, maar biedt u de mogelijkheid om te profiteren van de mogelijkheden van Azure Monitor om de Cloud werkbelasting van de volgende generatie te bewaken, Operations Manager terwijl u de mogelijkheden van de server software en infrastructuur onderdelen die on-premises of andere Clouds zijn, kunt bewaken. 


## <a name="components-to-monitor"></a>Te bewaken onderdelen
Het helpt bij het categoriseren van de verschillende typen werk belastingen die u moet bewaken om een afzonderlijke bewakings strategie voor elk te bepalen. [Cloud monitoring-hand leiding: een bewakings strategie formuleren](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) biedt een gedetailleerde uitsplitsing van de verschillende lagen in uw omgeving die moeten worden bewaakt tijdens de voortgang van verouderde zakelijke toepassingen naar moderne toepassingen in de Cloud.

Voor de Cloud hebt u Operations Manager gebruikt om alle lagen te bewaken. Wanneer u de overgang met de infra structuur als een service (IaaS) start, blijft u Operations Manager gebruiken voor uw virtuele machines, maar kunt u Azure Monitor voor uw cloud resources gebruiken. Als u een andere overstap maakt naar moderne toepassingen die gebruikmaken van platform as a Service (PaaS), kunt u zich richten op Azure Monitor en Operations Manager functionaliteit buiten gebruik stellen.


![Cloud modellen](https://docs.microsoft.com/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Deze lagen kunnen in de volgende categorieën worden vereenvoudigd, die verder in de rest van dit artikel worden beschreven. Hoewel elke bewakings werk belasting in uw omgeving mogelijk niet netjes in een van deze categorieën past, moet elk systeem voldoende zijn voor een bepaalde categorie, zodat de algemene aanbevelingen kunnen worden toegepast.

**Zakelijke toepassingen.** Toepassingen die functionaliteit bieden die specifiek zijn voor uw bedrijf. Ze kunnen intern of extern zijn en worden vaak intern ontwikkeld met aangepaste code. Uw oudere toepassingen worden doorgaans gehost op virtuele of fysieke machines waarop Windows of Linux wordt uitgevoerd, terwijl uw nieuwere toepassingen worden gebaseerd op toepassings Services in azure, zoals Azure Web Apps en Azure Functions.

**Azure-Services.** Resources in azure die ondersteuning bieden voor uw bedrijfs toepassingen die zijn gemigreerd naar de Cloud. Dit omvat services als Azure Storage, Azure SQL en Azure IoT. Dit omvat ook virtuele machines van Azure, omdat deze worden bewaakt als andere Azure-Services, maar de toepassingen en software die worden uitgevoerd op het gast besturingssysteem van die virtuele machines, hebben meer controle nodig dan de host.

**Server software.** Software die wordt uitgevoerd op virtuele en fysieke machines die ondersteuning bieden voor uw bedrijfs toepassingen of verpakte toepassingen die algemene functionaliteit aan uw bedrijf leveren. Voor beelden zijn IIS (Internet Information Server), SQL Server, Exchange en share point. Dit omvat ook het Windows-of Linux-besturings systeem op uw virtuele en fysieke computers.

**Lokale infra structuur.** Onderdelen die specifiek zijn voor uw on-premises omgeving die moeten worden bewaakt. Dit omvat resources als fysieke servers, opslag en netwerk onderdelen. Dit zijn de onderdelen die worden gevirtualiseerd wanneer u naar de Cloud gaat.

## <a name="sample-walkthrough"></a>Walkthrough voor voorbeeld
Hier volgt een hypothetisch overzicht van een migratie van Operations Manager naar Azure Monitor. Dit is niet bedoeld om de volledige complexiteit van een daad werkelijke migratie te vertegenwoordigen, maar biedt ten minste de basis stappen en de volg orde. In de volgende secties worden deze stappen uitvoeriger beschreven.

Uw omgeving voordat u onderdelen naar Azure verplaatst, is gebaseerd op virtuele en fysieke machines op locatie of met een beheerde service provider. Het is afhankelijk van Operations Manager voor het bewaken van bedrijfs toepassingen, server software en andere infrastructuur onderdelen in uw omgeving, zoals fysieke servers en netwerken. U gebruikt standaard Management Packs voor Server software, zoals IIS, SQL Server en verschillende leveranciers software, en u kunt deze Management Packs afstemmen op uw specifieke vereisten. U maakt aangepaste Management Packs voor uw zakelijke toepassingen en andere onderdelen die niet kunnen worden bewaakt met bestaande Management Packs en Operations Manager configureren ter ondersteuning van uw bedrijfs processen.

Uw migratie naar Azure begint met IaaS, waarbij virtuele machines worden verplaatst die bedrijfs toepassingen ondersteunen in Azure. De bewakings vereisten voor deze toepassingen en de server software waarvan ze afhankelijk zijn, worden niet gewijzigd en u kunt Operations Manager op deze servers blijven gebruiken met uw bestaande Management Packs. 

Azure Monitor is ingeschakeld voor uw Azure-Services zodra u een Azure-abonnement hebt gemaakt. Er worden automatisch platform metrieken en het activiteiten logboek verzameld, en u configureert bron Logboeken zodat u alle beschik bare telemetrie kunt analyseren met behulp van logboek query's. U schakelt Azure Monitor voor VM's op uw virtuele machines in om bewakings gegevens in uw hele omgeving samen te analyseren en om relaties tussen computers en processen te ontdekken. U breidt uw gebruik van Azure Monitor uit naar uw on-premises fysieke en virtuele machines door Azure Arc ingeschakelde servers in te scha kelen. 

U schakelt Application Insights in voor elk van uw zakelijke toepassingen. Het identificeert de verschillende onderdelen van elke toepassing, begint met het verzamelen van gebruiks-en prestatie gegevens en identificeert fouten die in de code optreden. U maakt beschikbaarheids tests om uw externe toepassingen proactief te testen en u te waarschuwen voor problemen met prestaties of Beschik baarheid. Hoewel Application Insights u krachtige functies biedt die u niet in Operations Manager hebt, kunt u de aangepaste Management Packs blijven gebruiken die u hebt ontwikkeld voor uw zakelijke toepassingen, omdat deze bewakings scenario's bevatten die nog niet door Azure Monitor worden gedekt. 

Als u vertrouwd bent met Azure Monitor, kunt u waarschuwings regels maken die management pack functionaliteit kunnen vervangen en de bedrijfs processen zo ontwikkelen dat ze het nieuwe bewakings platform gebruiken. Zo kunt u beginnen met het verwijderen van machines en Management Packs uit de Operations Manager beheer groep. U kunt Management Packs blijven gebruiken voor essentiële server software en on-premises infra structuur, maar u kunt ook door gaan met de nieuwe functies in Azure Monitor waarmee u extra functionaliteit kunt intrekken.

## <a name="monitor-azure-services"></a>Azure-Services bewaken
Azure-Services vereisen Azure Monitor voor het verzamelen van telemetrie en zijn ingeschakeld wanneer u een Azure-abonnement maakt. Het [activiteiten logboek](platform/activity-log.md) wordt automatisch verzameld voor het abonnement en de [metrische gegevens](platform/data-platform-metrics.md) van het platform worden automatisch verzameld van de Azure-resources die u maakt. U kunt meteen aan de slag met [metrische gegevens Verkenner](platform/metrics-getting-started.md), die vergelijkbaar is met prestatie weergaven in de operations-console, maar biedt interactieve analyses en [Geavanceerde aggregaties](platform/metrics-charts.md) van data. [Een waarschuwing voor metrische gegevens maken](platform/alerts-metric.md) om te worden gewaarschuwd wanneer een waarde een drempel overschrijdt of [een grafiek aan een Azure-dash board toevoegt](platform/metrics-charts.md#pin-charts-to-dashboards) voor zicht baarheid.

[![Metrics-explorer](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Maak een diagnostische instelling](platform/diagnostic-settings.md) voor elke Azure-resource voor het verzenden van metrische gegevens en [resource logboeken](platform/resource-logs.md), die informatie geven over de interne bewerking van elke resource, naar een log Analytics-werk ruimte. Hiermee beschikt u over alle beschik bare telemetrie voor uw resources en kunt u [log Analytics](log-query/log-analytics-overview.md) gebruiken om logboek-en prestatie gegevens interactief te analyseren met behulp van een geavanceerde query taal die geen equivalent in Operations Manager heeft. U kunt ook [waarschuwingen voor logboek query's](platform/alerts-log-query.md)maken, waarmee u complexe logica kunt gebruiken om de waarschuwings voorwaarden te bepalen en gegevens te correleren tussen meerdere resources.

[![Logboekanalyse](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

[Inzichten](monitor-reference.md) in azure monitor zijn vergelijkbaar met Management Packs in dat ze unieke controles bieden voor een bepaalde Azure-service. Inzichten zijn momenteel beschikbaar voor verschillende services, waaronder netwerken, opslag en containers, en andere worden voortdurend toegevoegd.

[![Inzicht-voor beeld](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Inzichten zijn gebaseerd op [werkmappen](platform/workbooks-overview.md) in azure monitor, waarmee metrische gegevens worden gecombineerd en query's worden vastgelegd in uitgebreide, interactieve rapporten. Maak uw eigen werkmappen om gegevens uit meerdere services te combi neren, vergelijkbaar met de manier waarop u aangepaste weer gaven en rapporten in de operations-console kunt maken.

### <a name="azure-management-pack"></a>Azure management pack
Met [azure Management Pack](https://www.microsoft.com/download/details.aspx?id=50013) kan Operations Manager Azure-resources detecteren en hun status controleren op basis van een bepaalde reeks bewakings scenario's. Voor dit management pack moet u aanvullende configuratie uitvoeren voor elke resource in azure, maar het kan handig zijn om uw Azure-resources in de operations-console te presen teren tot u uw bedrijfs processen hebt ontwikkeld om te focussen op Azure Monitor.

[![Azure management pack](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 U kunt ervoor kiezen om het Management Pack van Azure te gebruiken als u inzicht wilt hebben in bepaalde Azure-resources in de operations-console en een aantal eenvoudige waarschuwingen wilt integreren met uw bestaande processen. De gegevens die worden verzameld door Azure Monitor worden feitelijk gebruikt. U moet Azure Monitor voor een volledige controle van uw Azure-resources op lange termijn. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Server software en lokale infra structuur bewaken
Wanneer u machines naar de Cloud verplaatst, worden de bewakings vereisten voor de software niet gewijzigd. Het is niet meer nodig om de fysieke onderdelen te controleren omdat deze zijn gevirtualiseerd, maar het gast besturingssysteem en de werk belastingen hebben dezelfde vereisten, ongeacht hun omgeving.

[Azure monitor voor VM's](insights/vminsights-overview.md) is de belangrijkste functie in azure monitor voor het bewaken van virtuele machines en het bijbehorende gast besturingssysteem en werk belastingen. Net als bij Operations Manager gebruikt Azure Monitor voor VM's een agent voor het verzamelen van gegevens van het gast besturingssysteem van virtuele machines. Dit zijn dezelfde prestatie-en gebeurtenis gegevens die meestal worden gebruikt door Management Packs voor analyse en waarschuwingen. Er zijn geen bestaande regels die kunnen worden gebruikt om problemen te identificeren en op te lossen voor de bedrijfs toepassingen en server software die op die computers worden uitgevoerd. U moet uw eigen waarschuwings regels maken om proactief op de hoogte te worden gesteld van gedetecteerde problemen.

[![Prestaties Azure Monitor voor VM's](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor de status van verschillende toepassingen en services die op een virtuele machine worden uitgevoerd ook niet meten. Metrische waarschuwingen kunnen automatisch worden omgezet wanneer een waarde onder de drempel waarde daalt, maar Azure Monitor heeft momenteel niet de mogelijkheid om de status criteria te definiëren voor toepassingen en services die op de machine worden uitgevoerd, noch biedt de functie status samen voegen om de status van verwante onderdelen te groeperen.

> [!NOTE]
> Een nieuwe [functie gast status voor Azure monitor voor VM's](insights/vminsights-health-overview.md) is nu beschikbaar in de open bare preview-versie en geeft een waarschuwing op basis van de status van een set prestatie gegevens. Dit is in eerste instantie beperkt tot een specifieke set prestatie meter items die betrekking hebben op het gast besturingssysteem en niet voor toepassingen of andere workloads die worden uitgevoerd op de virtuele machine.
> 
> [![Gast status Azure Monitor voor VM's](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

Het bewaken van de software op uw computers in een hybride omgeving maakt doorgaans gebruik van een combi natie van Azure Monitor voor VM's en Operations Manager, afhankelijk van de vereisten van elke machine en op uw vervaldag om operationele processen rond Azure Monitor te ontwikkelen. De micro soft-beheer agent (aangeduid als de Log Analytics agent in Azure Monitor) wordt door beide platforms gebruikt, zodat één computer gelijktijdig kan worden bewaakt door beide.

> [!NOTE]
> In de toekomst wordt Azure Monitor voor VM's overgezet naar de [Azure monitor agent](platform/azure-monitor-agent-overview.md), die momenteel beschikbaar is als open bare preview. Het is compatibel met de micro soft monitoring agent zodat dezelfde virtuele machine nog steeds door beide platforms kan worden bewaakt.

Ga door met het gebruik van Operations Manager voor functionaliteit die nog niet door Azure Monitor is verschaft. Dit omvat Management Packs voor essentiële server software, zoals IIS, SQL Server of Exchange. Mogelijk hebt u ook aangepaste Management Packs ontwikkeld voor een on-premises infra structuur die niet kan worden bereikt met Azure Monitor. Blijf ook Operations Manager gebruiken als deze nauw geïntegreerd is in uw operationele processen totdat u kunt overstappen op het moderniseren van uw service bewerkingen, waarbij Azure Monitor en andere Azure-Services kunnen worden uitgebreid of vervangen. 

Gebruik Azure Monitorloze Vm's om uw huidige bewaking te verbeteren, zelfs als deze niet direct wordt vervangen Operations Manager. Hier volgen enkele voor beelden van functies die uniek zijn voor Azure Monitor:

- Ontdek en bewaak relaties tussen virtuele machines en hun externe afhankelijkheden.
- Bekijk geaggregeerde prestatie gegevens op meerdere virtuele machines in interactieve grafieken en werkmappen.
- Gebruik [logboek query's](log-query/log-query-overview.md) om telemetrie van uw virtuele machines interactief te analyseren met gegevens van uw andere Azure-resources.
- Maak [logboek waarschuwings regels](platform/alerts-log-query.md) op basis van complexe logica op meerdere virtuele machines.

[![Azure Monitor voor VM's kaart](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Naast virtuele machines van Azure kunnen Azure Monitor voor VM's machines on-premises en in andere Clouds controleren met behulp van [Azure Arc-servers](../azure-arc/servers/overview.md). Met Arc ingeschakelde servers kunt u uw Windows-en Linux-machines die worden gehost buiten Azure, op uw bedrijfs netwerk of een andere Cloud provider beheren die overeenkomt met de manier waarop u systeem eigen Azure virtual machines beheert.



## <a name="monitor-business-applications"></a>Bedrijfs toepassingen bewaken
U hebt doorgaans aangepaste Management Packs nodig om uw zakelijke toepassingen te bewaken met Operations Manager, waarbij u gebruikmaakt van agents die op elke virtuele machine zijn geïnstalleerd. Application Insights in Azure Monitor webtoepassingen bewaakt, ongeacht of deze zich in azure, andere Clouds of on-premises bevinden, zodat deze kunnen worden gebruikt voor al uw toepassingen, ongeacht of ze wel of niet zijn gemigreerd naar Azure.

Als uw bewaking van een zakelijke toepassing beperkt is tot de functionaliteit van de [sjabloon voor .net-app-prestaties]() in Operations Manager, kunt u de migratie naar Application Insights zonder verlies van de functionaliteit waarschijnlijk niet uitvoeren. Application Insights bevat eigenlijk een groot aantal extra functies, waaronder de volgende:

- Toepassings onderdelen automatisch detecteren en bewaken.
- Verzamel gedetailleerde gegevens over het gebruik en de prestaties van toepassingen, zoals reactie tijd, fout tarieven en aanvraag frequenties.
- Verzamel browser gegevens, zoals pagina weergaven en de prestaties van de belasting.
- Detecteer uitzonde ringen en zoom in stack trace en gerelateerde aanvragen.
- Geavanceerde analyses uitvoeren met behulp van functies zoals [gedistribueerde tracering](app/distributed-tracing.md) en [Slimme detectie](app/proactive-diagnostics.md).
- [Metrics Explorer](platform/metrics-getting-started.md) gebruiken om de prestatie gegevens interactief te analyseren.
- Gebruik [logboek query's](log-query/log-query-overview.md) om verzamelde telemetrie samen te analyseren met gegevens die worden verzameld voor Azure-Services en-Azure monitor voor VM's.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Er zijn bepaalde scenario's waarbij u Operations Manager naast Application Insights pas kunt blijven gebruiken als u de vereiste functionaliteit hebt. Hier volgen enkele voor beelden van de Operations Manager het volgende bevatten:

-  [Beschikbaarheids tests](app/monitor-web-app-availability.md), waarmee u de beschik baarheid en reactie tijd van uw toepassingen kunt bewaken en waarschuwen, vereisen inkomende aanvragen van de IP-adressen van webtest agents. Als uw beleid deze toegang niet toestaat, moet u mogelijk de [beschikbaarheids monitors voor webtoepassingen](/system-center/scom/web-application-availability-monitoring-template) in Operations Manager blijven gebruiken.
- In Operations Manager kunt u een polling interval voor beschikbaarheids tests instellen, waarbij veel klanten elke 60-120 seconden controleren. Application Insights heeft een mini maal polling-interval van 5 minuten. Dit kan te lang zijn voor sommige klanten.
- Een aanzienlijke hoeveelheid bewaking in Operations Manager wordt uitgevoerd door het verzamelen van gebeurtenissen die worden gegenereerd door toepassingen en door scripts uit te voeren op de lokale agent. Dit zijn niet de standaard opties in Application Insights, zodat u aangepaste werk nodig hebt om uw bedrijfs vereisten te verzorgen. Dit kan aangepaste waarschuwings regels bevatten met gebeurtenis gegevens die zijn opgeslagen in een Log Analytics-werk ruimte en scripts die worden gestart in een gast voor virtuele machines met [hybride runbook worker](../automation/automation-hybrid-runbook-worker.md).
- Afhankelijk van de taal waarin uw toepassing is geschreven, kan het zijn dat u beperkt bent in de [instrumentatie die u kunt gebruiken met Application Insights](app/platforms.md).

Volg de basis strategie in de andere secties van deze hand leiding om Operations Manager te gebruiken voor uw zakelijke toepassingen, maar profiteer van aanvullende functies van Application Insights. U kunt de essentiële functionaliteit van Azure Monitor vervangen door uw aangepaste Management Packs buiten gebruik te stellen.


## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [Cloud monitoring-hand leiding](/azure/cloud-adoption-framework/manage/monitor/) voor een gedetailleerde vergelijking van Azure Monitor en System Center Operations Manager en meer informatie over het ontwerpen en implementeren van een hybride bewakings omgeving.
- Meer informatie over het [bewaken van Azure-resources in azure monitor](insights/monitor-azure-resource.md).
- Meer informatie over het [bewaken van virtuele Azure-machines in azure monitor](insights/monitor-vm-azure.md).
- Lees meer over [Azure monitor voor VM's](insights/vminsights-overview.md).
- Lees meer over [Application Insights](app/app-insights-overview.md).
