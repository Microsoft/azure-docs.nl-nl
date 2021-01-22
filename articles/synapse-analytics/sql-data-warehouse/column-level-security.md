---
title: Beveiliging op kolom niveau voor toegewezen SQL-groep
description: Met Column-Level Security kunnen klanten de toegang tot database tabel kolommen beheren op basis van de uitvoerings context of het groepslid maatschap van de gebruiker, het ontwerp en de code ring van de beveiliging in uw toepassing vereenvoudigen, en kunt u beperkingen voor kolom toegang implementeren.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: fb34051f7d4b24190806dde939c8cc6d9c2a4896
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679944"
---
# <a name="column-level-security"></a>Beveiliging op kolomniveau

Met Column-Level beveiliging kunnen klanten de toegang tot tabel kolommen beheren op basis van de uitvoerings context of het groepslid maatschap van de gebruiker.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Omdat deze video is gepost, is de [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) beschikbaar voor exclusieve SQL-groep in azure Synapse.

Beveiliging op kolom niveau vereenvoudigt het ontwerp en de code ring van beveiliging in uw toepassing, zodat u kolom toegang kunt beperken voor het beveiligen van gevoelige gegevens. Bijvoorbeeld, om ervoor te zorgen dat specifieke gebruikers alleen toegang hebben tot bepaalde kolommen van een tabel die relevant is voor hun afdeling. De logica van de toegangs beperking bevindt zich in de database laag, in plaats van dat de gegevens in een andere toepassingslaag worden verwijderd. De-data base past de toegangs beperkingen toe telkens wanneer gegevens toegang vanuit een wille keurige laag wordt uitgevoerd. Deze beperking zorgt ervoor dat uw beveiliging betrouwbaarder en robuuster wordt door de surface area van uw algehele beveiligings systeem te verminderen. Daarnaast elimineert beveiliging op kolom niveau ook de nood zaak van de introductie van weer gaven om kolommen uit te filteren voor het opleggen van toegangs beperkingen voor de gebruikers.

U kunt beveiliging op kolom niveau implementeren met de instructie [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL. Bij dit mechanisme worden de verificatie van SQL-en Azure Active Directory (Azure AD) ondersteund.

![Diagram toont een schematische tabel met de eerste kolom met een gesloten hang slot en de cellen in een oranje kleur terwijl de andere kolommen witte cellen zijn.](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u `TestUser` de toegang tot de `SSN` kolom van de `Membership` tabel beperkt:

`Membership`Tabel maken met SSN-kolom die wordt gebruikt voor het opslaan van sociale-beveiligings nummers:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

`TestUser`Toegang tot alle kolommen toestaan, behalve de kolom SSN, die de gevoelige gegevens bevat:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Query's die worden uitgevoerd als `TestUser` , mislukken als ze de kolom SSN bevatten:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Gebruiksscenario's

Hier volgen enkele voor beelden van de manier waarop beveiliging op kolom niveau momenteel wordt gebruikt:

- Met een onderneming met financiële services kunnen alleen account beheerders toegang hebben tot sofi-nummer (Social Security Number), telefoon nummers en andere persoonlijke gegevens van klanten.
- Een Health Care-provider staat toe dat alleen artsen en verpleegt toegang hebben tot gevoelige medische records en dat leden van de facturerings afdeling deze gegevens niet kunnen bekijken.
