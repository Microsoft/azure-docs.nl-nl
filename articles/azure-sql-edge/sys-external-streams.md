---
title: sys.external_streams (Transact-SQL)-Azure SQL Edge
description: Meer informatie over het gebruik van sys.external_streams in Azure SQL Edge
keywords: sys.external_streams, SQL-rand
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900310"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Retourneert een rij voor elk extern Stream-object dat binnen het bereik van de data base wordt gemaakt.

|Kolomnaam|Gegevenstype|Beschrijving|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|De naam van de stroom. Is uniek binnen de data base.|
|**object_id**|**int**|object-id-nummer voor het Stream-object. Is uniek binnen de data base.|
|**principal_id**|**int**|ID van de principal die eigenaar is van deze assembly|
|**schema_id**|**int**| De ID van het schema dat het object bevat.|
|**parent_object_id**|**id**| object-id-nummer voor het bovenliggende object voor deze stroom. In de huidige implementatie is deze waarde altijd Null|
|**type**|**char (2)**|Object type. Voor Stream-objecten is het type altijd ' ES '|
|**type_desc**|**nvarchar (60)**| Beschrijving van het object type. Voor Stream-objecten is het type altijd ' EXTERNAL_STREAM '|
|**create_date**|**datum/tijd**| De datum waarop het object is gemaakt.|
|**modify_date**|**datum/tijd**| De datum waarop het object voor het laatst is gewijzigd met behulp van een instructie ALTER.|
|**is_ms_shipped**|**bitmask**| Object gemaakt door een intern onderdeel.|  
|**is_published**|**bitmask**|Het object wordt gepubliceerd.|  
|**is_schema_published**|**bitmask**|Alleen het schema van het object wordt gepubliceerd.|
|**max_column_id_used**|**bitmask**| Deze kolom wordt gebruikt voor interne doel einden en wordt in de toekomst verwijderd|  
|**uses_ansi_nulls**|**bitmask**| Stream-object is gemaakt met de optie SET ANSI_NULLS Data Base op|
|**data_source_id**|**int**| De object-ID voor de externe gegevens bron die wordt vertegenwoordigd door het Stream-object |  
|**file_format_id**|**int**| De object-ID voor de externe bestands indeling die wordt gebruikt door het Stream-object. De externe bestands indeling is vereist om de werkelijke indeling op te geven van de gegevens waarnaar wordt verwezen door een externe stroom| 
|**location**|**varchar (max)**| Het doel voor het externe Stream-object. Raadpleeg voor meer informatie [externe stroom maken](overview.md) |
|**input_option**|**varchar (max)**| De invoer opties die worden gebruikt tijdens het maken van de externe stroom. Raadpleeg voor meer informatie [externe stroom maken](overview.md) |
|**output_option**|**varchar (max)**| De uitvoer opties die worden gebruikt tijdens het maken van de externe stroom. Raadpleeg voor meer informatie [externe stroom maken](overview.md) | 

## <a name="permissions"></a>Machtigingen

De zicht baarheid van de meta gegevens in catalogus weergaven is beperkt tot Beveilig bare items die eigendom zijn van een gebruiker of waarvoor de gebruiker toestemming heeft gekregen. Zie voor meer informatie [configuratie van zicht baarheid van meta gegevens](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Zie ook

- [Catalogus weergaven (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systeem weergaven (Transact-SQL)](/sql/t-sql/language-reference/)
