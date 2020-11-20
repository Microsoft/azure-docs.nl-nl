---
title: Overzicht en architectuur van SAP-oplossingen Azure Monitor Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure monitor for SAP-oplossingen
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 5bb40ec930731b58d457500d83b66c7a187b44d8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957432"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure monitor voor SAP-oplossingen (preview-versie)

## <a name="overview"></a>Overzicht

Azure Monitor voor SAP-oplossingen is een Azure-systeem eigen bewakings product voor klanten, waarop hun SAP-landschappen op Azure wordt uitgevoerd. Het product werkt met zowel [SAP on Azure virtual machines](./hana-get-started.md) als [SAP on Azure grote instanties](./hana-overview-architecture.md).
Met Azure Monitor voor SAP-oplossingen kunnen klanten telemetriegegevens van Azure-infra structuur en-data bases op één centrale locatie verzamelen en telemetriegegevens visueel correleren om sneller problemen op te lossen.

Azure Monitor voor SAP-oplossingen wordt aangeboden via Azure Marketplace. Het biedt een eenvoudige, intuïtieve installatie-ervaring en er zijn slechts enkele klikken om de resource te implementeren voor Azure Monitor voor SAP-oplossingen (ook wel **SAP-monitor bron** genoemd).

Klanten kunnen verschillende onderdelen van een SAP-landschap bewaken, zoals Azure Virtual Machines, cluster met hoge Beschik baarheid, SAP HANA data base, enzovoort, door de bijbehorende **provider** voor dat onderdeel toe te voegen.

Ondersteunde infra structuur:

- Virtuele Azure-machine
- Azure large-exemplaar

Ondersteunde data bases:
- SAP HANA-database
- Micro soft SQL Server

Azure Monitor voor SAP-oplossingen maakt gebruik van de kracht van bestaande [Azure monitor](../../../azure-monitor/overview.md) mogelijkheden, zoals log Analytics en [werkmappen](../../../azure-monitor/platform/workbooks-overview.md) , om extra bewakings mogelijkheden te bieden. Klanten kunnen [aangepaste visualisaties](../../../azure-monitor/platform/workbooks-overview.md#getting-started) maken door de standaard werkmappen te bewerken die worden meegeleverd door Azure monitor voor SAP-oplossingen, [aangepaste query's](../../../azure-monitor/log-query/get-started-portal.md) te schrijven en [aangepaste waarschuwingen](../../../azure-monitor/learn/tutorial-response.md) te maken met behulp van Azure log Analytics-werk ruimte, profiteren van de [flexibele Bewaar periode](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) en het koppelen van bewakings gegevens met hun ticket systeem.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Welke gegevens worden Azure Monitor voor SAP-oplossingen verzameld?

Het verzamelen van gegevens in Azure Monitor voor SAP-oplossingen is afhankelijk van de providers die door klanten zijn geconfigureerd. Tijdens de open bare preview worden de volgende gegevens verzameld.

Pacemaker voor hoge Beschik baarheid:
- Status van knoop punt, resource en SBD-apparaat
- Pacemaker locatie beperkingen
- Quorum stemmen en ring status
- [Overige](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Telemetrie SAP HANA:
- CPU, geheugen, schijf en netwerk gebruik
- HANA-systeem replicatie (HSR)
- HANA-back-up
- HANA-status van de host
- Index Server-en naam server functies

Micro soft SQL Server-telemetrie:
- CPU, geheugen, schijf gebruik
- Hostnaam, naam van SQL-exemplaar, SAP-systeem-ID
- Batch-aanvragen, compilaties en levens duur van pagina's verwachting in de loop van de tijd
- Top 10 van duurste SQL-instructies gedurende een periode
- Top 12 van de grootste tabel in het SAP-systeem
- Problemen die worden vastgelegd in de SQL Server-fouten logboeken
- Blok keren van processen en SQL-wacht statistieken gedurende een periode

## <a name="data-sharing-with-microsoft"></a>Gegevens delen met micro soft

Met Azure Monitor voor SAP-oplossingen worden systeem meta gegevens verzameld voor betere ondersteuning voor onze SAP on Azure klanten. Er wordt geen PII/EUII verzameld.
Klanten kunnen gegevens delen met micro soft inschakelen op het moment van het maken van Azure Monitor voor SAP-oplossings resources door *delen* te kiezen in de vervolg keuzelijst.
Het wordt ten zeerste aangeraden om gegevens delen in te scha kelen, omdat micro soft ondersteuning en technische teams meer informatie geeft over de klant omgeving en verbeterde ondersteuning biedt voor onze essentiële SAP on Azure klanten.

## <a name="architecture-overview"></a>Overzicht van de architectuur

Op hoog niveau wordt in het volgende diagram uitgelegd hoe Azure Monitor voor SAP-oplossingen telemetrie van SAP HANA data base verzamelt. De architectuur is neutraal om te bepalen of SAP HANA wordt geïmplementeerd in azure Virtual Machines of Azure large instances.

![Azure Monitor voor SAP-oplossingen architectuur](./media/azure-monitor-sap/azure-monitor-architecture.png)

De belangrijkste onderdelen van de architectuur zijn:
- Azure Portal: het begin punt voor klanten. Klanten kunnen naar Marketplace navigeren binnen Azure Portal en Azure Monitor voor SAP-oplossingen ontdekken
- Azure Monitor voor SAP-oplossingen resource: een landings plaats voor klanten om de telemetrie van bewaking te bekijken
- Beheerde resource groep: wordt automatisch geïmplementeerd als onderdeel van de Azure Monitor voor de implementatie van SAP-oplossingen. De resources die zijn geïmplementeerd in de Help van de beheerde resource groep in verzameling van telemetrie. De belangrijkste resources die zijn geïmplementeerd en hun doel zijn:
   - Virtuele Azure-machine: ook bekend als *Collector-VM*. Dit is een Standard_B2ms VM. Het belangrijkste doel van deze virtuele machine is het hosten van de *bewakings lading*. Bewakings Payload verwijst naar de logica van het verzamelen van telemetrie van de bron systemen en het overdragen van de verzamelde gegevens naar het bewakings raamwerk. In het bovenstaande diagram bevat de bewakings lading de logica om verbinding te maken met SAP HANA data base via SQL-poort.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): deze resource is geïmplementeerd voor het veilig vasthouden van SAP Hana database referenties en voor het opslaan van informatie over [providers](./azure-monitor-providers.md).
   - Log Analytics-werk ruimte: de bestemming waar de telemetriegegevens zich bevinden.
      - Visualisatie is gebaseerd op telemetrie in Log Analytics met behulp van [Azure-werkmappen](../../../azure-monitor/platform/workbooks-overview.md). Klanten kunnen visualisatie aanpassen. Klanten kunnen hun werkmappen of specifieke visualisatie in werkmappen ook vastmaken aan Azure-dash board voor automatisch vernieuwen met de laagste granulatie van 30 minuten.
      - Klanten kunnen hun bestaande werk ruimte binnen hetzelfde abonnement gebruiken als SAP-bewakings resource door deze optie te kiezen op het moment van de implementatie.
      - Klanten kunnen Kusto query language (KQL) gebruiken om [query's](../../../azure-monitor/log-query/log-query-overview.md) uit te voeren op de onbewerkte tabellen in log Analytics werk ruimte. Bekijk de *aangepaste logboeken*.

> [!Note]
> Klanten zijn verantwoordelijk voor het patchen en onderhouden van de virtuele machine, geïmplementeerd in de beheerde resource groep.

> [!Tip]
> Klanten kunnen kiezen voor het gebruik van een bestaande Log Analytics werk ruimte voor telemetrie-verzameling, als deze is geïmplementeerd in hetzelfde Azure-abonnement als de resource voor Azure Monitor voor SAP-oplossingen.

### <a name="architecture-highlights"></a>Architectuur kenmerken

Hier volgen de belangrijkste aandachtspunten van de architectuur:
 - **Meerdere** instanties: klanten kunnen monitors maken voor meerdere exemplaren van een bepaald onderdeel type (bijvoorbeeld Hana DB, ha-cluster, micro soft SQL Server) in meerdere SAP-sid's binnen een VNET met één resource van Azure monitor voor SAP-oplossingen.
 - **Meerdere providers** : in het bovenstaande architectuur diagram wordt de SAP Hana provider als voor beeld weer gegeven. Klanten kunnen ook extra providers configureren voor de bijbehorende onderdelen (bijvoorbeeld HANA DB, HA-cluster, micro soft SQL Server) om gegevens te verzamelen van deze onderdelen.
 - **Open source** : de bron code van Azure monitor voor SAP-oplossingen is beschikbaar in [github](https://github.com/Azure/AzureMonitorForSAPSolutions). Klanten kunnen naar de provider code verwijzen en meer weten over het product, bijdragen of feedback delen.
 - **Uitbreidbaar query Framework** : SQL-query's voor het verzamelen van telemetriegegevens worden in [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)geschreven. Extra SQL-query's voor het verzamelen van meer telemetriegegevens kunnen eenvoudig worden toegevoegd. Klanten kunnen specifieke telemetriegegevens aanvragen om toe te voegen aan Azure Monitor voor SAP-oplossingen door feedback te geven via een koppeling aan het einde van dit document of contact op te nemen met hun account team.

## <a name="pricing"></a>Prijzen
Azure Monitor voor SAP-oplossingen is een gratis product (geen licentie kosten). Klanten zijn verantwoordelijk voor de betaling van de kosten voor de onderliggende onderdelen in de beheerde resource groep.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over providers en het maken van uw eerste Azure Monitor voor SAP-oplossingen resource.
 - Meer informatie over [providers](./azure-monitor-providers.md)
 - [Azure Monitor for SAP Solutions implementeren met Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Hebt u vragen over Azure Monitor voor SAP-oplossingen? Raadpleeg de sectie [Veelgestelde vragen](./azure-monitor-faq.md)
