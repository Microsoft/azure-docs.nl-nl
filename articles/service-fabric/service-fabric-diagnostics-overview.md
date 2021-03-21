---
title: Overzicht van Azure Service Fabric bewaking en diagnostische gegevens
description: Meer informatie over controle en diagnostische gegevens voor Azure Service Fabric-clusters,-toepassingen en-services.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 25a50a2841a03929804be45be8012f9b5d0457ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91357128"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Bewaking en diagnostische gegevens voor Azure Service Fabric

Dit artikel bevat een overzicht van controle en diagnostische gegevens voor Azure Service Fabric. Bewaking en diagnose zijn essentieel voor het ontwikkelen, testen en implementeren van workloads in elke cloudomgeving. U kunt bijvoorbeeld bijhouden hoe uw toepassingen worden gebruikt, de acties die worden uitgevoerd door het Service Fabric platform, het resource gebruik met prestatie meter items en de algehele status van uw cluster. U kunt deze informatie gebruiken om problemen vast te stellen en te verhelpen en te voor komen dat ze in de toekomst optreden. In de volgende gedeelten wordt elk gebied van Service Fabric bewaking in het kort uitgelegd voor productie werkbelastingen. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Toepassingsbewaking
Met toepassings bewaking wordt bijgehouden hoe onderdelen en onderdelen van uw toepassing worden gebruikt. U wilt uw toepassingen controleren om ervoor te zorgen dat er problemen optreden die van invloed zijn op gebruikers. De verantwoordelijkheid van toepassings bewaking bevindt zich op de gebruikers die een toepassing en de bijbehorende services ontwikkelen, omdat deze uniek is voor de bedrijfs logica van uw toepassing. Het bewaken van uw toepassingen kan nuttig zijn in de volgende scenario's:
* Hoeveel verkeer is mijn toepassing ondervonden? -Wilt u uw services schalen om te voldoen aan de behoeften van de gebruiker of een mogelijke knel punt in uw toepassing oplossen?
* Zijn mijn service voor service aanroepen geslaagd en getraceerd?
* Welke acties worden uitgevoerd door de gebruikers van mijn toepassing? -Telemetrie verzamelen kan de ontwikkeling van toekomstige functies en betere diagnoses voor toepassings fouten hand leiding
* Worden niet-verwerkte uitzonde ringen toegepast op mijn toepassing? 
* Wat gebeurt er binnen de services die in mijn containers worden uitgevoerd?

Het grote voor de toepassing van toepassingen is dat ontwikkel aars de hulpprogram ma's en het Framework kunnen gebruiken die ze graag sinds in de context van uw toepassing. U vindt meer informatie over de Azure-oplossing voor toepassings bewaking met Azure Monitor-Application Insights in [gebeurtenis analyse met Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Er is ook een zelf studie over het [instellen van dit voor .NET-toepassingen](service-fabric-tutorial-monitoring-aspnet.md). In deze zelf studie wordt uitgelegd hoe u de juiste hulpprogram ma's installeert, een voor beeld van het schrijven van aangepaste telemetriegegevens in uw toepassing en het weer geven van de Application Diagnostics en telemetrie in de Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Platform-bewaking (cluster)
Een gebruiker heeft controle over welke telemetrie afkomstig is van de toepassing, omdat een gebruiker de code zelf schrijft, maar wat de diagnostische gegevens van het Service Fabric platform? Een van de doelen van Service Fabric is om toepassingen te laten overgaan op hardwarestoringen. Dit doel wordt bereikt door de mogelijkheid van de systeem services van het platform om infrastructuur problemen en snelle failover-workloads te detecteren naar andere knoop punten in het cluster. Maar in dit geval is het wat als de systeem services zelf problemen ondervinden? Of als u een werk belasting probeert te implementeren of verplaatsen, worden de regels voor het plaatsen van services geschonden? Service Fabric voorziet in diagnostische gegevens voor deze en meer om er zeker van te zijn dat u op de hoogte bent van de activiteit die in uw cluster wordt uitgevoerd. Enkele voor beelden van scenario's voor cluster bewaking zijn:

Service Fabric biedt een uitgebreide set gebeurtenissen uit het vak. Deze [service Fabric gebeurtenissen](service-fabric-diagnostics-events.md) zijn toegankelijk via de Event Store of het operationele kanaal (gebeurtenis kanaal dat wordt weer gegeven door het platform). 

* Service Fabric gebeurtenis kanalen: in Windows zijn Service Fabric gebeurtenissen beschikbaar van één ETW-provider met een set relevante `logLevelKeywordFilters` gegevens die worden gebruikt om te kiezen tussen operationele en Data & Messa ging channels. Dit is de manier waarop we uitgaande service Fabric gebeurtenissen die moeten worden gefilterd, scheiden. In Linux komen Service Fabric gebeurtenissen via LTTng en worden ze in één opslag tabel geplaatst, van waaruit ze kunnen worden gefilterd als ze nodig zijn. Deze kanalen bevatten gestructureerde, gestructureerde gebeurtenissen die kunnen worden gebruikt om de status van uw cluster beter te begrijpen. Diagnostische gegevens zijn standaard ingeschakeld tijdens het maken van het cluster, waardoor een Azure Storage tabel wordt gemaakt waarin de gebeurtenissen van deze kanalen voor u in de toekomst worden verzonden. 

* Event Store: de Event Store is een functie die wordt aangeboden door het platform dat Service Fabric platform gebeurtenissen bevat die beschikbaar zijn in de Service Fabric Explorer en via REST API. Op basis van de tijd van de gebeurtenis ziet u een moment opname van wat er in uw cluster gebeurt voor elke entiteit, bijvoorbeeld knoop punt, service, toepassing en query. Meer informatie over de Event Store vindt u in het [overzicht van Event Store](service-fabric-diagnostics-eventstore.md).    

![Scherm afbeelding toont het tabblad gebeurtenissen van het deel venster knoop punten met verschillende gebeurtenissen, met inbegrip van een NodeDown-gebeurtenis.](media/service-fabric-diagnostics-overview/eventstore.png)

De beschik bare diagnostische gegevens bevinden zich in de vorm van een uitgebreide set gebeurtenissen uit het vak. Deze [service Fabric gebeurtenissen](service-fabric-diagnostics-events.md) illustreren acties die door het platform worden uitgevoerd op verschillende entiteiten, zoals knoop punten, toepassingen, services, partities, enzovoort. Als er in het laatste scenario hierboven een knoop punt actief was, zou het platform een gebeurtenis kunnen genereren `NodeDown` en kan hij onmiddellijk op de hoogte worden gesteld door het bewakings programma van Choice. Andere veelvoorkomende voor beelden zijn `ApplicationUpgradeRollbackStarted` of `PartitionReconfigured` tijdens een failover. **Dezelfde gebeurtenissen zijn beschikbaar voor zowel Windows-als Linux-clusters.**

De gebeurtenissen worden verzonden via de standaard kanalen in Windows en Linux en kunnen worden gelezen door elk bewakings programma dat deze ondersteunt. De Azure Monitor oplossing is Azure Monitor Logboeken. U kunt meer lezen over de integratie van de [Azure monitor logboeken](service-fabric-diagnostics-event-analysis-oms.md) , met daarin een aangepast operationeel dash board voor uw cluster en enkele voorbeeld query's van waaruit u waarschuwingen kunnen maken. Er zijn meer concepten voor cluster bewaking beschikbaar op [platform niveau gebeurtenis en logboek generatie](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Statuscontrole
Het Service Fabric-platform bevat een status model dat uitbreid bare status rapportage biedt voor de status van entiteiten in een cluster. Elk knoop punt, elke toepassing, service, partitie, replica of exemplaar, heeft een voortdurend bij te werken status. De status kan ' OK ', ' warning ' of ' fout ' zijn. Denk aan Service Fabric gebeurtenissen zoals werk woorden die door het cluster worden uitgevoerd, naar verschillende entiteiten en status als een bijvoegings groep voor elke entiteit. Telkens wanneer de status van een bepaalde entiteit overgang is, wordt er ook een gebeurtenis verzonden. Op deze manier kunt u query's en waarschuwingen instellen voor status gebeurtenissen in het bewakings programma van Choice, net als bij elke andere gebeurtenis. 

Daarnaast kunnen gebruikers de status van entiteiten zelfs negeren. Als uw toepassing een upgrade uitvoert en er validatie tests worden uitgevoerd, kunt u naar Service Fabric status schrijven met de status-API om aan te geven dat uw toepassing niet meer in orde is en dat de upgrade automatisch wordt teruggedraaid door Service Fabric. Lees voor meer informatie over het status model de [Inleiding tot service Fabric status controle](service-fabric-health-introduction.md)

![SFX-status dashboard](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdog
Over het algemeen is een watchdog een afzonderlijke service die de status kan bekijken en laden tussen services, ping-eind punten en status rapporten voor alles in het cluster. Dit kan helpen voor komen dat fouten die niet worden gedetecteerd op basis van de weer gave van één service. Watchdog zijn ook een goede plaats voor het hosten van code voor het uitvoeren van herstel acties zonder tussen komst van de gebruiker (bijvoorbeeld het opschonen van logboek bestanden in de opslag met bepaalde tijds intervallen). [Hier](https://github.com/Azure-Samples/service-fabric-watchdog-service)vindt u een voor beeld van een watchdog-service-implementatie.

## <a name="infrastructure-performance-monitoring"></a>Bewaking van infra structuur (prestaties)
Nu we de diagnostische gegevens in uw toepassing en het platform hebben behandeld, hoe weten we dat de hardware goed werkt zoals verwacht? Het bewaken van de onderliggende infra structuur is een belang rijk onderdeel van de status van uw cluster en het gebruik van resources. Het meten van de systeem prestaties is afhankelijk van een groot aantal factoren die afhankelijk van uw werk belastingen kunnen worden toegepast. Deze factoren worden doorgaans gemeten via prestatie meter items. Deze prestatie meter items kunnen afkomstig zijn uit verschillende bronnen, waaronder het besturings systeem, het .NET Framework of het Service Fabric-platform zelf. Sommige scenario's waarin ze nuttig zijn, zijn

* Kan ik mijn hardware efficiënt gebruiken? Wilt u uw hardware gebruiken met een CPU van 90% of 10%. Dit is handig bij het schalen van uw cluster of het optimaliseren van de processen van uw toepassing.
* Kan ik de infrastructuur problemen proactief voors pellen? -veel problemen worden voorafgegaan door onverwachte wijzigingen (drup pels) in prestaties, zodat u prestatie meter items zoals netwerk-I/O-en CPU-gebruik kunt gebruiken om de problemen proactief te voors pellen en te diagnosticeren.

Een lijst met prestatie meter items die moeten worden verzameld op het niveau van de infra structuur, is te vinden op [prestatie gegevens](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric biedt ook een aantal prestatie meter items voor de programmeer modellen Reliable Services en actors. Als u een van deze modellen gebruikt, kunnen deze prestatie meter items gegevens controleren om ervoor te zorgen dat uw actors correct draaien en naar behoren werken, of dat uw betrouw bare service aanvragen snel genoeg worden verwerkt. Zie [bewaking voor betrouw bare service externe communicatie](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) en [prestaties controleren voor reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters)voor meer informatie. 

De Azure Monitor oplossing voor het verzamelen van deze gegevens is Azure Monitor logboeken, net als op platform niveau controleren. U moet de [log Analytics agent](service-fabric-diagnostics-oms-agent.md) gebruiken om de juiste prestatie meter items te verzamelen en weer te geven in azure monitor-Logboeken.

## <a name="recommended-setup"></a>Aanbevolen installatie
Nu we over elk gebied van bewakings-en voorbeeld scenario's hebben gewerkt, is hier een samen vatting van de Azure-controle hulpprogramma's en zo nodig ingesteld om alle bovenstaande gebieden te bewaken. 

* Toepassings bewaking met [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Cluster controle met [Diagnostische agent](service-fabric-diagnostics-event-aggregation-wad.md) en [Azure monitor-logboeken](service-fabric-diagnostics-oms-setup.md)
* Infra structuur controleren met [Azure monitor-logboeken](service-fabric-diagnostics-oms-agent.md)

U kunt ook de voor beeld-ARM-sjabloon die [hier](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) zich bevindt, gebruiken en wijzigen om de implementatie van alle benodigde resources en agents te automatiseren. 

## <a name="other-logging-solutions"></a>Andere oplossingen voor logboek registratie

Hoewel de twee oplossingen worden aanbevolen, [Azure monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md) en [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) zijn geïntegreerd met Service Fabric, worden veel gebeurtenissen geschreven via etw-providers en kunnen ze worden uitgebreid met andere logboek registratie oplossingen. U moet ook kijken naar de [elastische stack](https://www.elastic.co/products) (met name als u overweegt een cluster in een offline omgeving uit te voeren), [Dynatrace](https://www.dynatrace.com/)of een ander platform van uw voor keur. Hier vindt u een lijst met geïntegreerde partners die u [hier](service-fabric-diagnostics-partners.md)kunt vinden.

De belangrijkste punten voor elk platform dat u kiest, moeten de volgende zaken bevatten: de gebruikers interface, de query mogelijkheden, de aangepaste visualisaties en dash boards die beschikbaar zijn, en de extra hulp middelen die ze bieden om uw bewakings ervaring te verbeteren. 

## <a name="next-steps"></a>Volgende stappen

* Zie [gebeurtenis-en logboek registratie op toepassings niveau](service-fabric-diagnostics-event-generation-app.md)voor het aan de slag gaan met het instrumenteren van uw toepassingen.
* Door loop de stappen om Application Insights voor uw toepassing in te stellen met behulp van [een ASP.net core-toepassing in service Fabric te controleren en te diagnosticeren](service-fabric-tutorial-monitoring-aspnet.md).
* Meer informatie over het bewaken van het platform en de gebeurtenissen Service Fabric biedt u de beschikking over [gebeurtenis-en logboek genereren op platform niveau](service-fabric-diagnostics-event-generation-infra.md).
* De integratie van de Azure Monitor logboeken configureren met Service Fabric bij [het instellen van Azure monitor logboeken voor een cluster](service-fabric-diagnostics-oms-setup.md)
* Meer informatie over het instellen van Azure Monitor-logboeken voor het bewaken van containers: [bewaking en diagnose voor Windows-containers in Azure service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Bekijk voor beelden van diagnostische problemen en oplossingen met Service Fabric bij het [vaststellen van veelvoorkomende scenario's](service-fabric-diagnostics-common-scenarios.md)
* Bekijk andere diagnostische producten die met Service Fabric worden geïntegreerd in [service Fabric diagnostische partners](service-fabric-diagnostics-partners.md)
* Meer informatie over algemene controle aanbevelingen voor Azure-resources- [Aanbevolen procedures-bewaking en diagnose](/azure/architecture/best-practices/monitoring). 
