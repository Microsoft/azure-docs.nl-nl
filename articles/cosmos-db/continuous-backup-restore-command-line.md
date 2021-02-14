---
title: Gebruik Azure CLI voor het configureren van doorlopende back-up en herstel naar een tijdstip in Azure Cosmos DB.
description: Informatie over het inrichten van een account met doorlopende back-up en herstel van gegevens met behulp van Azure CLI.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377326"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Continue back-ups en herstel tijdstippen configureren en beheren (preview)-Azure CLI gebruiken
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de functie voor het terugzetten van het tijdstip van een onbedoelde wijziging in een container kunt u een verwijderde account, data base of container herstellen of herstellen in een regio (waarbij back-ups bestaan). Azure Cosmos DB Met de continue back-upmodus kunt u een herstel bewerking uitvoeren naar een wille keurig tijdstip in de afgelopen 30 dagen.

In dit artikel wordt beschreven hoe u een account met doorlopende back-up-en herstel gegevens kunt inrichten met behulp van Azure CLI.

## <a name="install-azure-cli"></a><a id="install"></a>Azure CLI installeren

1. De nieuwste versie van Azure CLI installeren

   * Installeer de nieuwste versie van de [Azure cli](/cli/azure/install-azure-cli) of versie hoger dan 2.17.1.
   * Als u de CLI al hebt geïnstalleerd, voert u `az upgrade` de opdracht uit om naar de nieuwste versie bij te werken. Deze opdracht werkt alleen met een CLI-versie die hoger is dan 2,11. Als u een eerdere versie hebt, gebruikt u de bovenstaande koppeling om de nieuwste versie te installeren.

1. Installeer de `cosmosdb-preview` cli-extensie.

   * De opdrachten voor herstel naar een bepaald tijdstip zijn beschikbaar onder `cosmosdb-preview` uitbrei ding.
   * U kunt deze uitbrei ding installeren door de volgende opdracht uit te voeren: `az extension add --name cosmosdb-preview`
   * U kunt deze uitbrei ding verwijderen door de volgende opdracht uit te voeren: `az extension remove --name cosmosdb-preview`

1. Aanmelden en abonnement selecteren

   * Meld u aan bij uw Azure-account met de `az login` opdracht.
   * Selecteer het vereiste abonnement met behulp van de `az account set -s <subscriptionguid>` opdracht.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Een SQL-API-account inrichten met doorlopende back-up

Als u een SQL-API-account wilt inrichten met doorlopende back-up, moet u een extra argument `--backup-policy-type Continuous` door gegeven, samen met de normale inrichtings opdracht. De volgende opdracht is een voor beeld van één regio schrijf account `pitracct2` met een continu back-upbeleid dat is gemaakt in de regio *VS-West* onder *myrg* -resource groep:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Een Azure Cosmos DB-API inrichten voor een MongoDB-account met doorlopende back-up

Met de volgende opdracht wordt een voor beeld weer gegeven van een schrijf account voor één regio `pitracct3` met de naam met continu back-upbeleid is de regio *VS West* onder *myrg* -resource groep gemaakt:

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Een herstel bewerking activeren met CLI

De eenvoudigste manier om een herstel bewerking te activeren, is door de Restore-opdracht te geven met de naam van het doel account, bron account, locatie, resource groep, tijds tempel (in UTC) en optioneel de data base-en container namen. Hier volgen enkele voor beelden van het activeren van de herstel bewerking:

1. Maak een nieuw Azure Cosmos DB-account door het herstellen van een bestaand account.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Maak een nieuw Azure Cosmos DB-account door alleen geselecteerde data bases en containers van een bestaand database account te herstellen.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Herstorable bronnen voor SQL-API opsommen

Met de opsommings opdrachten die hieronder worden beschreven, kunt u de resources ontdekken die kunnen worden hersteld met verschillende tijds tempels. Daarnaast bieden ze ook een feed van belang rijke gebeurtenissen voor het herstorable account, de data base en de container bronnen.

**Alle accounts weer geven die kunnen worden hersteld in het huidige abonnement**

Voer de volgende CLI-opdracht uit om alle accounts weer te geven die in het huidige abonnement kunnen worden hersteld

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

Het antwoord bevat alle database accounts (zowel live als verwijderd) die kunnen worden hersteld en de regio's waarvan ze kunnen worden hersteld:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Net als de `CreationTime` of `DeletionTime` voor het account, is er `CreationTime` ook een of `DeletionTime` voor de regio. Op deze momenten kunt u de juiste regio en een geldig tijds bereik kiezen om in die regio te herstellen.

**Alle versies van data bases in een live-database account weer geven**

Door alle versies van data bases weer te geven, kunt u de juiste data base kiezen in een scenario waarin de werkelijke tijd voor het bestaan van de data base onbekend is.

Voer de volgende CLI-opdracht uit om alle versies van data bases weer te geven. Deze opdracht werkt alleen met Live-accounts. De `instanceId` `location` para meters en worden opgehaald uit `name` de `location` Eigenschappen en in het antwoord van de `az cosmosdb restorable-database-account list` opdracht. Het kenmerk instanceId is ook een eigenschap van het bron database account dat wordt hersteld:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Deze opdracht uitvoer wordt nu weer gegeven wanneer een Data Base is gemaakt en verwijderd.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Alle versies van SQL-containers van een data base in een live data base-account weer geven**

Gebruik de volgende opdracht om alle versies van SQL-containers weer te geven. Deze opdracht werkt alleen met Live-accounts. De `databaseRid` para meter is de `ResourceId` Data Base die u wilt herstellen. Het is de waarde van het kenmerk dat is `ownerResourceid` gevonden in het antwoord van de `az cosmosdb sql restorable-database list` opdracht.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Met deze opdracht uitvoer wordt een lijst met bewerkingen weer gegeven die zijn uitgevoerd op alle containers in deze Data Base:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Data bases of containers zoeken die kunnen worden hersteld met een wille keurig tijds tempel**

Gebruik de volgende opdracht om de lijst met data bases of containers op te halen die kunnen worden hersteld op een wille keurige tijds tempel. Deze opdracht werkt alleen met Live-accounts.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Herstorable bronnen voor het MongoDB-API-account opsommen

Met de opsommings opdrachten die hieronder worden beschreven, kunt u de resources ontdekken die kunnen worden hersteld met verschillende tijds tempels. Daarnaast bieden ze ook een feed van belang rijke gebeurtenissen voor het herstorable account, de data base en de container bronnen. Net als bij SQL-API kunt u de `az cosmosdb` opdracht gebruiken, maar met `mongodb` als para meter in plaats van `sql` . Deze opdrachten werken alleen voor Live-accounts.

**Alle versies van MongoDb-data bases in een live-database account weer geven**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Alle versies van MongoDb-verzamelingen van een data base in een live-database account weer geven**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Een lijst met alle resources van een MongoDb-database account die beschikbaar zijn voor herstel bij een bepaalde tijds tempel en regio**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Volgende stappen

* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [power shell](continuous-backup-restore-powershell.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
