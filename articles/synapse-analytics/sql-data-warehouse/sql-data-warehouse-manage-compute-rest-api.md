---
title: Onderbreken, hervatten, schalen met REST API's voor toegewezen SQL-pool (voorheen SQL DW)
description: Beheer de rekenkracht voor toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics via REST API's.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: aab2897b4042657492d04494b589fbaa2605cc6d
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886780"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>REST API's voor toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics

REST API's voor het beheren van rekenkracht voor toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics.

> [!NOTE]
> De REST API's die in dit artikel worden beschreven, zijn niet van toepassing op een toegewezen SQL-pool die is gemaakt in een Azure Synapse Analytics werkruimte. Zie Azure Synapse Analytics workspace REST API voor meer informatie over REST API's die specifiek voor een [Azure Synapse Analytics-werkruimte moeten worden REST API.](/rest/api/synapse/)

## <a name="scale-compute"></a>De schaal van Compute aanpassen

Als u de datawarehouse-eenheden wilt wijzigen, gebruikt u [de](/rest/api/sql/databases/createorupdate) REST API. In het volgende voorbeeld worden de datawarehouse-eenheden ingesteld op DW1000 voor de database MySQLDW, die wordt gehost op server MyServer. De server bevindt zich in een Azure-resourcegroep met de naam ResourceGroep1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>Berekening onderbreken

Als u een database wilt onderbreken, gebruikt u [de REST API.](/rest/api/sql/databases/pause) In het volgende voorbeeld wordt een database met de naam Database02 onderbroken die wordt gehost op een server met de naam Server01. De server bevindt zich in een Azure-resourcegroep met de naam ResourceGroep1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Berekening hervatten

Als u een database wilt starten, gebruikt u [de REST API.](/rest/api/sql/databases/resume) In het volgende voorbeeld wordt een database met de naam Database02 gestart die wordt gehost op een server met de naam Server01. De server bevindt zich in een Azure-resourcegroep met de naam ResourceGroep1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Databasetoestand controleren

> [!NOTE]
> Op dit moment retourneert Check database state mogelijk ONLINE terwijl de database de onlinewerkstroom voltooit, wat leidt tot verbindingsfouten. Mogelijk moet u een vertraging van 2 tot 3 minuten aan de toepassingscode toevoegen als u deze API-aanroep gebruikt om verbindingspogingen te activeren.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Onderhoudsschema op halen

Controleer de onderhoudsplanning die is ingesteld voor een toegewezen SQL-pool (voorheen SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Onderhoudsplanning instellen

Een onderhoudsschema instellen en bijwerken voor een bestaande toegewezen SQL-pool (voorheen SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Volgende stappen

Zie Compute beheren [voor meer informatie.](sql-data-warehouse-manage-compute-overview.md)
