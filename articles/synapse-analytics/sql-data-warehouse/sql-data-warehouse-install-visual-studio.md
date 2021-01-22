---
title: Installeer Visual Studio 2019
description: Visual Studio en SQL Server Development tools (SSDT) voor Synapse SQL installeren
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 093193b8d74b443b4ff4b15da16c610cabb23b3b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676223"
---
# <a name="getting-started-with-visual-studio-2019"></a>Aan de slag met Visual Studio 2019

Visual Studio **2019** SQL Server Data tools (SSDT) is één hulp programma waarmee u het volgende kunt doen:

- Toepassingen verbinden, query's uitvoeren en ontwikkelen
- Maak gebruik van een object Verkenner om alle objecten in uw gegevens model visueel te verkennen, met inbegrip van tabellen, weer gaven, opgeslagen procedures en etc.
- Een DDL-script (T-SQL Data Definition Language) voor uw objecten genereren
- Ontwikkel uw data warehouse met behulp van een op status gebaseerde benadering met SSDT-database projecten
- Integreer uw database project met broncode beheer systemen zoals Git met Azure opslag plaatsen
- Continue integratie en implementatie pijplijnen instellen met automatiserings servers zoals Azure DevOps

## <a name="install-visual-studio-2019"></a>Installeer Visual Studio 2019

Zie [Visual studio 2019 downloaden](https://visualstudio.microsoft.com/downloads/) om visual studio **16,3 en hoger** te downloaden en te installeren. Selecteer tijdens de installatie de werk belasting gegevens opslag en-verwerking. Zelfstandige installatie van SSDT is niet meer vereist in Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Niet-ondersteunde functies in SSDT

Er zijn momenten waarop functie releases voor Synapse SQL mogelijk geen ondersteuning voor SSDT bevat. De volgende functies worden momenteel niet ondersteund:


- [Workload Management](sql-data-warehouse-workload-management.md) -werkbelasting groepen en classificaties
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (met inbegrip van functies voor tabel waarden)
  - Dien een [ondersteunings ticket of stem](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) in om de functie te kunnen ondersteunen.
  - Dien een [ondersteunings ticket of stem](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) in om de functie te kunnen ondersteunen.
- Tabellen met een [identiteits kolom](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)
- Bepaalde T-SQL-functies, zoals:
   - In de component *Group* in de functie String [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) .

## <a name="next-steps"></a>Volgende stappen

Nu u de nieuwste versie van SSDT hebt, bent u klaar om [verbinding te maken](sql-data-warehouse-query-visual-studio.md) met uw SQL-groep.