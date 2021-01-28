---
title: Onderbreken, hervatten, schalen met REST-Api's
description: Beheer de reken kracht voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics via REST Api's.
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
ms.openlocfilehash: 41436da5ed9d82b44a9e1e63fb023c163a9761cf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934221"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>REST-Api's voor toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics

REST-Api's voor het beheren van de reken kracht voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics.

## <a name="scale-compute"></a>De schaal van Compute aanpassen

Als u de Data Warehouse-eenheden wilt wijzigen, gebruikt u de REST API [Data Base maken of bijwerken](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . In het volgende voor beeld worden de Data Warehouse-eenheden ingesteld op DW1000 voor de data base-MySQLDW, die wordt gehost op server mijnserver. De server bevindt zich in een Azure-resourcegroep met de naam ResourceGroep1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": "DW1000c"
    }
}
```

## <a name="pause-compute"></a>Berekening onderbreken

Gebruik de [pause data base](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) rest API om een Data Base te onderbreken. In het volgende voor beeld wordt een Data Base met de naam Database02 die wordt gehost op een server met de naam Server01, onderbroken. De server bevindt zich in een Azure-resourcegroep met de naam ResourceGroep1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Berekening hervatten

Als u een Data Base wilt starten, gebruikt u de REST API [Data Base hervatten](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . In het volgende voor beeld wordt een Data Base met de naam Database02 gehost op een server met de naam Server01. De server bevindt zich in een Azure-resourcegroep met de naam ResourceGroep1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Database status controleren

> [!NOTE]
> Momenteel kan de status van de data base ONLINE worden geretourneerd terwijl de data base de online werk stroom voltooit, wat resulteert in verbindings fouten. Mogelijk moet u een vertraging van 2 tot 3 minuten toevoegen aan de toepassings code als u deze API-aanroep gebruikt om verbindings pogingen te activeren.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Onderhouds planning ophalen

Controleer het onderhouds schema dat is ingesteld voor een toegewezen SQL-groep (voorheen SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Onderhouds planning instellen

Voor het instellen en bijwerken van een onderhouds planning voor een bestaande toegewezen SQL-groep (voorheen SQL DW).

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

Zie [Compute beheren](sql-data-warehouse-manage-compute-overview.md)voor meer informatie.
