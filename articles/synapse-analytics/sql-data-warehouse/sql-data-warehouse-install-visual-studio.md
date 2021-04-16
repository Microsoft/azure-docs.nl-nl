---
title: Installeer Visual Studio 2019
description: Installeer Visual Studio en SQL Server Development Tools (SSDT) voor Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: c8c07997b3ef8cb050ea4609a650a3e3b1bd21fb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568247"
---
# <a name="getting-started-with-visual-studio-2019"></a>Aan de slag met Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) is een hulpprogramma waarmee u het volgende kunt doen:

- Toepassingen verbinden, er query's op uitvoeren en toepassingen ontwikkelen
- Maak gebruik van een objectverkenner om alle objecten in uw gegevensmodel visueel te verkennen, inclusief tabellen, weergaven, opgeslagen procedures, enzovoort.
- DDL-scripts (T-SQL Data Definition Language) genereren voor uw objecten
- Uw datawarehouse ontwikkelen met behulp van een state-based benadering met SSDT Database Projects
- Uw databaseproject integreren met broncodebeheersystemen zoals Git met Azure Repos
- Continue integratie- en implementatiepijplijnen instellen met automatiseringsservers zoals Azure DevOps

## <a name="install-visual-studio-2019"></a>Installeer Visual Studio 2019

Zie [Download Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) om Visual Studio **16.3 en hoger te downloaden en installeren.** Selecteer tijdens de installatie de workload voor gegevensopslag en -verwerking. Zelfstandige SSDT-installatie is niet langer vereist in Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Niet-ondersteunde functies in SSDT

Er zijn momenten waarop functiereleases voor Synapse SQL mogelijk geen ondersteuning voor SSDT bevatten. De volgende functies worden momenteel niet ondersteund:


- [Workloadbeheer](sql-data-warehouse-workload-management.md) - werkbelastinggroepen en classificaties
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (inclusief functies met tabelwaarde)
  - Dien een [ondersteuningsticket in of stem om](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) de functie te laten ondersteunen.
  - Dien een [ondersteuningsticket in of stem om](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) de functie te laten ondersteunen.
- Bepaalde T-SQL-functies, zoals:
   - *Component WITHIN GROUP* in de [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) tekenreeksfunctie.

## <a name="next-steps"></a>Volgende stappen

Nu u de nieuwste versie van SSDT hebt, kunt u verbinding [maken](sql-data-warehouse-query-visual-studio.md) met uw SQL-pool.