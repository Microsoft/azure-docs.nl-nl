---
title: Bewerkingen van Azure Cosmos DB Control-vlak controleren
description: Meer informatie over het controleren van de besturings vlak bewerkingen, zoals het toevoegen van een regio, het bijwerken van de door Voer, de regionale failover, het toevoegen van een VNet, enzovoort in Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 6f3e408343fc75d6587d1a67a0179edf13d56e36
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658245"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Bewerkingen van Azure Cosmos DB Control-vlak controleren
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Besturings vlak in Azure Cosmos DB is een REST-service waarmee u een gevarieerde set bewerkingen kunt uitvoeren op het Azure Cosmos-account. Er wordt een openbaar resource model (bijvoorbeeld: data base, account) en verschillende bewerkingen voor de eind gebruikers weer gegeven om acties uit te voeren op het resource model. De bewerkingen van het besturings vlak bevatten wijzigingen in het Azure Cosmos-account of de container. Bijvoorbeeld: bewerkingen zoals het maken van een Azure Cosmos-account, het toevoegen van een regio, het bijwerken van de door Voer, de regionale failover, het toevoegen van een VNet enz. zijn een aantal van de beheer bewerkingen. In dit artikel wordt uitgelegd hoe u de bewerkingen voor het beheer vlak in Azure Cosmos DB kunt controleren. U kunt de bewerkingen voor het beheer vlak uitvoeren op Azure Cosmos-accounts met behulp van Azure CLI, Power shell of Azure Portal, terwijl u voor containers gebruikmaakt van Azure CLI of Power shell.

Hier volgen enkele voor beelden van scenario's waarbij acties voor het beheren van het besturings vlak handig zijn:

* U wilt een waarschuwing ontvangen wanneer de firewall regels voor uw Azure Cosmos-account worden gewijzigd. De waarschuwing is vereist voor het vinden van niet-geautoriseerde wijzigingen in regels die de netwerk beveiliging van uw Azure Cosmos-account regelen en snelle actie ondernemen.

* U wilt een waarschuwing ontvangen als er een nieuwe regio wordt toegevoegd aan of verwijderd uit uw Azure Cosmos-account. Het toevoegen of verwijderen van regio's heeft gevolgen voor de facturerings-en data soevereiniteit-vereisten. Deze waarschuwing helpt u bij het detecteren van een onbedoelde toevoeging of verwijdering van een regio voor uw account.

* U wilt meer informatie ophalen uit de diagnostische logboeken op wat er is gewijzigd. Een VNet is bijvoorbeeld gewijzigd.

## <a name="disable-key-based-metadata-write-access"></a>Schrijf toegang op basis van sleutels uitschakelen

Voordat u de bewerkingen van het besturings vlak controleert in Azure Cosmos DB, schakelt u de schrijf toegang op basis van de meta gegevens uit voor uw account. Wanneer op sleutels gebaseerde meta gegevens schrijf toegang is uitgeschakeld, kunnen clients die verbinding maken met het Azure Cosmos-account via account sleutels geen toegang krijgen tot het account. U kunt schrijf toegang uitschakelen door de `disableKeyBasedMetadataWriteAccess` eigenschap in te stellen op True. Nadat u deze eigenschap hebt ingesteld, kunnen wijzigingen aan resources worden aangebracht van een gebruiker met de juiste Azure-rol en-referenties. Zie het artikel [wijzigingen van sdk's voor komen](role-based-access-control.md#prevent-sdk-changes) voor meer informatie over het instellen van deze eigenschap. 

Als de `disableKeyBasedMetadataWriteAccess` is ingeschakeld en de op SDK gebaseerde clients Create-of update-bewerkingen uitvoeren, is een fout *' bewerking ' post ' op resource ' ContainerNameorDatabaseName ' niet toegestaan via Azure Cosmos DB-eind punt* wordt geretourneerd. U moet toegang tot dergelijke bewerkingen voor uw account inschakelen of de bewerkingen voor maken/bijwerken uitvoeren via Azure Resource Manager, Azure CLI of Azure PowerShell. Als u wilt overschakelen, stelt u de disableKeyBasedMetadataWriteAccess in op **False** door gebruik te maken van Azure CLI, zoals beschreven in het artikel [Wijzigingen verhinderen in Cosmos SDK](role-based-access-control.md#prevent-sdk-changes) . Zorg ervoor dat u de waarde `disableKeyBasedMetadataWriteAccess` Onwaar wijzigt in plaats van waar.

Houd rekening met de volgende punten wanneer u de schrijf toegang voor meta gegevens uitschakelt:

* Evalueer en zorg ervoor dat uw toepassingen geen meta gegevens aanroepen waarbij de bovenstaande bronnen worden gewijzigd (bijvoorbeeld het maken van een verzameling, het bijwerken van de door Voer,...) met behulp van de SDK-of account sleutels.

* Op dit moment gebruikt de Azure Portal account sleutels voor meta gegevens bewerkingen en worden deze bewerkingen dus geblokkeerd. U kunt ook de implementaties van Azure CLI, Sdk's of de Resource Manager-sjabloon gebruiken om dergelijke bewerkingen uit te voeren.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Diagnostische logboeken inschakelen voor bewerkingen in het vlak van het besturings element

U kunt Diagnostische logboeken inschakelen voor bewerkingen op het vlak van besturings elementen met behulp van de Azure Portal. Nadat de diagnostische logboeken zijn ingeschakeld, wordt de bewerking opgenomen als een paar gebeurtenissen voor starten en volt ooien met relevante gegevens. De *RegionFailoverStart* en *RegionFailoverComplete* volt ooien bijvoorbeeld de failover-gebeurtenis van de regio.

Gebruik de volgende stappen om logboek registratie in te scha kelen voor bewerkingen in het besturings element vlak:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Cosmos-account.

1. Open het deel venster **Diagnostische instellingen** en geef een **naam** op voor de logboeken die u wilt maken.

1. Selecteer **ControlPlaneRequests** voor logboek type en selecteer de optie **verzenden naar log Analytics** .

U kunt de logboeken ook opslaan in een opslag account of stream naar een Event Hub. In dit artikel wordt beschreven hoe u logboeken naar log Analytics verzendt en er query's op uitvoert. Nadat u hebt ingeschakeld, duurt het enkele minuten voordat de diagnostische logboeken van kracht worden. Alle bewerkingen voor het beheer vlak die na dat punt worden uitgevoerd, kunnen worden bijgehouden. De volgende scherm afbeelding laat zien hoe u de logboeken voor besturings elementen kunt inschakelen:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Registratie van aanvragen voor beheer vlak inschakelen":::

## <a name="view-the-control-plane-operations"></a>De bewerkingen van het besturings vlak weer geven

Nadat u logboek registratie hebt ingeschakeld, gebruikt u de volgende stappen om bewerkingen voor een specifiek account bij te houden:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Open het tabblad **monitor** op de navigatie balk aan de linkerkant en selecteer vervolgens het deel venster **Logboeken** . Er wordt een gebruikers interface geopend waarin u eenvoudig query's kunt uitvoeren met dat specifieke account binnen het bereik. Voer de volgende query uit om de logboeken van het besturings vlak weer te geven:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

De volgende scherm afbeeldingen vastleggen Logboeken wanneer een consistentie niveau wordt gewijzigd voor een Azure Cosmos-account:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Besturings vlak Logboeken wanneer een VNet wordt toegevoegd":::

De volgende scherm afbeeldingen vastleggen Logboeken wanneer de spatie of een tabel van een Cassandra-account wordt gemaakt en wanneer de door Voer wordt bijgewerkt. De besturings vlak logboeken voor het maken en bijwerken van bewerkingen in de data base en de container worden afzonderlijk geregistreerd, zoals wordt weer gegeven in de volgende scherm afbeelding:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Besturings vlak Logboeken wanneer de door Voer wordt bijgewerkt":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>De identiteit identificeren die aan een specifieke bewerking is gekoppeld

Als u meer fouten wilt opsporen, kunt u een specifieke bewerking in het **activiteiten logboek** identificeren met behulp van de activiteits-id of door de tijds tempel van de bewerking. Tijds tempel wordt gebruikt voor sommige Resource Manager-clients waarbij de activiteits-ID niet expliciet is door gegeven. Het activiteiten logboek bevat details over de identiteit waarmee de bewerking is gestart. De volgende scherm afbeelding laat zien hoe u de activiteit-ID gebruikt en de bewerkingen vindt die hieraan zijn gekoppeld in het activiteiten logboek:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="De activiteit-ID gebruiken en de bewerkingen vinden":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Bewerkingen voor het beheer vlak voor Azure Cosmos-account

Hier volgen de besturings vlak bewerkingen die beschikbaar zijn op account niveau. De meeste bewerkingen worden bijgehouden op account niveau. Deze bewerkingen zijn beschikbaar als metrische gegevens in azure monitor:

* Regio toegevoegd
* Regio is verwijderd
* Account is verwijderd
* Er is een failover uitgevoerd voor de regio
* Account gemaakt
* Het virtuele netwerk is verwijderd
* De netwerk instellingen voor het account zijn bijgewerkt
* De replicatie-instellingen voor het account zijn bijgewerkt
* Bijgewerkte account sleutels
* De back-upinstellingen voor het account zijn bijgewerkt
* De diagnostische instellingen voor het account zijn bijgewerkt

## <a name="control-plane-operations-for-database-or-containers"></a>Bewerkingen voor het vlak van een Data Base of containers

Hieronder vindt u de besturings vlak bewerkingen die beschikbaar zijn op de data base en op het niveau van de container. Deze bewerkingen zijn beschikbaar als metrische gegevens in azure monitor:

* SQL Database gemaakt
* SQL Database bijgewerkt
* SQL Database door Voer bijgewerkt
* SQL Database verwijderd
* SQL-container gemaakt
* SQL-container is bijgewerkt
* SQL-container doorvoer bijgewerkt
* SQL-container verwijderd
* Cassandra-opslag ruimte gemaakt
* Cassandra-opslag ruimte bijgewerkt
* Cassandra voor de door Voer van de opslag ruimte bijgewerkt
* Cassandra-opslag ruimte verwijderd
* Cassandra-tabel gemaakt
* Cassandra-tabel bijgewerkt
* Cassandra-tabel doorvoer bijgewerkt
* Cassandra-tabel verwijderd
* Gremlin-data base gemaakt
* Gremlin-data base bijgewerkt
* Gremlin-data base-door Voer bijgewerkt
* Gremlin-data base verwijderd
* Gremlin Graph gemaakt
* Gremlin-grafiek bijgewerkt
* Gremlin-grafiek doorvoer bijgewerkt
* Gremlin-grafiek verwijderd
* Mongo-data base gemaakt
* Mongo-data base bijgewerkt
* Mongo-data base-door Voer bijgewerkt
* Mongo-data base verwijderd
* Mongo-verzameling gemaakt
* Mongo-verzameling bijgewerkt
* Door Voer van Mongo-verzameling bijgewerkt
* Mongo-verzameling verwijderd
* AzureTable-tabel gemaakt
* AzureTable-tabel bijgewerkt
* AzureTable-tabel doorvoer bijgewerkt
* AzureTable-tabel verwijderd

## <a name="diagnostic-log-operations"></a>Diagnostische logboek bewerkingen

Hieronder vindt u de bewerkings namen in Diagnostische logboeken voor verschillende bewerkingen:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Voor API-specifieke bewerkingen wordt de bewerking benoemd met de volgende indeling:

* Soort + ApiKindResourceType + OperationType
* Soort + ApiKindResourceType + "door Voer" + operationType

**Voorbeeld** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

De eigenschap *Resourcedetails* bevat de gehele resource hoofdtekst als een aanvraag lading en bevat alle eigenschappen die moeten worden bijgewerkt

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Diagnostische logboek query's voor beheer vlak bewerkingen

Hier volgen enkele voor beelden van het ophalen van Diagnostische logboeken voor bewerkingen in het besturings vlak:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersThroughputUpdate"
```

Query voor het ophalen van de activityId en de aanroeper die de bewerking voor het verwijderen van de container hebben gestart:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Query voor het ophalen van index-of TTL-updates. U kunt de uitvoer van deze query vervolgens vergelijken met een eerdere update om de wijziging in de index of TTL te bekijken.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**uitvoer**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor verkennen voor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Controleren en fouten opsporen met metrische gegevens in Azure Cosmos DB](use-metrics.md)
