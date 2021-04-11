---
title: Bewaking van Azure Service Fabric platform niveau
description: Meer informatie over gebeurtenissen op platform niveau en logboeken die worden gebruikt voor het bewaken en diagnosticeren van Azure Service Fabric-clusters.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: f9104c390ab4115c626beb4759c6b6952d691ca9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626517"
---
# <a name="monitoring-the-cluster"></a>Het cluster bewaken

Het is belang rijk om op het cluster niveau te controleren om te bepalen of uw hardware en cluster naar verwachting werken. Hoewel Service Fabric de uitvoering van toepassingen tijdens een hardwarestoring kunt blijven gebruiken, moet u nog steeds vaststellen of er een fout optreedt in een toepassing of in de onderliggende infra structuur. U moet ook uw clusters bewaken om de capaciteit beter te plannen, zodat u beslist over het toevoegen of verwijderen van hardware.

Service Fabric biedt verschillende gestructureerd platform gebeurtenissen, zoals [service Fabric gebeurtenissen](service-fabric-diagnostics-events.md), via de Event Store en verschillende logboek kanalen out-of-the-box. 

In Windows zijn Service Fabric gebeurtenissen beschikbaar van één ETW-provider met een set relevante `logLevelKeywordFilters` gegevens die worden gebruikt om te kiezen tussen operationele en Data & Messa ging-kanalen. Dit is de manier waarop we uitgaande service Fabric gebeurtenissen die worden gefilterd, gescheiden moeten worden gemaakt.

* **Operationeel** Bewerkingen op hoog niveau die worden uitgevoerd door Service Fabric en het cluster, met inbegrip van gebeurtenissen voor een knoop punt dat wordt geleverd, een nieuwe toepassing die wordt geïmplementeerd of het terugdraaien van een upgrade, enzovoort. Bekijk [hier](service-fabric-diagnostics-event-generation-operational.md)de volledige lijst met gebeurtenissen.  

* **Operationeel-gedetailleerd**  
Status rapporten en taken voor taak verdeling.

Het bewerkings kanaal kan worden geopend via verschillende manieren, waaronder ETW/Windows-gebeurtenis logboeken, de [Event Store](service-fabric-diagnostics-eventstore.md) (beschikbaar op Windows in versie 6,2 en hoger voor Windows-clusters). Met de Event Store krijgt u toegang tot de gebeurtenissen van uw cluster op basis van een eenheid per entiteit (entiteiten zoals cluster, knoop punten, toepassingen, services, partities, replica's en containers) en beschikbaar gesteld via REST-Api's en de Service Fabric-client bibliotheek. Gebruik de Event Store om uw ontwikkel-en test clusters te bewaken en voor het verkrijgen van een Point-in-time-overeenkomst met de status van uw productie clusters.

* **Gegevens & berichten**  
Kritieke logboeken en gebeurtenissen die in de Messa ging worden gegenereerd (momenteel alleen de ReverseProxy) en het gegevenspad (reliable Services-modellen).

* **Gegevens & berichten-detail**  
Uitgebreid kanaal dat alle niet-kritieke Logboeken bevat van gegevens en berichten in het cluster (dit kanaal heeft een zeer groot aantal gebeurtenissen).

Daarnaast zijn er twee gestructureerde Event source-kanalen, evenals logboeken die we verzamelen voor ondersteunings doeleinden.

* [Reliable Services-gebeurtenissen](service-fabric-reliable-services-diagnostics.md)  
Specifieke gebeurtenissen voor het programmeer model.

* [Reliable Actors-gebeurtenissen](service-fabric-reliable-actors-diagnostics.md)  
Specifieke gebeurtenissen en prestatie meter items voor het model worden geprogrammeerd.

* Ondersteunings logboeken  
Systeem logboeken die door Service Fabric worden gegenereerd, moeten alleen door ons worden gebruikt bij het leveren van ondersteuning.

Deze verschillende kanalen omvatten het grootste deel van de logboek registratie op platform niveau die wordt aanbevolen. Als u de logboek registratie op platform niveau wilt verbeteren, kunt u overwegen investeren in beter inzicht in het status model en aangepaste status rapporten toe te voegen en aangepaste **prestatie meter items** toe te voegen om een real-time inzicht te krijgen in de gevolgen van uw services en toepassingen op het cluster.

Als u gebruik wilt maken van deze logboeken, wordt het ten zeerste aanbevolen om "diagnostiek" in te scha kelen tijdens het maken van het cluster in azure Portal. Door Diagnostische gegevens in te scha kelen, kunt u met Windows Azure Diagnostics de bewerkings-, Reliable Services-en betrouw bare actoren van het cluster bevestigen en de gegevens opslaan zoals wordt uitgelegd in [statistische gebeurtenissen met Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric status en belasting rapportage

Service Fabric heeft een eigen status model, dat uitvoerig wordt beschreven in deze artikelen:

- [Inleiding tot de statuscontrole Service Fabric](service-fabric-health-introduction.md)
- [Servicestatus rapporteren en controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Aangepaste Service Fabric status rapporten toevoegen](service-fabric-report-health.md)
- [Service Fabric status rapporten weer geven](service-fabric-view-entities-aggregated-health.md)

Status controle is van cruciaal belang voor meerdere aspecten van het uitvoeren van een service, met name tijdens een toepassings upgrade. Nadat elk upgrade domein van de service is bijgewerkt, moet het upgrade domein status controles door geven voordat de implementatie naar het volgende upgrade domein wordt verplaatst. Als de status van OK niet kan worden bereikt, wordt de implementatie teruggedraaid, zodat de toepassing de status bekend OK heeft. Hoewel sommige klanten mogelijk worden beïnvloed voordat de services worden teruggedraaid, hebben de meeste klanten geen last van het probleem. Daarnaast treedt er relatief snel een oplossing op zonder te hoeven wachten op actie van een menselijke operator. De meer status controles die zijn opgenomen in uw code, des te flexibeler is het implementeren van problemen.

Een ander aspect van de service status is het rapporteren van metrische gegevens van de service. Metrische gegevens zijn belang rijk in Service Fabric omdat ze worden gebruikt om het resource gebruik te verdelen. Metrische gegevens kunnen ook een indicatie van de systeem status zijn. U kunt bijvoorbeeld een toepassing hebben met veel services, en elk exemplaar rapporteert een RPS-metriek (aanvragen per seconde). Als een service meer resources dan een andere service gebruikt, Service Fabric het verplaatsen van service-exemplaren rond het cluster, om het gebruik van zelfs resources te hand haven. Zie [Resource verbruik beheren en laden in service fabric met metrische gegevens](service-fabric-cluster-resource-manager-metrics.md)voor een gedetailleerde uitleg over de werking van resource gebruik.

Met metrische gegevens kunt u ook inzicht krijgen in de prestaties van uw service. In de loop van de tijd kunt u metrische gegevens gebruiken om te controleren of de service wordt uitgevoerd binnen de verwachte para meters. Als trends bijvoorbeeld laten zien dat op maandag morgen de gemiddelde RPS is 1.000, kunt u een status rapport instellen waarmee u wordt gewaarschuwd als de RPS lager is dan 500 of hoger 1.500. Alles kan perfect zijn, maar het is mogelijk dat u zeker weet dat uw klanten een fantastische ervaring hebben. Met uw service kunt u een set metrische gegevens definiëren die voor de status controle kunnen worden gerapporteerd, maar die geen invloed hebben op de resource verdeling van het cluster. U doet dit door het metrieke gewicht in te stellen op nul. We raden u aan alle metrische gegevens te starten met een gewicht van nul en het gewicht niet te verhogen totdat u zeker weet dat u weet hoe de metrische gegevens van invloed zijn op de resource verdeling voor uw cluster.

> [!TIP]
> Gebruik niet te veel gewogen metrische gegevens. Het kan lastig zijn om te begrijpen waarom service-exemplaren worden verplaatst om te worden gebalanceerd. Een aantal metrische gegevens kan een lange manier duren!

Alle informatie die de status en prestaties van uw toepassing kan aangeven, is een kandidaat voor metrische gegevens en status rapporten. **Met een CPU-prestatie meter item kunt u zien hoe het knoop punt wordt gebruikt, maar wordt niet aangegeven of een bepaalde service in orde is, omdat meerdere services op één knoop punt kunnen worden uitgevoerd.** Maar metrische gegevens, zoals RPS, items verwerkt en latentie van aanvragen, kunnen de status van een specifieke service aangeven.

## <a name="service-fabric-support-logs"></a>Ondersteunings logboeken Service Fabric

Als u contact moet opnemen met micro soft ondersteuning voor hulp bij uw Azure Service Fabric-cluster, zijn de ondersteunings logboeken bijna altijd vereist. Als uw cluster wordt gehost in azure, worden ondersteunings logboeken automatisch geconfigureerd en verzameld als onderdeel van het maken van een cluster. De logboeken worden opgeslagen in een specifiek opslag account in de resource groep van uw cluster. Het opslag account heeft geen vaste naam, maar in het account ziet u BLOB-containers en-tabellen met namen die beginnen met *Fabric*. Zie [een zelfstandige Azure service Fabric-cluster](service-fabric-cluster-creation-for-windows-server.md) en [configuratie-instellingen voor een zelfstandig Windows-cluster](service-fabric-cluster-manifest.md)maken en beheren voor meer informatie over het instellen van logboek verzamelingen voor een zelfstandig cluster. Voor zelfstandige Service Fabric instanties moeten de logboeken worden verzonden naar een lokale bestands share. U **moet** deze logboeken voor ondersteuning hebben, maar deze zijn niet bedoeld om te worden gebruikt door iedereen buiten het micro soft-team voor klanten ondersteuning.

## <a name="measuring-performance"></a>Prestaties meten

Door de prestaties van uw cluster te meten, krijgt u inzicht in de manier waarop het laden en schalen rond uw cluster kan verwerken (Zie meer informatie over het schalen [van een cluster op Azure](service-fabric-cluster-scale-in-out.md)of [on-premises](service-fabric-cluster-windows-server-add-remove-nodes.md)). Prestatie gegevens zijn ook nuttig in vergelijking met de acties die u of uw toepassingen en services mogelijk hebben ondernomen bij het analyseren van Logboeken in de toekomst. 

Zie [prestatie meter items in service Fabric](service-fabric-diagnostics-event-generation-perf.md) voor een lijst met prestatie meter items die moeten worden verzameld bij het gebruik van service Fabric.

Hier volgen twee algemene manieren waarop u het verzamelen van prestatie gegevens voor uw cluster kunt instellen:

* **Een agent gebruiken**  
Dit is de voorkeurs methode voor het verzamelen van prestaties van een machine, omdat agents meestal een lijst bevatten met mogelijke prestatie gegevens die kunnen worden verzameld, en een relatief eenvoudig proces is om de metrische gegevens te kiezen die u wilt verzamelen of wijzigen. De informatie over de Azure Monitor aanbieding Azure Monitor Logboeken in Service Fabric [Azure monitor logboeken integreren](service-fabric-diagnostics-event-analysis-oms.md) en [instellen van de log Analytics agent](../azure-monitor/agents/agent-windows.md) voor meer informatie over de log Analytics agent, wat een dergelijke bewakings agent is waarmee u prestatie gegevens kunt ophalen voor virtuele machines in het cluster en geïmplementeerde containers.

* **Prestatie meter items voor Azure Table Storage**  
U kunt ook prestatie gegevens verzenden naar dezelfde tabel opslag als de gebeurtenissen. Hiervoor moet u de Azure Diagnostics configuratie wijzigen om de juiste prestatie meter items van de virtuele machines in uw cluster op te halen, zodat u docker-statistieken kunt ophalen als u containers gaat implementeren. Meer informatie over het configureren van [prestatie meter items in wad](service-fabric-diagnostics-event-aggregation-wad.md) in service Fabric voor het instellen van verzameling prestatie meters.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [integratie van de Azure monitor logboeken](service-fabric-diagnostics-event-analysis-oms.md) van service Fabric voor het verzamelen van cluster diagnostiek en het maken van aangepaste query's en waarschuwingen
* Meer informatie over Service Fabric in de ingebouwde diagnostische ervaring, de [Event Store](service-fabric-diagnostics-eventstore.md)
* Door loop enkele [veelvoorkomende diagnostische scenario's](service-fabric-diagnostics-common-scenarios.md) in service Fabric
