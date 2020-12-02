---
title: Automatisch instellen inschakelen
description: U kunt het automatisch afstemmen van uw Data Base op eenvoudige wijze inschakelen met behulp van de Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 12/03/2019
ms.openlocfilehash: 35e2a73b0cfae104cee417e7d4a159e7fd169a17
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500900"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Automatisch afstemmen inschakelen in de Azure Portal om query's te bewaken en de prestaties van de werk belasting te verbeteren
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL Database beheert automatisch gegevens services die voortdurend uw query's bewaken en identificeert de actie die u kunt uitvoeren om de prestaties van uw workload te verbeteren. U kunt aanbevelingen bekijken en deze hand matig Toep assen of Azure SQL Database corrigerende acties automatisch Toep assen. dit wordt ook wel de **automatische afstemmings modus** genoemd.

Automatisch afstemmen kan worden ingeschakeld op de server of op het niveau van de data base via:

- De [Azure Portal](automatic-tuning-enable.md#azure-portal)
- [Rest API](automatic-tuning-enable.md#rest-api) -aanroepen
- [T-SQL-](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) opdrachten

> [!NOTE]
> Voor Azure SQL Managed instance kan de ondersteunde optie FORCE_LAST_GOOD_PLAN alleen worden geconfigureerd via [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . De opties Azure Portal op basis van configuratie en automatische index afstemming die in dit artikel worden beschreven, zijn niet van toepassing op beheerde exemplaren van Azure SQL.

> [!NOTE]
> Het configureren van opties voor automatisch afstemmen via de ARM-sjabloon (Azure Resource Manager) wordt op dit moment niet ondersteund.

## <a name="enable-automatic-tuning-on-server"></a>Automatisch afstemmen op server inschakelen

Op server niveau kunt u kiezen voor het overnemen van de automatische afstemmings configuratie van ' Azure defaults ' of de configuratie niet overnemen. De standaard waarden van Azure zijn FORCE_LAST_GOOD_PLAN ingeschakeld, CREATE_INDEX is uitgeschakeld en DROP_INDEX is uitgeschakeld.

> [!IMPORTANT]
> Vanaf maart 2020 nieuwe standaard instellingen van Azure voor automatische afstemming zijn als volgt:
>
> - FORCE_LAST_GOOD_PLAN = ingeschakeld, CREATE_INDEX = uitgeschakeld en DROP_INDEX = uitgeschakeld.
> - Bestaande servers zonder geconfigureerde voor keuren voor automatisch afstemmen worden automatisch geconfigureerd om de standaard instellingen van Azure te overnemen. Dit geldt voor alle klanten die momenteel server instellingen voor automatisch afstemmen in een niet-gedefinieerde status hebben.
> - Nieuwe servers die worden gemaakt, worden automatisch geconfigureerd om de standaard instellingen van Azure te overnemen (in tegens telling tot eerdere versies van de automatische afstemmings configuratie tijdens het maken van een nieuwe server).

### <a name="azure-portal"></a>Azure Portal

Als u automatisch afstemmen wilt inschakelen op een [Server](logical-servers.md) in Azure SQL database, gaat u naar de-server in de Azure Portal en selecteert u vervolgens **automatisch afstemmen** in het menu.

![Scherm opname toont automatische afstemming in de Azure Portal, waar u opties voor een server kunt Toep assen.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Houd er rekening mee dat de optie **DROP_INDEX** op dit moment niet compatibel is met toepassingen die gebruikmaken van partitie-switches en index hints en niet in deze gevallen moeten worden ingeschakeld. Het verwijderen van niet-gebruikte indexen wordt niet ondersteund voor Premium-en Bedrijfskritiek-service lagen.

Selecteer de opties voor automatisch afstemmen die u wilt inschakelen en selecteer **Toep assen**.

Opties voor automatisch afstemmen op een server worden toegepast op alle data bases op deze server. Standaard nemen alle data bases de configuratie over van de bovenliggende server, maar dit kan worden overschreven en voor elke Data Base afzonderlijk worden opgegeven.

### <a name="rest-api"></a>REST-API

Zie [automatisch afstemmen bijwerken en HTTP-methoden ophalen](/rest/api/sql/serverautomatictuning)voor meer informatie over het gebruik van een rest API om automatisch afstemmen op een **Server** in te scha kelen.

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Automatisch afstemmen inschakelen voor een afzonderlijke data base

Met Azure SQL Database kunt u de automatische afstemmings configuratie voor elke Data Base afzonderlijk opgeven. Op het niveau van de Data Base kunt u ervoor kiezen om de automatische afstemmings configuratie over te nemen van de bovenliggende server, "Azure defaults" of de configuratie niet overnemen. De standaard waarden van Azure worden ingesteld op FORCE_LAST_GOOD_PLAN is ingeschakeld, CREATE_INDEX is uitgeschakeld en DROP_INDEX is uitgeschakeld.

> [!TIP]
> De algemene aanbeveling is de configuratie voor automatisch afstemmen op **server niveau** te beheren, zodat dezelfde configuratie-instellingen automatisch kunnen worden toegepast op elke Data Base. Configureer automatisch afstemmen voor een afzonderlijke Data Base alleen als u die data base andere instellingen moet hebben dan andere de instellingen overnemen van dezelfde server.

### <a name="azure-portal"></a>Azure Portal

Als u automatisch afstemmen wilt inschakelen voor **één data base**, gaat u naar de data base in de Azure Portal en selecteert u **automatisch afstemmen**.

Individuele instellingen voor automatisch afstemmen kunnen afzonderlijk worden geconfigureerd voor elke Data Base. U kunt hand matig een afzonderlijke optie voor automatisch afstemmen configureren of opgeven dat een optie de instellingen van de server overneemt.

![Scherm opname toont automatische afstemming in de Azure Portal, waar u opties voor één Data Base kunt Toep assen.](./media/automatic-tuning-enable/database.png)

Houd er rekening mee dat DROP_INDEX optie op dit moment niet compatibel is met toepassingen die gebruikmaken van partitie-switches en index hints en niet in deze gevallen mogen worden ingeschakeld.

Wanneer u de gewenste configuratie hebt geselecteerd, klikt u op **Toep assen**.

### <a name="rest-api"></a>Rest API

Zie [Azure SQL database automatisch afstemmen bijwerken en HTTP-methoden ophalen](/rest/api/sql/databaseautomatictuning)voor meer informatie over het gebruik van een rest API om automatisch afstemmen in te scha kelen voor één data base.

### <a name="t-sql"></a>T-SQL

Als u automatisch afstemmen wilt inschakelen voor één data base via T-SQL, maakt u verbinding met de data base en voert u de volgende query uit:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Als u automatisch afstemmen instelt op automatisch, worden de standaard instellingen van Azure toegepast. Als u deze instelt op INHERIT, wordt de configuratie voor automatisch afstemmen overgenomen van de bovenliggende server. Als u Aangepast kiest, moet u automatisch afstemmen hand matig configureren.

Als u afzonderlijke opties voor automatisch afstemmen wilt configureren via T-SQL, maakt u verbinding met de data base en voert u de query uit, zoals deze:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Als u de afzonderlijke afstemmings optie instelt op aan, worden alle instellingen die zijn overgenomen van de data base, overschreven en wordt de optie afstemmen ingeschakeld Als u deze instelling uitschakelt, wordt ook alle instellingen die door de Data Base zijn overgenomen en de afstemmings optie uitgeschakeld. De optie automatisch afstemmen, waarvoor standaard is opgegeven, neemt de configuratie voor automatisch afstemmen over van de instellingen op server niveau.  

> [!IMPORTANT]
> In het geval van [actieve geo-replicatie](auto-failover-group-overview.md)moet automatisch afstemmen worden geconfigureerd op de primaire data base. Automatisch toegepaste afstemmings acties, zoals het maken of verwijderen van een index, worden automatisch gerepliceerd naar het secundaire kenmerk alleen-lezen. Als u probeert automatische afstemming via T-SQL in te scha kelen op het alleen-lezen secundair, resulteert dit in een fout omdat een andere afstemmings configuratie op het secundaire kenmerk alleen-lezen niet wordt ondersteund.
>

Zie [ALTER data base set Options (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)voor meer informatie over over T-SQL-opties voor het configureren van automatisch afstemmen.

## <a name="disabled-by-the-system"></a>Uitgeschakeld door het systeem

Automatisch afstemmen bewaken alle acties die ze in de Data Base uitvoeren. in sommige gevallen kan het bepalen dat automatisch afstemmen niet goed kan worden uitgevoerd op de data base. In dit geval wordt de afstemmings optie uitgeschakeld door het systeem. In de meeste gevallen gebeurt dit omdat query Store niet is ingeschakeld of de status alleen-lezen heeft voor een specifieke data base.

## <a name="permissions"></a>Machtigingen

Als automatische afstemming een Azure-functie is, moet u de ingebouwde rollen van Azure gebruiken om het te gebruiken. Het gebruik van alleen SQL-verificatie is niet voldoende om de functie van de Azure Portal te gebruiken.

Als u automatisch afstemmen wilt gebruiken, is de mini maal vereiste machtiging voor het verlenen aan de gebruiker de ingebouwde rol [SQL database Inzender](../../role-based-access-control/built-in-roles.md#sql-db-contributor) van Azure. U kunt ook overwegen om meer bevoegdheden te gebruiken, zoals SQL Server Inzender, het door SQL beheerde exemplaar van Inzender, Inzender en de eigenaar.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>E-mail meldingen automatisch afstemmen configureren

Raadpleeg de hand leiding voor het [automatisch afstemmen van e-mail berichten](automatic-tuning-email-notifications-configure.md) .

## <a name="next-steps"></a>Volgende stappen

- Lees het [artikel automatisch afstemmen](automatic-tuning-overview.md) voor meer informatie over automatisch afstemmen en hoe u dit kunt helpen bij het verbeteren van de prestaties.
- Bekijk de [aanbevelingen voor prestaties](database-advisor-implement-performance-recommendations.md) voor een overzicht van Azure SQL database prestatie aanbevelingen.
- Zie [query performance Insights](query-performance-insight-use.md) voor meer informatie over het weer geven van de prestatie-impact van uw meest voorkomende query's.
