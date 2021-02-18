---
title: Gegevens archiveren van Log Analytics werk ruimte naar Azure Storage met behulp van Logic app
description: Beschrijft een methode voor het gebruik van Azure Logic Apps om gegevens uit een Log Analytics-werk ruimte op te vragen en te verzenden naar Azure Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 040c487df83c117e177b8a8b0e8fddde8682c67f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609381"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Gegevens archiveren van Log Analytics werk ruimte naar Azure Storage met behulp van Logic app
In dit artikel wordt een methode beschreven voor het gebruik van [Azure Logic apps](../../logic-apps/index.yml) om gegevens op te vragen uit een log Analytics-werk ruimte in azure monitor en te verzenden naar Azure Storage. Gebruik dit proces wanneer u uw Azure Monitor-logboek gegevens wilt exporteren voor controle-en nalevings scenario's of als u wilt toestaan dat een andere service deze gegevens ophaalt.  

## <a name="other-export-methods"></a>Andere export methoden
De methode die in dit artikel wordt beschreven, beschrijft een geplande export van een logboek query met behulp van een logische app. Andere opties voor het exporteren van gegevens voor bepaalde scenario's zijn onder andere:

- Als u alle gegevens uit uw Log Analytics-werk ruimte wilt exporteren naar een Azure-opslag account of Event Hub, gebruikt u de functie Log Analytics werkruimte gegevens exporteren van Azure Monitor Logboeken. Zie [log Analytics werk ruimte gegevens exporteren in azure monitor (preview-versie)](logs-data-export.md)
- Eenmalig exporteren met behulp van een logische app. Zie [Azure monitor logs connector voor Logic apps en energie automatisering](logicapp-flow-connector.md).
- Eenmalig exporteren naar een lokale computer met behulp van Power shell-script. Zie [invoke-AzOperationalInsightsQueryExport]] ( https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>Overzicht
Deze procedure maakt gebruik van de [Azure monitor logs connector](/connectors/azuremonitorlogs/) waarmee u een logboek query kunt uitvoeren vanuit een logische app en de uitvoer ervan kunt gebruiken in andere acties in de werk stroom. De [Azure Blob Storage-connector](/connectors/azureblob/) wordt in deze procedure gebruikt om de query-uitvoer naar Azure Storage te verzenden. De andere acties worden beschreven in de volgende secties.

![Overzicht van logische apps](media/logs-export-logic-app/logic-app-overview.png)

Wanneer u gegevens uit een Log Analytics-werk ruimte exporteert, moet u uw logboek gegevens filteren en samen voegen en uw query optimaliseren om de hoeveelheid gegevens die door de werk stroom van uw logische app wordt verwerkt, te beperken tot de vereiste gegevens. Als u bijvoorbeeld aanmeldings gebeurtenissen wilt archiveren, kunt u de vereiste gebeurtenissen filteren en alleen de vereiste velden projecteren met de volgende query: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Wanneer u de gegevens op een schema exporteert, gebruikt u de functie ingestion_time () in uw query om ervoor te zorgen dat u geen gegevens mist die laat arriveren. Als gegevens worden vertraagd als gevolg van netwerk-of platform problemen, zorgt de opname tijd ervoor dat deze wordt opgenomen in de volgende uitvoering van de logische app. Zie [Azure monitor-logboeken actie toevoegen](#add-azure-monitor-logs-action) voor een voor beeld.

## <a name="prerequisites"></a>Vereisten
Hieronder volgen de vereisten die moeten worden voltooid voordat u deze procedure voltooit.

- Log Analytics-werkruimte. De gebruiker die de logische app maakt, moet ten minste de machtiging lezen hebben voor de werk ruimte. 
- Azure-opslag account. Het opslag account hoeft zich niet in hetzelfde abonnement als uw Log Analytics-werk ruimte te bevinden. De gebruiker die de logische app maakt, moet schrijf machtigingen hebben voor het opslag account. 


## <a name="connector-limits"></a>Limieten voor connector
Log Analytics werkruimte-en logboek query's in Azure Monitor zijn multitenancy-services die limieten bevatten voor het beveiligen en isoleren van klanten en het onderhouden van de kwaliteit van de service. Bij het uitvoeren van een query voor een grote hoeveelheid gegevens moet u rekening houden met de volgende limieten. Dit kan van invloed zijn op de manier waarop u het terugkeer patroon van de logische app en uw logboek query configureert:

- In logboek query's kunnen Maxi maal 500.000 rijen worden geretourneerd.
- In logboek query's kunnen niet meer dan 64.000.000 bytes worden geretourneerd.
- Logboek query's kunnen standaard niet meer dan 10 minuten worden uitgevoerd. 
- Log Analytics-connector is beperkt tot 100-oproep per minuut.


## <a name="create-container-in-the-storage-account"></a>Container maken in het opslag account
Gebruik de procedure in [een container maken](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) om een container toe te voegen aan uw opslag account om de geëxporteerde gegevens te bewaren. De naam die wordt gebruikt voor de container in dit artikel is **loganalytics-data**, maar u kunt elke naam gebruiken.


## <a name="create-logic-app"></a>Een logische app maken

Ga naar **Logic apps** in het Azure Portal en klik op **toevoegen**. Selecteer een **abonnement**, **resource groep** en **regio** om de nieuwe logische app op te slaan en geef deze een unieke naam. U kunt **log Analytics** instelling inschakelen om informatie over runtime gegevens en-gebeurtenissen te verzamelen, zoals wordt beschreven in [Azure monitor logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Deze instelling is niet vereist voor het gebruik van de Azure Monitor logs connector.

![Logische app maken](media/logs-export-logic-app/create-logic-app.png)


Klik op **beoordeling + maken** en vervolgens op **maken**. Wanneer de implementatie is voltooid, klikt u op **Ga naar resource** om de **Logic apps Designer** te openen.

## <a name="create-a-trigger-for-the-logic-app"></a>Een trigger maken voor de logische app
Selecteer onder **beginnen met een gemeen schappelijke trigger de** optie **terugkeer patroon**. Hiermee maakt u een logische app die automatisch met een regel matig interval wordt uitgevoerd. In het vak **frequentie** van de actie selecteert u **dag** en voert u in het vak **interval** **1** in om de werk stroom eenmaal per dag uit te voeren.

![Herhalings actie](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Actie Azure Monitor logboeken toevoegen
Klik op **+ nieuwe stap** om een actie toe te voegen die wordt uitgevoerd na de actie van het terugkeer patroon. Onder **Kies een actie**, typt u **Azure monitor** en selecteert u **Azure monitor logboeken**.

![Actie Azure Monitor logboeken](media/logs-export-logic-app/select-azure-monitor-connector.png)

Klik op **Azure log Analytics: query-en Lijst resultaten uitvoeren**.

![Scherm opname van een nieuwe actie wordt toegevoegd aan een stap in de ontwerp functie voor logische apps. Azure Monitor-Logboeken wordt gemarkeerd onder Kies een actie.](media/logs-export-logic-app/select-query-action-list.png)

U wordt gevraagd een Tenant te selecteren en toegang te verlenen tot de Log Analytics-werk ruimte met het account dat door de werk stroom wordt gebruikt om de query uit te voeren.


## <a name="add-azure-monitor-logs-action"></a>Actie Azure Monitor logboeken toevoegen
Met de actie Azure Monitor Logboeken kunt u opgeven welke query moet worden uitgevoerd. De logboek query die in dit voor beeld wordt gebruikt, is geoptimaliseerd voor het hele uur en verzamelt de gegevens die zijn opgenomen voor de specifieke uitvoerings tijd. Als de werk stroom bijvoorbeeld wordt uitgevoerd om 4:35, zou het tijds bereik 4:00 tot 5:00 zijn. Als u de logische app wijzigt zodat deze op een andere frequentie kan worden uitgevoerd, moet u de query ook wijzigen. Als u bijvoorbeeld het terugkeer patroon zo instelt dat het dagelijks wordt uitgevoerd, stelt u startTime in de query in op startofday (make_datetime (jaar, maand, dag, 0, 0)). 

Selecteer het **abonnement** en de **resource groep** voor uw log Analytics-werk ruimte. Selecteer *log Analytics werk ruimte* voor het **resource type** en selecteer vervolgens de naam van de werk ruimte onder **resource naam**.

Voeg de volgende logboek query toe aan het **query** -venster.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

In het **tijds bereik** worden de records opgegeven die worden opgenomen in de query op basis van de kolom **TimeGenerated** . Dit moet worden ingesteld op een waarde die gelijk is aan of hoger is dan het tijds bereik dat u hebt geselecteerd in de query. Omdat deze query niet de kolom **TimeGenerated** gebruikt, is de optie **in query** niet beschikbaar. Zie [query bereik](../log-query/scope.md) voor meer informatie over het tijds bereik. 

Selecteer de **laatste vier uur** voor het **tijds bereik**. Dit zorgt ervoor dat records met een opname tijd groter dan **TimeGenerated** worden opgenomen in de resultaten.
   
![Scherm afbeelding van de instellingen voor de nieuwe Azure Monitor logboeken actie met de naam query's uitvoeren en resultaten visualiseren.](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>JSON-activiteit voor parsering toevoegen (optioneel)
De uitvoer van de actie **query uitvoeren en lijst met resultaten** wordt ingedeeld in JSON. U kunt deze gegevens parseren en deze bewerken als onderdeel van de voor bereiding voor de actie **opstellen** . 

U kunt een JSON-schema opgeven dat de payload beschrijft die u verwacht te ontvangen. De ontwerper parseert JSON-inhoud door gebruik te maken van dit schema en genereert gebruikers vriendelijke tokens die de eigenschappen in uw JSON-inhoud vertegenwoordigen. U kunt deze eigenschappen vervolgens eenvoudig raadplegen en gebruiken in de werk stroom van de logische app. 


Klik op **+ nieuwe stap** en vervolgens op **+ een actie toevoegen**. Onder **Kies een actie**, typt u **JSON** en selecteert u **JSON parseren**.

![JSON-activiteit parseren selecteren](media/logs-export-logic-app/select-parse-json.png)

Klik in het vak **inhoud** om een lijst met waarden uit eerdere activiteiten weer te geven. Selecteer **hoofd tekst** in de actie **query uitvoeren en lijst met resultaten** . Dit is de uitvoer van de logboek query.

[![Hoofd tekst selecteren](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  Klik op **voor beeld Payload gebruiken om een schema te genereren**. Voer de logboek query uit en kopieer de uitvoer om te gebruiken voor de nettolading van de steek proef.  Voor de voorbeeld query kunt u de volgende uitvoer gebruiken:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![JSON-nettolading parseren](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>De actie opstellen toevoegen
De actie **opstellen** neemt de geparseerde JSON-uitvoer mee en maakt het object dat u in de BLOB moet opslaan.

Klik op **+ nieuwe stap** en vervolgens op **+ een actie toevoegen**. Onder **Kies een actie**, typt u **opstellen** en selecteert u vervolgens de actie **opstellen** .

![Actie voor opstellen selecteren](media/logs-export-logic-app/select-compose.png)


Klik op het vak **invoer** in een lijst met waarden van vorige activiteiten. Selecteer **hoofd tekst** in de actie **JSON parseren** . Dit is de geparseerde uitvoer van de logboek query.

[![Hoofd tekst selecteren voor de actie opstellen](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>De actie Blob maken toevoegen
De actie Blob maken schrijft de bestaande JSON naar opslag.

Klik op **+ nieuwe stap** en vervolgens op **+ een actie toevoegen**. Onder **Kies een actie**, typt u **BLOB** en selecteert u vervolgens de actie **Blob maken** .

![Selecteer Blob maken](media/logs-export-logic-app/select-create-blob.png)

Typ een naam voor de verbinding met uw opslag account in de naam van de **verbinding** en klik vervolgens op het mappictogram in het vak **pad naar map** om de container in uw opslag account te selecteren. Klik op de naam van de **BLOB** om een lijst met waarden uit eerdere activiteiten weer te geven. Klik op **expressie** en voer een expressie in die overeenkomt met uw tijds interval. Voor deze query die elk uur wordt uitgevoerd, wordt met de volgende expressie de naam van de BLOB ingesteld per vorig uur: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![BLOB-expressie](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

Klik op het vak **blob-inhoud** om een lijst met waarden uit eerdere activiteiten weer te geven en selecteer vervolgens **uitvoer** in de sectie **opstellen** .


![BLOB-expressie maken](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>De logische app testen
Test de werk stroom door te klikken op **uitvoeren**. Als de werk stroom fouten bevat, wordt deze in de stap aangegeven met het probleem. U kunt de uitvoeringen en inzoomen weer geven in elke stap om de invoer en uitvoer te bekijken om fouten te onderzoeken. Zie [problemen met werk stromen oplossen en fouten opsporen in azure Logic apps,](../../logic-apps/logic-apps-diagnosing-failures.md) indien nodig.

[![Geschiedenis van uitvoeringen](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Logboeken in de opslag weer geven
Ga naar het menu **opslag accounts** in de Azure Portal en selecteer uw opslag account. Klik op de tegel **blobs** en selecteer de container die u hebt opgegeven in de actie Blob maken. Selecteer een van de blobs en **Bewerk vervolgens BLOB**.

[![BLOB-gegevens](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboek query's vindt u in azure monitor](../log-query/log-query-overview.md).
- Meer informatie over [Logic apps](../../logic-apps/index.yml)
- Meer informatie over het [automatiseren van energie](https://flow.microsoft.com).
