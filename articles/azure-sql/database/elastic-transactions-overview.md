---
title: Over clouddatabases gedistribueerde transacties (preview)
description: Overzicht van Elastic Database transacties met Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793344"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Over clouddatabases gedistribueerde transacties (preview)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met Elastic data base-trans acties voor Azure SQL Database en Azure SQL Managed Instance kunt u trans acties uitvoeren die meerdere data bases omvatten. Elastic data base-trans acties zijn beschikbaar voor .NET-toepassingen die gebruikmaken van ADO.NET en kunnen worden geïntegreerd met de bekende programmeer ervaring met behulp van de [System. Trans Action](/dotnet/api/system.transactions) -klassen. Zie [.NET Framework 4.6.1 (web installer)](https://www.microsoft.com/download/details.aspx?id=49981)om de tape wisselaar op te halen.
Daarnaast zijn voor beheerde instantie gedistribueerde trans acties beschikbaar in [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql).

In een on-premises-scenario moet micro soft gedistribueerde transactie (MSDTC) meestal worden uitgevoerd. Omdat MSDTC niet beschikbaar is voor platform-as-a-service-toepassingen in azure, is de mogelijkheid om gedistribueerde trans acties te coördineren, nu rechtstreeks geïntegreerd in SQL Database of een beheerd exemplaar. Toepassingen kunnen verbinding maken met elke Data Base om gedistribueerde trans acties te starten. een van de data bases of servers zal de gedistribueerde trans actie transparant coördineren, zoals wordt weer gegeven in de volgende afbeelding.

In dit document worden "gedistribueerde trans acties" en "Elastic data base trans actions" beschouwd als synoniemen en worden ze door elkaar gebruikt.

  ![Gedistribueerde trans acties met Azure SQL Database met Elastic data base-trans acties ][1]

## <a name="common-scenarios"></a>Algemene scenario's

Met Elastic data base transacties kunnen toepassingen atomische wijzigingen aanbrengen in gegevens die in verschillende data bases zijn opgeslagen. Het voor beeld is gericht op de ontwikkelings ervaringen aan client zijde in C# en .NET. Een server ervaring (code die is geschreven in opgeslagen procedures of scripts aan de server zijde) met behulp van [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) is alleen beschikbaar voor beheerde exemplaren.
> [!IMPORTANT]
> In de preview-periode wordt het uitvoeren van elastische database transacties tussen Azure SQL Database en Azure SQL Managed instance op dit moment niet ondersteund. Elastische-database transactie kan alleen over meerdere sets SQL-data bases of een set beheerde exemplaren beschikken.

Elastische-database transacties richten zich op de volgende scenario's:

* Toepassingen voor meerdere data bases in Azure: in dit scenario worden gegevens verticaal gepartitioneerd over meerdere data bases in SQL Database of een beheerd exemplaar, zodat verschillende soorten gegevens zich op verschillende data bases bevinden. Voor sommige bewerkingen zijn wijzigingen in gegevens vereist, die in twee of meer data bases worden bewaard. De toepassing maakt gebruik van Elastic data base-trans acties voor het coördineren van de wijzigingen in data bases en zorgt voor een atomiciteit.
* Shard-database toepassingen in Azure: in dit scenario gebruikt de gegevenslaag de [Elastic database-client bibliotheek](elastic-database-client-library.md) of zelf-sharding om de gegevens Horizon taal te partitioneren in veel data bases in SQL database of een beheerd exemplaar. Eén prominente use case is de nood zaak om atomische wijzigingen uit te voeren voor een Shard multi tenant-toepassing wanneer de tenants worden gewijzigd. Denk na over een overdracht van de ene Tenant naar de andere, beide op verschillende data bases. Een tweede geval is een nauw keurige sharding voor de capaciteits behoeften van een grote Tenant, die op zijn beurt doorgaans impliceert dat sommige atomische bewerkingen moeten worden uitgerekt over verschillende data bases die worden gebruikt voor dezelfde Tenant. Een derde geval is atomische updates om te verwijzen naar gegevens die in data bases worden gerepliceerd. Atomische, transactionele bewerkingen op deze regels kunnen nu worden gecoördineerd over verschillende data bases met behulp van de preview-versie.
  Elastische-database transacties gebruiken twee fasen door voeren om transactie atomiciteit in data bases te garanderen. Het is geschikt voor trans acties die minder dan 100 data bases tegelijk binnen één trans actie omvatten. Deze limieten worden niet afgedwongen, maar één moet rekening houden met prestaties en succes percentages voor Elastic data base-trans acties wanneer deze limieten worden overschreden.

## <a name="installation-and-migration"></a>Installatie en migratie

De mogelijkheden voor Elastic data base-trans acties worden geboden via updates voor de .NET-bibliotheken System.Data.dll en System.Transactions.dll. De Dll's zorgen ervoor dat door voeren in twee fasen wordt gebruikt wanneer dit nodig is om atomisch te garanderen. Als u toepassingen wilt ontwikkelen met Elastic data base trans acties, installeert u [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) of een latere versie. Bij het uitvoeren van een eerdere versie van .NET Framework kunnen trans acties niet worden gepromoveerd naar een gedistribueerde trans actie. er wordt een uitzonde ring gegenereerd.

Na de installatie kunt u de Api's voor Distributed Trans Action gebruiken in System. Trans actions met verbindingen met SQL Database en een beheerd exemplaar. Als u beschikt over bestaande MSDTC-toepassingen die gebruikmaken van deze Api's, bouwt u uw bestaande toepassingen voor .NET 4,6 opnieuw op nadat u het 4.6.1-Framework hebt geïnstalleerd. Als uw projecten .NET 4,6 doel hebben, gebruiken ze automatisch de bijgewerkte dll-bestanden van de nieuwe Framework-versie en Distributed Trans Action API-aanroepen in combi natie met verbindingen met SQL Database of een beheerd exemplaar.

Houd er rekening mee dat voor Elastic data base-trans acties geen MSDTC moet worden geïnstalleerd. In plaats daarvan worden Elastic data base-trans acties rechtstreeks beheerd door en binnen de service. Dit vereenvoudigt de Cloud scenario's omdat een implementatie van MSDTC niet nodig is voor het gebruik van gedistribueerde trans acties met SQL Database of een beheerd exemplaar. Sectie 4 bevat meer informatie over het implementeren van elastische database transacties en de vereiste .NET Framework samen met uw Cloud toepassingen naar Azure.

## <a name="net-installation-for-azure-cloud-services"></a>.NET-installatie voor Azure Cloud Services

Azure biedt verschillende aanbiedingen voor het hosten van .NET-toepassingen. Een vergelijking van de verschillende aanbiedingen is beschikbaar in [Azure app service, Cloud Services en virtual machines vergelijking](/azure/architecture/guide/technology-choices/compute-decision-tree). Als het gast besturingssysteem van de aanbieding kleiner is dan .NET 4.6.1 die vereist zijn voor elastische trans acties, moet u het gast besturingssysteem upgraden naar 4.6.1.

Voor Azure App Service worden upgrades naar het gast besturingssysteem momenteel niet ondersteund. Voor Azure Virtual Machines meldt u zich aan bij de virtuele machine en voert u het installatie programma uit voor de nieuwste versie van .NET Framework. Voor Azure Cloud Services moet u de installatie van een nieuwere versie van .NET toevoegen aan de opstart taken van uw implementatie. De concepten en stappen worden beschreven in [.net installeren op een Cloud service functie](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Houd er rekening mee dat het installatie programma voor .NET 4.6.1 mogelijk meer tijdelijke opslag vereist tijdens het Boots trap proces van Azure Cloud Services dan het installatie programma voor .NET 4,6. Om ervoor te zorgen dat de installatie is voltooid, moet u de tijdelijke opslag voor uw Azure-Cloud service verhogen in het bestand ServiceDefinition. csdef in het gedeelte LocalResources en de omgevings instellingen van de opstart taak, zoals wordt weer gegeven in het volgende voor beeld:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET-ontwikkelings ervaring

### <a name="multi-database-applications"></a>Toepassingen voor meerdere data bases

De volgende voorbeeld code maakt gebruik van de bekende programmeer ervaring met .NET System. Trans actions. De TransactionScope-klasse brengt een ambient trans actie in .NET tot stand. (Een ' Ambient trans actie ' is een ' in de huidige thread '.) Alle verbindingen die in de TransactionScope zijn geopend, nemen deel aan de trans actie. Als verschillende data bases deel nemen, wordt de trans actie automatisch verhoogd naar een gedistribueerde trans actie. De uitkomst van de trans actie wordt bepaald door het bereik in te stellen op voltooid om aan te geven dat er een doorvoer bewerking is.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Shard-database toepassingen

Elastic data base-trans acties voor SQL Database en beheerde exemplaren ondersteunen ook coördinatie van gedistribueerde trans acties waarbij u de methode OpenConnectionForKey van de client bibliotheek voor Elastic data base gebruikt om verbindingen te openen voor een uitgeschaalde gegevenslaag. Houd rekening met gevallen waarin u transactionele consistentie moet garanderen voor wijzigingen in verschillende sharding-sleutel waarden. Verbindingen met de Shards die de verschillende sharding-sleutel waarden hosten, zijn met behulp van OpenConnectionForKey Broker. In het algemeen is het mogelijk om verbinding te maken met verschillende Shards die ervoor zorgen dat voor transactionele garanties een gedistribueerde trans actie is vereist.
Het volgende codevoorbeeld illustreert deze aanpak. Hierbij wordt ervan uitgegaan dat een variabele met de naam shardmap wordt gebruikt om een Shard-toewijzing te vertegenwoordigen uit de client bibliotheek voor Elastic Data Base:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Ontwikkelings ervaring voor Transact-SQL

Een gedistribueerde trans actie op de server met behulp van Transact-SQL is alleen beschikbaar voor Azure SQL Managed instance. Gedistribueerde trans actie kan alleen worden uitgevoerd tussen beheerde instanties die tot dezelfde [Server vertrouwens groep](../managed-instance/server-trust-group-overview.md)behoren. In dit scenario moeten beheerde exemplaren een [gekoppelde server](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) gebruiken om naar elkaar te verwijzen.

De volgende Transact-SQL-voorbeeld code maakt gebruik van de [begin GEdistribueerde trans actie](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) om gedistribueerde trans actie te starten.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Ontwikkel ervaring met .NET en Transact-SQL combi neren

.NET-toepassingen die gebruikmaken van System. Trans Action-klassen kunnen TransactionScope-klasse combi neren met de Transact-SQL-instructie BEGIN gedistribueerde trans actie. Binnen TransactionScope wordt de interne trans actie die de BEGIN DITRIBUTED-trans actie uitvoert, expliciet gepromoveerd tot gedistribueerde trans actie. Als er in de TransactionScope een tweede SqlConnecton wordt geopend, wordt deze impliciet gepromoveerd tot gedistribueerde trans actie. Zodra de gedistribueerde trans actie is gestart, worden alle volgende transactie aanvragen, ongeacht of ze afkomstig zijn van .NET of Transact-SQL, toegevoegd aan de bovenliggende gedistribueerde trans actie. Als gevolg hiervan zullen alle geneste transactie bereiken die worden geïnitieerd door de BEGIN instructie eindigen op dezelfde trans actie-en COMMIT/ROLLBACK-instructies, van invloed zijn op het algemene resultaat:
 * De instructie COMMIT heeft geen invloed op het transactie bereik dat is geïnitieerd door de instructie BEGIN, dat wil zeggen dat er geen resultaten worden doorgevoerd voordat de methode complete () wordt aangeroepen op het TransactionScope-object. Als TransactionScope-object wordt vernietigd voordat het is voltooid, worden alle wijzigingen die in het bereik zijn aangebracht, teruggedraaid.
 * De instructie ROLLBACK leidt ertoe dat de volledige TransactionScope terugdraait. Elke poging om nieuwe trans acties in TransactionScope te registreren, zal daarna mislukken en er wordt geprobeerd om voltooid () aan te roepen op TransactionScope-object.

Hier volgt een voor beeld waarin trans actie expliciet wordt bevorderd tot gedistribueerde trans actie met Transact-SQL.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

In het volgende voor beeld ziet u een trans actie die impliciet wordt bevorderd tot gedistribueerde trans actie zodra de tweede SqlConnecton is gestart in de TransactionScope.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Trans acties op meerdere servers voor Azure SQL Database

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Elastische-database transacties worden ondersteund op verschillende servers in Azure SQL Database. Als trans acties meerdere server grenzen, moeten de deelnemende servers eerst worden ingevoerd in een wederzijdse communicatie relatie. Zodra de communicatie relatie tot stand is gebracht, kan elke data base in een van de twee servers deel nemen aan elastische trans acties met data bases van de andere server. Met trans acties die meer dan twee servers omspannen, moet er een communicatie relatie zijn voor elk paar servers.

Gebruik de volgende Power shell-cmdlets voor het beheren van communicatie relaties tussen servers voor Elastic data base-trans acties:

* **New-AzSqlServerCommunicationLink**: gebruik deze cmdlet om een nieuwe communicatie relatie te maken tussen twee servers in Azure SQL database. De relatie is symmetrisch, wat betekent dat beide servers trans acties kunnen initiëren met de andere server.
* **Get-AzSqlServerCommunicationLink**: gebruik deze cmdlet om bestaande communicatie relaties en hun eigenschappen op te halen.
* **Remove-AzSqlServerCommunicationLink**: gebruik deze cmdlet om een bestaande communicatie relatie te verwijderen.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Trans acties op meerdere servers voor Azure SQL Managed instance

Gedistribueerde trans acties worden ondersteund op verschillende servers in Azure SQL Managed instance. Wanneer trans acties intermanaged instance grenzen overschrijden, moeten de deelnemende instanties eerst worden ingevoerd in een onderlinge beveiligings-en communicatie relatie. Dit doet u door een [Server vertrouwensrelatie groep](../managed-instance/server-trust-group-overview.md)te maken die kan worden uitgevoerd op Azure Portal. Als beheerde exemplaren zich niet in hetzelfde virtuele netwerk bevinden, moet de [peering van het virtuele netwerk](../../virtual-network/virtual-network-peering-overview.md) worden ingesteld en binnenkomende en uitgaande regels van de netwerk beveiligings groep moeten poorten 5024 en 11000-12000 toestaan voor alle deelnemende virtuele netwerken.

  ![Server vertrouwens groepen in azure Portal][3]

In het volgende diagram ziet u de vertrouwens groep van de server met beheerde instanties waarmee gedistribueerde trans acties kunnen worden uitgevoerd met .NET of Transact-SQL.

  ![Gedistribueerde trans acties met Azure SQL Managed instance met elastische trans acties][2]

## <a name="monitoring-transaction-status"></a>Controle transactie status

Gebruik dynamische beheer weergaven (Dmv's) om de status en voortgang van uw lopende elastische database transacties te bewaken. Alle Dmv's die betrekking hebben op trans acties zijn relevant voor gedistribueerde trans acties in SQL Database en een beheerd exemplaar. U vindt de bijbehorende lijst met Dmv's hier: [trans actie-gerelateerde dynamische beheer weergaven en-functies (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Deze Dmv's zijn bijzonder nuttig:

* **sys.DM \_ Tran \_ actieve \_ trans acties**: een lijst met momenteel actieve trans acties en hun status. De kolom UOW (eenheid van werk) kan de verschillende onderliggende trans acties identificeren die deel uitmaken van dezelfde gedistribueerde trans actie. Alle trans acties binnen dezelfde gedistribueerde trans actie hebben dezelfde UOW-waarde. Zie de [dmv-documentatie](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql)voor meer informatie.
* **sys.DM \_ Tran- \_ database \_ transacties**: biedt aanvullende informatie over trans acties, zoals plaatsing van de trans actie in het logboek. Zie de [dmv-documentatie](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)voor meer informatie.
* **sys.DM \_ Tran- \_ vergren delingen**: bevat informatie over de vergren delingen die momenteel worden bewaard door lopende trans acties. Zie de [dmv-documentatie](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)voor meer informatie.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn momenteel van toepassing op elastische data base-trans acties in SQL Database:

* Alleen trans acties over data bases in SQL Database worden ondersteund. Andere [X//Open XA-](https://en.wikipedia.org/wiki/X/Open_XA) resource providers en data bases buiten SQL database kunnen geen deel uitmaken van elastische data base-trans acties. Dit betekent dat Elastic data base-trans acties niet kunnen worden uitgerekt over on-premises SQL Server en Azure SQL Database. Ga verder met het gebruik van MSDTC voor gedistribueerde trans acties op locatie.
* Alleen door de client samengestelde trans acties van een .NET-toepassing worden ondersteund. De server ondersteuning voor T-SQL zoals BEGIN gedistribueerde trans actie is gepland, maar is nog niet beschikbaar.
* Trans acties via WCF-services worden niet ondersteund. U hebt bijvoorbeeld een WCF-service methode waarmee een trans actie wordt uitgevoerd. Het sluiten van de aanroep binnen een transactie bereik mislukt als [System. service model. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

De volgende beperkingen zijn momenteel van toepassing op gedistribueerde trans acties in een beheerd exemplaar:

* Alleen trans acties over data bases in een beheerd exemplaar worden ondersteund. Andere [X//Open XA-](https://en.wikipedia.org/wiki/X/Open_XA) resource providers en data bases buiten een Azure SQL Managed instance kunnen niet deel nemen aan gedistribueerde trans acties. Dit betekent dat gedistribueerde trans acties niet kunnen worden uitgerekt over on-premises SQL Server en Azure SQL Managed instance. Ga verder met het gebruik van MSDTC voor gedistribueerde trans acties op locatie.
* Trans acties via WCF-services worden niet ondersteund. U hebt bijvoorbeeld een WCF-service methode waarmee een trans actie wordt uitgevoerd. Het sluiten van de aanroep binnen een transactie bereik mislukt als [System. service model. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* Azure SQL Managed instance moet deel uitmaken van een [Server vertrouwens groep](../managed-instance/server-trust-group-overview.md) om deel te nemen aan gedistribueerde trans acties.
* Beperkingen van [Server vertrouwens groepen](../managed-instance/server-trust-group-overview.md) beïnvloeden gedistribueerde trans acties.
* Beheerde instanties die deel nemen aan gedistribueerde trans acties, moeten verbinding hebben via particuliere eind punten (met behulp van een privé IP-adres van het virtuele netwerk waar ze worden geïmplementeerd) en moeten onderling worden gerefereerd met behulp van particuliere FQDN-namen. Client toepassingen kunnen gedistribueerde trans acties op privé-eind punten gebruiken. Daarnaast kunnen client toepassingen gedistribueerde trans acties op open bare eind punten gebruiken wanneer Transact-SQL gebruikmaakt van gekoppelde servers die verwijzen naar privé-eind punten. Deze beperking wordt uitgelegd in het volgende diagram.
  ![Connectiviteits beperking van het particuliere eind punt][4]
## <a name="next-steps"></a>Volgende stappen

* Neem voor vragen contact op met de [micro soft Q&een vraag pagina voor SQL database](/answers/topics/azure-sql-database.html).
* Voor functie aanvragen voegt u deze toe aan het [SQL database feedback forum](https://feedback.azure.com/forums/217321-sql-database/) of het [Managed instance-forum](https://feedback.azure.com/forums/915676-sql-managed-instance).



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
