---
title: sys.external_streaming_jobs (Transact-SQL)-Azure SQL Edge
description: Meer informatie over het gebruik van sys.external_streaming_jobs in Azure SQL Edge
keywords: sys.external_streaming_jobs, SQL-rand
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90900344"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Retourneert een rij voor elke externe streaming-taak die binnen het bereik van de data base is gemaakt.

|Kolomnaam|Gegevenstype|Beschrijving|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|De naam van de stroom. Is uniek binnen de data base.|
|**object_id**|**int**|object-id-nummer voor het Stream-object. Is uniek binnen de data base.|
|**principal_id**|**int**|ID van de principal die eigenaar is van deze assembly|
|**schema_id**|**int**| De ID van het schema dat het object bevat.|
|**parent_object_id**|**id**| object-id-nummer voor het bovenliggende object voor deze stroom. In de huidige implementatie is deze waarde altijd Null|
|**type**|**char (2)**|Object type. Voor Stream-objecten is het type altijd ' EJ '|
|**type_desc**|**nvarchar (60)**| Beschrijving van het object type. Voor Stream-objecten is het type altijd ' EXTERNAL_STREAMING_JOB '|
|**create_date**|**datum/tijd**| De datum waarop het object is gemaakt.|
|**modify_date**|**datum/tijd**| In de huidige implementatie is deze waarde hetzelfde als de create_date voor het Stream-object |
|**is_ms_shipped**|**bitmask**| Object gemaakt door een intern onderdeel.|  
|**is_published**|**bitmask**| Het object wordt gepubliceerd.|  
|**is_schema_published**|**bitmask**|Alleen het schema van het object wordt gepubliceerd.|
|**uses_ansi_nulls**|**bitmask**| Stream-object is gemaakt met de optie SET ANSI_NULLS Data Base op|
|**rekeningen**|**varchar (max)**| De tekst van de stream Analytics-query voor de streaming-taak. Zie [sp_create_streaming_job](overview.md) voor meer informatie. |
|**status**|**int**| De huidige status van de streaming-taak. De mogelijke waarden zijn <br /><br /> **Gemaakt** = 0. De streaming-taak is gemaakt, maar is nog niet gestart. <br /><br /> **Begin** = 1. De streaming-taak bevindt zich in de begin fase. <br /><br /> **Mislukt** = 6. De streaming-taak is mislukt. Dit is doorgaans een indicatie van een fatale fout tijdens de verwerking. <br /><br /> **Gestopt** = 4. De streaming-taak is gestopt. <br /><br /> **Inactief** = 7. De streaming-taak wordt uitgevoerd, maar er is geen invoer om te verwerken. <br /><br /> **Verwerken** = 8. De streaming-taak wordt uitgevoerd en verwerkt de invoer. Deze status geeft aan dat de streaming-taak een goede status heeft. <br /><br /> **Gedegradeerd** = 9. De streaming-taak wordt uitgevoerd, maar er zijn een aantal niet-fatale invoer/uitvoer-serialisatie/deserialisatie-fouten opgetreden tijdens de invoer verwerking. De invoer taak wordt nog steeds uitgevoerd, maar gaat weg van invoer die fouten tegen komt.|

## <a name="permissions"></a>Machtigingen

De zicht baarheid van de meta gegevens in catalogus weergaven is beperkt tot Beveilig bare items die eigendom zijn van een gebruiker of waarvoor de gebruiker toestemming heeft gekregen. Zie voor meer informatie [configuratie van zicht baarheid van meta gegevens](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Zie ook

- [Catalogus weergaven voor T-SQL-streaming](overview.md)
- [Catalogus weergaven (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systeem weergaven (Transact-SQL)](/sql/t-sql/language-reference/)

