---
title: Gebruik Azure PowerShell voor het configureren van doorlopende back-ups en herstel naar een tijdstip in Azure Cosmos DB.
description: Meer informatie over het inrichten van een account met doorlopende back-up en herstel van gegevens met behulp van Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381831"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>Continue back-ups en herstel naar een tijdstip configureren en beheren (preview)-Azure PowerShell gebruiken
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de functie voor het terugzetten van het tijdstip van een onbedoelde wijziging in een container kunt u een verwijderde account, data base of container herstellen of herstellen in een regio (waarbij back-ups bestaan). Azure Cosmos DB Met de continue back-upmodus kunt u een herstel bewerking uitvoeren naar een wille keurig tijdstip in de afgelopen 30 dagen.

In dit artikel wordt beschreven hoe u een account met doorlopende back-up-en herstel gegevens kunt inrichten met behulp van Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Azure PowerShell installeren

1. Voer de volgende opdracht uit Azure PowerShell om de `Az.CosmosDB` Preview-module te installeren. Deze bevat de opdrachten die betrekking hebben op Point in time restore:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Nadat u de modules hebt geïnstalleerd, meldt u zich aan bij Azure met

   ```azurepowershell
   Connect-AzAccount
   ```

1. Selecteer een specifiek abonnement met de volgende opdracht:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Een SQL-API-account inrichten met doorlopende back-up

Als u een account met doorlopende back-up wilt inrichten, voegt u een argument `-BackupPolicyType Continuous` samen met de normale inrichtings opdracht toe.

De volgende cmdlet is een voor beeld van een enkele regio schrijf account `pitracct2` met continu back-upbeleid dat is gemaakt in de regio *VS West* onder *myrg* -resource groep:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Een MongoDB-API-account inrichten met doorlopende back-up

De volgende cmdlet is een voor beeld van een continu back-upaccount *pitracct2* dat is gemaakt in de regio *VS-West* onder *myrg* -resource groep:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Een herstel bewerking activeren

De volgende cmdlet is een voor beeld van het activeren van een herstel bewerking met de opdracht Restore met behulp van het doel account, het bron account, de locatie, de resource groep en de tijds tempel:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Voor beeld 1:** Het hele account herstellen:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Voor beeld 2:** Herstellen van specifieke verzamelingen en data bases. In dit voor beeld worden de verzamelingen myCol1, myCol2 van myDB1 en de gehele data base myDB2 teruggezet, die alle containers daaronder bevat.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Herstorable bronnen voor SQL-API opsommen

Met de opsommings-cmdlets kunt u de resources ontdekken die kunnen worden hersteld met verschillende tijds tempels. Daarnaast bieden ze ook een feed van belang rijke gebeurtenissen voor het herstorable account, de data base en de container bronnen.

**Alle accounts weer geven die kunnen worden hersteld in het huidige abonnement**

Voer de `Get-AzCosmosDBRestorableDatabaseAccount` Power shell-opdracht uit om alle accounts weer te geven die kunnen worden hersteld in het huidige abonnement.

Het antwoord bevat alle database accounts (zowel live als verwijderd) die kunnen worden hersteld en de regio's waarvan ze kunnen worden hersteld.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Net als de `CreationTime` of `DeletionTime` voor het account, is er `CreationTime` ook een of `DeletionTime` voor de regio. Op deze momenten kunt u de juiste regio en een geldig tijds bereik kiezen om in die regio te herstellen.

**Alle versies van SQL-data bases in een live-database account weer geven**

Door alle versies van data bases weer te geven, kunt u de juiste data base kiezen in een scenario waarin de werkelijke tijd voor het bestaan van de data base onbekend is.

Voer de volgende Power shell-opdracht uit om alle versies van data bases weer te geven. Deze opdracht werkt alleen met Live-accounts. De `DatabaseAccountInstanceId` `LocationName` para meters en worden opgehaald uit `name` de `location` Eigenschappen en in het antwoord van de `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet. Het `DatabaseAccountInstanceId` kenmerk verwijst naar de `instanceId` eigenschap van het bron database account dat wordt hersteld:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Alle versies van SQL-containers van een data base in een live-database account weer geven.**

Gebruik de volgende opdracht om alle versies van SQL-containers weer te geven. Deze opdracht werkt alleen met Live-accounts. De `DatabaseRid` para meter is de `ResourceId` Data Base die u wilt herstellen. Het is de waarde van het kenmerk dat is `ownerResourceid` gevonden in het antwoord van de `Get-AzCosmosdbSqlRestorableDatabase` cmdlet. Het antwoord bevat ook een lijst met bewerkingen die worden uitgevoerd op alle containers in deze data base.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Data bases of containers zoeken die kunnen worden hersteld met een wille keurig tijds tempel**

Gebruik de volgende opdracht om de lijst met data bases of containers op te halen die kunnen worden hersteld op een wille keurige tijds tempel. Deze opdracht werkt alleen met Live-accounts.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Herstorable bronnen voor MongoDB opsommen

Met de opsommings opdrachten die hieronder worden beschreven, kunt u de resources ontdekken die kunnen worden hersteld met verschillende tijds tempels. Daarnaast bieden ze ook een feed van belang rijke gebeurtenissen voor het herstorable account, de data base en de container bronnen. Deze opdrachten werken alleen voor Live-accounts en ze zijn vergelijkbaar met SQL-API-opdrachten, maar met `MongoDB` in de opdracht naam in plaats van `sql` .

**Alle versies van MongoDB-data bases in een live-database account weer geven**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Alle versies van MongoDb-verzamelingen van een data base in een live-database account weer geven**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Een lijst met alle resources van een MongoDb-database account die beschikbaar zijn voor herstel bij een bepaalde tijds tempel en regio**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Volgende stappen

* Configureer en beheer doorlopende back-ups met behulp van [Azure cli](continuous-backup-restore-command-line.md), [Resource Manager](continuous-backup-restore-template.md)of [Azure Portal](continuous-backup-restore-portal.md).
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
