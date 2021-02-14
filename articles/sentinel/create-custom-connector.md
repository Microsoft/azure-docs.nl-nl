---
title: Resources voor het maken van aangepaste Azure Sentinel-connectors | Microsoft Docs
description: Meer informatie over beschik bare bronnen voor het maken van aangepaste connectors voor Azure Sentinel. Methoden zijn de Log Analytics agent en API, Logstash, Logic Apps, Power shell en Azure Functions.
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
ms.openlocfilehash: 90646339ef41d0629a4d1ce8efed4b50427d3b2b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417906"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Resources voor het maken van aangepaste Azure Sentinel-connectors

Azure Sentinel biedt een breed scala aan [ingebouwde connectors voor Azure-Services en externe oplossingen](connect-data-sources.md), en biedt ook ondersteuning voor het opnemen van gegevens uit sommige bronnen zonder een speciale connector.

Als u de gegevens bron niet kunt verbinden met Azure Sentinel met behulp van een van de bestaande oplossingen die beschikbaar zijn, kunt u uw eigen gegevens bron connector maken.

Zie voor een volledige lijst met ondersteunde connectors het blog bericht [Azure Sentinel: The Connects Grand (CEF, syslog, direct, agent, aangepast en meer)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) .

## <a name="compare-custom-connector-methods"></a>Aangepaste connector methoden vergelijken

De volgende tabel vergelijkt de essentiële details van elke methode voor het maken van aangepaste connectors die in dit artikel worden beschreven. Selecteer de koppelingen in de tabel voor meer informatie over elke methode.

|Beschrijving van methode  |Mogelijkheid | Serverloos    |Complexiteit  |
|---------|---------|---------|---------|
|**[Log Analytics-agent](#connect-with-the-log-analytics-agent)** <br>Het beste voor het verzamelen van bestanden van on-premises en IaaS bronnen   | Alleen bestanden verzamelen  |   No      |Beperkt         |
|**[Logstash](#connect-with-logstash)** <br>De beste optie voor on-premises en IaaS-bronnen, een bron waarvoor een invoeg toepassing beschikbaar is en organisaties die al bekend zijn met Logstash  | Beschik bare invoeg toepassingen, plus aangepaste invoeg toepassing, bieden een aanzienlijke flexibiliteit.   |   Geen vereist dat een VM of VM-cluster wordt uitgevoerd           |   Gebrek ondersteunt veel scenario's met plugins      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Hoge kosten; Vermijd gegevens met een hoog volume <br>Geschikt voor Cloud bronnen met weinig volume  | Programmeerloze programmering biedt beperkte flexibiliteit, zonder ondersteuning voor het implementeren van algoritmen.<br><br> Als geen beschik bare actie uw vereisten al ondersteunt, kan het maken van een aangepaste actie leiden tot complexiteit.    |    Yes         |   Gebrek eenvoudige, code ontwikkeling      |
|**[PowerShell](#connect-with-powershell)** <br>Beste voor het maken van prototypen en periodieke uploads van bestanden | Rechtstreekse ondersteuning voor bestands verzameling. <br><br>Power shell kan worden gebruikt voor het verzamelen van meer bronnen, maar vereist het coderen en configureren van het script als een service.      |No               |  Beperkt       |
|**[Log Analytics-API](#connect-with-the-log-analytics-api)** <br>Beste voor Isv's die integratie implementeren en voor unieke verzamelings vereisten   | Ondersteunt alle mogelijkheden die beschikbaar zijn met de code.  | Is afhankelijk van de implementatie           |     Hoog    |
|**[Azure functions](#connect-with-azure-functions)** Geschikt voor Cloud bronnen met een hoog volume en voor unieke verzamelings vereisten  | Ondersteunt alle mogelijkheden die beschikbaar zijn met de code.  |  Yes             |     Hogesnelheidsnet vereist programmeer kennis    |
|     |         |                |

> [!TIP]
> Zie voor vergelijkingen van het gebruik van Logic Apps en Azure Functions voor dezelfde connector:
> 
> - [Snelle opname van Web Application firewall Logboeken in azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel github Community): [logische app-connector](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure function-connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Verbinding maken met de Log Analytics-agent

Als uw gegevens bron gebeurtenissen in bestanden levert, raden wij u aan de Azure Monitor Log Analytics agent te gebruiken om uw aangepaste connector te maken.

- Zie [aangepaste logboeken verzamelen in azure monitor](/azure/azure-monitor/platform/data-sources-custom-logs)voor meer informatie.

- Zie voor een voor beeld van deze methode [aangepaste JSON-gegevens bronnen verzamelen met de log Analytics agent voor Linux in azure monitor](/azure/azure-monitor/platform/data-sources-json).

## <a name="connect-with-logstash"></a>Verbinding maken met Logstash

Als u bekend bent met [Logstash](https://www.elastic.co/logstash), kunt u Logstash gebruiken met de [Logstash output-invoeg toepassing voor Azure Sentinel](connect-logstash.md) om uw aangepaste connector te maken.

Met de Azure Sentinel Logstash-uitvoer-invoeg toepassing kunt u alle Logstash-invoer-en filter-invoeg toepassingen gebruiken en Azure Sentinel configureren als de uitvoer voor een Logstash-pijp lijn. Logstash heeft een grote bibliotheek met invoeg toepassingen die invoer vanuit verschillende bronnen mogelijk maken, zoals Event Hubs, Apache Kafka, bestanden, data bases en Cloud Services. Gebruik filter-invoeg toepassingen voor het parseren van gebeurtenissen, het filteren van overbodige gebeurtenissen, het opwaarderen van waarden en nog veel meer.

Zie voor voor beelden van het gebruik van Logstash als een aangepaste connector:

- [Jacht met een inbreuk TTPs in AWS-logboeken met behulp van Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog)
- [Implementatie handleiding voor Radware Azure](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Voor voor beelden van handige Logstash-invoeg toepassingen raadpleegt u:

- [Cloudwatch-invoer-invoeg toepassing](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Invoeg toepassing voor Azure Event Hubs](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Invoeg toepassing voor invoer van Google Cloud Storage](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Invoer-plugin Google_pubsub](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash maakt ook het verzamelen van gegevens geschaald met behulp van een cluster. Zie [een LOGSTASH VM met gelijke taak verdeling op schaal gebruiken](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)voor meer informatie.
>

## <a name="connect-with-logic-apps"></a>Verbinding maken met Logic Apps

Een [Azure Logic-app](/azure/logic-apps/) gebruiken om een serverloze, aangepaste connector voor Azure Sentinel te maken.

> [!NOTE]
> Bij het maken van serverloze connectors met behulp van Logic Apps kan het handig zijn om de Logic Apps voor uw connectors te gebruiken voor grote hoeveel heden gegevens.
>
> U wordt aangeraden deze methode alleen te gebruiken voor gegevens bronnen met een laag volume of om uw gegevens te verrijken.
>

1. **Gebruik een van de volgende triggers om uw Logic apps te starten**:

    |Trigger  |Description  |
    |---------|---------|
    |**Een terugkerende taak**     |   Plan bijvoorbeeld uw logische app om gegevens regel matig op te halen uit specifieke bestanden, data bases of externe Api's. <br>Zie [terugkerende taken en werk stromen maken, plannen en uitvoeren in azure Logic apps](/azure/connectors/connectors-native-recurrence)voor meer informatie.      |
    |**Activeren op aanvraag**     | Voer uw logische app op aanvraag uit voor het hand matig verzamelen en testen van gegevens. <br>Zie  [Logic apps aanroepen, activeren of nesten met behulp van HTTPS-eind punten](/azure/logic-apps/logic-apps-http-endpoint)voor meer informatie.        |
    |**HTTP/S-eind punt**     |  Aanbevolen voor streaming en als het bron systeem de gegevens overdracht kan starten. <br>Zie [service-eind punten aanroepen via http of https](/azure/connectors/connectors-native-http)voor meer informatie.       |
    |     |         |

1. **Gebruik een van de logische app-connectors die informatie lezen om uw gebeurtenissen op te halen**. Bijvoorbeeld:

    - [Verbinding maken met een REST API](/connectors/custom-connectors/)
    - [Verbinding maken met een SQL Server](/connectors/sql/)
    - [Verbinding maken met een bestands systeem](/connectors/filesystem/)

    > [!TIP]
    > Aangepaste connectors voor REST-Api's, SQL-servers en bestands systemen bieden ook ondersteuning voor het ophalen van gegevens uit on-premises gegevens bronnen. Zie documentatie [over on-premises gegevens gateway installeren](/connectors/filesystem/) voor meer informatie.
    >

1. **Bereid de informatie voor die u wilt ophalen**.

    Gebruik bijvoorbeeld de [actie JSON parseren](/azure/logic-apps/logic-apps-perform-data-operations#parse-json-action) om toegang te krijgen tot eigenschappen in JSON-inhoud, zodat u deze eigenschappen kunt selecteren in de lijst met dynamische inhoud wanneer u invoer opgeeft voor uw logische app.

    Zie [gegevens bewerkingen uitvoeren in azure Logic apps](/azure/logic-apps/logic-apps-perform-data-operations)voor meer informatie.

1. **Schrijf de gegevens naar log Analytics**.

    Zie de documentatie van [Azure log Analytics Data Collector](/connectors/azureloganalyticsdatacollector/) voor meer informatie.

Zie voor voor beelden van hoe u een aangepaste connector voor Azure-Sentinel kunt maken met behulp van Logic Apps:

- [Een gegevens pijplijn maken met de Data Collector-API](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto prisma Logic app-connector met een webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel github Community)
- [Uw micro soft teams-vergaderingen beveiligen met geplande activering](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [ALIENVAULT OTX Threat Indica tors opnemen in azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)
- [Proofpoint verzenden door te tikken op Logboeken naar Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (blog)


## <a name="connect-with-powershell"></a>Verbinding maken met PowerShell

Met het [Power shell-script upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) kunt u Power shell gebruiken voor het streamen van gebeurtenissen of context informatie naar Azure Sentinel vanaf de opdracht regel. Deze streaming maakt effectief een aangepaste connector tussen uw gegevens bron en Azure Sentinel.

Het volgende script uploadt bijvoorbeeld een CSV-bestand naar Azure Sentinel:

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

Het [Power shell-script script upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) maakt gebruik van de volgende para meters:

|Parameter  |Beschrijving  |
|---------|---------|
|**WorkspaceId**     |   De ID van uw Azure Sentinel-werk ruimte, waar u uw gegevens opslaat.  [Zoek uw werk ruimte-id en-sleutel](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   De primaire of secundaire sleutel voor de Azure Sentinel-werk ruimte waar u uw gegevens wilt opslaan. [Zoek uw werk ruimte-id en-sleutel](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    De naam van de aangepaste logboek tabel waarin u de gegevens wilt opslaan. Het achtervoegsel van **_CL** wordt automatisch toegevoegd aan het einde van de tabel naam.  |
|**AddComputerName**     |   Als deze para meter bestaat, voegt het script de naam van de huidige computer toe aan elke logboek record in een veld met de naam **computer**.      |
|**TaggedAzureResourceId**     | Als deze para meter bestaat, worden alle geüploade logboek records door het script gekoppeld aan de opgegeven Azure-resource. <br><br>Met deze koppeling kunnen de geüploade logboek records voor resource context query's worden uitgevoerd en wordt gerespecteerd aan resource gerichte, op rollen gebaseerd toegangs beheer.       |
|**AdditionalDataTaggingName**     |      Als deze para meter bestaat, voegt het script nog een veld toe aan elk logboek record, met de geconfigureerde naam en de waarde die is geconfigureerd voor de para meter **AdditionalDataTaggingValue** . <br><br>In dit geval mag **AdditionalDataTaggingValue** niet leeg zijn. |
|**AdditionalDataTaggingValue**     |   Als deze para meter bestaat, voegt het script nog een veld toe aan elk logboek record, met de geconfigureerde waarde en de veld naam die is geconfigureerd voor de para meter **AdditionalDataTaggingName** . <br><br>Als de para meter **AdditionalDataTaggingName** leeg is, maar een waarde is geconfigureerd, is de standaard veld naam **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Uw werk ruimte-ID en-sleutel zoeken

Zoek de Details voor de para meters **WorkspaceID** en **WorkspaceKey** in azure Sentinel:

1. In azure Sentinel selecteert u **instellingen** aan de linkerkant en selecteert u vervolgens het tabblad **werk ruimte-instellingen** .

1. Onder **aan de slag met log Analytics**  >  **1 verbinding maken met een gegevens bron**, selecteert u het **beheer van Windows-en Linux-agents**.

1. Zoek uw werk ruimte-ID, primaire sleutel en secundaire sleutel op de tabbladen **Windows-servers** .
## <a name="connect-with-the-log-analytics-api"></a>Verbinding maken met de Log Analytics-API

U kunt gebeurtenissen streamen naar Azure Sentinel met behulp van de Log Analytics Data Collector API om een REST-eind punt rechtstreeks aan te roepen.

Hoewel het aanroepen van een REST-eind punt rechtstreeks meer Program meren vereist, biedt het ook meer flexibiliteit.

Zie voor meer informatie de [log Analytics Data Collector API](/azure/azure-monitor/platform/data-collector-api), met name de volgende voor beelden:

- [C#](https://docs.microsoft.com/azure/azure-monitor/platform/data-collector-api#c-sample)
- [Python 2](https://docs.microsoft.com/azure/azure-monitor/platform/data-collector-api#python-2-sample)

## <a name="connect-with-azure-functions"></a>Verbinding maken met Azure Functions

Gebruik Azure Functions in combi natie met een REST API en verschillende coderings talen, zoals [Power shell](/azure/azure-functions/functions-reference-powershell), om een serverloze aangepaste connector te maken.

Zie voor voor beelden van deze methode:

- [Uw VMware Carbon Black-Cloud eindpunt standaard koppelen aan Azure Sentinel met Azure function](connect-vmware-carbon-black.md)
- [Verbind uw Okta single Sign-On met Azure Sentinel met Azure function](connect-okta-single-sign-on.md)
- [Uw Proofpoint-taak tikken met Azure Sentinel met Azure function](connect-proofpoint-tap.md)
- [Uw Qualys-VM verbinden met Azure Sentinel met Azure function](connect-qualys-vm.md)
- [XML, CSV of andere gegevens indelingen opnemen](/azure/azure-monitor/platform/create-pipeline-datacollector-api#ingesting-xml-csv-or-other-formats-of-data)
- [Inzoomen met Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Een functie-app implementeren voor het verkrijgen van Office 365-beheer-API-gegevens in azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel github Community)

## <a name="parse-your-custom-connector-data"></a>Uw aangepaste connector gegevens parseren

U kunt de ingebouwde parserings techniek van uw aangepaste connector gebruiken om de relevante gegevens op te halen en de relevante velden in te vullen in azure Sentinel.

Bijvoorbeeld:

- **Als u Logstash hebt gebruikt**, gebruikt u de [grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) -filter-invoeg toepassing om uw gegevens te parseren.
- **Als u een Azure-functie hebt gebruikt**, parseert u uw gegevens met code. Zie [parsers](normalization.md#parsers)voor meer informatie.

Azure Sentinel ondersteunt parsering op de query tijd. Bij het parseren op de query tijd kunt u gegevens in de oorspronkelijke indeling pushen en vervolgens op aanvraag parseren wanneer dat nodig is.

Bij het parseren op de query tijd hoeft u de exacte structuur van uw gegevens niet vooraf te kennen wanneer u uw aangepaste connector maakt, of zelfs de informatie die u nodig hebt om te extra heren. In plaats daarvan kunt u uw gegevens op elk gewenst moment parseren, zelfs tijdens een onderzoek.

> [!NOTE]
> Het bijwerken van uw parser geldt ook voor gegevens die u al hebt opgenomen in azure Sentinel.
> 
## <a name="next-steps"></a>Volgende stappen

Gebruik de gegevens die zijn opgenomen in azure Sentinel om uw omgeving te beveiligen met een van de volgende processen:

- [Inzicht krijgen in waarschuwingen](quickstart-get-visibility.md)
- [Uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)
- [Incidenten onderzoeken](tutorial-investigate-cases.md)
- [Bedreigingen detecteren](tutorial-detect-threats-built-in.md)
- [Bedreigingspreventie automatiseren](tutorial-respond-threats-playbook.md)
- [Bedreigingen opsporen](hunting.md)
