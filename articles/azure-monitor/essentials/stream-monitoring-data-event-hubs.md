---
title: Azure-bewakings gegevens streamen naar Event Hub en externe partners
description: Meer informatie over het streamen van uw Azure-bewakings gegevens naar een Event Hub om de gegevens op te halen in een partner SIEM of een analyse programma.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.subservice: ''
ms.openlocfilehash: db8f8628f77ef2a04a7e6d42d6470f254e458e01
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708081"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Azure-bewakings gegevens streamen naar een Event Hub of externe partner

Azure Monitor biedt een volledige stack bewakings oplossing voor toepassingen en services in azure, in andere Clouds en on-premises. Naast het gebruik van Azure Monitor voor het analyseren van die gegevens en het gebruiken voor verschillende bewakings scenario's, moet u deze mogelijk verzenden naar andere controle hulpprogramma's in uw omgeving. In de meeste gevallen is het gebruik van [Azure Event hubs](../../event-hubs/index.yml)de meest efficiënte methode voor het streamen van bewakings gegevens naar externe hulpprogram ma's. In dit artikel vindt u een korte beschrijving van hoe u dit doet en een lijst met een aantal partners waar u gegevens kunt verzenden. Sommige hebben speciale integratie met Azure Monitor en kunnen worden gehost op Azure.  

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Voordat u streaming voor een gegevens bron configureert, moet u [een event hubs naam ruimte en Event hub maken](../../event-hubs/event-hubs-create.md). Deze naam ruimte en Event Hub is het doel voor al uw bewakings gegevens. Een Event Hubs naam ruimte is een logische groepering van Event hubs die hetzelfde toegangs beleid delen, net zoals een opslag account afzonderlijke blobs in dat opslag account heeft. Houd rekening met de volgende details over de Event hubs-naam ruimte en Event hubs die u gebruikt voor het streamen van bewakings gegevens:

* Met het aantal doorvoer eenheden kunt u de doorvoer schaal voor uw event hubs verhogen. Normaal gesp roken is er slechts één doorvoer eenheid nodig. Als u de schaal van het logboek gebruik wilt verg Roten, kunt u het aantal doorvoer eenheden voor de naam ruimte hand matig verhogen of de automatische inflatie inschakelen.
* Met het aantal partities kunt u het verbruik van verschillende gebruikers parallelliseren. Eén partitie kan Maxi maal 20MBps of ongeveer 20.000 berichten per seconde ondersteunen. Afhankelijk van het hulp programma dat de gegevens gebruikt, kan het al dan niet voor komen dat er meerdere partities worden verbruikt. Vier partities zijn redelijk om te beginnen met als u niet zeker weet hoeveel partities er moeten worden ingesteld.
* U stelt de Bewaar periode voor berichten in op uw Event Hub ten minste 7 dagen. Als uw verbruikte hulp programma meer dan een dag uitvalt, zorgt u ervoor dat het hulp programma kan ophalen waar het niet langer dan zeven dagen oud was.
* U moet de standaard Consumer groep voor uw Event Hub gebruiken. U hoeft geen andere consumenten groepen te maken of een afzonderlijke consumenten groep te gebruiken, tenzij u van plan bent om twee verschillende hulpprogram ma's dezelfde gegevens van dezelfde Event Hub te laten gebruiken.
* Voor het Azure-activiteiten logboek kiest u een Event Hubs naam ruimte en Azure Monitor maakt u een Event Hub binnen die naam ruimte met de naam _Insights-logboeken: operationele logboeken_. Voor andere logboek typen kunt u een bestaande Event Hub kiezen of Azure Monitor een Event Hub per logboek categorie maken.
* De uitgaande poort 5671 en 5672 moeten normaal gesp roken worden geopend op de computer of het VNET dat gegevens uit het Event Hub gebruiken.

## <a name="monitoring-data-available"></a>Beschik bare bewakings gegevens
[Bronnen van bewakings gegevens voor Azure monitor](../agents/data-sources.md) beschrijven de verschillende gegevens lagen voor Azure-toepassingen en de soorten beschik bare bewakings gegevens voor elk. De volgende tabel geeft een lijst van elk van deze lagen en een beschrijving van hoe deze gegevens naar een Event Hub kunnen worden gestreamd. Volg de onderstaande koppelingen voor meer informatie.

| Laag | Gegevens | Methode |
|:---|:---|:---|
| [Azure-tenant](../agents/data-sources.md#azure-tenant) | Controle logboeken Azure Active Directory | Configureer een diagnostische instelling voor tenants op uw AAD-Tenant. Zie  [zelf studie: Stream Azure Active Directory logboeken naar een Azure Event hub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) voor meer informatie. |
| [Azure-abonnement](../agents/data-sources.md#azure-subscription) | Azure-activiteitenlogboek | Maak een logboek profiel om gebeurtenissen in het activiteiten logboek te exporteren naar Event Hubs.  Zie [Azure platform-logboeken streamen naar azure Event hubs](../essentials/resource-logs.md#send-to-azure-event-hubs) voor meer informatie. |
| [Azure-resources](../agents/data-sources.md#azure-resources) | Metrische platformgegevens<br> Resourcelogboeken |Beide typen gegevens worden verzonden naar een Event Hub met behulp van de diagnostische instelling van de resource. Zie [Azure-resource logboeken streamen naar een event hub](../essentials/resource-logs.md#send-to-azure-event-hubs) voor meer informatie. |
| [Besturings systeem (gast)](../agents/data-sources.md#operating-system-guest) | Azure-VM's | Installeer de [Azure Diagnostics-extensie](../agents/diagnostics-extension-overview.md) op virtuele Windows-en Linux-machines in Azure. Zie [Azure Diagnostics gegevens streamen in het warme pad met behulp van Event hubs](../agents/diagnostics-extension-stream-event-hubs.md) voor meer informatie over virtuele Windows-machines en [Linux diagnostische uitbrei ding voor het bewaken van gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) voor meer informatie over Linux-vm's. |
| [Toepassings code](../agents/data-sources.md#application-code) | Application Insights | Application Insights biedt geen directe methode voor het streamen van gegevens naar Event hubs. U kunt [continue export](../app/export-telemetry.md) van de Application Insights gegevens naar een opslag account instellen en vervolgens een logische app gebruiken om de gegevens naar een event hub te verzenden zoals beschreven in [hand matige streaming met logische app](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Hand matige streaming met logische app
Voor gegevens die u niet rechtstreeks naar een Event Hub kunt streamen, schrijft u naar Azure Storage en gebruikt u vervolgens een met tijd geactiveerde logische app die [gegevens uit de Blob-opslag ophaalt](../../connectors/connectors-create-api-azureblobstorage.md#add-action) en [deze als een bericht naar de Event hub pusht](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partner hulpprogramma's met Azure Monitor-integratie

Door uw bewakings gegevens te routeren naar een Event Hub met Azure Monitor kunt u eenvoudig integreren met externe hulpprogram ma's voor SIEM en controle. Voor beelden van hulpprogram ma's met Azure Monitor-integratie zijn onder andere:

| Hulpprogramma | Gehost in azure | Beschrijving |
|:---|:---| :---|
|  IBM QRadar | Nee | Het Microsoft Azure DSM en Microsoft Azure Event hub-protocol kunnen worden gedownload via [de website van IBM-ondersteuning](https://www.ibm.com/support). Meer informatie over de integratie met Azure vindt u in de [configuratie van QRADAR DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Nee | [Microsoft Azure Add-On voor Splunk](https://splunkbase.splunk.com/app/3757/) is een open-source project dat beschikbaar is in Splunkbase. <br><br> Als u een invoeg toepassing in uw Splunk-exemplaar niet kunt installeren, als u bijvoorbeeld een proxy gebruikt of wordt uitgevoerd op Splunk Cloud, kunt u deze gebeurtenissen door sturen naar de Splunk HTTP-gebeurtenis verzamelaar met de [Azure-functie voor Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), die wordt geactiveerd door nieuwe berichten in de Event hub. |
| SumoLogic | Nee | Instructies voor het instellen van SumoLogic om gegevens van een Event Hub te gebruiken, zijn beschikbaar in [Logboeken verzamelen voor de Azure audit-app vanuit Event hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Nee | De ArcSight Azure Event hub Smart connector is beschikbaar als onderdeel van [de verzameling van de ArcSight slimme connector](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Syslog-server | Nee | Als u Azure Monitor gegevens rechtstreeks naar een syslog-server wilt streamen, kunt u een [oplossing gebruiken op basis van een Azure-functie](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | Nee| Instructies voor het instellen van LogRhythm voor het verzamelen van logboeken van een Event Hub zijn [hier](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)beschikbaar. 
|Logz.io | Ja | Zie voor meer informatie aan de slag [met bewaking en logboek registratie met behulp van Logz.io voor java-apps die worden uitgevoerd op Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Er zijn mogelijk ook andere partners beschikbaar. Zie [Azure monitor partner integraties](../partners.md)voor een volledig overzicht van alle Azure monitor partners en hun mogelijkheden.

## <a name="next-steps"></a>Volgende stappen
* [Het activiteiten logboek archiveren in een opslag account](./activity-log.md#legacy-collection-methods)
* [Lees het overzicht van het Azure-activiteiten logboek](../essentials/platform-logs-overview.md)
* [Een waarschuwing instellen op basis van een gebeurtenis in een activiteiten logboek](../alerts/alerts-log-webhook.md)