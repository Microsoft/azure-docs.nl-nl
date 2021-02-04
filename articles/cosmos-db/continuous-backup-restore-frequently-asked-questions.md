---
title: Veelgestelde vragen over Azure Cosmos DB functie voor herstel naar een bepaald tijdstip.
description: In dit artikel vindt u een lijst met veelgestelde vragen over de Azure Cosmos DB functie voor herstel naar een bepaald tijdstip met behulp van de doorlopende back-upmodus.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 7a41eb8bdd573ac08b0c76eb9a6c2b0724637c39
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527720"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature"></a>Veelgestelde vragen over de functie voor het terugzetten van Azure Cosmos DB naar een bepaalde tijd
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In dit artikel vindt u een lijst met veelgestelde vragen over de functionaliteit van het Azure Cosmos DB herstel naar een bepaald tijdstip met behulp van de doorlopende back-upmodus.

## <a name="how-much-time-does-it-takes-to-restore"></a>Hoe lang duurt het om te herstellen?
De duur van het herstel is afhankelijk van de grootte van uw gegevens.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Kan ik de herstel tijd in de lokale tijd indienen?
Het herstel kan niet worden uitgevoerd, afhankelijk van het feit of de belangrijkste resources zoals data bases of containers op dat moment bestaan. U kunt controleren door de tijd in te voeren en te kijken naar de geselecteerde data base of container voor een bepaalde tijd. Als er geen resources zijn om te herstellen, werkt het herstel proces niet.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Hoe kan ik bijhouden of een account wordt hersteld?
Nadat u de opdracht Restore hebt verzonden en op dezelfde pagina wacht, nadat de bewerking is voltooid, wordt in de status balk het account bericht hersteld weer gegeven. U kunt ook zoeken naar het herstelde account en [de status bijhouden van het account dat wordt hersteld](continuous-backup-restore-portal.md#track-restore-status). Wanneer de herstel bewerking wordt uitgevoerd, wordt de status van het account ' maken ', nadat het herstel is voltooid, wordt de account status gewijzigd in ' online '.

Net als bij Power shell en CLI kunt u de voortgang van de herstel bewerking volgen door de `az cosmosdb show` opdracht als volgt uit te voeren:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

De provisioningState toont ' geslaagd ' als het account online is.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Hoe kan ik controleren of een account is teruggezet vanuit een ander account?
Voer de `az cosmosdb show` opdracht uit. in de uitvoer kunt u zien dat de waarde van de `createMode` eigenschap. Als de waarde is ingesteld op **herstellen**. Dit geeft aan dat het account is hersteld vanuit een ander account. De `restoreParameters` eigenschap heeft meer details, zoals `restoreSource` , die de bron account-id heeft. De laatste GUID in de `restoreSource` para meter is de instanceId van het bron account.

In de volgende uitvoer is de exemplaar-ID van het bron account bijvoorbeeld "7b4bb-f6a0-430E-ade1-638d781830cc"

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Wat gebeurt er wanneer ik een gedeelde doorvoer database of een container in een gedeelde doorvoer database herstel?
De hele gedeelde doorvoer database wordt hersteld. U kunt geen subset van containers in een gedeelde doorvoer database kiezen om te herstellen.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Wat is het gebruik van InstanceID in de account definitie?
De eigenschap AccountName van Azure Cosmos DB account is op elk gewenst moment wereld wijd uniek wanneer deze actief is. Nadat het account is verwijderd, is het echter mogelijk dat u een ander account met dezelfde naam maakt en dat de "AccountName" niet langer genoeg is om een exemplaar van een account te identificeren. 

ID of ' instanceId ' is een eigenschap van een exemplaar van een account en wordt gebruikt om te dubbel zinnigheid voor meerdere accounts (Live en verwijderd) als deze dezelfde naam hebben voor herstellen. U kunt de exemplaar-ID ophalen door de of-opdrachten uit te voeren `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` . De waarde van het kenmerk name geeft de ' InstanceID ' aan.

## <a name="next-steps"></a>Volgende stappen

* Wat is de [continue back-](continuous-backup-restore-introduction.md) upmodus?
* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)