---
title: Veelgestelde vragen over Azure Cosmos DB functie voor herstel naar een bepaald tijdstip.
description: In dit artikel vindt u een lijst met veelgestelde vragen over de Azure Cosmos DB functie voor herstel naar een bepaald tijdstip met behulp van de doorlopende back-upmodus.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393221"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Veelgestelde vragen over de functie voor het terugzetten van Azure Cosmos DB naar een bepaald tijdstip (preview-versie)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel vindt u een lijst met veelgestelde vragen over de functionaliteit van het Azure Cosmos DB herstel naar een bepaald tijdstip (preview) die wordt behaald met behulp van de doorlopende back-upmodus.

## <a name="how-much-time-does-it-takes-to-restore"></a>Hoe lang duurt het om te herstellen?
De duur van het herstel is afhankelijk van de grootte van uw gegevens.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Kan ik de herstel tijd in de lokale tijd indienen?
Het herstel kan niet worden uitgevoerd, afhankelijk van het feit of de belangrijkste resources zoals data bases of containers op dat moment bestaan. U kunt controleren door de tijd in te voeren en te kijken naar de geselecteerde data base of container voor een bepaalde tijd. Als er geen resources zijn om te herstellen, werkt het herstel proces niet.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Hoe kan ik bijhouden of een account wordt hersteld?
Nadat u de opdracht Restore hebt verzonden en op dezelfde pagina wacht, nadat de bewerking is voltooid, wordt in de status balk het account bericht hersteld weer gegeven. U kunt ook zoeken naar het herstelde account en [de status bijhouden van het account dat wordt hersteld](continuous-backup-restore-portal.md#track-restore-status). Wanneer het terugzetten wordt uitgevoerd, wordt de status van het account *gemaakt* nadat de herstel bewerking is voltooid, wordt de account status gewijzigd in *online*.

Net als bij Power shell en CLI kunt u de voortgang van de herstel bewerking volgen door de `az cosmosdb show` opdracht als volgt uit te voeren:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

De provisioningState wordt *weer gegeven* wanneer het account online is.

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

In de volgende uitvoer is de exemplaar-ID van het bron account bijvoorbeeld *7b4bb-f6a0-430E-ade1-638d781830cc*

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
Azure Cosmos DB account eigenschap is op een bepaald moment in de tijd `accountName` wereld wijd uniek wanneer deze actief is. Nadat het account is verwijderd, is het echter mogelijk dat u een ander account met dezelfde naam maakt en dat de "AccountName" niet langer genoeg is om een exemplaar van een account te identificeren. 

ID of de `instanceId` is een eigenschap van een exemplaar van een account en deze wordt gebruikt om te dubbel zinnigheid voor meerdere accounts (Live en verwijderd) als deze dezelfde naam hebben voor terugzetten. U kunt de exemplaar-ID ophalen door de of-opdrachten uit te voeren `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` . De waarde van het kenmerk name geeft de ' InstanceID ' aan.

## <a name="next-steps"></a>Volgende stappen

* Wat is de [continue back-](continuous-backup-restore-introduction.md) upmodus?
* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)