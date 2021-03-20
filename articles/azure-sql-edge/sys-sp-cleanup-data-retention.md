---
title: sys.sp_cleanup_data_retention (Transact-SQL)-Azure SQL Edge
description: Meer informatie over het gebruik van sys.sp_cleanup_data_retention (Transact-SQL) in Azure SQL Edge
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935756"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Van toepassing op:** Azure SQL Edge

Hiermee wordt het opruimen van verouderde records uit tabellen met ingeschakelde beleids regels voor het bewaren van gegevens. Zie [gegevens retentie](data-retention-overview.md)voor meer informatie. 

## <a name="syntax"></a>Syntaxis 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argumenten  
`[ @schema_name = ] schema_name`    
 De naam is van het schema dat eigenaar is van de tabel waarop de opschoning moet worden uitgevoerd. *SCHEMA_NAME* is een vereiste para meter van het type **SysName**.
  
`[ @table_name = ] 'table_name'`    
 Is de naam van de tabel waarop opschonings bewerking moet worden uitgevoerd. *table_name* is een vereiste para meter van het type **SysName**.

## <a name="output-parameter"></a>Uitvoer parameter  

`[ @rowcount = ] rowcount OUTPUT`   
 RowCount is een optionele uitvoer parameter die het aantal opschoon records van de tabel vertegenwoordigt. *RowCount* is int.
  
## <a name="permissions"></a>Machtigingen    
 Vereist db_owner machtigingen.

## <a name="next-steps"></a>Volgende stappen
- [Bewaren van gegevens en automatisch opschonen van gegevens](data-retention-overview.md)
- [Historische gegevens beheren met Bewaar beleid](data-retention-cleanup.md) 
- [Bewaren van gegevens in-en uitschakelen](data-retention-enable-disable.md)
