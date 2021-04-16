---
title: Azure Monitor for SAP Solutions overzicht en architectuur| Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Monitor voor SAP-oplossingen
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 45085c910974402a968075a66087a04fb30e8bd9
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576200"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor voor SAP-oplossingen (preview)

## <a name="overview"></a>Overzicht

Azure Monitor for SAP Solutions is een systeemeigen Azure-bewakingsproduct voor klanten die hun SAP-landschap uitvoeren in Azure. Het product werkt met zowel [SAP on Azure Virtual Machines](./hana-get-started.md) als [SAP on Azure Large Instances.](./hana-overview-architecture.md)
Met Azure Monitor for SAP Solutions kunnen klanten telemetriegegevens van de Azure-infrastructuur en -databases op één centrale locatie verzamelen en telemetriegegevens visueel correleren voor snellere probleemoplossing.

Azure Monitor for SAP Solutions wordt aangeboden via Azure Marketplace. Het biedt een eenvoudige, intuïtieve installatie-ervaring en duurt slechts enkele klikken om de resource voor Azure Monitor for SAP Solutions (ook wel **SAP Monitor-resource genoemd) te implementeren.**

Klanten kunnen verschillende onderdelen van een SAP-landschap bewaken, zoals Azure Virtual Machines, een cluster met hoge beschikbaarheid, SAP HANA-database, SAP NetWeaver, bijvoorbeeld door de bijbehorende **provider** voor dat onderdeel toe te voegen.

Ondersteunde infrastructuur:

- Virtuele Azure-machine
- Azure Large Instance

Ondersteunde databases:
- SAP HANA-database
- Microsoft SQL Server

Azure Monitor for SAP Solutions maakt gebruik van de kracht van [Azure Monitor](../../../azure-monitor/overview.md) functies zoals Log Analytics en [Workbooks](../../../azure-monitor/visualize/workbooks-overview.md) om meer bewakingsmogelijkheden te bieden. Klanten kunnen aangepaste [visualisaties](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) maken door de standaardwerkboeken van Azure Monitor for SAP Solutions te [](../../../azure-monitor/alerts/tutorial-response.md) bewerken, aangepaste query's te schrijven [](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) en aangepaste waarschuwingen te maken met behulp van de Azure Log Analytics-werkruimte, te profiteren van een flexibele bewaarperiode en [bewakingsgegevens](../../../azure-monitor/logs/log-analytics-tutorial.md) te verbinden met hun ticketsysteem.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Welke gegevens worden Azure Monitor sap-oplossingen verzameld?

Gegevensverzameling in Azure Monitor for SAP Solutions is afhankelijk van de providers die door klanten zijn geconfigureerd. Tijdens de openbare preview worden de volgende gegevens verzameld.

Telemetrie van Pacemaker-cluster met hoge beschikbaarheid:
- Status van knooppunt, resource en SBD-apparaat
- Locatiebeperkingen voor Pacemaker
- Quorumstemmen en ringstatus
- [Overige](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetrie:
- CPU-, geheugen-, schijf- en netwerkgebruik
- HANA-systeemreplicatie (HSR)
- HANA-back-up
- HANA-hoststatus
- Server- en naamserverfuncties indexeren

Microsoft SQL Server-telemetrie:
- CPU, geheugen, schijfgebruik
- Hostnaam, naam van SQL-exemplaar, SAP-systeem-id
- Batchaanvragen, compilaties en de levensverwachting van pagina's gedurende een periode
- Top 10 duurste SQL-instructies gedurende een periode
- Top 12 grootste tabel in het SAP-systeem
- Problemen die zijn vastgelegd in het SQL Server foutenlogboek
- Processen en SQL-wachtstatistieken gedurende een bepaalde periode blokkeren

Telemetrie van besturingssysteem (Linux) 
- CPU-gebruik, het aantal forks, de processen die worden uitgevoerd en geblokkeerd. 
- Geheugengebruik en distributie tussen gebruikt, in cache opgeslagen, gebufferd. 
- Wisselgebruik, wisselfrequentie en wisselsnelheid. 
- Bestandssysteemgebruik, aantal bytes gelezen en geschreven per blokapparaat. 
- Lees-/schrijflatentie per blokapparaat. 
- Doorlopend aantal I/O's, permanent geheugen gelezen/geschreven bytes. 
- Netwerkpakketten in/uit, netwerk bytes in/uit 

SAP NetWeaver-telemetrie:

- Beschikbaarheid van SAP-systeem en toepassingsserver, inclusief beschikbaarheid van exemplaarproces van Dispatcher, ICM, Gateway, Message Server, Enqueue Server, IGS Watchdog
- Statistieken en trends over het gebruik van werkproces
- Statistieken en trends voor vergrendeling in de gaten houden
- Statistieken en trends over het gebruik van wachtrijen

## <a name="data-sharing-with-microsoft"></a>Gegevens delen met Microsoft

Azure Monitor for SAP Solutions verzamelt systeemmetagegevens voor verbeterde ondersteuning voor onze SAP on Azure klanten. Er wordt geen PII/EUII verzameld.
Klanten kunnen het delen van gegevens met Microsoft inschakelen op het moment dat ze Azure Monitor for SAP Solutions resource maken door Delen te *kiezen* in de vervolgkeuzekeuze.
Het wordt ten zeerste aanbevolen dat klanten gegevens delen, omdat het Microsoft-ondersteunings- en engineeringteams meer informatie geeft over de klantomgeving en betere ondersteuning biedt aan onze bedrijfskritieke SAP on Azure klanten.

## <a name="architecture-overview"></a>Overzicht van de architectuur

Op hoog niveau wordt in het volgende diagram uitgelegd hoe Azure Monitor for SAP Solutions telemetrie verzamelt van SAP HANA database. De architectuur is afhankelijk van of SAP HANA is geïmplementeerd in Azure Virtual Machines of Azure Large Instances.

![Azure Monitor voor SAP-oplossingsarchitectuur](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

De belangrijkste onderdelen van de architectuur zijn:
- Azure Portal: het beginpunt voor klanten. Klanten kunnen navigeren naar marketplace binnen Azure Portal en deze Azure Monitor for SAP Solutions
- Azure Monitor for SAP Solutions resource: een landingsplaats waar klanten bewakingstelemetrie kunnen bekijken
- Beheerde resourcegroep: automatisch geïmplementeerd als onderdeel van de implementatie Azure Monitor for SAP Solutions resource. De resources die in de beheerde resourcegroep zijn geïmplementeerd, helpen bij het verzamelen van telemetrie. Belangrijke resources die zijn geïmplementeerd en hun doel zijn:
   - Virtuele Azure-machine: ook wel *collector-VM genoemd.* Dit is een Standard_B2ms VM. Het belangrijkste doel van deze VM is het hosten van de *nettolading voor bewaking.* Het bewaken van de nettolading verwijst naar de logica van het verzamelen van telemetrie van de bronsystemen en het overbrengen van de verzamelde gegevens naar het bewakingsraamwerk. In het bovenstaande diagram bevat de nettolading van de bewaking de logica om verbinding te maken met SAP HANA database via SQL-poort.
   - [Azure Key Vault:](../../../key-vault/general/basic-concepts.md)deze resource wordt geïmplementeerd om veilig de referenties van SAP HANA database op te slaan en informatie over providers op [te slaan.](./azure-monitor-providers.md)
   - Log Analytics-werkruimte: de bestemming waar de telemetriegegevens zich bevinden.
      - Visualisatie is gebaseerd op telemetrie in Log Analytics met behulp [van Azure Workbooks.](../../../azure-monitor/visualize/workbooks-overview.md) Klanten kunnen visualisaties aanpassen. Klanten kunnen hun werkmappen of specifieke visualisatie in Workbooks ook vastmaken aan het Azure-dashboard voor automatischerefresh met de laagste granulariteit van 30 minuten.
      - Klanten kunnen hun bestaande werkruimte binnen hetzelfde abonnement als de SAP Monitor-resource gebruiken door deze optie te kiezen op het moment van implementatie.
      - Klanten kunnen Kusto-querytaal (KQL) gebruiken om query's uit te voeren op de onbewerkte tabellen in de Log Analytics-werkruimte. [](../../../azure-monitor/logs/log-query-overview.md) Bekijk aangepaste *logboeken.*

> [!Note]
> Klanten zijn verantwoordelijk voor het patchen en onderhouden van de VM, geïmplementeerd in de beheerde resourcegroep.

> [!Tip]
> Klanten kunnen ervoor kiezen om een bestaande Log Analytics-werkruimte te gebruiken voor het verzamelen van telemetriegegevens, als deze is geïmplementeerd binnen hetzelfde Azure-abonnement als de resource voor Azure Monitor for SAP Solutions.

### <a name="architecture-highlights"></a>Architectuur-highlights

Hier volgen de belangrijkste highlights van de architectuur:
 - Meerdere exemplaren: klanten kunnen een monitor maken voor meerdere exemplaren van een bepaald onderdeeltype (bijvoorbeeld HANA DB, **HA-cluster,** Microsoft SQL-server, SAP NetWeaver) op meerdere SAP-SID's binnen een VNET met één resource van Azure Monitor for SAP Solutions.
 - **Multi-provider:** het bovenstaande architectuurdiagram toont de SAP HANA provider als voorbeeld. Op dezelfde manier kunnen klanten meer providers configureren voor bijbehorende onderdelen (bijvoorbeeld HANA DB, HA-cluster, Microsoft SQL Server, SAP NetWeaver) om gegevens van deze onderdelen te verzamelen.
 - **Open source:** de broncode van Azure Monitor for SAP Solutions is beschikbaar in [GitHub.](https://github.com/Azure/AzureMonitorForSAPSolutions) Klanten kunnen verwijzen naar de providercode en meer informatie krijgen over het product, bijdragen leveren of feedback delen.
 - **Extensible query framework:** SQL-query's voor het verzamelen van telemetriegegevens worden geschreven in [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Meer SQL-query's voor het verzamelen van meer telemetriegegevens kunnen eenvoudig worden toegevoegd. Klanten kunnen aanvragen om specifieke telemetriegegevens toe te voegen aan Azure Monitor for SAP Solutions, door feedback te geven via een koppeling aan het einde van dit document of door contact op te nemen met hun accountteam.

## <a name="pricing"></a>Prijzen
Azure Monitor for SAP Solutions is een gratis product (geen licentiekosten). Klanten zijn verantwoordelijk voor het betalen van de kosten voor de onderliggende onderdelen in de beheerde resourcegroep.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over providers en uw eerste resource Azure Monitor for SAP Solutions maken.
 - Meer informatie over [providers](./azure-monitor-providers.md)
 - [Azure Monitor for SAP Solutions implementeren met Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Hebt u vragen over Azure Monitor for SAP Solutions? Raadpleeg de [sectie Veelgestelde](./azure-monitor-faq.md) vragen
