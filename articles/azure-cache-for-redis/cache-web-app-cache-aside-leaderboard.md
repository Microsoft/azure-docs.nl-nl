---
title: 'Zelfstudie: Een web-app maken (cache-aside): Azure Cache voor Redis'
description: Informatie over het maken van een web-app met Azure Cache voor Redis die gebruikmaakt van het cache-aside-patroon.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 90e60044e227ea1a18ea032d302b29abda1ea2e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536841"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Zelfstudie: een cache-aside-leaderboard maken in ASP.NET

In deze zelfstudie werkt u de ASP.NET-web-app *ContosoTeamStats* bij die is gemaakt in de [Quickstart van ASP.NET voor Azure Cache voor Redis](cache-web-app-howto.md), om een leaderboard op te nemen waarin het [cache-aside-patroon](/azure/architecture/patterns/cache-aside) wordt gebruikt met Azure Cache voor Redis. In de voorbeeldtoepassing wordt een lijst met teamstatistieken uit een database weergegeven en ziet u verschillende manieren waarop u Azure Cache voor Redis kunt gebruiken om gegevens op te slaan in en op te halen uit de cache om de prestaties te verbeteren. Wanneer u de zelfstudie hebt voltooid, hebt u een actieve web-app die naar een database leest en schrijft. Deze web-app is geoptimaliseerd met Azure Cache voor Redis en wordt gehost in Azure.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De doorvoer van gegevens verbeteren en de belasting van de database verminderen door gegevens op te slaan en op te halen met Azure Cache voor Redis.
> * Een in Redis gesorteerde set gebruiken om de beste vijf teams op te halen.
> * De Azure-resources voor de toepassing inrichten met een Resource Manager-sjabloon.
> * De toepassing publiceren in Azure met Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

* Deze zelfstudie gaat verder waar u was gebleven in [Quickstart van ASP.NET voor Azure Cache voor Redis](cache-web-app-howto.md). Als u dat nog niet hebt gedaan, volgt u eerst de snelstartgids.
* Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de volgende workloads:
    * ASP.NET-ontwikkeling en webontwikkeling
    * Azure-ontwikkeling
    * .NET-desktopontwikkeling met SQL Server Express LocalDB of [SQL Server 2017 Express-editie](https://www.microsoft.com/sql-server/sql-server-editions-express).

## <a name="add-a-leaderboard-to-the-project"></a>Een leaderboard aan het project toevoegen

In deze sectie van de zelfstudie configureert u het project *ContosoTeamStats* met een leaderboard waarin de winst-, verlies- en gelijkspelstatistieken worden aangegeven voor een lijst met fictieve teams.

### <a name="add-the-entity-framework-to-the-project"></a>Het Entity Framework toevoegen aan het project

1. Open in Visual Studio de oplossing *ContosoTeamStats* die u hebt gemaakt in de [Quickstart van ASP.NET voor Azure Cache voor Redis](cache-web-app-howto.md).
2. Klik op **Tools > NuGet Package Manager > Package Manager Console**.
3. Voer de volgende opdracht uit in het venster **Package Manager Console** om EntityFramework te installeren:

    ```powershell
    Install-Package EntityFramework
    ```

Meer informatie over dit pakket vindt u op de NuGet-pagina [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-team-model"></a>Het teammodel toevoegen

1. Klik in **Solution Explorer** met de rechtermuisknop op **Models** en kies **Add**, **Class**.

1. Voer `Team` in als de naam van de klasse en klik op **Add**.

    ![De modelklasse toevoegen](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Vervang de `using`-instructies boven aan het bestand *Team.cs* door de volgende `using`-instructies:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Vervang de definitie van klasse `Team` door het volgende codefragment. Dit fragment bevat een bijgewerkte definitie voor klasse `Team` en een aantal andere helperklassen van Entity Framework. In deze zelfstudie wordt de methode 'code eerst' gebruikt met Entity Framework. Met deze methode kan Entity Framework de database maken op basis van uw code. Zie [Code First naar een nieuwe database](/ef/ef6/modeling/code-first/workflows/new-database) voor meer informatie over de Code First-aanpak voor Entity Framework die in deze zelfstudie wordt gebruikt.

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. Dubbelklik in **Solution Explorer** op **web.config** om het bestand te openen.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Voeg de volgende sectie `connectionStrings` toe aan de sectie `configuration`. De naam van de verbindingsreeks moet overeenkomen met de naam van de contextklasse van de Entity Framework-database. Dit is `TeamContext`.

    Bij deze verbindingsreeks wordt ervan uitgegaan dat u hebt voldaan aan de [Vereisten](#prerequisites) en SQL Server Express LocalDB hebt geïnstalleerd, dat deel uitmaakt van de werkbelasting *.NET-desktopontwikkeling* die wordt geïnstalleerd met Visual Studio 2019.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    In het volgende voorbeeld ziet u de nieuwe sectie `connectionStrings` na `configSections` in de sectie `configuration`:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>De TeamsController en weergaven toevoegen

1. Maak het project in Visual Studio. 

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Controllers** en kies vervolgens **Add**, **Controller**.

1. Kies **MVC 5 Controller with views, using Entity Framework** en klik op **Add**. Als er een foutbericht wordt weergegeven nadat u op **Add** hebt geklikt, zorgt u ervoor dat u eerst het project bouwt.

    ![Controllerklasse toevoegen](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Selecteer in de vervolgkeuzelijst **Model class** de optie **Team (ContosoTeamStats.Models)** . Selecteer in de vervolgkeuzelijst **Data context class** de optie **TeamContext (ContosoTeamStats.Models)** . Typ `TeamsController` in het tekstvak voor de naam van de **Controller** (als dit niet automatisch wordt ingevuld). Klik op **Add** om de controllerklasse te maken en de standaardweergaven toe te voegen.

    ![Controller configureren](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. Vouw in **Solution Explorer** het bestand **Global.asax** uit en dubbelklik op **Global.asax.cs** om het te openen.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Voeg boven aan het bestand, onder de andere `using`-instructies, de volgende twee `using`-instructies toe:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Voeg de volgende regel code toe aan het einde van de `Application_Start`-methode:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. Vouw in **Solution Explorer**`App_Start` uit en dubbelklik op `RouteConfig.cs`.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. Vervang `controller = "Home"` in de route `Default` in de `RegisterRoutes`-methode door `controller = "Teams"`, zoals wordt weergegeven in het volgende voorbeeld:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>De indelingsweergave configuren

1. Vouw in **Solution Explorer** de map **Views** uit. Vouw vervolgens de map **Shared** uit en dubbelklik op **_Layout.cshtml**. 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Wijzig de inhoud van het element `title` en vervang `My ASP.NET Application` door `Contoso Team Stats`, zoals weergegeven in het volgende voorbeeld:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. Voeg in de sectie `body` de volgende nieuwe `Html.ActionLink`-instructie toe voor *Contoso Team Stats* net onder de koppeling voor *Azure Cache voor Redis-test*.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Codewijzigingen](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Druk op **Ctrl+F5** om de toepassing op te bouwen en uit te voeren. Deze versie van de toepassing leest de resultaten rechtstreeks uit de database. De acties **Nieuw**, **Bewerken**, **Details** en **Verwijderen** zijn automatisch aan de toepassing toegevoegd door de structuur **MVC 5 Controller with views, using Entity Framework**. In het volgende gedeelte van de zelfstudie voegt u Azure Cache voor Redis toe om de gegevenstoegang te optimaliseren en om extra functies te bieden voor de toepassing.

    ![Beginnerstoepassing](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>De app voor Azure Cache voor Redis configureren

In dit gedeelte van de zelfstudie configureert u de voorbeeldtoepassing om met behulp van de cacheclient [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) Contoso-teamstatistieken op te slaan en op te halen uit een exemplaar van Azure Cache voor Redis.

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Een cacheverbinding toevoegen aan de Teams-controller

U hebt het clientbibliotheekpakket *StackExchange.Redis* al geïnstalleerd in de snelstartgids. U hebt de app-instelling *CacheConnection* ook al zo geconfigureerd dat deze lokaal wordt gebruikt en met de gepubliceerde App Service. Gebruik deze zelfde clientbibliotheek en de *CacheConnection*-informatie in de *TeamsController*.

1. Vouw in **Solution Explorer** de map **Controllers** uit en dubbelklik op **TeamsController.cs** om dit bestand te openen.

    ![TeamsController](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Voeg de volgende twee `using`-instructies toe aan **TeamsController.cs**:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Voeg de volgende twee eigenschappen toe aan de klasse `TeamsController`:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>De TeamsController zo bijwerken dat hiermee wordt gelezen uit de cache of de database

In dit voorbeeld worden teamstatistieken opgehaald uit de database of uit de cache. Teamstatistieken worden opgeslagen in de cache als een geserialiseerde `List<Team>` en ook als een gesorteerde set met Redis-gegevenstypen. Bij het ophalen van items uit een gesorteerde set kunt u een query uitvoeren voor sommige items, voor alle items of alleen voor bepaalde items. In dit voorbeeld voert u een query uit voor de gesorteerde set voor de beste vijf teams, op basis van het aantal overwinningen.

Het is niet vereist om de teamstatistieken in verschillende indelingen in de cache op te slaan om Azure Cache voor Redis te kunnen gebruiken. In deze zelfstudie wordt gebruikgemaakt van meerdere indelingen ter illustratie van de verschillende manieren en gegevenstypen die u kunt gebruiken om gegevens in de cache op te slaan.

1. Voeg bovenaan, bij de andere `using`-instructies, de volgende `using`-instructies toe aan het `TeamsController.cs`-bestand:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Vervang de huidige implementatie van de `public ActionResult Index()`-methode door de volgende implementatie:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Voeg de volgende drie methoden voor de klasse `TeamsController` toe om de actietypen `playGames`, `clearCache` en `rebuildDB` te implementeren uit de switch-instructie die in het vorige codefragment is toegevoegd.

    De `PlayGames`-methode werkt de teamstatistieken bij door een seizoen aan wedstrijden te simuleren, slaat de resultaten vervolgens op in de database en wist de nu verouderde gegevens uit de cache.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    De `RebuildDB`-methode initialiseert de database opnieuw met de standaardset teams, genereert statistieken voor ze en wist de nu verouderde gegevens uit de cache.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    De `ClearCachedTeams`-methode verwijdert opgeslagen teamstatistieken uit de cache.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Voeg de volgende vier methoden toe aan klasse `TeamsController` om de verschillende manieren voor het ophalen van teamstatistieken uit de cache en de database te implementeren. Alle methoden retourneren een `List<Team>`, die vervolgens wordt weergegeven in de weergave.

    De `GetFromDB`-methode leest de teamstatistieken uit de database.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    De `GetFromList`-methode leest de teamstatistieken uit de cache als een geserialiseerde `List<Team>`. Als de statistieken niet aanwezig zijn in de cache, treedt er een cachemisser op. Bij een cachemisser worden de teamstatistieken gelezen uit de database en vervolgens voor de volgende aanvraag opgeslagen in de cache. In dit voorbeeld wordt gebruikgemaakt van JSON.NET-serialisatie om de .NET-objecten naar en uit de cache te serialiseren. Zie [Werken met .NET-objecten in Azure Cache voor Redis](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) voor meer informatie.

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    De `GetFromSortedSet`-methode leest de teamstatistieken uit een in de cache opgeslagen gesorteerde set. Als er een cache ontbreekt, worden de teamstatistieken uit de database gelezen en als een gesorteerde set opgeslagen in de cache.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    Met de `GetFromSortedSetTop5`-methode worden de beste vijf teams gelezen uit de in de cache opgeslagen, gesorteerde set. Deze methode begint met het controleren van de cache op de aanwezigheid van de `teamsSortedSet`-sleutel. Als deze sleutel niet aanwezig is, wordt de `GetFromSortedSet`-methode aangeroepen om de teamstatistieken te lezen en op te slaan in de cache. Vervolgens wordt een query voor de beste vijf teams uitgevoerd op de in de cache opgeslagen, gesorteerde set, die worden geretourneerd.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>De methoden Maken, Bewerken en Verwijderen bijwerken voor gebruik met de cache

De ondersteuningscode die als onderdeel van dit voorbeeld is gegenereerd, bevat de methoden om teams toe te voegen, te bewerken en te verwijderen. Telkens wanneer er een team wordt toegevoegd, bewerkt of verwijderd, raken de gegevens in de cache verouderd. In deze sectie bewerkt u deze drie methoden, zodat de in de cache opgeslagen teams worden gewist en de cache wordt vernieuwd.

1. Blader naar de `Create(Team team)`-methode in de klasse `TeamsController`. Voeg een aanroep toe aan de `ClearCachedTeams`-methode, zoals wordt weergegeven in het volgende voorbeeld:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Blader naar de `Edit(Team team)`-methode in de klasse `TeamsController`. Voeg een aanroep toe aan de `ClearCachedTeams`-methode, zoals wordt weergegeven in het volgende voorbeeld:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Blader naar de `DeleteConfirmed(int id)`-methode in de klasse `TeamsController`. Voeg een aanroep toe aan de `ClearCachedTeams`-methode, zoals wordt weergegeven in het volgende voorbeeld:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Cachemethoden toevoegen aan de weergave Teamindex

1. Vouw in **Solution Explorer** de map **Views** uit. Vouw vervolgens de map **Teams** uit en dubbelklik op **Index.cshtml**.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. Zoek boven in het bestand naar het volgende alinea-element:

    ![Actietabel](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Met deze koppeling wordt een nieuw team gemaakt. Vervang het alinea-element door de volgende tabel. Deze tabel bevat actiekoppelingen waarmee u een nieuw team kunt maken, een nieuw seizoen wedstrijden kunt spelen, de cache kunt wissen, de teams in verschillende indelingen kunt ophalen uit de cache, de teams kunt ophalen uit de database en de database opnieuw kunt opbouwen met de nieuwe voorbeeldgegevens.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Ga naar de onderkant van het bestand **Index.cshtml** en voeg het volgende `tr`-element toe, zodat dit de laatste rij in de laatste tabel in het bestand is:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Deze rij toont de waarde van `ViewBag.Msg`, die een statusrapport over de huidige bewerking bevat. De `ViewBag.Msg` wordt ingesteld wanneer u op een van de actiekoppelingen uit de vorige stap klikt.

    ![Statusbericht](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Druk op **F6** om het project te bouwen.

## <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Voer de toepassing lokaal uit op uw computer om de functionaliteit te testen die is toegevoegd om de teams te ondersteunen.

In deze test worden de toepassing en de database beide lokaal uitgevoerd. De Azure Cache voor Redis wordt echter extern gehost in Azure. Daarom presteert de cache waarschijnlijk iets slechter dan de database. Voor optimale prestaties moeten de clienttoepassing en het exemplaar van Azure Cache voor Redis zich op dezelfde locatie bevinden. In de volgende sectie implementeert u alle resources in Azure om de verbeterde prestaties vanwege het gebruik van een cache te bekijken.

De app lokaal uitvoeren:

1. Druk op **Ctrl+F5** om de toepassing uit te voeren.

    ![App die lokaal wordt uitgevoerd](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Test alle nieuwe methoden die aan de weergave zijn toegevoegd. Omdat de cache in deze tests extern is, moet de database iets beter presteren dan de cache.

## <a name="publish-and-run-in-azure"></a>Publiceren en uitvoeren in Azure

### <a name="provision-a-database-for-the-app"></a>Een database inrichten voor de app

In deze sectie richt u een nieuwe database in SQL Database in die de app gaat gebruiken terwijl deze wordt gehost in Azure.

1. Klik linksboven in [Azure Portal](https://portal.azure.com/) op **Een resource maken**.

1. Klik op de pagina **Nieuw** op **Databases** > **SQL-database**.

1. Gebruik de volgende instellingen voor de nieuwe SQL-database:

   | Instelling       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Databasenaam** | *ContosoTeamsDatabase* | Zie [Database-id's](/sql/relational-databases/databases/database-identifiers) voor geldige databasenamen. |
   | **Abonnement** | *Uw abonnement*  | Selecteer hetzelfde abonnement dat u gebruikt om de cache te maken en de App Service te hosten. |
   | **Resourcegroep**  | *TestResourceGroup* | Klik op **Bestaande gebruiken** en gebruik dezelfde resourcegroep als waarin u de cache en App Service hebt geplaatst. |
   | **Bron selecteren** | **Lege database** | Begin met een lege database. |

1. Klik onder **Server** op **Vereiste instellingen configureren** > **Een nieuwe server maken**, geef de volgende gegevens op en klik op de knop **Selecteren**:

   | Instelling       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servernaam** | Een wereldwijd unieke naam | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige servernamen. |
   | **Aanmeldgegevens van serverbeheerder** | Een geldige naam | Zie [Database-id's](/sql/relational-databases/databases/database-identifiers) voor geldige aanmeldingsnamen. |
   | **Wachtwoord** | Een geldig wachtwoord | Uw wachtwoord moet uit ten minste 8 tekens bestaan en moet tekens bevatten uit drie van de volgende categorieën: hoofdletters, kleine letters, cijfers en niet-alfanumerieke tekens. |
   | **Locatie** | *VS - oost* | Selecteer dezelfde regio als waarin u de cache en de App Service hebt gemaakt. |

1. Klik op **Vastmaken aan dashboard** en klik op **Maken** om de nieuwe database en server te maken.

1. Wanneer de nieuwe database is gemaakt, klikt u op **Databaseverbindingsreeksen tonen** en kopieert u de **ADO.NET**-verbindingsreeks.

    ![Verbindingsreeksen weergeven](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Navigeer in de Azure Portal naar uw App Service, klik op **Toepassingsinstellingen** en klik op **Nieuwe verbindingsreeks toevoegen** onder de sectie Verbindingsreeksen.

1. Voeg een nieuwe verbindingsreeks toe met de naam *TeamContext* die overeenkomt met de contextklasse van de Entity Framework-database. Plak de verbindingsreeks voor de nieuwe database als de waarde. Vervang de volgende tijdelijke aanduidingen in de verbindingsreeks en klik op **Opslaan**:

    | Tijdelijke aanduiding | Voorgestelde waarde |
    | --- | --- |
    | *{uw_gebruikersnaam}* | Gebruik de **aanmeldgegevens van de serverbeheerder** voor de server die u zojuist hebt gemaakt. |
    | *{uw_wachtwoord}* | Gebruik het wachtwoord voor de server die u zojuist hebt gemaakt. |

    Door de gebruikersnaam en het wachtwoord toe te voegen als een toepassingsinstelling, worden uw gebruikersnaam en wachtwoord niet opgenomen in de code. Met deze methode beschermt u deze referenties.

### <a name="publish-the-application-updates-to-azure"></a>De toepassingsupdates publiceren in Azure

In deze stap van de zelfstudie publiceert u de toepassingsupdate in Azure om deze uit te voeren in de cloud.

1. Klik in Visual Studio met de rechtermuisknop op het project **ContosoTeamStats** en kies **Publish**.

    ![Publiceren](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Klik op **Publiceren** om hetzelfde publicatieprofiel te gebruiken dat u hebt gemaakt in de snelstartgids.

3. Wanneer het publiceren is voltooid, wordt de app door Visual Studio in uw standaardwebbrowser gestart.

    ![Cache toegevoegd](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    De volgende tabel beschrijft elke actiekoppeling in de voorbeeldtoepassing:

    | Bewerking | Beschrijving |
    | --- | --- |
    | Create New |Een nieuw team maken |
    | Play Season |Speel een seizoen wedstrijden, werk de teamstatistieken bij en wis eventuele verouderde teamgegevens uit de cache. |
    | Clear Cache |Wis de teamstatistieken uit de cache. |
    | List from Cache |Haal de teamstatistieken op uit de cache. Als er een cache ontbreekt, moet u de statistieken uit de database laden en in de cache opslaan voor later gebruik. |
    | Sorted Set from Cache |Haal de teamstatistieken op uit de cache met behulp van een gesorteerde set. Als er een cache ontbreekt, moet u de statistieken uit de database laden en met behulp van een gesorteerde set opslaan in de cache. |
    | Top 5 Teams from Cache |Haal de beste vijf teams op uit de cache met behulp van een gesorteerde set. Als er een cache ontbreekt, moet u de statistieken uit de database laden en met behulp van een gesorteerde set opslaan in de cache. |
    | Load from DB |Haal de teamstatistieken op uit de database. |
    | Rebuild DB |Bouw de database opnieuw en laad deze opnieuw met voorbeeldgegevens van de teams. |
    | Edit/Details/Delete |Bewerk een team, geef details van een team weer en verwijder een team. |

Klik op een aantal acties en experimenteer met het ophalen van de gegevens vanuit de verschillende bronnen. Let op de verschillen in de tijd die nodig is om de gegevens op de diverse manieren op te halen uit de database en de cache.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de voorbeeldtoepassing uit de zelfstudie, kunt u de Azure-resources die u hebt gebruikt, verwijderen om kosten en resources te besparen. Alle resources moet zich in dezelfde resourcegroep bevinden. U kunt deze samen verwijderen in één bewerking door de resourcegroep te verwijderen. In de instructies voor dit onderwerp is een resourcegroep met de naam *TestResources* gebruikt.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via de respectievelijke blades.
>

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Resourcegroepen**.
2. Typ de naam van de resourcegroep in het tekstvak **Items filteren...** .
3. Klik op **...** rechts van de resourcegroep en klik op **Resourcegroep verwijderen**.

    ![Verwijderen](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ de naam van de resourcegroep om te bevestigen en klik op **Verwijderen**.

    Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Het schalen van Azure Cache voor Redis](./cache-how-to-scale.md)