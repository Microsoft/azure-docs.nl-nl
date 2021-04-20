---
title: Resources voor het maken Azure Sentinel aangepaste connectors | Microsoft Docs
description: Meer informatie over beschikbare resources voor het maken van aangepaste connectors voor Azure Sentinel. Methoden zijn onder andere de Log Analytics-agent en API, Logstash, Logic Apps, PowerShell en Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: a1aaf89624f8d0ab48692629d859f3c1bdb4ba67
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738896"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Resources voor het maken Azure Sentinel aangepaste connectors

Azure Sentinel biedt een breed scala aan [ingebouwde connectors voor Azure-services](connect-data-sources.md)en externe oplossingen en biedt ook ondersteuning voor het opnemen van gegevens uit sommige bronnen zonder een toegewezen connector.

Als u uw gegevensbron niet kunt verbinden met Azure Sentinel met behulp van een van de bestaande beschikbare oplossingen, kunt u overwegen om uw eigen gegevensbronconnector te maken.

Zie de blogpost Azure Sentinel: the [connectors grand (CEF, Syslog, Direct, Agent, Custom](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) en meer) voor een volledige lijst met ondersteunde connectors.

## <a name="compare-custom-connector-methods"></a>Aangepaste connectormethoden vergelijken

De volgende tabel vergelijkt essentiële details over elke methode voor het maken van aangepaste connectors die in dit artikel worden beschreven. Selecteer de koppelingen in de tabel voor meer informatie over elke methode.

|Beschrijving van methode  |Mogelijkheid | Serverloos    |Complexiteit  |
|---------|---------|---------|---------|
|**[Log Analytics-agent](#connect-with-the-log-analytics-agent)** <br>Het beste voor het verzamelen van bestanden van on-premises en IaaS-bronnen   | Alleen bestandsverzameling  |   No      |Beperkt         |
|**[Logstash](#connect-with-logstash)** <br>Het beste voor on-premises en IaaS-bronnen, elke bron waarvoor een invoegvoegcode beschikbaar is en organisaties die al bekend zijn met Logstash  | Beschikbare invoegfuncties, plus aangepaste invoegfuncties, bieden aanzienlijke flexibiliteit.   |   Nee; vereist dat een VM of VM-cluster wordt uitgevoerd           |   Laag; ondersteunt veel scenario's met invoegvoegingen      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Hoge kosten; vermijd voor gegevens met een hoog volume <br>Het beste voor cloudbronnen met een laag volume  | Programmeren zonder code biedt beperkte flexibiliteit, zonder ondersteuning voor het implementeren van algoritmen.<br><br> Als uw vereisten nog niet door een beschikbare actie worden ondersteund, kan het maken van een aangepaste actie complexiteit toevoegen.    |    Yes         |   Laag; eenvoudige ontwikkeling zonder code      |
|**[PowerShell](#connect-with-powershell)** <br>Het beste voor het maken van prototypen en periodieke bestanduploads | Directe ondersteuning voor bestandsverzameling. <br><br>PowerShell kan worden gebruikt voor het verzamelen van meer bronnen, maar vereist codering en configuratie van het script als een service.      |No               |  Beperkt       |
|**[Log Analytics-API](#connect-with-the-log-analytics-api)** <br>Het beste voor ISV's die integratie implementeren en voor unieke verzamelingsvereisten   | Ondersteunt alle mogelijkheden die beschikbaar zijn met de code.  | Afhankelijk van de implementatie           |     Hoog    |
|**[Azure Functions](#connect-with-azure-functions)** Het beste voor cloudbronnen met grote volumes en voor unieke verzamelingsvereisten  | Ondersteunt alle mogelijkheden die beschikbaar zijn met de code.  |  Yes             |     Hoog; programmeerkennis vereist    |
|     |         |                |

> [!TIP]
> Voor vergelijkingen van het gebruik Logic Apps en Azure Functions voor dezelfde connector, zie:
> 
> - [Fastly-logboeken Web Application Firewall in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel GitHub-community): [Logic App-connector](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure Function-connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Verbinding maken met de Log Analytics-agent

Als uw gegevensbron gebeurtenissen in bestanden levert, raden we u aan de log analytics Azure Monitor agent te gebruiken om uw aangepaste connector te maken.

- Zie Aangepaste logboeken verzamelen in Azure Monitor voor [meer Azure Monitor.](../azure-monitor/agents/data-sources-custom-logs.md)

- Zie Aangepaste JSON-gegevensbronnen verzamelen met de [Log Analytics-agent](../azure-monitor/agents/data-sources-json.md)voor Linux in Azure Monitor voor een voorbeeld van deze methode.

## <a name="connect-with-logstash"></a>Verbinding maken met Logstash

Als u bekend bent met [Logstash,](https://www.elastic.co/logstash)kunt u logstash gebruiken met de [logstash-uitvoer-in plug-in](connect-logstash.md) voor Azure Sentinel om uw aangepaste connector te maken.

Met de Azure Sentinel Logstash Output-invoeggebruik kunt u alle logstash-invoer- en filterinvoegingen gebruiken en Azure Sentinel configureren als uitvoer voor een Logstash-pijplijn. Logstash heeft een grote bibliotheek met invoegingen die invoer mogelijk maken uit verschillende bronnen, zoals Event Hubs, Apache Kafka, bestanden, databases en cloudservices. Gebruik filterin plug-ins om gebeurtenissen te parseren, onnodige gebeurtenissen te filteren, waarden te verduineren en meer.

Voor voorbeelden van het gebruik van Logstash als een aangepaste connector, zie:

- Hunting for Capital One Breach TTPs in AWS logs using Azure Sentinel (blog) (Hunting [for Capital One Breach TTPs in AWS-logboeken](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) met behulp van Azure Sentinel (blog)
- [Implementatiehandleiding voor Radware Azure Sentinel](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Zie voor voorbeelden van nuttige Logstash-invoegvoegingen:

- [Cloudwatch-invoerinvoegvoeging](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs-invoeg-app](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Invoegruimte voor Google Cloud Storage-invoer](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub invoegingsinvoegapparaat](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash maakt ook het verzamelen van geschaalde gegevens mogelijk met behulp van een cluster. Zie Using [a load-balanced Logstash VM at scale (Een Logstash-VM](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)met load balanced gebruiken op schaal) voor meer informatie.
>

## <a name="connect-with-logic-apps"></a>Verbinding maken met Logic Apps

Gebruik een [logische Azure-app](../logic-apps/index.yml) om een serverloze, aangepaste connector te maken voor Azure Sentinel.

> [!NOTE]
> Hoewel het maken van serverloze connectors Logic Apps handig kan zijn, kan het Logic Apps voor uw connectors kostbaar zijn voor grote hoeveelheden gegevens.
>
> U wordt aangeraden deze methode alleen te gebruiken voor gegevensbronnen met een laag volume of om uw gegevensuploads te verrijken.
>

1. **Gebruik een van de volgende triggers om uw Logic Apps**:

    |Trigger  |Description  |
    |---------|---------|
    |**Een terugkerende taak**     |   U kunt uw logische app bijvoorbeeld plannen om regelmatig gegevens op te halen uit specifieke bestanden, databases of externe API's. <br>Zie Terugkerende taken en werkstromen maken, plannen en uitvoeren [in Azure Logic Apps.](../connectors/connectors-native-recurrence.md)      |
    |**Triggering op aanvraag**     | Voer uw logische app op aanvraag uit voor handmatige gegevensverzameling en -tests. <br>Zie Logische apps aanroepen, activeren of nesten met [HTTPS-eindpunten voor meer informatie.](../logic-apps/logic-apps-http-endpoint.md)        |
    |**HTTP/S-eindpunt**     |  Aanbevolen voor streaming en als het bronsysteem de gegevensoverdracht kan starten. <br>Zie Service-eindpunten [aanroepen via HTTP of HTTPs voor meer informatie.](../connectors/connectors-native-http.md)       |
    |     |         |

1. **Gebruik een van de Logic App-connectors die informatie lezen om uw gebeurtenissen op te halen.** Bijvoorbeeld:

    - [Verbinding maken met een REST API](/connectors/custom-connectors/)
    - [Verbinding maken met een SQL Server](/connectors/sql/)
    - [Verbinding maken met een bestandssysteem](/connectors/filesystem/)

    > [!TIP]
    > Aangepaste connectors voor REST API's, SQL Servers en bestandssystemen ondersteunen ook het ophalen van gegevens uit on-premises gegevensbronnen. Zie Documentatie over [on-premises gegevensgateway](/connectors/filesystem/) installeren voor meer informatie.
    >

1. **Bereid de informatie voor die u wilt ophalen.**

    Gebruik bijvoorbeeld de actie [JSON parseren](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) om toegang te krijgen tot eigenschappen in JSON-inhoud, zodat u deze eigenschappen kunt selecteren in de lijst met dynamische inhoud wanneer u invoer voor uw logische app opgeeft.

    Zie Gegevensbewerkingen uitvoeren in Azure Logic Apps voor [meer Azure Logic Apps.](../logic-apps/logic-apps-perform-data-operations.md)

1. **Schrijf de gegevens naar Log Analytics.**

    Zie de documentatie voor [Azure Log Analytics-gegevensverzamelaar voor meer](/connectors/azureloganalyticsdatacollector/) informatie.

Zie voor voorbeelden van hoe u een aangepaste connector voor Azure Sentinel kunt Logic Apps:

- [Een gegevenspijplijn maken met de Data Collector-API](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logic App-connector met behulp van een webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel GitHub-community)
- [Uw Microsoft Teams-aanroepen beveiligen met geplande activering](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [Het opnemen van OtX-bedreigingsindicatoren](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) in Azure Sentinel (blog)

## <a name="connect-with-powershell"></a>Verbinding maken met PowerShell

Met [het PowerShell-script Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) kunt u PowerShell gebruiken om gebeurtenissen of contextinformatie te streamen naar Azure Sentinel vanaf de opdrachtregel. Met deze streaming wordt effectief een aangepaste connector gemaakt tussen uw gegevensbron en Azure Sentinel.

Met het volgende script wordt bijvoorbeeld een CSV-bestand geüpload naar Azure Sentinel:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

Het [PowerShell-script Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) maakt gebruik van de volgende parameters:

|Parameter  |Beschrijving  |
|---------|---------|
|**WorkspaceId**     |   Uw Azure Sentinel werkruimte-id, waar u uw gegevens opslaat.  [Zoek uw werkruimte-id en -sleutel.](#find-your-workspace-id-and-key)  |
|**WorkspaceKey**     |   De primaire of secundaire sleutel voor Azure Sentinel werkruimte waarin u uw gegevens opslaat. [Zoek uw werkruimte-id en -sleutel.](#find-your-workspace-id-and-key)  |
|**LogTypeName**     |    De naam van de aangepaste logboektabel waarin u de gegevens wilt opslaan. Er wordt automatisch **_CL** achtervoegsel toegevoegd aan het einde van de tabelnaam.  |
|**AddComputerName**     |   Wanneer deze parameter bestaat, voegt het script de huidige computernaam toe aan elke logboekrecord, in een veld met de **naam Computer**.      |
|**GelabeldAzureResourceId**     | Als deze parameter bestaat, koppelt het script alle geüploade logboekrecords aan de opgegeven Azure-resource. <br><br>Deze associatie maakt geüploade logboekrecords mogelijk voor query's in resourcecontext en voldoet aan op resources gericht, op rollen gebaseerd toegangsbeheer.       |
|**AdditionalDataTaggingName**     |      Als deze parameter bestaat, voegt het script een ander veld toe aan elke logboekrecord, met de geconfigureerde naam en de waarde die is geconfigureerd voor de parameter **AdditionalDataTaggingValue.** <br><br>In dit geval mag **AdditionalDataTaggingValue** niet leeg zijn. |
|**AdditionalDataTaggingValue**     |   Als deze parameter bestaat, voegt het script een ander veld toe aan elke logboekrecord, met de geconfigureerde waarde en de veldnaam die is geconfigureerd voor de parameter **AdditionalDataTaggingName.** <br><br>Als de **parameter AdditionalDataTaggingName** leeg is, maar er een waarde is geconfigureerd, is de standaardveldnaam **DataTagging.**       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Uw werkruimte-id en -sleutel zoeken

Zoek de details voor de parameters **WorkspaceID** en **WorkspaceKey** in Azure Sentinel:

1. Selecteer Azure Sentinel instellingen **aan de** linkerkant en selecteer vervolgens het tabblad **Werkruimte-instellingen.**

1. Selecteer **onder Aan de slag met Log Analytics**  >  **1 Verbinding maken met een gegevensbron** de optie **Windows- en Linux-agentsbeheer.**

1. Zoek uw werkruimte-id, primaire sleutel en secundaire sleutel op de **tabbladen Windows-servers.**
## <a name="connect-with-the-log-analytics-api"></a>Verbinding maken met de Log Analytics-API

U kunt gebeurtenissen streamen naar Azure Sentinel behulp van de Log Analytics Data Collector-API om rechtstreeks een RESTful-eindpunt aan te roepen.

Hoewel het rechtstreeks aanroepen van een RESTful-eindpunt meer programmering vereist, biedt het ook meer flexibiliteit.

Zie de Log [Analytics-gegevensverzamelaar-API](../azure-monitor/logs/data-collector-api.md)voor meer informatie, met name de volgende voorbeelden:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Verbinding maken met Azure Functions

Gebruik Azure Functions samen met een RESTful-API en verschillende programmeertalen, zoals [PowerShell,](../azure-functions/functions-reference-powershell.md)om een serverloze aangepaste connector te maken.

Zie voor voorbeelden van deze methode:

- [Uw VMware Carbon Black Cloud Endpoint Standard verbinden met Azure Sentinel azure-functie](connect-vmware-carbon-black.md)
- [Uw Okta Single-Sign-On verbinden met Azure Sentinel azure-functie](connect-okta-single-sign-on.md)
- [Uw Proofpoint TAP verbinden met Azure Sentinel azure-functie](connect-proofpoint-tap.md)
- [Uw Qualys-VM verbinden met Azure Sentinel azure-functie](connect-qualys-vm.md)
- [XML-, CSV- of andere indelingen van gegevens opnemen](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Zoomen bewaken met Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Een functie-app implementeren om Office 365-gegevens Beheer API in](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) Azure Sentinel (Azure Sentinel GitHub-community)

## <a name="parse-your-custom-connector-data"></a>Uw aangepaste connectorgegevens parseren

U kunt de ingebouwde parseringstechniek van uw aangepaste connector gebruiken om de relevante informatie te extraheren en de relevante velden in te vullen in Azure Sentinel.

Bijvoorbeeld:

- **Als u Logstash hebt gebruikt,** gebruikt u de [Grok-filterinvoeging](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) om uw gegevens te parseren.
- **Als u een Azure-functie hebt gebruikt,** parseert u uw gegevens met code. Zie [Parsers voor meer informatie.](normalization.md#parsers)

Azure Sentinel ondersteuning voor parseren tijdens het uitvoeren van query's. Met parseren tijdens het uitvoeren van query's kunt u gegevens in de oorspronkelijke indeling pushen en vervolgens op aanvraag parseren wanneer dat nodig is.

Parseren tijdens het uitvoeren van query's betekent ook dat u de exacte structuur van uw gegevens niet van tevoren hoeft te kennen, wanneer u uw aangepaste connector maakt of zelfs de informatie die u moet extraheren. In plaats daarvan kunt u uw gegevens op elk moment parseren, zelfs tijdens een onderzoek.

> [!NOTE]
> Het bijwerken van uw parser is ook van toepassing op gegevens die u al hebt opgenomen in Azure Sentinel.
> 
## <a name="next-steps"></a>Volgende stappen

Gebruik de gegevens die zijn opgenomen in Azure Sentinel om uw omgeving te beveiligen met een van de volgende processen:

- [Inzicht krijgen in waarschuwingen](quickstart-get-visibility.md)
- [Uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)
- [Incidenten onderzoeken](tutorial-investigate-cases.md)
- [Bedreigingen detecteren](tutorial-detect-threats-built-in.md)
- [Bedreigingspreventie automatiseren](tutorial-respond-threats-playbook.md)
- [Bedreigingen opsporen](hunting.md)
