---
title: Kostenbeheer voor serverloze SQL-pool
description: In dit document wordt beschreven hoe u de kosten van een serverloze SQL-pool beheert en hoe de verwerkte gegevens worden berekend bij het opvragen van gegevens in azure Storage.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491566"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Kosten beheer voor serverloze SQL-groepen in azure Synapse Analytics

In dit artikel wordt uitgelegd hoe u kosten kunt ramen en beheren voor een serverloze SQL-groep in azure Synapse Analytics:
- Geschatte hoeveelheid gegevens die is verwerkt voordat een query wordt uitgegeven
- Kosten beheer functie gebruiken om het budget in te stellen

U begrijpt dat de kosten voor serverloze SQL-groepen in azure Synapse Analytics slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Als u andere Azure-Services gebruikt, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden. In dit artikel wordt uitgelegd hoe u kosten voor een serverloze SQL-groep plant en beheert in azure Synapse Analytics.

## <a name="data-processed"></a>Verwerkte gegevens

De gegevens die worden *verwerkt* , zijn de hoeveelheid gegevens die het systeem tijdelijk opslaat tijdens het uitvoeren van een query. De gegevens die worden verwerkt, bestaan uit de volgende aantallen:

- Hoeveelheid gegevens die uit de opslag ruimte is gelezen. Dit bedrag omvat:
  - Gegevens lezen tijdens het lezen van gegevens.
  - Gegevens lezen tijdens het lezen van meta gegevens (voor bestands indelingen die meta gegevens bevatten, zoals Parquet).
- Hoeveelheid gegevens in tussenliggende resultaten. Deze gegevens worden overgebracht tussen de knoop punten terwijl de query wordt uitgevoerd. Het bevat de gegevens overdracht naar uw eind punt, in een niet-gecomprimeerde indeling. 
- De hoeveelheid gegevens die naar de opslag wordt geschreven. Als u CETAS gebruikt om uw resultatenset naar opslag te exporteren, wordt de hoeveelheid gegevens die wordt geschreven, toegevoegd aan de hoeveelheid gegevens die wordt verwerkt voor het SELECT-deel van CETAS.

Het lezen van bestanden uit de opslag is zeer geoptimaliseerd. Het proces maakt gebruik van:

- Vooraf ophalen, waardoor enige overhead kan worden toegevoegd aan de hoeveelheid gegevens die moet worden gelezen. Als een query een heel bestand leest, is er geen overhead. Als een bestand gedeeltelijk wordt gelezen, zoals in de eerste N query's, worden er nog meer gegevens gelezen met vooraf ophalen.
- Een geoptimaliseerde parser met door komma's gescheiden waarden (CSV). Als u PARSER_VERSION = ' 2.0 ' gebruikt voor het lezen van CSV-bestanden, nemen de hoeveel heden gegevens die uit de opslag zijn gelezen, enigszins toe. Met een geoptimaliseerde CSV-parser worden bestanden parallel gelezen, in segmenten van gelijke grootte. Segmenten hoeven niet altijd hele rijen te bevatten. Om ervoor te zorgen dat alle rijen worden geparseerd, leest de geoptimaliseerde CSV-parser ook kleine fragmenten van aangrenzende segmenten. Dit proces voegt een kleine hoeveelheid overhead toe.

## <a name="statistics"></a>Statistieken

De query optimalisatie van de serverloze SQL-groep is afhankelijk van de statistieken voor het genereren van optimale query-uitvoerings plannen. U kunt statistieken hand matig maken. Anders worden ze door de serverloze SQL-groep automatisch gemaakt. In beide gevallen worden statistieken gemaakt door een afzonderlijke query uit te voeren die een specifieke kolom retourneert met een opgegeven sample frequentie. Aan deze query is een hoeveelheid gegevens verwerkt.

Als u dezelfde of een andere query uitvoert die kan profiteren van gemaakte statistieken, worden indien mogelijk de statistieken opnieuw gebruikt. Er zijn geen aanvullende gegevens verwerkt voor het maken van statistieken.

Wanneer statistieken worden gemaakt voor een kolom Parquet, wordt alleen de relevante kolom gelezen uit bestanden. Wanneer statistieken worden gemaakt voor een CSV-kolom, worden volledige bestanden gelezen en geparseerd.

## <a name="rounding"></a>Afronden

De hoeveelheid verwerkte gegevens wordt afgerond naar de dichtstbijzijnde MB per query. Voor elke query zijn mini maal 10 MB aan gegevens verwerkt.

## <a name="what-data-processed-doesnt-include"></a>Welke gegevens worden verwerkt, is niet inbegrepen

- Meta gegevens op server niveau (zoals aanmeldingen, functies en referenties op server niveau).
- Data bases die u in uw eind punt maakt. Deze data bases bevatten alleen meta gegevens (zoals gebruikers, rollen, schema's, weer gaven, in-line functies voor tabel waarden [TVFs], opgeslagen procedures, referenties voor database bereik, externe gegevens bronnen, externe bestands indelingen en externe tabellen).
  - Als u schema-deinterferentie gebruikt, worden bestands fragmenten gelezen voor het afleiden van kolom namen en gegevens typen, en wordt de hoeveelheid gelezen gegevens toegevoegd aan de hoeveelheid verwerkte gegevens.
- DDL-instructies (Data Definition Language), met uitzonde ring van de instructie CREATE STATISTICs, omdat hiermee gegevens uit de opslag worden verwerkt op basis van het opgegeven steek proef percentage.
- Query's met alleen meta gegevens.

## <a name="reducing-the-amount-of-data-processed"></a>De hoeveelheid verwerkte gegevens verminderen

U kunt de hoeveelheid verwerkte gegevens die per query worden verwerkt, optimaliseren en de prestaties verbeteren door uw gegevens te partitioneren en te converteren naar een gecomprimeerde, op kolom gebaseerd formaat, zoals Parquet.

## <a name="examples"></a>Voorbeelden

Stel drie tabellen voor.

- De tabel population_csv wordt ondersteund door 5 TB aan CSV-bestanden. De bestanden zijn ingedeeld in vijf kolommen met een gelijke grootte.
- De tabel population_parquet heeft dezelfde gegevens als de population_csv tabel. Er wordt een back-up gemaakt van 1 TB Parquet-bestanden. Deze tabel is kleiner dan het vorige bestand omdat de gegevens in de Parquet-indeling zijn gecomprimeerd.
- De very_small_csv tabel wordt ondersteund door 100 KB aan CSV-bestanden.

**Query 1**: Selecteer Sum (populatie) van population_csv

Met deze query worden hele bestanden gelezen en geparseerd om waarden op te halen voor de kolom populatie. Knoop punten verwerken fragmenten van deze tabel en de som van de populatie voor elk fragment wordt overgebracht tussen de knoop punten. Het eind totaal wordt overgedragen naar uw eind punt. 

Met deze query worden 5 TB aan gegevens verwerkt, plus een kleine hoeveelheid overhead voor het overdragen van de som van fragmenten.

**Query 2**: Selecteer Sum (populatie) van population_parquet

Wanneer u gecomprimeerde en op kolommen gebaseerde notaties zoals Parquet doorzoekt, worden er minder gegevens gelezen dan in query 1. U ziet dit resultaat omdat een SQL-pool zonder server een gecomprimeerde kolom leest in plaats van het hele bestand. In dit geval wordt 0,2 TB gelezen. (Vijf kolommen met even grootte zijn elk 0,2 TB.) Knoop punten verwerken fragmenten van deze tabel en de som van de populatie voor elk fragment wordt overgebracht tussen de knoop punten. Het eind totaal wordt overgedragen naar uw eind punt. 

Deze query verwerkt 0,2 TB plus een kleine hoeveelheid overhead voor het overdragen van de som van fragmenten.

**Query 3**: Select * from population_parquet

Met deze query worden alle kolommen gelezen en worden alle gegevens overgebracht naar een niet-gecomprimeerde indeling. Als de compressie-indeling 5:1 is, verwerkt de query 6 TB, omdat deze 1 TB leest en vijf TB aan ongecomprimeerde gegevens overdraagt.

**Query 4**: aantal selecteren (*) van very_small_csv

Met deze query worden hele bestanden gelezen. De totale grootte van de bestanden in de opslag voor deze tabel is 100 KB. Knoop punten verwerken fragmenten van deze tabel en de som voor elk fragment wordt tussen knoop punten overgedragen. Het eind totaal wordt overgedragen naar uw eind punt. 

Met deze query worden iets meer dan 100 KB aan gegevens verwerkt. De hoeveelheid gegevens die voor deze query wordt verwerkt, wordt tot 10 MB afgerond, zoals is opgegeven in de sectie [afronding](#rounding) van dit artikel.

## <a name="cost-control"></a>Kostenbeheer

Met de functie voor kosten beheer in een serverloze SQL-pool kunt u het budget instellen voor de hoeveelheid verwerkte gegevens. U kunt het budget instellen in TB aan gegevens die worden verwerkt voor een dag, week en maand. Op hetzelfde moment kunt u een of meer budgetten instellen. Als u kosten beheer wilt configureren voor een serverloze SQL-groep, kunt u Synapse Studio of T-SQL gebruiken.

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>Kosten beheer voor een serverloze SQL-groep configureren in Synapse Studio
 
Voor het configureren van kosten beheer voor een serverloze SQL-groep in Synapse Studio navigeert u naar items in het menu aan de linkerkant te beheren, dan selecteert u het item SQL-groep onder Analytics-Pools. Wanneer u de serverloze SQL-pool aanwijst, ziet u een pictogram voor Cost Control-klik op dit pictogram.

![Kosten beheer navigatie](./media/data-processed/cost-control-menu.png)

Wanneer u op het pictogram kosten beheer klikt, wordt er een kantlijn balk weer gegeven:

![Configuratie van kosten beheer](./media/data-processed/cost-control-sidebar.png)

Als u een of meer budgetten wilt instellen, klikt u eerst op keuze rondje inschakelen voor een budget dat u wilt instellen, dan voert u de waarde geheel getal in het tekstvak in. Eenheid voor de waarde is TBs. Wanneer u de gewenste budgetten hebt geconfigureerd, klikt u op de knop Toep assen onder aan de balk aan de zijkant. Dat is alles, u kunt budget nu instellen.

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>Kosten beheer voor een serverloze SQL-groep configureren in T-SQL

Als u kosten beheer wilt configureren voor een SQL-groep zonder server in T-SQL, moet u een of meer van de volgende opgeslagen procedures uitvoeren.

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

Voer de volgende T-SQL-instructie uit om de huidige configuratie te bekijken:

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

Voer de volgende T-SQL-instructie uit om na te gaan hoeveel gegevens er zijn verwerkt tijdens de huidige dag, week of maand:

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor serverloze SQL-groepen](best-practices-sql-on-demand.md)voor meer informatie over het optimaliseren van uw query's voor prestaties.
