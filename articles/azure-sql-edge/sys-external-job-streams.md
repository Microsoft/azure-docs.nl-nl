---
title: sys.external_job_streams (Transact-SQL)-Azure SQL Edge
description: Meer informatie over het gebruik van sys.external_job_streams in Azure SQL Edge
keywords: sys.external_job_streams, SQL-rand
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 35010d3aba7f6d5ee3185291c917ff7726ba8bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900360"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Retourneert een rij voor het externe stroom object input of output dat is toegewezen aan een externe streaming-taak.

|Kolomnaam|Gegevenstype|Beschrijving|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Object-id-nummer voor het streaming-taak object. Deze kolom is gekoppeld aan de object_id kolom van sys.external_streaming_jobs.|
|**stream_id**|**int**| Object-id-nummer voor het Stream-object. Deze kolom is gekoppeld aan de object_id kolom van sys.external_streams. |
|**is_input**|**bitmask**| 1 als het Stream-object wordt gebruikt als invoer voor de streaming-taak, anders 0.|
|**is_output**|**bitmask**| 1 als het Stream-object wordt gebruikt als uitvoer voor de streaming-taak, anders 0.|

## <a name="example"></a>Voorbeeld

Deze catalogus weergave wordt samen met `sys.external_streams` en `sys.external_streaming_jobs` catalogus weergaven gebruikt. Hieronder ziet u een voor beeld van een query

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Machtigingen

De zicht baarheid van de meta gegevens in catalogus weergaven is beperkt tot Beveilig bare items die eigendom zijn van een gebruiker of waarvoor de gebruiker toestemming heeft gekregen. Zie voor meer informatie [configuratie van zicht baarheid van meta gegevens](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Zie ook

- [Catalogus weergaven (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systeem weergaven (Transact-SQL)](/sql/t-sql/language-reference/)
