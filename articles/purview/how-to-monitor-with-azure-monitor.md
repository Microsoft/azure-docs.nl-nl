---
title: Azure controle sfeer liggen bewaken
description: Meer informatie over het configureren van Azure controle sfeer liggen-metrische gegevens, waarschuwingen en diagnostische instellingen met behulp van Azure Monitor.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 22c69288479e0247e499a33c2e818c19f7edb2ae
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879945"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>De metrische gegevens voor Azure controle sfeer liggen in Azure Monitor

In dit artikel wordt beschreven hoe u metrische gegevens, waarschuwingen en diagnostische instellingen configureert voor Azure controle sfeer liggen met behulp van Azure Monitor.

## <a name="monitor-azure-purview"></a>Azure-controle sfeer liggen bewaken

Azure controle sfeer liggen-beheerders kunnen Azure Monitor gebruiken om de operationele status van het controle sfeer liggen-account bij te houden. Metrieken worden verzameld om gegevens punten te bieden voor het bijhouden van potentiële problemen, het oplossen van problemen en het verbeteren van de betrouw baarheid van het controle sfeer liggen-account. De metrische gegevens worden verzonden naar Azure monitor voor gebeurtenissen die optreden in azure controle sfeer liggen.

## <a name="aggregated-metrics"></a>Cumulatieve metrische gegevens

De metrische gegevens zijn toegankelijk vanuit het Azure Portal voor een controle sfeer liggen-account. Toegang tot de metrische gegevens wordt bepaald door de roltoewijzing van het controle sfeer liggen-account. Gebruikers moeten deel uitmaken van de rol ' controle lezer ' in azure controle sfeer liggen om de metrische gegevens te bekijken. Bekijk de [rol van bewakings lezers rollen](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles) voor meer informatie over de toegangs niveaus voor rollen.

De persoon die het controle sfeer liggen-account heeft gemaakt, krijgt automatisch machtigingen voor het weer geven van metrische gegevens. Als iemand anders de metrische gegevens wil zien, voegt u deze toe aan de rol **controle lezer** door de volgende stappen uit te voeren:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Een gebruiker toevoegen aan de rol bewakings lezer

Als u een gebruiker wilt toevoegen aan de rol van **bewakings lezer** , kan de eigenaar van het controle sfeer liggen-account of de eigenaar van het abonnement de volgende stappen volgen:

1. Ga naar de [Azure Portal](https://portal.azure.com) en zoek de naam van het Azure controle sfeer liggen-account.

2. Klik op **Toegangsbeheer (IAM)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Scherm afbeelding die laat zien hoe u toegang krijgt tot IAM.":::

3. Selecteer **een roltoewijzing toevoegen**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Scherm afbeelding die laat zien hoe roltoewijzing wordt toegevoegd.":::

4. Selecteer de rol **bewakings lezer** en stel toegang toewijzen in voor de **Azure AD-gebruiker,-groep of Service-Principal**. En wijs het AAD-account toe om toegang te krijgen tot de metrische gegevens.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Scherm afbeelding die laat zien hoe u de rol van bewakings lezer kunt toevoegen.":::

## <a name="metrics-visualization"></a>Visualisatie van metrische gegevens

Gebruikers in de rol **controle lezer** kunnen de geaggregeerde metrische gegevens en Diagnostische logboeken zien die naar Azure monitor zijn verzonden. De metrische gegevens worden weer gegeven in de Azure Portal voor het bijbehorende controle sfeer liggen-account. Selecteer in de Azure Portal de sectie metrische gegevens om de lijst met alle beschik bare metrische gegevens weer te geven.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Scherm opname van beschik bare controle sfeer liggen metrische gegevens sectie." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure controle sfeer liggen-gebruikers kunnen ook rechtstreeks toegang krijgen tot de pagina met metrische gegevens vanuit het beheer centrum van het Azure controle sfeer liggen-account. Selecteer Azure Monitor op de hoofd pagina van controle sfeer liggen Management Center om Azure Portal te starten.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Scherm opname van het starten van controle sfeer liggen-metrische gegevens uit het Management Center." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Beschikbare metrische gegevens

Om vertrouwd te raken met het gebruik van de sectie metric in de Azure Portal de volgende twee documenten vooraf te lezen. [Aan de slag met metrische Explorer](../azure-monitor/platform/metrics-getting-started.md) en [geavanceerde functies van metrische Explorer](../azure-monitor/platform/metrics-charts.md).

De volgende tabel bevat de lijst met metrische gegevens die u kunt verkennen in de Azure Portal:

| Naam meetwaarde | Metrische naamruimte | Type aggregatie | Beschrijving |
| ------------------- | ------------------- | ------------------- | ----------------- |
| De scan is geannuleerd | Automatische scan | Sum <br> Count | De geannuleerde gegevens bron wordt gecontroleerd op basis van de tijds periode |
| De scan is voltooid | Automatische scan | Sum <br> Count | De voltooide scan van gegevens bronnen samen voegen gedurende een bepaalde periode |
| Kan niet scannen | Automatische scan | Sum <br> Count | De mislukte scan van gegevens bronnen samen voegen gedurende een bepaalde periode |
| Gebruikte scan tijd | Automatische scan | Min. <br> Max <br> Sum <br> Gemiddeld | De totale tijd die wordt gebruikt door scans, samen voegen in een tijds periode |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Diagnostische logboeken voor Azure Storage account

Onbewerkte telemetriegegevens worden verzonden naar Azure Monitor. Gebeurtenissen kunnen worden vastgelegd in het opslag account van de klant voor verdere analyse. Het exporteren van Logboeken wordt uitgevoerd via de diagnostische instellingen voor het controle sfeer liggen-account op de Azure Portal.

Volg de stappen voor het maken van een diagnostische instelling voor uw Azure controle sfeer liggen-account.

1. Maak een nieuwe diagnostische instelling voor het verzamelen van platform logboeken en metrische gegevens aan de hand van dit artikel: [Diagnostische instellingen maken voor het verzenden van platform logboeken en metrische gegevens naar verschillende bestemmingen](../azure-monitor/platform/diagnostic-settings.md). Selecteer de bestemming alleen als Azure Storage-account.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Scherm opname van het maken van een diagnostisch logboek." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Registreer de gebeurtenissen in een opslag account. Een specifiek opslag account wordt aanbevolen voor het archiveren van de diagnostische Logboeken. Volg dit artikel om [een opslag account te maken](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Scherm opname van het toewijzen van een opslag account voor Diagnostische logboeken." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Het duurt Maxi maal 15 minuten om logboeken te ontvangen in het zojuist gemaakte opslag account. [Zie gegevens retentie en schema van bron Logboeken in azure Storage-account](../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Zodra de diagnostische logboeken zijn geconfigureerd, stroomt de gebeurtenissen naar het opslag account.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

De gebeurtenis houdt de scan levenscyclus bij. Een scan bewerking wordt uitgevoerd via een volg orde van statussen, van de wachtrij, uitvoering en uiteindelijk een eind status van geslaagd | Mislukt | Geannuleerd. Er wordt een gebeurtenis geregistreerd voor elke status overgang en het schema van de gebeurtenis heeft de volgende eigenschappen.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Het voorbeeld logboek voor een gebeurtenis exemplaar wordt weer gegeven in de onderstaande sectie.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Volgende stappen

[Asset Insights weer geven](asset-insights.md)
