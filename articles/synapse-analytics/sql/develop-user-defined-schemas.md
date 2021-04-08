---
title: Door de gebruiker gedefinieerde schema's in Synapse SQL
description: In de onderstaande secties vindt u verschillende tips voor het gebruik van door de gebruiker gedefinieerde en met de p-SQL-schema's om oplossingen te ontwikkelen met de Synapse SQL-mogelijkheid van Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451695"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Door de gebruiker gedefinieerde schema's in Synapse SQL

In de onderstaande secties vindt u verschillende tips voor het gebruik van door de gebruiker gedefinieerde T-SQL-schema's voor het ontwikkelen van oplossingen in Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Schema's voor toepassings grenzen

Traditionele analyse architectuur maakt vaak gebruik van afzonderlijke data bases om toepassings grenzen te maken op basis van werk belasting, domein of beveiliging. Een traditionele SQL Server analyse-infra structuur kan bijvoorbeeld bestaan uit een faserings database, een Analytics-Data Base en datamart-data bases. In deze topologie fungeert elke Data Base als een werk belasting en beveiligings grens in de architectuur.

In plaats daarvan voert Synapse SQL de volledige analyse werk belasting binnen één Data Base uit. Cross-data base-samen voegingen zijn niet toegestaan. Synapse SQL verwacht dat alle tabellen die door het magazijn worden gebruikt, worden opgeslagen in de ene data base.

> [!NOTE]
> Exclusieve SQL-groepen bieden geen ondersteuning voor query's voor meerdere data bases van welke aard dan ook. Daarom moeten analyse-implementaties die gebruikmaken van dit patroon, worden gereviseerd. Serverloze SQL-pool ondersteunt query's tussen data bases.

## <a name="user-defined-schema-recommendations"></a>Door de gebruiker gedefinieerde schema aanbevelingen

Inbegrepen zijn aanbevelingen voor het samen voegen van werk belastingen, beveiliging, domein en functionele grenzen door gebruik te maken van door de gebruiker gedefinieerde schema's:

- Gebruik één Data Base om uw gehele analytische werk belasting uit te voeren.
- Consolideer uw bestaande analyse omgeving om één Data Base te gebruiken.
- Gebruik door de **gebruiker gedefinieerde schema's** om de grens te leveren die eerder is geïmplementeerd met behulp van data bases.

Als door de gebruiker gedefinieerde schema's niet eerder zijn gebruikt, hebt u een schone pastel. Gebruik de oude database naam als basis voor uw door de gebruiker gedefinieerde schema's in de Synapse-SQL database.

Als er al schema's zijn gebruikt, hebt u een aantal opties:

- Verwijder de oude schema namen en begin vers
- Behoud de oude schema namen door de oude schema naam vooraf in behandeling te laten nemen aan de naam van de tabel
- Behoud de oude schema namen door weer gaven te implementeren via de tabel in een extra schema, waardoor de oude schema structuur opnieuw wordt gemaakt.

> [!NOTE]
> Op de eerste inspectie lijkt optie 3 mogelijk als de meest aantrekkelijke keuze. Weer gaven zijn alleen-lezen in Synapse SQL. Eventuele gegevens of tabel wijzigingen moeten worden uitgevoerd voor de basis tabel. Met optie 3 wordt ook een laag van weer gaven in uw systeem geïntroduceerd. U kunt hiervan een extra idee geven als u al gebruikmaakt van weer gaven in uw architectuur.
> 
> 

### <a name="examples"></a>Voorbeelden

Door de gebruiker gedefinieerde schema's implementeren op basis van database namen.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behoud de oude schema namen door deze vooraf in te wachten op de tabel naam. Schema's gebruiken voor de grens van de werk belasting.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behoud de oude schema namen met behulp van weer gaven.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Voor elke wijziging in de schema strategie moet het beveiligings model voor de Data Base worden gecontroleerd. In veel gevallen kunt u het beveiligings model vereenvoudigen door machtigingen toe te wijzen op schema niveau.

Als u meer gedetailleerde machtigingen nodig hebt, kunt u database rollen gebruiken. Zie het artikel [database rollen en-gebruikers beheren](../../analysis-services/analysis-services-database-users.md) voor meer informatie over database rollen.

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL Development Overview](develop-overview.md)(Engelstalig) voor meer tips voor ontwikkel aars.
