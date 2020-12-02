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
ms.openlocfilehash: 3ab0188fa44eeb09e3981b8e1809832948963d47
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511638"
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
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (met inbegrip van functies voor tabel waarden)
  - Dien een [ondersteunings ticket of stem](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) in om de functie te kunnen ondersteunen.
  - Dien een [ondersteunings ticket of stem](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) in om de functie te kunnen ondersteunen.
- Tabellen met een [identiteits kolom](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)
- Bepaalde T-SQL-functies, zoals:
   - In de component *Group* in de functie String [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql?view=sqlallproducts-allversions) .

## <a name="next-steps"></a>Volgende stappen

Nu u de nieuwste versie van SSDT hebt, bent u klaar om [verbinding te maken](sql-data-warehouse-query-visual-studio.md) met uw SQL-groep.
