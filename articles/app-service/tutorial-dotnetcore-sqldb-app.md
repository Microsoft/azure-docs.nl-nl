---
title: 'Zelfstudie: ASP.NET Core met Azure SQL Database'
description: Leer hoe u een .NET Core-app kunt laten werken in Azure App Service via een met verbinding met Azure SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 52a5b127312ef979791d17b27ca67b21a779e310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765724"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>Zelfstudie: een ASP.NET Core- en Azure SQL Database-app bouwen in Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie in Azure. In deze zelfstudie leert u hoe u een .NET Core-app maakt en hoe u deze verbindt met SQL Database. Als u klaar bent, hebt u een .NET Core MVC-app die onder Windows in App Service wordt uitgevoerd.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie via het Linux-besturingssysteem. In deze zelfstudie leert u hoe u een .NET Core-app maakt en hoe u deze verbindt met een SQL-database. Als u klaar bent, hebt u een .NET Core MVC-app die onder Linux in App Service wordt uitgevoerd.

::: zone-end

![app die in App Service wordt uitgevoerd](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een SQL-database in Azure maken
> * Een .NET Core-app verbinden met SQL Database
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken met diagnostische gegevens vanaf Azure streamen
> * De app in Azure Portal beheren

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- <a href="https://git-scm.com/" target="_blank">Git installeren</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">De nieuwste versie van .NET Core 3.1 SDK installeren</a>

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Lokale .NET Core-app maken

In deze stap stelt u het lokale .NET Core-project in.

### <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer in het terminalvenster de opdracht `cd` naar een werkmap uit.

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen en de hoofdmap ervan te wijzigen.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Het voorbeeldproject bevat een eenvoudige CRUD-app (create-read-update-delete) die gebruikmaakt van [Entity Framework Core](/ef/core/).

### <a name="run-the-application"></a>De toepassing uitvoeren

Voer de volgende opdrachten uit om de vereiste pakketten te installeren, databasemigraties uit te voeren en de toepassing te starten.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Ga naar `http://localhost:5000` in een browser. Selecteer de koppeling **Nieuwe maken** en maak enkele _taakitems_.

![is verbonden met SQL Database](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Als u .NET Core wilt stoppen, drukt u in de terminal op `Ctrl+C`.

## <a name="create-production-sql-database"></a>SQL-productiedatabase maken

In deze stap maakt u een SQL-database in Azure. Als de app is geïmplementeerd in Azure, wordt deze cloud-database gebruikt.

Voor SQL Database wordt in deze zelfstudie gebruikgemaakt van [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Een logische SQL Database-server maken

Maak een logische Azure SQL Database-server in Cloud Shell met de opdracht [`az sql server create`](/cli/azure/sql/server#az_sql_server_create).

Vervang de tijdelijke aanduiding *\<server-name>* door een *unieke* SQL Database-naam. Deze naam wordt gebruikt als onderdeel van het globaal unieke SQL Database eindpunt `<server-name>.database.windows.net`. Geldige tekens zijn `a`-`z`, `0`-`9`, `-`. Vervang tevens *\<db-username>* en *\<db-password>* door een zelfgekozen gebruikersnaam en wachtwoord. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Wanneer de logische SQL Database-server wordt gemaakt, toont de Azure CLI informatie die lijkt op het volgende voorbeeld:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Een serverfirewallregel configureren

Maak een [firewallregel op Azure SQL Database-serverniveau](../azure-sql/database/firewall-configure.md) met de opdracht [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create). Als zowel het IP-beginadres als het IP-eindadres zijn ingesteld op 0.0.0.0, wordt de firewall alleen geopend voor andere Azure-resources. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> U kunt uw firewallregel nog beperkender maken door [alleen de uitgaande IP-adressen te gebruiken die in uw app worden gebruikt](overview-inbound-outbound-ips.md#find-outbound-ips).
>

Voer in Cloud Shell de opdracht opnieuw uit om toegang vanaf uw lokale computer mogelijk te maken door *\<your-ip-address>* te vervangen door [uw lokale IPv4 IP-adres](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Een database maken

Maak een database met een [prestatieniveau van S0](../azure-sql/database/service-tiers-dtu.md) op de server met de opdracht [`az sql db create`](/cli/azure/sql/db#az_sql_db_create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Verbindingsreeks maken

Haal de verbindingsreeks op met behulp van de opdracht [`az sql db show-connection-string`](/cli/azure/sql/db#az_sql_db_show_connection_string).

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

Vervang in de uitvoer van de opdracht *\<username>* en *\<password>* door de databasebeheerdersreferenties die u eerder hebt gebruikt.

Dit is de verbindingsreeks voor uw app .NET Core-app. Kopieer deze voor later gebruik.

### <a name="configure-app-to-connect-to-production-database"></a>De app configureren om verbinding te maken met de productiedatabase

Open Startup.cs in de lokale opslagplaats en zoek de volgende code:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Vervang deze door de volgende code.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> Voor productie-apps die u wilt uitbreiden, volgt u de best practices in [toepassen van migraties in productie](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

### <a name="run-database-migrations-to-the-production-database"></a>Databasemigraties naar de productiedatabase uitvoeren

Uw app maakt momenteel verbinding met een lokale Sqlite-database. Nu u een Azure SQL Database hebt geconfigureerd, maakt u de eerste migratie opnieuw om deze als doel te gebruiken. 

Voer vanuit de hoofdmap van de opslagplaats de volgende opdrachten uit. Vervang *\<connection-string>* met de verbindingsreeks die u eerder hebt gemaakt.

```
# Delete old migrations
rm -r Migrations
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>De app uitvoeren met de nieuwe configuratie

Nu de databasemigraties worden uitgevoerd op de productiedatabase, test u uw app door deze opdracht uit te voeren:

```
dotnet run
```

Ga naar `http://localhost:5000` in een browser. Selecteer de koppeling **Nieuwe maken** en maak enkele _taakitems_. Uw app leest en schrijft nu gegevens van en naar de productiedatabase.

Voer uw lokale wijzigingen door en voer de app door naar de Git-opslagplaats. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

U bent nu klaar om uw code te implementeren.

## <a name="deploy-app-to-azure"></a>App in Azure implementeren

In deze stap implementeert u de met SQL Database verbonden .NET Core-app met App Service.

### <a name="configure-local-git-deployment"></a>Lokale Git-implementatie configureren

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Een webtoepassing maken

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>Verbindingsreeks configureren

Als u verbindingsreeksen voor de Azure-app wilt instellen, gebruikt u de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) in Cloud Shell. In de volgende opdracht vervangt u *\<app-name>* en de parameter *\<connection-string>* door de eerder gemaakte verbindingsreeks.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

In ASP.NET Core kunt u deze benoemde verbindingsreeks (`MyDbConnection`) gebruiken met het standaardpatroon, zoals elke verbindingsreeks die wordt opgegeven in *appsettings.json*. In dit geval wordt `MyDbConnection` ook gedefinieerd in uw *appsettings.json*. Bij het uitvoeren in App Service heeft de in App Service gedefinieerde verbindingsreeks voorrang op de verbindingsreeks die wordt gedefinieerd in uw *appsettings.json*. De code gebruikt de waarde uit *appsettings.json* tijdens de lokale ontwikkeling, en dezelfde code gebruikt de waarde van App Service wanneer deze is geïmplementeerd.

Zie [De app configureren om verbinding te maken met de productiedatabase](#configure-app-to-connect-to-production-database) als u wilt zien hoe in uw code naar de verbindingsreeks wordt verwezen.

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader in de webbrowser naar de geïmplementeerde app.

```bash
http://<app-name>.azurewebsites.net
```

Voeg enkele taakitems toe.

![app die in App Service wordt uitgevoerd](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Gefeliciteerd!** U voert een gegevensgestuurde .NET Core-app uit in App Service.

## <a name="update-locally-and-redeploy"></a>Lokaal bijwerken en opnieuw implementeren

In deze stap wijzigt u het databaseschema en publiceert u het in Azure.

### <a name="update-your-data-model"></a>Gegevensmodel bijwerken

Open _Models/Todo.cs_ in the code-editor. Voeg de volgende eigenschap toe aan de klasse `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Databasemigraties opnieuw uitvoeren

Voer enkele opdrachten uit om de productiedatabase bij te werken.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Als u een nieuw terminalvenster opent, moet u de verbindingsreeks in de terminal instellen op de productiedatabase, zoals u hebt gedaan in [Databasemigraties naar de productiedatabase uitvoeren](#run-database-migrations-to-the-production-database).
>

### <a name="use-the-new-property"></a>Nieuwe eigenschap gebruiken

Breng enkele wijzigingen aan de code aan zodat de eigenschap `Done` kan worden gebruikt. Om deze zelfstudie eenvoudig te houden, wijzigt u eerst de weergaven `Index` en `Create` om de eigenschap in actie te zien.

Open _Controllers/TodosController.cs_.

Zoek de methode `Create([Bind("ID,Description,CreatedDate")] Todo todo)` en voeg `Done` toe aan de lijst met eigenschappen in het attribuut `Bind`. Als u klaar bent, ziet uw methode `Create()` er uit als de onderstaande code:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Open _Views/Todos/Create.cshtml_.

In de Razor-code zou u een `<div class="form-group">`-element voor `Description` moeten zien en vervolgens een ander element `<div class="form-group">` voor `CreatedDate`. Voeg direct na deze twee elementen een ander element `<div class="form-group">` voor `Done` toe:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Open _Views/Todos/Index.cshtml_.

Zoek het lege element `<th></th>`. Vlak boven dit element voegt u de volgende Razor-code toe:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Zoek het element `<td>` dat de taghelpers voor `asp-action` bevat. Vlak boven dit element voegt u de volgende Razor-code toe:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Meer hebt u niet nodig om de wijzigingen in de weergaven `Index` en `Create` te zien.

### <a name="test-your-changes-locally"></a>De wijzigingen lokaal testen

Voer de app lokaal uit.

```bash
dotnet run
```

> [!NOTE]
> Als u een nieuw terminalvenster opent, moet u de verbindingsreeks in de terminal instellen op de productiedatabase, zoals u hebt gedaan in [Databasemigraties naar de productiedatabase uitvoeren](#run-database-migrations-to-the-production-database).
>

Ga in de browser naar `http://localhost:5000/`. U kunt nu een taakitem toevoegen en **Gereed** aanvinken. Het moet nu als een voltooid taakitem worden weergegeven op uw startpagina. In de weergave `Edit` wordt het veld `Done` niet getoond omdat u de weergave `Edit` niet hebt gewijzigd.

### <a name="publish-changes-to-azure"></a>Wijzigingen publiceren in Azure

```bash
git add .
git commit -m "added done field"
git push azure main
```

Nadat `git push` is voltooid, gaat u naar de App Service-app en probeert u een taakitem toe te voegen en selecteert u **Gereed**.

![Azure-app na Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Alle bestaande taakitems worden nog steeds weergegeven. Als u de ASP.NET Core-app opnieuw publiceert, blijven bestaande gegevens in SQL Database behouden. En met Entity Framework Core Migrations wordt alleen het gegevensschema gewijzigd. De bestaande gegevens blijven ongewijzigd.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

Terwijl uw ASP.NET Core-app wordt uitgevoerd in Azure App Service, kunt u de consolelogboeken doorsluizen naar de Cloud Shell. Op die manier krijgt u de dezelfde diagnostische berichten om toepassingsfouten op te sporen.

Het voorbeeldproject volgt al de instructies in [ASP.NET Core-logboekregistratie in Azure](/aspnet/core/fundamentals/logging#azure-app-service-provider) met twee configuratiewijzigingen:

- Bevat een verwijzing naar `Microsoft.Extensions.Logging.AzureAppServices` in *DotNetCoreSqlDb.csproj*.
- Roept `loggerFactory.AddAzureWebAppDiagnostics()` in *Program.cs* aan.

Om het [logboekniveau](/aspnet/core/fundamentals/logging#log-level) van ASP.NET Core in App Service te wijzigen van het standaardniveau `Error` in `Information`, gebruikt u de opdracht [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) in de Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging filesystem --level information
```

> [!NOTE]
> Het logboekniveau van het project is al ingesteld op `Information` in *appsettings.json*.

Gebruik voor het starten van logboekstreaming de opdracht [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) in de Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Nadat logboekstreaming is gestart, vernieuwt u de Azure-app in de browser om wat webverkeer te genereren. U kunt nu zien dat consolelogboeken worden doorgegeven aan de terminal. Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

U kunt op elk gewenst moment `Ctrl`+`C` typen om te stoppen met logboekstreaming.

Zie [Logboekregistratie in ASP.NET Core](/aspnet/core/fundamentals/logging) voor meer informatie over het aanpassen van de ASP.NET Core-logboeken.

## <a name="manage-your-azure-app"></a>Uw Azure-app beheren

Als u de app wilt zien die u hebt gemaakt, zoekt u in de [Azure-portal](https://portal.azure.com) naar **App Services** en selecteert u dit.

![App Services selecteren in de Azure-portal](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

Selecteer op de pagina **App Services** de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![App Service-pagina in Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Een SQL-database in Azure maken
> * Een .NET Core-app verbinden met SQL Database
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken vanaf Azure naar uw terminal streamen
> * De app in Azure Portal beheren

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepaste DNS-naam aan uw app toewijzen](app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [ASP.NET Core-app configureren](configure-language-dotnetcore.md)
