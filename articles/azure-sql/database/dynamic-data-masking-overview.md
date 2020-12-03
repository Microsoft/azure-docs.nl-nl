---
title: Dynamische gegevensmaskering
description: Dynamische gegevens maskering beperkt de bloot stelling van gevoelige gegevens door deze te maskeren voor niet-gemachtigde gebruikers voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/04/2020
tags: azure-synpase
ms.openlocfilehash: f8d352dac98f953f7f6d8033d0d9e1376c4da313
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532241"
---
# <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics bieden ondersteuning voor dynamische gegevens maskering. Met dynamische gegevensmaskering wordt de blootstelling van gevoelige gegevens beperkt door deze gegevens te maskeren voor niet-gemachtigde gebruikers. 

Dynamische gegevensmaskering helpt onbevoegde toegang tot gevoelige gegevens te voorkomen, doordat klanten kunnen aangeven hoeveel van de gevoelige gegevens mag worden vrijgegeven, met minimale gevolgen voor de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.

Een service medewerker in Call Center kan bijvoorbeeld bellers identificeren met verschillende cijfers van hun creditcard nummer, maar deze gegevens items mogen niet volledig worden blootgesteld aan de mede werker van de service. Er kan een maskerings regel worden gedefinieerd waarmee alle, behalve de laatste vier cijfers van een creditcard nummer in de resultatenset van een wille keurige query worden gemaskeerd. Een ander voor beeld is dat er een geschikt gegevens masker kan worden gedefinieerd om persoonlijke gegevens te beveiligen, zodat een ontwikkelaar productie omgevingen kan opvragen voor het oplossen van problemen zonder dat nalevings voorschriften worden geschonden.

## <a name="dynamic-data-masking-basics"></a>Basis beginselen van dynamische gegevens maskering

U stelt een beleid voor dynamische gegevens maskering in de Azure Portal in door de Blade **dynamische gegevens maskering** te selecteren onder **beveiliging** in het deel venster SQL database configuratie. Deze functie kan niet worden ingesteld met behulp van de portal voor SQL Managed instance (gebruik Power shell of REST API). Zie [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking)voor meer informatie.

### <a name="dynamic-data-masking-permissions"></a>Machtigingen voor dynamische gegevens maskering

Dynamische gegevens maskering kan worden geconfigureerd door de rollen Azure SQL Database beheerder, Server beheerder of [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Beleid voor dynamische gegevens maskering

* **SQL-gebruikers die zijn uitgesloten van maskering** : een set SQL-gebruikers of Azure AD-identiteiten die niet-gemaskeerde gegevens in de SQL-query resultaten ophalen. Gebruikers met beheerders bevoegdheden zijn altijd uitgesloten van maskering en zien de oorspronkelijke gegevens zonder masker.
* **Maskerings regels** : een set regels waarmee de aangewezen velden worden gedefinieerd die moeten worden gemaskeerd en de maskerings functie die wordt gebruikt. De aangewezen velden kunnen worden gedefinieerd met behulp van de naam van het database schema, de tabel naam en de kolom naam.
* **Masker functies** : een set methoden waarmee de bloot stelling van gegevens voor verschillende scenario's wordt bepaald.

| Maskerings functie | Logica maskeren |
| --- | --- |
| **Standaard** |**Volledige maskering volgens de gegevens typen van de aangewezen velden**<br/><br/>• Gebruik XXXX of minder XS als de grootte van het veld kleiner is dan 4 tekens voor teken reeks gegevens typen (NCHAR, ntext, nvarchar).<br/>• Gebruik de waarde nul voor numerieke gegevens typen (bigint, bit, Decimal, int, Money, numeric, smallint, smallmoney, tinyint, float, Real).<br/>• Gebruik 01-01-1900 voor datum-en tijd gegevens typen (datum, DATETIME2, datetime, date time offset, smalldatetime, time).<br/>• Voor SQL-variant wordt de standaard waarde van het huidige type gebruikt.<br/>• Voor XML wordt het document \<masked/> gebruikt.<br/>• Gebruik een lege waarde voor speciale gegevens typen (Time Stamp-tabel, hierarchyid, GUID, binary, afbeelding, type Spatial). |
| **Credit Card** |**Maskerings methode, waarmee de laatste vier cijfers van de aangewezen velden worden** weer gegeven en een constante teken reeks als voor voegsel wordt toegevoegd in de vorm van een credit card.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maskerings methode, waarmee de eerste letter wordt weer gegeven en het domein wordt vervangen door xxx.com** met behulp van een constante voor voegsel van een teken reeks in de vorm van een e-mail adres.<br/><br/>aXX@XXXX.com |
| **Wille keurig getal** |**Maskerings methode, waarmee een wille keurig getal wordt gegenereerd** op basis van de geselecteerde grenzen en de werkelijke gegevens typen. Als de aangewezen grenzen gelijk zijn, is de maskerings functie een constant getal.<br/><br/>![Scherm opname van de maskerings methode voor het genereren van een wille keurig getal.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Aangepaste tekst** |**Maskerings methode, waarmee de eerste en laatste tekens worden** weer gegeven en een aangepaste opvullings teken reeks wordt toegevoegd aan het midden. Als de oorspronkelijke teken reeks korter is dan het weer gegeven voor voegsel en achtervoegsel, wordt alleen de opvullings teken reeks gebruikt. <br/>achtervoegsel [opvulling] voor voegsel<br/><br/>![Navigatiedeelvenster](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Aanbevolen velden om te maskeren

De DDM-engine voor aanbevelingen, markeert bepaalde velden uit uw data base als mogelijk gevoelige velden. Dit kan goede kandidaten zijn voor maskering. In de Blade dynamische gegevens maskering in de portal worden de aanbevolen kolommen voor uw Data Base weer gegeven. U hoeft alleen maar op **masker toevoegen** te klikken voor een of meer kolommen en vervolgens op te **slaan** om een masker voor deze velden toe te passen.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Dynamische gegevens maskering instellen voor uw data base met behulp van Power shell-cmdlets

### <a name="data-masking-policies"></a>Beleid voor gegevens maskering

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Regels voor gegevens maskering

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>Dynamische gegevens maskering instellen voor uw data base met behulp van de REST API

U kunt de REST API gebruiken om beleid en regels voor gegevens maskering programmatisch te beheren. De gepubliceerde REST API ondersteunt de volgende bewerkingen:

### <a name="data-masking-policies"></a>Beleid voor gegevens maskering

- [Maken of bijwerken](/rest/api/sql/datamaskingpolicies/createorupdate): Hiermee wordt een beleid voor database gegevens maskering gemaakt of bijgewerkt.
- [Ophalen](/rest/api/sql/datamaskingpolicies/get): Hiermee wordt een beleid voor database gegevens maskering opgehaald. 

### <a name="data-masking-rules"></a>Regels voor gegevens maskering

- [Maken of bijwerken](/rest/api/sql/datamaskingrules/createorupdate): Hiermee wordt een regel voor database gegevens maskering gemaakt of bijgewerkt.
- [Lijst op data base](/rest/api/sql/datamaskingrules/listbydatabase): Hiermee wordt een lijst met regels voor database gegevens maskering opgehaald.
