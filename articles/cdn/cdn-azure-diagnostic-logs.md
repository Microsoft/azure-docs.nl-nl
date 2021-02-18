---
title: Diagnostische logboeken
titleSuffix: Azure Content Delivery Network
description: Informatie over het gebruik van Diagnostische logboeken van Azure om kern analyses op te slaan, waarmee u metrische gegevens over het gebruik van uw Azure-Content Delivery Network-eind punt kunt exporteren.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 96e80de5b8b5ab0a046913ce40ca2d7254dd0133
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573215"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Diagnostische logboeken-Azure Content Delivery Network

Met Diagnostische logboeken van Azure kunt u kern analyses bekijken en deze opslaan in een of meer bestemmingen, waaronder:

* Azure Storage-account
* Log Analytics-werkruimte
* Azure Event Hubs

Deze functie is beschikbaar voor CDN-eind punten voor alle prijs categorieën. 

Met Diagnostische logboeken kunt u elementaire metrische gegevens over het gebruik van uw CDN-eind punt exporteren naar verschillende soorten bronnen, zodat u ze op een aangepaste manier kunt gebruiken. U kunt de volgende typen gegevens export uitvoeren:

* Gegevens exporteren naar Blob Storage, exporteren naar CSV en grafieken genereren in Excel.
* Gegevens exporteren naar Event Hubs en correleren met gegevens van andere Azure-Services.
* Gegevens exporteren naar Azure Monitor logboeken en gegevens weer geven in uw eigen Log Analytics werk ruimte

Een Azure CDN profiel is vereist voor de volgende stappen. Raadpleeg [een Azure CDN profiel en eind punt maken](cdn-create-new-endpoint.md) voordat u doorgaat.

## <a name="enable-logging-with-the-azure-portal"></a>Logboekregistratie inschakelen met de Azure-portal

Voer de volgende stappen uit om logboek registratie in te scha kelen voor uw Azure CDN-eind punt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Ga in het Azure Portal naar **alle resources**  ->  **uw-CDN-profiel**

2. Selecteer het CDN-eind punt waarvoor u Diagnostische logboeken wilt inschakelen:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Selecteer het CDN-eind punt." border="true":::

3. Selecteer **Diagnostische logboeken** in het gedeelte **bewaking** :

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Selecteer Diagnostische logboeken." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Logboek registratie inschakelen met Azure Storage

Als u een opslag account wilt gebruiken om de logboeken op te slaan, voert u de volgende stappen uit:

 >[!NOTE] 
 >U hebt een opslag account nodig om deze stappen te volt ooien. Raadpleeg: **[een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** voor meer informatie.
    
1. Voer een naam in voor de diagnostische logboek instellingen voor de naam van de **Diagnostische instelling**.
 
2. Selecteer **archiveren naar een opslag account** en selecteer vervolgens **CoreAnalytics**. 

3. Kies voor **retentie (dagen)** het aantal Bewaar dagen. Een Bewaar periode van nul dagen slaat de logboeken voor onbepaalde tijd op. 

4. Selecteer het abonnement en het opslag account voor de logboeken.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Diagnostische logboeken: opslag." border="true":::

3. Selecteer **Opslaan**.

### <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics

Voer de volgende stappen uit om Log Analytics te gebruiken voor de logboeken:

>[!NOTE] 
>Er is een log Analytics-werk ruimte vereist om deze stappen uit te voeren. Raadpleeg: **[een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md)** voor meer informatie.
    
1. Voer een naam in voor de diagnostische logboek instellingen voor de naam van de **Diagnostische instelling**.

2. Selecteer **verzenden naar log Analytics** en selecteer vervolgens **CoreAnalytics**. 

3. Selecteer het abonnement en de Log Analytics werk ruimte voor de logboeken.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Diagnostische logboeken-Log Analytics." border="true":::

4. Selecteer **Opslaan**.

### <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub

Voer de volgende stappen uit om een Event Hub te gebruiken voor de logboeken:

>[!NOTE] 
>Voor het uitvoeren van deze stappen is een Event Hub vereist. Raadpleeg: **[Snelstartgids: een event hub maken met behulp van Azure Portal](../event-hubs/event-hubs-create.md)** voor meer informatie.
    
1. Voer een naam in voor de diagnostische logboek instellingen voor de naam van de **Diagnostische instelling**.

2. Selecteer **Stream naar een event hub** en selecteer vervolgens **CoreAnalytics**. 

3. Selecteer het abonnement en de naam ruimte van de Event Hub voor de logboeken.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Diagnostische logboeken-Event hub." border="true":::

4. Selecteer **Opslaan**.


## <a name="enable-logging-with-powershell"></a>Logboekregistratie inschakelen met PowerShell

In het volgende voor beeld ziet u hoe u Diagnostische logboeken inschakelt via de Azure PowerShell-cmdlets.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Diagnostische logboeken inschakelen in een opslag account

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Voer de volgende opdrachten in om Diagnostische logboeken in te scha kelen in een opslag account. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Diagnostische logboeken voor Log Analytics werk ruimte inschakelen

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Als u Diagnostische logboeken wilt inschakelen voor een Log Analytics-werk ruimte, voert u deze opdrachten in. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Diagnostische logboeken inschakelen voor Event Hub naam ruimte

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Als u Diagnostische logboeken wilt inschakelen voor een Log Analytics-werk ruimte, voert u deze opdrachten in. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnostische logboeken van Azure Storage gebruiken
In deze sectie wordt het schema van de CDN-kern analyse en de organisatie in een Azure Storage-account beschreven en vindt u voorbeeld code voor het downloaden van de logboeken in een CSV-bestand.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer gebruiken
Zie [Azure Storage Explorer](https://storageexplorer.com/)als u het hulp programma wilt downloaden. Nadat u de software hebt gedownload en geïnstalleerd, configureert u deze voor gebruik van hetzelfde Azure-opslag account dat is geconfigureerd als een bestemming voor de CDN Diagnostische logboeken.

1.  **Microsoft Azure Storage Explorer** openen
2.  Zoek het opslag account
3.  Vouw het knoop punt **BLOB containers** onder dit opslag account uit.
4.  Selecteer de container met de naam *Insights-logs-coreanalytics*.
5.  Resultaten worden weer gegeven in het rechterdeel venster, te beginnen met het eerste niveau, als *resourceId =*. Ga door met het selecteren van elk niveau totdat u het bestand hebt gevonden *PT1H.js*. Zie [indeling van BLOB-pad](cdn-azure-diagnostic-logs.md#blob-path-format)voor een uitleg van het pad.
6.  Elke BLOB- *PT1H.jsin* het bestand vertegenwoordigt de analyse logboeken gedurende één uur voor een specifiek CDN-eind punt of het aangepaste domein.
7.  Het schema van de inhoud van dit JSON-bestand wordt beschreven in het sectie schema van de logboeken van de kern analyse.


#### <a name="blob-path-format"></a>Indeling van BLOB-pad

Kern analyse logboeken worden elk uur gegenereerd en de gegevens worden verzameld en opgeslagen in één Azure-Blob als JSON-nettolading. Het hulp programma opslag Verkenner interpreteert/als een mappen scheidings teken en toont de hiërarchie. Het pad naar de Azure-Blob verschijnt alsof er een hiërarchische structuur is en de naam van de BLOB vertegenwoordigt. De naam van de BLOB volgt de volgende naam Conventie:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Beschrijving van velden:**

|Waarde|Beschrijving|
|-------|---------|
|Abonnements-id    |ID van het Azure-abonnement in GUID-indeling.|
|Naam resourcegroep |De naam van de resource groep waarvan de CDN-bronnen deel uitmaken.|
|Profile Name (Profielnaam) |Naam van het CDN-profiel|
|Naam van eind punt |Naam van het CDN-eind punt|
|Year|  Weer gave van vier cijfers van het jaar, bijvoorbeeld 2017|
|Maand| Weer gave van twee cijfers van het maand nummer. 01 = januari... 12 = december|
|Dag|   Weer gave van twee cijfers van de dag van de maand|
|PT1H.jsop| Het daad werkelijke JSON-bestand waarin de Analytics-gegevens worden opgeslagen|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>De kern analyse gegevens exporteren naar een CSV-bestand

Voor toegang tot de kern analyse wordt een voorbeeld code voor een hulp programma gegeven. Met dit hulp programma kunt u de JSON-bestanden downloaden naar een door komma's gescheiden bestands indeling, die kan worden gebruikt voor het maken van grafieken of andere aggregaties.

U kunt het hulp programma als volgt gebruiken:

1.  Ga naar de GitHub-koppeling: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Down load de code.
3.  Volg de instructies voor het compileren en configureren.
4.  Voer het hulp programma uit.
5.  In het resulterende CSV-bestand worden de analyse gegevens weer gegeven in een eenvoudige, vlakke hiërarchie.

## <a name="log-data-delays"></a>Gegevens vertragingen vastleggen

De volgende tabel geeft een logboek gegevens vertraging voor **Azure CDN standaard van micro soft**, **Azure CDN standaard van Akamai** en **Azure CDN Standard/Premium van Verizon**.

Micro soft logboek gegevens vertragingen | Vertragingen bij Verizon-logboek gegevens | Vertragingen in Akamai-logboek gegevens
--- | --- | ---
Uitgesteld met een tijd van 1 uur. | Vertraagd met 1 uur en kan Maxi maal twee uur duren voordat de voltooiing van het eind punt wordt weer gegeven. | Uitgesteld met 24 uur; Als de app meer dan 24 uur geleden is gemaakt, duurt het Maxi maal twee uur om weer te starten. Als deze onlangs is gemaakt, kan het tot 25 uur duren voordat de logboeken worden weer gegeven.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostische logboek typen voor CDN-kern analyse

Micro soft biedt momenteel alleen kern analyse logboeken, die metrische gegevens bevatten met statistieken over HTTP-antwoorden en uitgangs statistieken, zoals u kunt zien van de CDN-Pop's/-randen.

### <a name="core-analytics-metrics-details"></a>Details van metrische gegevens van basis analyse
In de volgende tabel ziet u een lijst met metrische gegevens die beschikbaar zijn in de logboeken van de kern analyse voor:

* **Azure CDN Standard van Microsoft**
* **Azure CDN Standard van Akamai**
* **Azure CDN Standard/Premium van Verizon**

Niet alle metrische gegevens zijn beschikbaar voor alle providers, hoewel deze verschillen mini maal zijn. In de tabel wordt ook weer gegeven of een bepaalde metriek beschikbaar is van een provider. De metrische gegevens zijn alleen beschikbaar voor de CDN-eind punten die verkeer hebben.


|Metrisch                     | Beschrijving | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Totaal aantal aanvraag treffers tijdens deze periode. | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Het aantal aanvragen dat heeft geleid tot een 2xx HTTP-code (bijvoorbeeld 200, 202). | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Het aantal aanvragen dat heeft geleid tot een 3xx HTTP-code (bijvoorbeeld 300, 302). | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Het aantal aanvragen dat heeft geleid tot een 4xx HTTP-code (bijvoorbeeld 400, 404). | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Het aantal aanvragen dat heeft geleid tot een 5xx HTTP-code (bijvoorbeeld 500, 504). | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Aantal andere HTTP-codes (buiten 2xx-5xx). | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Het aantal aanvragen dat heeft geleid tot een 200 HTTP-code antwoord. | Ja | Nee  |Ja |
| RequestCountHttpStatus206 | Het aantal aanvragen dat heeft geleid tot een 206 HTTP-code antwoord. | Ja | Nee  |Ja |
| RequestCountHttpStatus302 | Het aantal aanvragen dat heeft geleid tot een 302 HTTP-code antwoord. | Ja | Nee  |Ja |
| RequestCountHttpStatus304 | Het aantal aanvragen dat heeft geleid tot een 304 HTTP-code antwoord. | Ja | Nee  |Ja |
| RequestCountHttpStatus404 | Het aantal aanvragen dat heeft geleid tot een 404 HTTP-code antwoord. | Ja | Nee  |Ja |
| RequestCountCacheHit | Het aantal aanvragen dat heeft geleid tot een cache-treffer. De Asset werd rechtstreeks vanuit de POP naar de client geleverd. | Ja | Ja | Nee  |
| RequestCountCacheMiss | Het aantal aanvragen dat heeft geleid tot een Missing in de cache. Een cache-Misser betekent dat het activum niet is gevonden op de POP die het dichtst bij de client ligt en is opgehaald van de oorsprong. | Ja | Ja | Nee |
| RequestCountCacheNoCache | Het aantal aanvragen voor een Asset dat niet in de cache kan worden opgeslagen vanwege een gebruikers configuratie aan de rand. | Ja | Ja | Nee |
| RequestCountCacheUncacheable | Het aantal aanvragen voor assets dat wordt voor komen dat in de cache wordt opgeslagen door de Cache-Control van de Asset en verlopen kopteksten. Dit aantal geeft aan dat het niet in de cache moet worden opgeslagen in een POP of de HTTP-client. | Ja | Ja | Nee |
| RequestCountCacheOthers | Aantal aanvragen met een cache status die niet onder bovenstaande vallen. | Nee | Ja | Nee  |
| EgressTotal | Uitgaande gegevens overdracht in GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Uitgaande gegevens overdracht * voor antwoorden met 2xx HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressHttpStatus3xx | Uitgaande gegevens overdracht voor antwoorden met 3xx HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressHttpStatus4xx | Uitgaande gegevens overdracht voor antwoorden met 4xx HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressHttpStatus5xx | Uitgaande gegevens overdracht voor antwoorden met 5xx HTTP-status codes in GB. | Ja | Ja | Nee |
| EgressHttpStatusOthers | Uitgaande gegevens overdracht voor antwoorden met andere HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressCacheHit | Uitgaande gegevens overdracht voor antwoorden die rechtstreeks vanuit de CDN-cache op de CDN Pop's/randen zijn geleverd. | Ja | Ja | Nee |
| EgressCacheMiss. | Uitgaande gegevens overdracht voor antwoorden die niet zijn gevonden op de dichtstbijzijnde POP-server en die is opgehaald van de oorspronkelijke server. | Ja | Ja | Nee |
| EgressCacheNoCache | Uitgaande gegevens overdracht voor activa die niet in de cache worden opgeslagen vanwege een gebruikers configuratie aan de rand. | Ja | Ja | Nee |
| EgressCacheUncacheable | Uitgaande gegevens overdracht voor activa die voor komen dat deze in de cache worden opgeslagen door de Cache-Control van de Asset en of verlopen kopteksten. Geeft aan dat deze niet in de cache moet worden opgeslagen in een POP of de HTTP-client. | Ja | Ja | Nee |
| EgressCacheOthers | Uitgaande gegevens overdracht voor andere cache scenario's. | Nee | Ja | Nee |

* Uitgaande gegevens overdracht verwijst naar verkeer dat van CDN POP-servers naar de client wordt geleverd.


### <a name="schema-of-the-core-analytics-logs"></a>Schema van de logboeken met kern analyse 

Alle logboeken worden opgeslagen in JSON-indeling en elk item bevat teken reeks velden volgens het volgende schema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

WHERE- *tijd* vertegenwoordigt de begin tijd van de grens van het uur waarvoor de statistieken worden gerapporteerd. Een metrische waarde die niet wordt ondersteund door een CDN-provider, in plaats van een double of geheel getal, resulteert in een null-waarde. Deze null-waarde geeft het ontbreken van een metriek aan en wijkt af van de waarde 0. Eén set van deze metrische gegevens per domein is geconfigureerd op het eind punt.

Voorbeeld eigenschappen:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Aanvullende bronnen

* [Diagnostische logboeken van Azure](../azure-monitor/essentials/platform-logs-overview.md)
* [Kern analyse via Azure CDN aanvullende portal](./cdn-analyze-usage-patterns.md)
* [Azure Monitor-logboeken](../azure-monitor/logs/log-query-overview.md)
* [Azure Log Analytics REST API](/rest/api/loganalytics)