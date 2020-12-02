---
title: Werken met opgeslagen procedures
description: Tips voor het ontwikkelen van oplossingen door het implementeren van opgeslagen procedures voor toegewezen SQL-groepen in azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cc6a58b4ef78aca60d2a26870980e032c0b11a52
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463214"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Opgeslagen procedures gebruiken voor toegewezen SQL-groepen in azure Synapse Analytics

In dit artikel vindt u tips voor het ontwikkelen van specifieke oplossingen voor SQL-groepen door het implementeren van opgeslagen procedures.

## <a name="what-to-expect"></a>Wat u kunt verwachten

De toegewezen SQL-groep ondersteunt veel van de T-SQL-functies die worden gebruikt in SQL Server. Belang rijker is dat er specifieke functies zijn die u kunt gebruiken om de prestaties van uw oplossing te optimaliseren.

Om u te helpen de schaal en prestaties van de toegewezen SQL-groep te behouden, zijn er aanvullende functies en functionaliteiten die gedrags verschillen hebben.

## <a name="introducing-stored-procedures"></a>Inleiding tot opgeslagen procedures

Opgeslagen procedures zijn een uitstekende manier om uw SQL-code te integreren, die wordt opgeslagen in de buurt van uw toegewezen SQL-groeps gegevens. Opgeslagen procedures helpen ontwikkel aars ook om hun oplossingen te modularize door de code in te delen in beheer bare eenheden, waardoor een grotere bruikbaarheid van code kan worden vergemakkelijkt. Elke opgeslagen procedure kan ook para meters accepteren om ze nog flexibeler te maken.

De toegewezen SQL-groep biedt een vereenvoudigde en gestroomlijnde implementatie van opgeslagen procedures. Het grootste verschil ten opzichte van SQL Server is dat de opgeslagen procedure geen vooraf gecompileerde code is.

Over het algemeen geldt dat voor data warehouses de compilatie tijd klein is in vergelijking met de tijd die nodig is om query's uit te voeren op grote gegevens volumes. Het is belang rijker om ervoor te zorgen dat de opgeslagen procedure code correct is geoptimaliseerd voor grote query's.

> [!TIP]
> Het doel is om uren, minuten en seconden op te slaan, niet in milliseconden. Het is dus handig om opgeslagen procedures te beschouwen als containers voor SQL-logica.

Wanneer een toegewezen SQL-groep uw opgeslagen procedure uitvoert, worden de SQL-instructies geparseerd, vertaald en geoptimaliseerd tijdens runtime. Tijdens dit proces wordt elke instructie geconverteerd naar gedistribueerde query's. De SQL-code die wordt uitgevoerd op basis van de gegevens, wijkt af van de query die is verzonden.

## <a name="nesting-stored-procedures"></a>Opgeslagen procedures nesten

Wanneer opgeslagen procedures andere opgeslagen procedures aanroepen of dynamische SQL uitvoeren, wordt de interne opgeslagen procedure of het aanroepen van de code genest.

De toegewezen SQL-pool ondersteunt Maxi maal acht geneste niveaus. Het nest niveau in SQL Server daarentegen is 32.

De op het hoogste niveau opgeslagen procedure aanroep is gelijk aan nest niveau 1.

```sql
EXEC prc_nesting
```

Als de opgeslagen procedure ook een andere EXEC-aanroep maakt, neemt het nest niveau toe tot twee.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Als de tweede procedure vervolgens een aantal dynamische SQL-procedures uitvoert, neemt het nest niveau toe tot drie.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

De toegewezen SQL-groep biedt momenteel geen ondersteuning voor [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Als zodanig moet u het nest niveau bijhouden. Het is niet waarschijnlijk dat u de limiet van acht geneste niveaus overschrijdt. Maar als u dat wel doet, moet u uw code opnieuw instellen zodat deze overeenkomt met de geneste niveaus binnen deze limiet.

## <a name="insertexecute"></a>INSERT..EXESCHATTIGE

De toegewezen SQL-groep staat niet toe dat u de resultatenset van een opgeslagen procedure gebruikt met een instructie INSERT. Er is echter een andere methode die u kunt gebruiken. Zie het artikel over [tijdelijke tabellen](sql-data-warehouse-tables-temporary.md)voor een voor beeld.

## <a name="limitations"></a>Beperkingen

Er zijn een aantal aspecten van opgeslagen Transact-SQL-procedures die niet zijn geïmplementeerd in een toegewezen SQL-groep. Dit zijn de volgende:

* tijdelijke opgeslagen procedures
* genummerde opgeslagen procedures
* uitgebreide opgeslagen procedures
* Opgeslagen CLR-procedures
* versleutelings optie
* replicatie optie
* tabelwaardeparameter
* alleen-lezen-para meters
* standaard parameters
* uitvoerings contexten
* instructie return

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
