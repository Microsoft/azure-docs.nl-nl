---
title: Dynamische SQL gebruiken in Synapse SQL
description: Tips voor het gebruik van dynamische SQL in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 86d5028a09a805142f7632f93530f8a54965d82f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675032"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamische SQL in Synapse SQL

In dit artikel vindt u tips voor het gebruik van dynamische SQL en het ontwikkelen van oplossingen met behulp van Synapse SQL.

## <a name="dynamic-sql-example"></a>Voor beeld van dynamische SQL

Bij het ontwikkelen van toepassings code moet u mogelijk gebruikmaken van dynamische SQL om flexibele, algemene en modulaire oplossingen te leveren.

> [!NOTE]
> De toegewezen SQL-pool ondersteunt op dit moment geen BLOB-gegevens typen. Het ondersteunen van BLOB-gegevens typen kan de grootte van uw teken reeksen beperken omdat de BLOB-gegevens typen zowel varchar (max) als nvarchar (max)-typen bevatten. Als u deze typen in de toepassings code hebt gebruikt om grote teken reeksen te maken, moet u de code in segmenten afsplitsen en in plaats daarvan de instructie EXEC gebruiken.

Een eenvoudig voor beeld:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Als de teken reeks kort is, kunt u [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?view=azure-sqldw-latest&preserve-view=true) als normaal gebruiken.

> [!NOTE]
> Voor de instructies die worden uitgevoerd als dynamische SQL gelden alle regels voor T-SQL-validatie.

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.
