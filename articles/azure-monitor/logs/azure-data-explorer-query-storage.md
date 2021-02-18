---
title: Gegevens van Azure Monitor geëxporteerd met Azure Data Explorer (preview-versie)
description: Gebruik Azure Data Explorer om query's uit te voeren op gegevens die vanuit uw Log Analytics-werk ruimte zijn geëxporteerd naar een Azure-opslag account.
ms.subservice: logs
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 5a26e5d045be78772ee5dc940fcfecd3cfde9dc7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611418"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Gegevens van Azure Monitor geëxporteerd met Azure Data Explorer (preview-versie)
Bij het exporteren van gegevens uit Azure Monitor naar een Azure Storage-account is een lage Bewaar periode en de mogelijkheid om Logboeken opnieuw toe te wijzen aan verschillende regio's. Gebruik Azure Data Explorer om query's uit te voeren op gegevens die zijn geëxporteerd uit uw Log Analytics-werk ruimten. Eenmaal geconfigureerd, worden ondersteunde tabellen die vanuit uw werk ruimten worden verzonden naar een Azure-opslag account, beschikbaar als gegevens bron voor Azure-Data Explorer.

De proces stroom is als volgt: 

1.  Gegevens exporteren van Log Analytics werkruimte naar een Azure-opslag account.
2.  Maak een externe tabel in uw Azure Data Explorer-cluster en wijs deze toe aan de gegevens typen.
3.  Query's uitvoeren op gegevens uit Azure Data Explorer.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Gegevens query stroom van Azure Data Explorer geëxporteerd.":::



## <a name="send-data-to-azure-storage"></a>Gegevens naar Azure Storage verzenden
Azure Monitor-logboeken kunnen worden geëxporteerd naar een Azure Storage-account met behulp van een van de volgende opties.

- Als u alle gegevens uit uw Log Analytics-werk ruimte wilt exporteren naar een Azure-opslag account of Event Hub, gebruikt u de functie Log Analytics werkruimte gegevens exporteren van Azure Monitor Logboeken. Zie [log Analytics werk ruimte gegevens exporteren in azure monitor (preview-versie)](../platform/logs-data-export.md)
- Geplande export vanuit een logboek query met behulp van een logische app. Dit is vergelijkbaar met de functie voor gegevens export, maar u kunt gefilterde of geaggregeerde gegevens verzenden naar Azure Storage. Deze methode is afhankelijk van de [query limieten](../service-limits.md#log-analytics-workspaces)  van het logboek Zie [gegevens van log Analytics werk ruimte archiveren in azure Storage met behulp van een logische app](../platform/logs-export-logic-app.md).
- Eenmalig exporteren met behulp van een logische app. Zie [Azure monitor logs connector voor Logic apps en energie automatisering](../platform/logicapp-flow-connector.md).
- Eenmalig exporteren naar een lokale computer met behulp van Power shell-script. Zie [invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> U kunt een bestaand Azure Data Explorer-cluster gebruiken of een nieuw toegewezen cluster maken met de benodigde configuraties.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Een externe tabel maken die zich in Azure Blob Storage bevindt
Gebruik [externe tabellen](/azure/data-explorer/kusto/query/schema-entities/externaltables) om azure-Data Explorer te koppelen aan een Azure-opslag account. Een externe tabel is een Kusto-schema-entiteit die verwijst naar gegevens die buiten een Kusto-Data Base zijn opgeslagen. Net als tabellen heeft een externe tabel een goed gedefinieerd schema. In tegens telling tot tabellen worden gegevens opgeslagen en beheerd buiten een Kusto-cluster. De geëxporteerde gegevens uit de vorige sectie worden opgeslagen in JSON-regels.

Als u een verwijzing wilt maken, moet u het schema van de geëxporteerde tabel. Gebruik de operator [getschema](/azure/data-explorer/kusto/query/getschemaoperator) van log Analytics om deze informatie op te halen die de kolommen van de tabel en de bijbehorende gegevens typen bevat.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics table-schema.":::

U kunt nu de uitvoer gebruiken om de Kusto-query voor het bouwen van de externe tabel te maken.
Volg de instructies in [Create and Alter External tables in azure Storage of Azure data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake), maak een externe tabel in een JSON-indeling en voer de query uit vanuit uw Azure Data Explorer-data base.

>[!NOTE]
>De externe tabel is gemaakt op basis van twee processen. De eerste is het maken van de externe tabel, terwijl de tweede de toewijzing maakt.

Met het volgende Power shell-script worden de opdrachten voor [maken](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) voor de tabel en de toewijzing gemaakt.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

In de volgende afbeelding ziet u een voor beeld van de uitvoer.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Opdracht uitvoer ExternalTable maken.":::

[![Voorbeelduitvoer](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Kopieer, plak en voer vervolgens de uitvoer van het script in uw Azure Data Explorer-client hulpprogramma uit om de tabel en toewijzing te maken.
>* Als u alle gegevens in de container wilt gebruiken, wijzigt u het script en wijzigt u de URL in ' https://your.blob.core.windows.net/containername ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Query's uitvoeren op de geëxporteerde gegevens van Azure Data Explorer 

Nadat u de toewijzing hebt geconfigureerd, kunt u een query uitvoeren op de geëxporteerde gegevens van Azure Data Explorer. Uw query vereist de functie [external_table](/azure/data-explorer/kusto/query/externaltablefunction) , zoals in het volgende voor beeld.

```kusto
external_table("HBTest","map") | take 10000
```

[![Query's uitvoeren op Log Analytics geëxporteerde gegevens](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [schrijven van query's in Azure Data Explorer](/azure/data-explorer/write-queries)