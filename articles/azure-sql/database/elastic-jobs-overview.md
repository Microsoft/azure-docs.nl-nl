---
title: Taken voor Elastic Database (preview)
description: Elastic Database taken (preview) configureren om Transact-SQL-scripts (T-SQL) uit te voeren op een set van een of meer data bases in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: f9a026ed47d662b80ef01e505bfbcf8f32d20b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792171"
---
# <a name="create-configure-and-manage-elastic-jobs-preview"></a>Elastische taken maken, configureren en beheren (preview-versie)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u informatie over het maken, configureren en beheren van elastische taken.

Als u geen elastische taken hebt gebruikt, [leest u meer over de concepten van de taak automatisering in Azure SQL database](job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>De agent maken en configureren

1. Een lege S0 of een hogere data base maken of identificeren. Deze data base wordt gebruikt als de *taak database* tijdens het maken van een elastische taak agent.
2. Een elastische taak agent maken in de [Portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) of met [Power shell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent).

   ![Een elastische taak agent maken](./media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Taken maken, uitvoeren en beheren

1. Een referentie maken voor taak uitvoering in de *taak database* met behulp van [Power shell](elastic-jobs-powershell-create.md) of [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-credential-for-job-execution).
2. Definieer de doel groep (de data bases waarvoor u de taak wilt uitvoeren) met behulp van [Power shell](elastic-jobs-powershell-create.md) of [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-target-group-servers).
3. Maak een taakagentreferentie in elke database waarin de taak wordt uitgevoerd [(voeg de gebruiker (of rol) toe aan elke database in de groep)](logins-create-manage.md). Zie de [PowerShell-zelfstudie](elastic-jobs-powershell-create.md) voor een voorbeeld.
4. Maak een taak met behulp van [Power shell](elastic-jobs-powershell-create.md) of [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases).
5. Voeg taakstappen toe met [PowerShell](elastic-jobs-powershell-create.md) of [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases).
6. Voer een taak uit met [Power shell](elastic-jobs-powershell-create.md#run-the-job) of [T-SQL](elastic-jobs-tsql-create-manage.md#begin-unplanned-execution-of-a-job).
7. Bewaak de uitvoerings status van de taak met behulp van de portal, [Power shell](elastic-jobs-powershell-create.md#monitor-status-of-job-executions) of [T-SQL](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status).

   ![Portal](./media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Referenties voor het uitvoeren van taken

Taken gebruiken [databasereferenties](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) om verbinding te maken met de databases die tijdens de uitvoering zijn opgegeven door de doelgroep. Als een doelgroep servers of pools bevat, worden deze databasereferenties gebruikt om verbinding te maken met de hoofddatabase en de beschikbare databases op te sommen.

Het instellen van de juiste referenties voor het uitvoeren van een taak kan enigszins verwarrend zijn. Houd daarom de volgende punten in gedachten:

- De referenties van het data base-bereik moeten worden gemaakt in de *taak database*.
- **Alle doel databases moeten een aanmelding hebben met [voldoende machtigingen](/sql/relational-databases/security/permissions-database-engine) voor het volt ooien van de taak** ( `jobuser` in het onderstaande diagram).
- Referenties kunnen opnieuw worden gebruikt voor verschillende taken en de referentie wachtwoorden worden versleuteld en beveiligd door gebruikers met alleen-lezen toegang tot taak objecten.

De volgende afbeelding is gemaakt als hulp bij het begrijpen en instellen van de juiste taakreferenties. **Denk eraan dat de gebruiker moet worden gemaakt in elke database (alle *doelgebruikersdatabases*) waarin de taak moet worden uitgevoerd**.

![Referenties voor Elastic Jobs](./media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Aanbevolen procedures voor beveiliging

Een paar aandachtspunten voor best practices voor het werken met elastische taken:

- Beperk het gebruik van de API's tot vertrouwde personen.
- Referenties moeten slechts de minimale bevoegdheden hebben die nodig zijn om de taakstap uit te voeren. Zie [autorisatie en machtigingen](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)voor meer informatie.
- Wanneer u een lid van een server en/of groeps doel groep gebruikt, is het zeer raadzaam een afzonderlijke referentie te maken met rechten op de hoofd database om data bases weer te geven/te vermelden die worden gebruikt om de database lijsten van de server (s) en/of pool (s) uit te breiden voordat de taak wordt uitgevoerd.

## <a name="agent-performance-capacity-and-limitations"></a>Agentprestaties, - capaciteit en -beperkingen

Elastische taken gebruiken minimale rekenresources tijdens het wachten tot langlopende taken zijn voltooid.

Afhankelijk van de grootte van de doelgroep van databases en de gewenste uitvoeringstijd voor een taak (aantal gelijktijdige werkrollen), vraagt de agent meer of minder rekenkracht en prestaties van de *taakdatabase* (hoe meer doelen en taken, hoe hoger de vereiste hoeveelheid rekenkracht).

Op dit moment is de preview beperkt tot 100 gelijktijdige taken.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Voorkomen dat taken de prestaties van de doeldatabase doen afnemen

Om ervoor te zorgen dat resources niet worden overbelast tijdens het uitvoeren van taken voor databases in een elastische SQL-pool, kunnen taken zo worden geconfigureerd dat het aantal databases waarvoor een taak tegelijkertijd wordt uitgevoerd, wordt beperkt.

Stel het aantal gelijktijdige data bases in waarop een taak wordt uitgevoerd door de `sp_add_jobstep` para meter van de opgeslagen procedure `@max_parallelism` in T-SQL of `Add-AzSqlElasticJobStep -MaxParallelism` in Power shell in te stellen.

## <a name="best-practices-for-creating-jobs"></a>Best practices voor het maken van taken

### <a name="idempotent-scripts"></a>Idempotente scripts
De T-SQL-scripts van een taak moeten [idempotent](https://en.wikipedia.org/wiki/Idempotence) zijn. **Idempotent** betekent dat de resultaten van een geslaagd script hetzelfde zijn wanneer dit script opnieuw wordt uitgevoerd. Een script kan mislukken door tijdelijke netwerkproblemen. In dat geval wordt automatisch een vooraf ingesteld aantal keer opnieuw geprobeerd om de taak uit te voeren alvorens het wordt afgebroken. Een idempotent script heeft hetzelfde resultaat, zelfs als het twee keer (of vaker) wordt uitgevoerd.

Een eenvoudige tactiek is te testen of een object bestaat voordat u het maakt.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Op dezelfde manier moet een script met succes kunnen worden uitgevoerd door logisch te testen op problemen en hierop te reageren.



## <a name="next-steps"></a>Volgende stappen

- [Elastische taken maken en beheren met PowerShell](elastic-jobs-powershell-create.md)
- [Elastische taken maken en beheren met Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)