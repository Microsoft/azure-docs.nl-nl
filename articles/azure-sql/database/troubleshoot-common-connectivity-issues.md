---
title: Werken met tijdelijke fouten
description: Informatie over het oplossen van problemen, het vaststellen en voor komen van een SQL-verbindings fout of tijdelijke fout bij het verbinden met Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics.
keywords: SQL-verbinding, connection string, verbindings problemen, tijdelijke fout, verbindings fout
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 9f2e755047910aefa89c2f187cda956aca608b98
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093754"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>Tijdelijke verbindings fouten in SQL Database en SQL Managed instance oplossen

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In dit artikel wordt beschreven hoe u verbindings fouten en tijdelijke fouten voor komt, oplost en verhelpt die uw client toepassing tegen komt wanneer deze communiceert met Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics. Meer informatie over het configureren van de logica voor opnieuw proberen, het bouwen van de connection string en het aanpassen van andere Verbindings instellingen.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Tijdelijke fouten (tijdelijke storingen)

Een tijdelijke fout, ook wel bekend als een tijdelijke fout, heeft een onderliggende oorzaak die binnenkort wordt opgelost. Een incidentele oorzaak van tijdelijke fouten is wanneer het Azure-systeem snel hardwarebronnen verschuift voor een betere taak verdeling van verschillende werk belastingen. De meeste van deze herconfiguratie gebeurtenissen eindigen in minder dan 60 seconden. Tijdens deze herconfiguraties periode hebt u mogelijk problemen met het maken van verbinding met uw data base in SQL Database. Toepassingen die verbinding maken met uw data base moeten worden gebouwd om deze tijdelijke fouten te verwachten. Als u deze wilt afhandelen, implementeert u de logica voor opnieuw proberen in hun code in plaats van deze te halen aan gebruikers als toepassings fouten.

Als uw client programma ADO.NET gebruikt, wordt het programma op de tijdelijke fout gemeld door het genereren van **SQLException**.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Verbinding versus opdracht

Voer de SQL Database en de SQL Managed instance-verbinding opnieuw uit, afhankelijk van het volgende:

- **Er is een tijdelijke fout opgetreden tijdens een verbinding probeer**

Probeer de verbinding na een vertraging van enkele seconden opnieuw.

- **Er treedt een tijdelijke fout op tijdens een query opdracht van SQL Database en SQL Managed instance**

Voer de opdracht niet onmiddellijk opnieuw uit. In plaats daarvan kunt u na een vertraging de verbinding tot stand brengen. Voer de opdracht vervolgens opnieuw uit.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Pogingslogica voor tijdelijke problemen

Client Programma's die af en toe een tijdelijke fout ondervinden, zijn robuuster wanneer ze logica voor nieuwe pogingen bevatten. Wanneer uw programma communiceert met uw data base in SQL Database via middleware van derden, vraagt u de leverancier of de middleware logica voor tijdelijke fouten bevat.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principes voor opnieuw proberen

- Als de fout tijdelijk is, probeert u opnieuw een verbinding te openen.
- Probeer niet rechtstreeks een instructie SQL Database of SQL Managed instance uit `SELECT` te voeren die is mislukt met een tijdelijke fout. Maak in plaats daarvan een nieuwe verbinding tot stand en probeer het opnieuw `SELECT` .
- Wanneer een SQL Database of SQL Managed instance `UPDATE` -instructie mislukt met een tijdelijke fout, moet u een nieuwe verbinding maken voordat u de update opnieuw probeert uit te voeren. De logica voor opnieuw proberen moet ervoor zorgen dat de hele database transactie is voltooid of dat de hele trans actie wordt teruggedraaid.

### <a name="other-considerations-for-retry"></a>Andere overwegingen voor nieuwe pogingen

- Een batch-programma dat automatisch wordt gestart na het werk uren en eindigt vóór morgen, kan een zeer lange tijds intervallen hebben tussen de nieuwe pogingen.
- Een gebruikers interface programma moet er rekening mee maken dat de menselijke tendens na te lange tijd een ogen blik geduld. De oplossing mag niet om de paar seconden opnieuw worden geprobeerd, omdat dit beleid het systeem kan overlopen met aanvragen.

### <a name="interval-increase-between-retries"></a>Interval toename tussen nieuwe pogingen

U wordt aangeraden vijf seconden te wachten voordat u voor het eerst een nieuwe poging voert. Opnieuw proberen na een vertraging die korter is dan 5 seconden Risico's voor de Cloud service. Voor elke volgende poging moet de vertraging exponentieel toenemen, tot een maximum van 60 seconden.

Zie [Connection Pooling (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling)voor een bespreking van de blokkerings periode voor clients die gebruikmaken van ADO.net.

Het is ook mogelijk dat u een maximum aantal nieuwe pogingen wilt instellen voordat het programma zelf wordt beëindigd.

### <a name="code-samples-with-retry-logic"></a>Code voorbeelden met pogings logica

Code voorbeelden met de logica voor nieuwe pogingen zijn beschikbaar op:

- [Maak robuuste verbinding met Azure SQL met ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Maak robuuste verbinding met Azure SQL met PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>De logica voor opnieuw proberen testen

Als u de logica voor opnieuw proberen wilt testen, moet u een fout simuleren of veroorzaken die kan worden gecorrigeerd terwijl het programma nog actief is.

#### <a name="test-by-disconnecting-from-the-network"></a>Testen door de verbinding met het netwerk te verbreken

U kunt de logica voor opnieuw proberen testen door de verbinding tussen de client computer en het netwerk te verbreken terwijl het programma wordt uitgevoerd. De fout is:

- **SQLException. Number** = 11001
- Bericht: "er is geen host bekend"

Als onderdeel van de eerste nieuwe poging kunt u de client computer opnieuw verbinding laten maken met het netwerk en vervolgens proberen verbinding te maken.

Als u deze test praktisch wilt uitvoeren, koppelt u uw computer los van het netwerk voordat u het programma start. Vervolgens herkent het programma een runtime-para meter waarmee het programma:

- Voeg 11001 tijdelijk toe aan de lijst met fouten die als tijdelijk moeten worden beschouwd.
- Probeer de eerste verbinding zoals gebruikelijk.
- Nadat de fout is opgetreden, verwijdert u 11001 in de lijst.
- Hiermee wordt een bericht weer gegeven waarin de gebruiker wordt gevraagd om de computer op het netwerk te koppelen.
- Onderbreek verdere uitvoering met behulp van de methode **console. readline** of een dialoog venster met een knop OK. De gebruiker drukt op ENTER wanneer de computer is aangesloten op het netwerk.
- Probeer het opnieuw om verbinding te maken. dit wordt verwacht.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Testen door de gebruikers naam foutief te spellen tijdens het verbinden

Het programma kan de gebruikers naam voor de eerste verbindings poging het meest misbruiken. De fout is:

- **SQLException. Number** = 18456
- Bericht: de aanmelding is mislukt voor de gebruiker WRONG_MyUserName.

Als onderdeel van de eerste poging, kan uw programma de spel fouten corrigeren en vervolgens proberen verbinding te maken.

Als u deze test praktisch wilt uitvoeren, herkent het programma een runtime-para meter waarmee het programma:

- Voeg 18456 tijdelijk toe aan de lijst met fouten die als tijdelijk moeten worden beschouwd.
- Voeg ' WRONG_ ' toe aan de gebruikers naam.
- Nadat de fout is opgetreden, verwijdert u 18456 in de lijst.
- Verwijder ' WRONG_ ' uit de gebruikers naam.
- Probeer het opnieuw om verbinding te maken. dit wordt verwacht.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection-para meters voor nieuwe verbinding

Als uw client programma verbinding maakt met uw data base in SQL Database met behulp van de .NET Framework Class **System. data. SqlClient. SqlConnection**, gebruikt u .net 4.6.1 of hoger (of .net core), zodat u de nieuwe functie verbinding opnieuw kunt gebruiken. Zie de [eigenschap SqlConnection. Connections Tring](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true)(Engelstalig) voor meer informatie over de functie.

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Wanneer u de [Connection String](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) voor uw **SqlConnection** -object bouwt, coördineert u de waarden uit de volgende para meters:

- **ConnectRetryCount**: de &nbsp; &nbsp; standaard waarde is 1. Het bereik is 0 tot en met 255.
- **ConnectRetryInterval**: de &nbsp; &nbsp; standaard waarde is 10 seconden. Het bereik is 1 tot en met 60.
- Verbindingstime **-out**: de &nbsp; &nbsp; standaard waarde is 15 seconden. Het bereik is 0 tot en met 2147483647.

Met name de gekozen waarden moeten de volgende gelijkheid doen: time-out voor verbinding = ConnectRetryCount * ConnectionRetryInterval

Als de telling bijvoorbeeld gelijk is aan 3 en het interval 10 seconden is, geeft een time-out van slechts 29 seconden het systeem niet voldoende tijd voor het derde en de laatste poging om verbinding te maken: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Verbinding versus opdracht

Met de **ConnectRetryCount** -en **ConnectRetryInterval** -para meters kan uw **SqlConnection** -object de verbindings bewerking opnieuw proberen zonder dat uw programma wordt weer gegeven, zoals het retour neren van uw programma. De nieuwe pogingen kunnen in de volgende situaties optreden:

- Aanroep van de methode SqlConnection. Open
- Aanroep van SqlConnection.Exemethode schattige

Er is een subtlety. Als er een tijdelijke fout optreedt tijdens het uitvoeren van uw *query* , probeert uw **SqlConnection** -object de verbindings bewerking niet opnieuw uit te voeren. De query wordt zeker niet opnieuw uitgevoerd. De verbinding wordt echter door **SqlConnection** zeer snel gecontroleerd voordat de query wordt verzonden voor uitvoering. Als de snelle controle een verbindings probleem detecteert, probeert **SqlConnection** de verbindings bewerking opnieuw. Als de nieuwe poging slaagt, wordt uw query verzonden voor uitvoering.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Moet ConnectRetryCount worden gecombineerd met de logica voor opnieuw proberen van de toepassing

Stel dat uw toepassing een robuuste, aangepaste logica voor opnieuw proberen heeft. De verbindings bewerking kan vier keer opnieuw worden uitgevoerd. Als u **ConnectRetryInterval** en **ConnectRetryCount** = 3 aan uw Connection String toevoegt, wordt het aantal nieuwe pogingen verhoogd tot 4 * 3 = 12 pogingen. Misschien bent u niet een groot aantal nieuwe pogingen.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Verbindingen met uw data base in SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Verbinding: verbindings reeks

De connection string die nodig is om verbinding te maken met uw data base, wijkt enigszins af van de teken reeks die wordt gebruikt om verbinding te maken met SQL Server. U kunt de connection string voor uw data base kopiëren vanuit de [Azure Portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Verbinding: IP-adres

U moet SQL Database configureren om communicatie te accepteren van het IP-adres van de computer die als host fungeert voor uw client programma. Als u deze configuratie wilt instellen, bewerkt u de firewall instellingen via de [Azure Portal](https://portal.azure.com/).

Als u het IP-adres vergeet te configureren, mislukt het programma met een handig fout bericht dat het benodigde IP-adres vermeldt.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

Zie [firewall instellingen configureren in SQL database](firewall-configure.md)voor meer informatie.
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Verbinding: poorten

Normaal gesp roken moet u ervoor zorgen dat alleen poort 1433 is geopend voor uitgaande communicatie op de computer die als host fungeert voor uw client programma.

Als uw client programma bijvoorbeeld wordt gehost op een Windows-computer, kunt u Windows Firewall op de host gebruiken om poort 1433 te openen.

1. Open het Configuratiescherm.
2. Selecteer **alle configuratie scherm-items**  >  **Windows Firewall**  >  **instellingen** voor de  >    >    >  **regel** voor uitgaande verbindingen

Als uw client programma wordt gehost op een virtuele machine (VM) van Azure, Lees [dan de poorten na 1433 voor ADO.NET 4,5 en SQL database](adonet-v12-develop-direct-route-ports.md).

Zie [Azure SQL database firewall](firewall-configure.md)voor achtergrond informatie over de configuratie van poorten en IP-adressen in uw data base.

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Verbinding: ADO.NET 4.6.2 of hoger

Als uw programma gebruikmaakt van ADO.NET-klassen als **System. data. SqlClient. SqlConnection** om verbinding te maken met SQL database, raden we u aan .NET Framework versie 4.6.2 of hoger te gebruiken.

#### <a name="starting-with-adonet-462"></a>Beginnen met ADO.NET 4.6.2

- Er wordt direct geprobeerd om de verbinding te verbreken voor Azure SQL, waardoor de prestaties van apps die in de Cloud zijn ingeschakeld, worden verbeterd.

#### <a name="starting-with-adonet-461"></a>Beginnen met ADO.NET 4.6.1

- Voor SQL Database is de betrouw baarheid verbeterd wanneer u een verbinding opent met behulp van de methode **SqlConnection. Open** . De methode **Open** bevat nu de methoden voor het opnieuw proberen van beste pogingen als reactie op tijdelijke fouten voor bepaalde fouten binnen de time-outperiode van de verbinding.
- Groepsgewijze verbindingen wordt ondersteund. Dit omvat een efficiënte verificatie dat het verbindings object dat het programma geeft, werkt.

Wanneer u een verbindings object van een verbindings groep gebruikt, wordt u aangeraden de verbinding tijdelijk te sluiten wanneer deze niet direct in gebruik is. Het is niet kostbaar om een verbinding opnieuw te openen, maar het maken van een nieuwe verbinding.

Als u ADO.NET 4,0 of eerder gebruikt, raden we u aan om een upgrade uit te voeren naar de meest recente versie van ADO.NET. Vanaf 2018 augustus kunt u [ADO.net 4.6.2 downloaden](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostiek

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostische gegevens: testen of hulpprogram ma's verbinding kunnen maken

Als uw programma geen verbinding kan maken met uw data base in SQL Database, moet u een diagnostische optie proberen om verbinding te maken met een hulp programma. In het ideale geval maakt het hulp programma verbinding met behulp van de bibliotheek die uw programma gebruikt.

Op elke Windows-computer kunt u deze hulpprogram ma's proberen:

- SQL Server Management Studio (ssms.exe), die verbinding maakt met behulp van ADO.NET
- `sqlcmd.exe`, die verbinding maakt met behulp van [ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)

Wanneer het programma is verbonden, test u of een korte SQL SELECT-query werkt.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostische gegevens: Controleer de open poorten

Als u vermoedt dat verbindings pogingen mislukken vanwege poort problemen, kunt u een hulp programma uitvoeren op uw computer die rapporteert over de poort configuraties.

In Linux kunnen de volgende hulpprogram ma's handig zijn:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Wijzig de voorbeeld waarde in uw IP-adres.

In Windows kan het hulp programma [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) nuttig zijn. Hier volgt een voor beeld van de uitvoering van een query naar de poort situatie op een data base in SQL Database en die werd uitgevoerd op een laptop computer:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostische gegevens: uw fouten vastleggen

Een onregelmatig probleem wordt soms het beste vastgesteld door de detectie van een algemeen patroon gedurende dagen of weken.

Uw client kan helpen bij een diagnose door alle fouten te registreren die worden aangetroffen. Mogelijk kunt u de logboek vermeldingen correleren met fout gegevens die intern door SQL Database geregistreerd.

Enter prise Library 6 (EntLib60) biedt .NET-beheerde klassen om te helpen bij het vastleggen van Logboeken. Zie [5-net zo eenvoudig als een logboek voor meer informatie: gebruik het toepassings blok voor logboek registratie](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostische gegevens: systeem logboeken controleren op fouten

Hier volgen enkele Transact-SQL-instructies voor het uitvoeren van query's op fout logboeken en andere informatie.

| Query op logboek | Description |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |De weer gave [sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) biedt informatie over afzonderlijke gebeurtenissen, waaronder een aantal dat kan leiden tot tijdelijke fouten of verbindings fouten.<br/><br/>In het ideale geval kunt u de **start_time** -of **end_time** waarden correleren met informatie over wanneer het client programma problemen heeft ondervonden.<br/><br/>U moet verbinding maken met de *hoofd* database om deze query uit te voeren. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |De weer gave [sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database) biedt geaggregeerde aantallen gebeurtenis typen voor aanvullende diagnostische gegevens.<br/><br/>U moet verbinding maken met de *hoofd* database om deze query uit te voeren. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostische gegevens: zoeken naar probleem gebeurtenissen in het SQL Database-logboek

U kunt zoeken naar vermeldingen over probleem gebeurtenissen in het SQL Database logboek. Probeer de volgende Transact-SQL-instructie SELECT in de *hoofd* database:

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Een paar geretourneerde rijen van sys.fn_xe_telemetry_blob_target_read_file

In het volgende voor beeld ziet u hoe een geretourneerde rij eruit kan zien. De Null-waarden die worden weer gegeven, zijn vaak niet null in andere rijen.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enter prise-bibliotheek 6

Enter prise Library 6 (EntLib60) is een framework van .NET-klassen waarmee u krachtige clients van Cloud Services kunt implementeren, een van SQL Database. Zie [Enter prise Library 6-April 2013](/previous-versions/msp-n-p/dn169621(v=pandp.10))om onderwerpen te vinden die zijn gericht op elk gebied waarin EntLib60 kan worden geholpen.

Pogings logica voor het afhandelen van tijdelijke fouten is één gebied waarin EntLib60 kan helpen. Zie voor meer informatie [4-Perseverance, geheim van alle Triumphs: gebruik het toepassings blok voor de tijdelijke fout afhandeling](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

> [!NOTE]
> De bron code voor EntLib60 is beschikbaar als open bare down load van het [Download centrum](https://github.com/MicrosoftArchive/enterprise-library). Micro soft heeft geen plannen om verdere onderdelen updates of onderhouds updates te maken voor EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60-klassen voor tijdelijke fouten en nieuwe poging

De volgende EntLib60-klassen zijn vooral handig voor de logica voor opnieuw proberen. Al deze klassen vindt u in of onder de naam ruimte **micro soft. practices. EnterpriseLibrary. TransientFaultHandling**.

In de naam ruimte **micro soft. practices. EnterpriseLibrary. TransientFaultHandling**:

- Klasse **RetryPolicy**
  - **ExecuteAction** -methode
- Klasse **ExponentialBackoff**
- Klasse **SqlDatabaseTransientErrorDetectionStrategy**
- Klasse **ReliableSqlConnection**
  - Methode **ExecuteCommand**

In de naam ruimte **Microsoft. practices. EnterpriseLibrary. TransientFaultHandling. TestSupport**:

- Klasse **AlwaysTransientErrorDetectionStrategy**
- Klasse **NeverTransientErrorDetectionStrategy**

Hier vindt u enkele koppelingen naar informatie over EntLib60:

- Gratis boek downloaden: [ontwikkelaars handleiding voor micro soft Enter prise Library, 2e editie](https://www.microsoft.com/download/details.aspx?id=41145).
- Best practices: [algemene richt lijnen voor opnieuw proberen](/azure/architecture/best-practices/transient-faults) hebben een uitstekende uitgebreide beschrijving van de logica voor opnieuw proberen.
- NuGet downloaden: [Enter prise Library-Application Block 6,0 voor tijdelijke fout afhandeling](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: de logboek registratie blok keren

- Het logboek registratie blok is een zeer flexibele en configureer bare oplossing die u kunt gebruiken voor het volgende:
  - Maak logboek berichten en sla deze op in een groot aantal verschillende locaties.
  - Berichten categoriseren en filteren.
  - Verzamel contextuele informatie die nuttig is voor fout opsporing en tracering, en voor controle-en algemene logboek registratie vereisten.
- In het logboek registratie wordt de functionaliteit van logboek registratie van de logboek bestemming abstract, zodat de toepassings code consistent is, onafhankelijk van de locatie en het type van de doel logboek registratie.

Zie [5-net zo eenvoudig als een logboek voor meer informatie: gebruik het toepassings blok voor logboek registratie](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Bron code van methode EntLib60 IsTransient

Vervolgens is de C#-bron code voor de methode **IsTransient** van de **SqlDatabaseTransientErrorDetectionStrategy** -klasse. De bron code verduidelijkt welke fouten werden beschouwd als tijdelijk en betrouw bare na een nieuwe poging, vanaf april 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Volgende stappen

- [Verbindings bibliotheken voor SQL Database en SQL Server](connect-query-content-reference-guide.md#libraries)
- [Groepsgewijze verbinding (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Nieuwe poging* is een Apache 2,0-gelicentieerde, algemeen nieuwe bibliotheek voor het opnieuw proberen, geschreven in Python,](https://pypi.python.org/pypi/retrying) om de taak voor het toevoegen van het gedrag voor opnieuw proberen toe te voegen aan vrijwel alles.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php