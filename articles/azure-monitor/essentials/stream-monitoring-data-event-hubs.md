---
title: Bewakingsgegevens van Azure streamen naar Event Hub en externe partners
description: Meer informatie over het streamen van uw Azure-bewakingsgegevens naar een Event Hub om de gegevens op te halen in een SIEM- of analysehulpprogramma van een partner.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 7592935afadc88c4b9e0e5f3c5f9c83d42c63209
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768737"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Bewakingsgegevens van Azure streamen naar een Event Hub of externe partner

Azure Monitor biedt een volledige bewakingsoplossing voor volledige stack voor toepassingen en services in Azure, in andere clouds en on-premises. Naast het gebruik van Azure Monitor voor het analyseren van die gegevens en het gebruik ervan voor verschillende bewakingsscenario's, moet u deze mogelijk verzenden naar andere bewakingshulpprogramma's in uw omgeving. In de meeste gevallen is het gebruik van Azure Event Hubs de meest efficiënte methode voor het streamen van [bewakingsgegevens naar Azure Event Hubs.](../../event-hubs/index.yml) In dit artikel vindt u een korte beschrijving van hoe u dit doet en worden vervolgens enkele partners vermeld waar u gegevens kunt verzenden. Sommige hebben speciale integratie met Azure Monitor en kunnen worden gehost in Azure.  

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Voordat u streaming configureert voor een gegevensbron, moet u een [Event Hubs-naamruimte en Event Hub maken.](../../event-hubs/event-hubs-create.md) Deze naamruimte en Event Hub zijn de bestemming voor al uw bewakingsgegevens. Een Event Hubs is een logische groepering van Event Hubs die hetzelfde toegangsbeleid delen, net zoals een opslagaccount afzonderlijke blobs binnen dat opslagaccount heeft. Houd rekening met de volgende details over de Event Hubs-naamruimte en Event Hubs die u gebruikt voor het streamen van bewakingsgegevens:

* Met het aantal doorvoereenheden kunt u de doorvoerschaal voor uw Event Hubs vergroten. Doorgaans is slechts één doorvoereenheid nodig. Als u omhoog moet schalen naarmate het logboekgebruik toeneemt, kunt u het aantal doorvoereenheden voor de naamruimte handmatig verhogen of automatisch inschakelen.
* Met het aantal partities kunt u het verbruik parallelliseren voor veel consumenten. Eén partitie kan maximaal 20 MBps of ongeveer 20.000 berichten per seconde ondersteunen. Afhankelijk van het hulpprogramma dat de gegevens verbruikt, biedt het al dan niet ondersteuning voor het verbruik van meerdere partities. Vier partities is om te beginnen redelijk als u niet zeker weet hoeveel partities u moet instellen.
* U stelt berichtretentie voor uw Event Hub in op ten minste 7 dagen. Als uw verbruikshulpprogramma langer dan een dag uit bedrijf gaat, zorgt u ervoor dat het hulpprogramma verder kan gaan waar het was gebleven voor gebeurtenissen die maximaal 7 dagen oud waren.
* Gebruik de standaard consumergroep voor uw Event Hub. U hoeft geen andere consumentengroepen te maken of een afzonderlijke consumentengroep te gebruiken, tenzij u van plan bent om twee verschillende hulpprogramma's dezelfde gegevens uit dezelfde Event Hub te laten gebruiken.
* Voor het Azure-activiteitenlogboek kiest u een Event Hubs-naamruimte en Azure Monitor maakt een Event Hub in die naamruimte met de naam _insights-logs-operational-logs._ Voor andere logboektypen kunt u een bestaande Event Hub kiezen of Azure Monitor event hub per logboekcategorie maken.
* Uitgaande poort 5671 en 5672 moeten doorgaans worden geopend op de computer of VNET die gegevens van de Event Hub verbruikt.

## <a name="monitoring-data-available"></a>Beschikbare bewakingsgegevens
[Bronnen van bewakingsgegevens voor Azure Monitor](../agents/data-sources.md) beschrijft de verschillende gegevenslagen voor Azure-toepassingen en de soorten bewakingsgegevens die beschikbaar zijn voor elke gegevenslaag. De volgende tabel bevat elk van deze lagen en een beschrijving van hoe die gegevens naar een Event Hub kunnen worden gestreamd. Volg de koppelingen voor meer informatie.

| Laag | Gegevens | Methode |
|:---|:---|:---|
| [Azure-tenant](../agents/data-sources.md#azure-tenant) | Azure Active Directory auditlogboeken | Configureer een diagnostische instelling voor tenants in uw AAD-tenant. Zie  [Zelfstudie: Logboeken Azure Active Directory naar een Azure Event Hub streamen](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) voor meer informatie. |
| [Azure-abonnement](../agents/data-sources.md#azure-subscription) | Azure-activiteitenlogboek | Maak een logboekprofiel om activiteitenlogboekgebeurtenissen te exporteren naar Event Hubs.  Zie [Logboeken van Het Azure-platform streamen Azure Event Hubs](../essentials/resource-logs.md#send-to-azure-event-hubs) voor meer informatie. |
| [Azure-resources](../agents/data-sources.md#azure-resources) | Metrische platformgegevens<br> Resourcelogboeken |Beide typen gegevens worden naar een Event Hub verzonden met behulp van een diagnostische instelling voor resources. Zie [Azure-resourcelogboeken streamen naar een Event Hub](../essentials/resource-logs.md#send-to-azure-event-hubs) voor meer informatie. |
| [Besturingssysteem (gast)](../agents/data-sources.md#operating-system-guest) | Azure-VM's | Installeer de [Azure Diagnostics-extensie](../agents/diagnostics-extension-overview.md) op virtuele Windows- en Linux-machines in Azure. Zie Streaming Azure Diagnostics data in the hot path by using Event Hubs (Streaming van Azure Diagnostics-gegevens in het [hot-pad](../agents/diagnostics-extension-stream-event-hubs.md) met behulp van Event Hubs) voor meer informatie over virtuele Windows-VM's en [De diagnostische Linux-extensie](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) gebruiken om metrische gegevens en logboeken te controleren voor meer informatie over virtuele Linux-VM's. |
| [Toepassingscode](../agents/data-sources.md#application-code) | Application Insights | Application Insights biedt geen directe methode voor het streamen van gegevens naar Event Hubs. U kunt [continue export](../app/export-telemetry.md) van de Application Insights-gegevens naar een opslagaccount instellen en vervolgens een logische app gebruiken om de gegevens naar een Event Hub te verzenden, zoals beschreven in Handmatig streamen met [logische app.](#manual-streaming-with-logic-app) |

## <a name="manual-streaming-with-logic-app"></a>Handmatig streamen met logische app
Voor gegevens die u niet rechtstreeks naar een Event Hub kunt streamen, kunt u naar Azure Storage schrijven en vervolgens een door tijd geactiveerde logische app gebruiken waarmee gegevens worden opgevraagd uit [blobopslag](../../connectors/connectors-create-api-azureblobstorage.md#add-action) en deze als een bericht naar de Event Hub worden [pusht.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnerhulpprogramma's Azure Monitor integratie

Door uw bewakingsgegevens naar een Event Hub te routeren met Azure Monitor kunt u eenvoudig integreren met externe SIEM- en bewakingshulpprogramma's. Voorbeelden van hulpprogramma's Azure Monitor integratie zijn:

| Hulpprogramma | Gehost in Azure | Description |
|:---|:---| :---|
|  IBM QRadar | No | De Microsoft Azure DSM en Microsoft Azure Event Hub Protocol kunnen worden gedownload van [de IBM-ondersteuningswebsite.](https://www.ibm.com/support) Meer informatie over de integratie met Azure vindt u in [QRadar DSM-configuratie.](https://www.ibm.com/docs/en/dsm?topic=options-configuring-microsoft-azure-event-hubs-communicate-qradar) |
| Splunk | No | [Microsoft Azure Add-On voor Splunk](https://splunkbase.splunk.com/app/3757/) is een open source project dat beschikbaar is in Splunkbase. <br><br> Als u een invoegtoepassing niet kunt installeren in uw Splunk-exemplaar, als u bijvoorbeeld een proxy gebruikt of wordt uitgevoerd in Splunk Cloud, kunt u deze gebeurtenissen doorsturen naar de Splunk HTTP Event Collector met behulp van [Azure Function For Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), die wordt geactiveerd door nieuwe berichten in de Event Hub. |
| SumoLogic | No | Instructies voor het instellen van SumoLogic voor het verbruiken van gegevens uit een Event Hub zijn beschikbaar op Logboeken verzamelen voor de [Azure Audit-app van Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | No | De slimme ArcSight Azure Event Hub-connector is beschikbaar als onderdeel van [de ArcSight-verzameling voor slimme connectors.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Syslog-server | No | Als u gegevens rechtstreeks Azure Monitor naar een syslog-server wilt streamen, kunt u een oplossing gebruiken op [basis van een Azure-functie](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | No| Instructies voor het instellen van LogRhythm voor het verzamelen van logboeken van een Event Hub zijn hier [beschikbaar.](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/) 
|Logz.io | Yes | Zie Aan de slag met bewaking en logboekregistratie met [Logz.io voor Java-apps die worden uitgevoerd in Azure voor meer informatie](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Andere partners zijn mogelijk ook beschikbaar. Zie partnerintegraties voor een Azure Monitor volledige Azure Monitor van alle partners en hun [mogelijkheden.](../partners.md)

## <a name="next-steps"></a>Volgende stappen
* [Het activiteitenlogboek archiveren in een opslagaccount](./activity-log.md#legacy-collection-methods)
* [Lees het overzicht van het Azure-activiteitenlogboek](../essentials/platform-logs-overview.md)
* [Een waarschuwing instellen op basis van een gebeurtenis in het activiteitenlogboek](../alerts/alerts-log-webhook.md)
