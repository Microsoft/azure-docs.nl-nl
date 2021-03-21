---
title: Gebruik ARM-sjabloon voor het configureren van doorlopende back-up en herstel naar een tijdstip in Azure Cosmos DB.
description: Meer informatie over het inrichten van een account met doorlopende back-up en herstel van gegevens met Azure Resource Manager sjablonen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381814"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Continue back-ups en herstel naar een tijdstip configureren en beheren (preview)-Azure Resource Manager sjablonen gebruiken
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de functie voor het terugzetten van het tijdstip van een onbedoelde wijziging in een container kunt u een verwijderde account, data base of container herstellen of herstellen in een regio (waarbij back-ups bestaan). Azure Cosmos DB Met de continue back-upmodus kunt u een herstel bewerking uitvoeren naar een wille keurig tijdstip in de afgelopen 30 dagen.

In dit artikel wordt beschreven hoe u een account met doorlopende back-up-en herstel gegevens kunt inrichten met behulp van Azure Resource Manager sjablonen.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Een account inrichten met doorlopende back-up

U kunt Azure Resource Manager sjablonen gebruiken om een Azure Cosmos DB account te implementeren met doorlopende modus. Wanneer u de sjabloon voor het inrichten van een account definieert, neemt u de `backupPolicy` para meter op zoals weer gegeven in het volgende voor beeld:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Implementeer de sjabloon vervolgens door Azure PowerShell of CLI te gebruiken. In het volgende voor beeld ziet u hoe u de sjabloon implementeert met een CLI-opdracht:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Herstellen met behulp van de Resource Manager-sjabloon

U kunt ook een account herstellen met behulp van Resource Manager-sjabloon. Bij het definiëren van de sjabloon zijn de volgende para meters:

* Stel de `createMode` para meter in op *herstellen*
* Definieer de `restoreParameters` , Let op: de `restoreSource` waarde wordt opgehaald uit de uitvoer van de `az cosmosdb restorable-database-account list` opdracht voor uw bron account. Het kenmerk instance ID voor uw account naam wordt gebruikt om de herstel bewerking uit te voeren.
* Stel de `restoreMode` para meter in op *PointInTime* en configureer de `restoreTimestampInUtc` waarde.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Implementeer de sjabloon vervolgens door Azure PowerShell of CLI te gebruiken. In het volgende voor beeld ziet u hoe u de sjabloon implementeert met een CLI-opdracht:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Volgende stappen

* Configureer en beheer doorlopende back-ups met behulp van [Azure cli](continuous-backup-restore-command-line.md), [power shell](continuous-backup-restore-command-line.md)of [Azure Portal](continuous-backup-restore-portal.md).
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.