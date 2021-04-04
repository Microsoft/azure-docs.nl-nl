---
title: Clientbibliotheek voor elastische database gebruiken met Entity Framework
description: Clientbibliotheek van elastische database gebruiken en Entity Framework gebruiken voor het coderen van databases
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 48d43cb2d3c51194d0708a2b9b739a0ee87843d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793395"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Clientbibliotheek van elastische database met Entity Framework
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit document vindt u de wijzigingen in een Entity Framework-toepassing die nodig zijn voor integratie met de [hulpprogramma's voor elastische databases](elastic-scale-introduction.md). Hierbij gaat het vooral om het afstemmen van [shard-toewijzingsbeheer](elastic-scale-shard-map-management.md) en [gegevensafhankelijke routering](elastic-scale-data-dependent-routing.md) op de methode **Code First** van Entity Framework. We gebruiken de zelfstudie [Code First - nieuwe database](/ef/ef6/modeling/code-first/workflows/new-database) voor EF als terugkerend voorbeeld in dit document. De voorbeeldcode bij dit document is onderdeel van de set voorbeelden van de hulpprogramma's voor de elastische database in de Visual Studio Code-voorbeelden.

## <a name="downloading-and-running-the-sample-code"></a>De voorbeeldcode downloaden en uitvoeren

De code voor dit artikel downloaden:

* Visual Studio 2012 of hoger is vereist.
* Download het [voorbeeld voor integratie van de hulpprogramma's voor de elastische database voor Azure SQL en Entity Framework](https://github.com/Azure/elastic-db-tools/). Pak het voorbeeld uit naar een locatie naar keuze.
* Start Visual Studio.
* Selecteer in Visual Studio de opties Bestand -> Project openen/Oplossing.
* Blader in het dialoogvenster **Project openen** naar het voorbeeld dat u hebt gedownload en selecteer **EntityFrameworkCodeFirst.sln** om het voorbeeld te openen.

Als u het voorbeeld wilt uitvoeren, moet u drie lege databases in Azure SQL Database maken:

* Database voor shard-toewijzingsbeheer
* Database voor shard 1
* Database voor shard 2

Zodra u deze databases hebt gemaakt, vervangt u de tijdelijke aanduidingen in **Program.cs** door uw servernaam, de databasenamen en uw referenties om verbinding te maken met de databases. Bouw de oplossing in Visual Studio. Visual Studio downloadt de benodigde NuGet-pakketten voor de clientbibliotheek van de elastische database, Entity Framework en tijdelijke foutverwerking als onderdeel van het bouwproces. Zorg ervoor dat het herstellen van NuGet-pakketten is ingeschakeld voor uw oplossing. U kunt deze instelling inschakelen door met de rechtermuisknop op het oplossingsbestand in de Visual Studio Solution Explorer te klikken.

## <a name="entity-framework-workflows"></a>Entity Framework-werkstromen

Entity Framework-ontwikkelaars vertrouwen op een van de volgende vier werkstromen om toepassingen te bouwen en te zorgen voor persistentie voor toepassingsobjecten:

* **Code First (nieuwe database)** : De EF-ontwikkelaar maakt het model in de toepassingscode en vervolgens genereert EF de database.
* **Code First (bestaande database)** : De ontwikkelaar laat EF de toepassingscode genereren voor het model vanuit een bestaande database.
* **Model First**: De ontwikkelaar maakt het model met de ontwerpfunctie van EF en vervolgens maakt EF de database vanuit het model.
* **Database First**: De ontwikkelaar gebruikt het hulpprogramma van EF om het model van een bestaande database af te leiden.

Al deze methoden zijn gebaseerd op de DbContext-klasse voor het transparant beheren van databaseverbindingen en databaseschema voor een toepassing. Verschillende constructors van de DbContext-basisklasse maken verschillende niveaus van controle over het maken van verbindingen, de bootstrapping van databases en het maken van schema's mogelijk. Problemen ontstaan voornamelijk doordat het databaseverbindingsbeheer dat door EF wordt geleverd, kruist met de verbindingsbeheermogelijkheden van de gegevensafhankelijke routeringsinterfaces van de clientbibliotheek van de elastische database.

## <a name="elastic-database-tools-assumptions"></a>Veronderstellingen over hulpprogramma's voor elastische databases

Zie de [woordenlijst voor hulpprogramma's van de elastische database](elastic-scale-glossary.md) voor definities van termen.

Met de clientbibliotheek van de elastische database definieert u de partities van uw toepassingsgegevens, die shardlets worden genoemd. Shardlets worden geïdentificeerd met behulp van een sharding-sleutel en worden toegewezen aan specifieke databases. Een toepassing kan zoveel databases hebben als nodig is en de shardlets distribueren om voldoende capaciteit of prestaties te leveren volgens de huidige bedrijfsvereisten. De toewijzing van sharding-sleutelwaarden aan de databases wordt opgeslagen door een shard-toewijzing die door de client-API's van de elastische database wordt geleverd. Deze mogelijkheid wordt **shard-toewijzingsbeheer** of SMM genoemd. De shard-toewijzing dient ook als broker voor databaseverbindingen voor aanvragen die een sharding-sleutel bevatten. Deze mogelijkheid staat bekend als **gegevensafhankelijke routering**.

Shard-toewijzingsbeheer beschermt gebruikers tegen inconsistente weergaven in shardlet-gegevens die kunnen optreden wanneer gelijktijdig shardlet-beheerbewerkingen (zoals het verplaatsen van gegevens van de ene shard naar de andere) plaatsvinden. Hiertoe fungeren de shard-toewijzingen die worden beheerd door de clientbibliotheek als broker voor de databaseverbindingen voor een toepassing. Zo kan de shard-toewijzingsfunctionaliteit automatisch een databaseverbinding uitschakelen wanneer de shard-beheerbewerkingen impact kunnen hebben op de shardlet waarvoor de verbinding is gemaakt. Deze aanpak moet worden geïntegreerd met een deel van de functionaliteit van EF, zoals het maken van nieuwe verbindingen vanuit een bestaande verbinding om te controleren of een database bestaat. In het algemeen werken de DbContext-standaardconstructors alleen betrouwbaar voor gesloten databaseverbindingen die veilig kunnen worden gekloond voor EF-werkzaamheden. Het ontwerpprincipe van de elastische database dient in plaats daarvan alleen als broker voor geopende verbindingen. U zou kunnen denken dat het sluiten van een verbinding die door de clientbibliotheek is gebrokerd voordat deze aan de EF DbContext wordt overgedragen, dit probleem kan oplossen. Als u de verbinding sluit en ervan uitgaat dat EF deze weer opent, kan het voorkomen dat de validatie en consistentiecontroles die door de bibliotheek worden uitgevoerd, worden overgeslagen. De migratiefunctionaliteit in EF gebruikt deze verbindingen echter om het onderliggende databaseschema op een voor de toepassing transparante manier te beheren. In de ideale situatie behoudt en combineert u al deze mogelijkheden van de clientbibliotheek van de elastische database en EF in dezelfde toepassing. In de volgende sectie wordt verder ingegaan op deze eigenschappen en vereisten.

## <a name="requirements"></a>Vereisten

Als u met de clientbibliotheek van de elastische database en Entity Framework-API's werkt, is het verstandig om de volgende eigenschappen te behouden:

* **Uitschalen**: Databases uit de gegevenslaag van de shard-toepassing toevoegen of verwijderen als dat nodig is voor de capaciteitseisen van de toepassing. Dit betekent controle over het maken en verwijderen van databases en het gebruik van API's voor het shard-toewijzingsbeheer voor de elastische database API's om databases te beheren en shardlets toe te wijzen.
* **Consistentie**: De toepassing maakt gebruik van sharding en gebruikt de gegevensafhankelijke routeringsmogelijkheden van de clientbibliotheek. Om beschadigde of verkeerde zoekresultaten te voorkomen, worden de verbindingen via het shard-toewijzingsbeheer tot stand gebracht. Hiermee blijft ook validatie en consistentie behouden.
* **Code First**: Om het gemak van het Code First-paradigma van EF te behouden. In Code First worden klassen in de toepassing transparant toegewezen aan de onderliggende databasestructuren. De toepassingscode werkt samen met DbSets die de meeste aspecten van de onderliggende databaseverwerking maskeren.
* **Schema**: Entity Framework maakt het eerste databaseschema en evalueert de daaropvolgende schema's via migraties. Door deze mogelijkheden te behouden, kan uw app eenvoudig worden aangepast naarmate de gegevens veranderen.

In de volgende richtlijnen wordt aangegeven hoe aan deze eisen voor Code First-toepassingen kan worden voldaan met behulp van hulpprogramma's voor elastische databases.

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Gegevensafhankelijke routering met behulp van EF DbContext

Databaseverbindingen met Entity Framework worden doorgaans beheerd via subklassen van **DbContext**. Maak deze subklassen door ze af te leiden van **DbContext**. Hier definieert u uw **DbSets** die de door de database ondersteunde verzamelingen van CLR-objecten voor uw toepassing implementeren. In de context van gegevensafhankelijke routering kunt u verschillende nuttige eigenschappen identificeren die niet noodzakelijkerwijs gelden voor andere Code First-toepassingsscenario's van EF:

* De database bestaat al en is geregistreerd in de shard-toewijzing van de elastische database.
* Het schema van de toepassing is al in de database geïmplementeerd (zie de uitleg hieronder).
* Gegevensafhankelijke routeringsverbindingen met de database worden door de shard-toepassing gebrokered.

**DbContexts** met gegevensafhankelijke routering voor uitschalen integreren:

1. Maak fysieke databaseverbindingen via de elastische-databaseclientinterfaces van het shard-toewijzingsbeheer.
2. Verpak de verbinding met de **DbContext**-subklasse
3. Geef de verbinding door in de **DbContext**-basisklassen om er zeker van te zijn dat alle verwerking ook aan de EF-zijde plaatsvindt.

Het volgende codevoorbeeld illustreert deze aanpak. (Deze code bevindt zich ook in het bijbehorende Visual Studio-project)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection
    // routed to the proper shard by the shard map manager.
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr),
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Hoofdpunten

* Een nieuwe constructor vervangt de standaardconstructor in de DbContext-subklasse
* De nieuwe constructor haalt de argumenten op die nodig zijn voor de gegevensafhankelijke routering via de clientbibliotheek van de elastische database:
  
  * de shard-toewijzing om toegang te krijgen tot de gegevensafhankelijke routeringsinterfaces;
  * de sharding-sleutel om de shardlet te identificeren;
  * een verbindingsreeks met de referenties voor de gegevensafhankelijke routeringsverbinding naar de shard.
* De aanroep aan de basisklasseconstructor verloopt via een omleiding naar een statische methode die alle stappen uitvoert die nodig zijn voor de gegevensafhankelijke routering.
  
  * Er wordt gebruikgemaakt van de OpenConnectionForKey-aanroep van de elastische-databaseclientinterfaces op de shard-toewijzing om een open verbinding tot stand te brengen.
  * De shard-toewijzing maakt de open verbinding met de shard die de shardlet voor de opgegeven sharding-sleutel bevat.
  * Deze open verbinding wordt teruggegeven aan de basisklasseconstructor van DbContext om aan te geven dat deze verbinding door EF moet worden gebruikt in plaats van dat EF automatisch een nieuwe verbinding maakt. Op deze manier is de verbinding getagd door de client-API van de elastische database, zodat consistentie kan worden gegarandeerd onder shard-toewijzingsbeheerbewerkingen.

Gebruik de nieuwe constructor voor uw DbContext-subklasse in plaats van de standaard constructor in uw code. Hier volgt een voorbeeld:

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: ");
var name = Console.ReadLine();

using (var db = new ElasticScaleContext<int>(
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString))
{
    var blog = new Blog { Name = name };
    db.Blogs.Add(blog);
    db.SaveChanges();

    // Display all Blogs for tenant 1
    var query = from b in db.Blogs
                orderby b.Name
                select b;
    …
}
```

De nieuwe constructor opent de verbinding met de shard die de gegevens bevat voor de shardlet die door de waarde van **tenantid1** wordt geïdentificeerd. De code in het blok **using** blijft ongewijzigd voor toegang tot de **DbSet** voor blogs die EF op de shard gebruikt voor **tenantid1**. Dit verandert de semantiek van de code in het blok using zodanig dat alle databasebewerkingen nu worden toegewezen aan de shard waarin **tenantid1** wordt behouden. Een LINQ-query over de blogs **DbSet** zou bijvoorbeeld alleen blogs retourneren die zijn opgeslagen op de huidige shard, maar niet die zijn opgeslagen op andere shards.  

### <a name="transient-faults-handling"></a>Afhandeling van tijdelijke fouten

Het Microsoft-team voor patronen en procedures heeft [het toepassingsblok voor het afhandelen van tijdelijke fouten](/previous-versions/msp-n-p/dn440719(v=pandp.60)) gepubliceerd. De bibliotheek wordt gebruikt met de clientbibliotheek voor elastische schaalaanpassing in combinatie met EF. Controleer of elke tijdelijke uitzondering terugkeert naar een plaats waar u ervoor kunt zorgen dat de nieuwe constructor wordt gebruikt na een tijdelijke fout. Zo kan elke nieuwe verbindingspoging worden uitgevoerd met behulp van de constructors die u hebt aangepast. Anders is een verbinding met de juiste shard niet gegarandeerd en zijn er geen garanties dat de verbinding behouden blijft als er wijzigingen in de shard-toewijzing optreden.

Het volgende codevoorbeeld illustreert hoe een SQL-beleid voor opnieuw proberen kan worden gebruikt voor de nieuwe **DbContext**-subklasseconstructors:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString))
        {
                var blog = new Blog { Name = name };
                db.Blogs.Add(blog);
                db.SaveChanges();
        …
        }
    });
```

**SqlDatabaseUtils.SqlRetryPolicy** in de bovenstaande code is gedefinieerd als een **SqlDatabaseTransientErrorDetectionStrategy** met maximaal 10 pogingen en een wachttijd van 5 seconden tussen de pogingen. Deze aanpak is vergelijkbaar met de richtlijnen voor EF en door de gebruiker geïnitieerde transacties (zie [Beperkingen bij uitvoeringsstrategieën voor opnieuw proberen (EF6 en hoger)](/ef/ef6/fundamentals/connection-resiliency/retry-logic)). Beide situaties vereisen dat de toepassing het bereik controleert waarnaar de tijdelijke uitzondering terugkeert: ofwel de transactie heropenen, ofwel (zoals weergegeven) de context opnieuw maken van de juiste constructor die gebruikmaakt van de clientbibliotheek van de elastische database.

De noodzaak om te bepalen waar tijdelijke uitzonderingen ons terugbrengen in het bereik, sluit ook het gebruik van de ingebouwde **SqlAzureExecutionStrategy** van EF uit. **SqlAzureExecutionStrategy** zou een verbinding heropenen, maar geen gebruikmaken van **OpenConnectionForKey** en dus alle validatie omzeilen die wordt uitgevoerd als onderdeel van de **OpenConnectionForKey**-aanroep. In plaats daarvan gebruikt het codevoorbeeld de ingebouwde **DefaultExecutionStrategy** die ook bij EF wordt geleverd. In tegenstelling tot **SqlAzureExecutionStrategy** werkt deze functie correct in combinatie met het beleid voor opnieuw proberen voor afhandeling van tijdelijke fouten. Het uitvoeringsbeleid is ingesteld in de klasse **ElasticScaleDbConfiguration**. We hebben besloten om **DefaultSqlExecutionStrategy** niet te gebruiken, omdat de functie suggereert **SqlAzureExecutionStrategy** te gebruiken als er tijdelijke uitzonderingen voorkomen. Dit zou leiden tot verkeerd gedrag, zoals eerder besproken. Zie voor meer informatie over de verschillende beleidsregels voor opnieuw proberen [Verbindingstolerantie in EF](/ef/ef6/fundamentals/connection-resiliency/retry-logic).

#### <a name="constructor-rewrites"></a>Herschrijfbewerkingen voor constructor

De bovenstaande codevoorbeelden illustreren de standaardherschrijfbewerkingen door de constructor die uw toepassing nodig heeft om gegevensafhankelijke routering met het Entity Framework te kunnen gebruiken. In de volgende tabel wordt deze aanpak naar andere constructors gegeneraliseerd.

| Huidige constructor | Herschreven constructor voor gegevens | Basisconstructor | Notities |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |De verbinding moet een functie zijn van de shard-toewijzing en de sleutel voor gegevensafhankelijke routering. U moet het automatisch maken van de verbinding door EF omzeilen en in plaats daarvan de shard-toewijzing gebruiken om de verbinding tot stand te brengen. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |De verbinding is een functie van de shard-toewijzing en de sleutel voor gegevensafhankelijke routering. Een vaste databasenaam of verbindingsreeks werkt niet, omdat hiermee validatie door de shard-toewijzing wordt omzeild. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |De verbinding wordt gemaakt voor de opgegeven shard-toewijzing en sharding-sleutel met het opgegeven model. Het samengestelde model wordt doorgegeven aan de basisconstructor. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |De verbinding moet worden afgeleid van de shard-toewijzing en de sleutel. Deze kan niet worden verstrekt als invoer (tenzij die invoer de shard-toewijzing en de sleutel al gebruikt). De booleaanse waarde wordt doorgegeven. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |De verbinding moet worden afgeleid van de shard-toewijzing en de sleutel. Deze kan niet worden verstrekt als invoer (tenzij die invoer de shard-toewijzing en de sleutel al gebruikt). Het samengestelde model wordt doorgegeven. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |De nieuwe constructor moet ervoor zorgen dat een verbinding in de ObjectContext die is door gegeven als invoer opnieuw wordt gerouteerd naar een verbinding die door Elastisch schalen wordt beheerd. Een gedetailleerde bespreking van ObjectContexts valt buiten het bereik van dit document. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |De verbinding moet worden afgeleid van de shard-toewijzing en de sleutel. De verbinding kan niet als invoer worden geleverd (tenzij die invoer al gebruikmaakt van de shard-toewijzing en de sleutel). Model en booleaanse waarde worden doorgegeven aan de basisklasseconstructor. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implementatie van shard-schema via EF-migraties

Automatisch schemabeheer is een handige functie van Entity Framework. In de context van toepassingen die gebruikmaken van hulpprogramma's voor elastische databases,is het verstandig deze mogelijkheid te behouden om het schema automatisch in te richten op nieuwe shards wanneer databases worden toegevoegd aan de shard-toepassing. Het primaire gebruiksscenario is het verhogen van de capaciteit op de gegevenslaag voor shard-toepassingen met behulp van EF. Door de mogelijkheden van EF te gebruiken voor het beheer van schema's, vermindert u de inspanning voor het beheer van de database met een shard-toepassing die op EF is gebouwd.

Schema-implementatie met behulp van EF-migraties werkt het beste op **ongeopende verbindingen**. Dit in tegenstelling tot het scenario voor gegevensafhankelijke routering dat is gebaseerd op de geopende verbinding die wordt geleverd door de client-API van de elastische database. Een ander verschil is de consistentievereiste: Hoewel het wenselijk is om te zorgen voor consistentie voor alle gegevensafhankelijke routeringsverbindingen ter bescherming tegen gelijktijdige manipulatie van de shard-toewijzing, is het geen punt van zorg bij de initiële implementatie van het schema in een nieuwe database die nog niet is geregistreerd in de shard-toewijzing en nog niet is toegewezen om shardlets te behouden. U kunt voor dit scenario dus vertrouwen op regelmatige databaseverbindingen, in tegenstelling tot gegevensafhankelijke routering.  

Dit leidt tot een aanpak waarbij de implementatie van schema's met behulp van EF-migraties strak wordt gekoppeld aan de registratie van de nieuwe database als shard in de shard-toewijzing van de toepassing. Dit is afhankelijk van de volgende voorwaarden:

* De database is al gemaakt.
* De database is leeg: deze bevat geen gebruikersschema en geen gebruikersgegevens.
* De database is nog niet toegankelijk via de client-API's van de elastische database voor gegevensafhankelijke routering.

Met deze voorwaarden kunt u een regelmatige ongeopende **SqlConnection** maken om EF-migraties te starten voor schema-implementatie. Het volgende codevoorbeeld illustreert deze aanpak.

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema

public void RegisterNewShard(string server, string database, string connStr, int key)
{

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database));

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr);
    connStrBldr.DataSource = server;
    connStrBldr.InitialCatalog = database;

    // Go into a DbContext to trigger migrations and schema deployment for the new shard.
    // This requires an un-opened connection.
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString))
    {
        // Run a query to engage EF migrations
        (from b in db.Blogs
            select b).Count();
    }

    // Register the mapping of the tenant to the shard in the shard map.
    // After this step, data-dependent routing on the shard map can be used

    this.ShardMap.CreatePointMapping(key, shard);
}
```

Dit voorbeeld toont de methode **RegisterNewShard** die de shard registreert in de shard-toewijzing, het schema implementeert met behulp van EF-migraties en een toewijzing van een sharding-sleutel op de shard opslaat. Het is gebaseerd op een constructor van de **DbContext**-subklasse (**ElasticScaleContext** in het voorbeeld) die een SQL-verbindingsreeks als invoer neemt. De code van deze constructor is eenvoudig, zoals het volgende voorbeeld laat zien:

```csharp
// C'tor to deploy schema and migrations to a new shard
protected internal ElasticScaleContext(string connectionString)
    : base(SetInitializerForConnection(connectionString))
{
}

// Only static methods are allowed in calls into base class c'tors
private static string SetInitializerForConnection(string connectionString)
{
    // You want existence checks so that the schema can get deployed
    Database.SetInitializer<ElasticScaleContext<T>>(
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>());

    return connectionString;
}
```

De versie van de constructor die is overgenomen van de basisklasse kan zijn gebruikt. Maar de code moet ervoor zorgen dat de standaardinitializer voor EF wordt gebruikt bij het verbinden. Vandaar de korte omleiding in de statische methode voor het aanroepen van de basisklasseconstructor met de verbindingsreeks. De registratie van shards moet in een ander app-domein of -proces worden uitgevoerd om ervoor te zorgen dat de initializer-instellingen voor EF niet tegenstrijdig zijn.

## <a name="limitations"></a>Beperkingen

De in dit document beschreven methoden brengen een aantal beperkingen met zich mee:

* EF-toepassingen die **LocalDb** gebruiken, moeten eerst migreren naar een gewone SQL Server-database voordat ze de clientbibliotheek van de elastische database kunnen gebruiken. Het uitschalen van een toepassing door middel van sharding met elastische schaalaanpassing is niet mogelijk met **LocalDb**. Ontwikkelaars kunnen nog steeds **LocalDb** gebruiken.
* Eventuele wijzigingen in de toepassing die wijzigingen in het databaseschema met zich meebrengen, moeten EF-migratie op alle shards ondergaan. De voorbeeldcode voor dit document toont niet aan hoe dit moet worden gedaan. Overweeg om Update-Database met een ConnectionString-parameter op alle shards te herhalen. U kunt ook het T-SQL-script voor de migratie in behandeling extraheren met behulp van Update-Database met de optie -Script en het T-SQL-script op uw shards toepassen.  
* Bij een aanvraag wordt ervan uitgegaan dat de volledige databaseverwerking in één shard is opgenomen, zoals die wordt geïdentificeerd door de in de aanvraag verstrekte sharding-sleutel. Deze veronderstelling gaat echter niet altijd op. Bijvoorbeeld wanneer het niet mogelijk is om een sharding-sleutel beschikbaar te maken. De clientbibliotheek biedt hiertoe de **MultiShardQuery**-klasse die een verbindingsabstractie implementeert voor het uitvoeren van query's op meerdere shards. Het leren gebruiken van de **MultiShardQuery** in combinatie met EF valt buiten het bereik van dit document

## <a name="conclusion"></a>Conclusie

Door middel van de stappen die in dit document worden beschreven, kunnen EF-toepassingen gebruikmaken van de mogelijkheden van de clientbibliotheek van de elastische database voor gegevensafhankelijke routering door constructors van de **DbContext**-subklassen die in de EF-toepassingen worden gebruikt, te herstructureren. Dit beperkt de vereiste veranderingen tot de plaatsen waar **DbContext**-klassen al bestaan. Daarnaast kunnen EF-toepassingen blijven profiteren van automatische implementatie van schema's door de stappen die de noodzakelijke EF-migraties aanroepen, te combineren met de registratie van nieuwe shards en toewijzingen in de shard-toewijzing.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png