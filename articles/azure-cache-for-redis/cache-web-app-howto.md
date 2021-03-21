---
title: Een ASP.NET-web-app maken met Azure Cache voor Redis
description: In deze snelstart leert u hoe u een ASP.NET-web-app maakt met Azure Cache voor Redis
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: yegu
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 88cfddb12de0949d56e4b8f9c3e363e4c8f75676
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657757"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Quickstart: Azure Cache voor Redis met ASP.NET-web-app gebruiken 

In deze snelstartgids gebruikt u Visual Studio 2019 om een ASP.NET-web-app die verbinding maakt met Azure Cache voor Redis om gegevens op te slaan en op te halen uit de cache. U kunt de app vervolgens in Azure App Service implementeren.

## <a name="skip-to-the-code-on-github"></a>Ga naar de code op GitHub

Als u direct naar de code wilt gaan, raadpleegt u de [ASP.net Snelstartgids](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet) op github.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de workloads **ASP.NET and web development** en **Azure development**.

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

1. Open Visual Studio en selecteer vervolgens **bestand**  >  **Nieuw**  >  **project**.

2. Voer in het dialoog venster **een nieuw project maken** de volgende stappen uit:

    ![Project maken](./media/cache-web-app-howto/cache-create-project.png)

    a. Voer in het zoekvak _C# ASP.NET-webtoepassing_ in.

    b. Selecteer **ASP.NET-webtoepassing (.NET Framework)**.

    c. Selecteer **Volgende**.

3. Geef in het vak **project naam** het project een naam. In dit voorbeeld wordt de naam **ContosoTeamStats** gebruikt.

4. Controleer of **.NET Framework 4.6.1** of hoger is geselecteerd.

5. Selecteer **Maken**.
   
6. Selecteer **MVC** als het projecttype.

7. Zorg ervoor dat **Geen verificatie** is opgegeven voor de instellingen bij **Verificatie**. Afhankelijk van uw versie van Visual Studio wijkt de standaardinstelling voor **Verificatie** mogelijk af. Als u dit wilt wijzigen, selecteert u **Verificatie wijzigen** en vervolgens **Geen verificatie**.

8. Selecteer **Maken** om het project te maken.

## <a name="create-a-cache"></a>Een cache maken

Maak vervolgens de cache voor de app.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>Het bestand *CacheSecrets.config* bewerken

1. Maak op de computer een bestand met de naam *CacheSecrets.config*. Sla dit bestand op een locatie op waar het niet wordt ingecheckt met de broncode van de voorbeeldtoepassing. Voor deze snelstart bevindt het bestand *CacheSecrets.config* zich op *C:\AppSecrets\CacheSecrets.config*.

1. Bewerk het bestand *CacheSecrets.config*. Voeg nu de volgende inhoud toe:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
    </appSettings>
    ```

1. Vervang `<cache-name>` door de hostnaam van uw cache.

1. Vervang `<access-key>` door de primaire sleutel voor uw cache.

    > [!TIP]
    > U kunt de secundaire toegangssleutel tijdens sleutelroulatie gebruiken als een alternatieve sleutel terwijl u de primaire toegangssleutel opnieuw genereert.
   >
1. Sla het bestand op.

## <a name="update-the-mvc-application"></a>De MVC-toepassing bijwerken

In deze sectie werkt u de toepassing bij voor de ondersteuning van een nieuwe weergave waarin een eenvoudige test wordt weergegeven op basis van Azure Cache voor Redis.

* [Het web.config-bestand bijwerken met een app-instelling voor de cache](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
* De toepassing configureren voor gebruik van de StackExchange.Redis-client
* De HomeController en lay-out bijwerken
* Een nieuwe RedisCache-weergave toevoegen

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Het web.config-bestand bijwerken met een app-instelling voor de cache

Als u de toepassing lokaal uitvoert, wordt de informatie in *CacheSecrets.config* gebruikt om verbinding te maken met uw instantie van Azure Cache voor Redis. Later implementeert u deze toepassing in Azure. Op dat moment configureert u een app-instelling in Azure die in de toepassing wordt gebruikt om de cacheverbindingsinformatie op te halen in plaats van dit bestand. 

Omdat het bestand *CacheSecrets.config* niet in Azure wordt geïmplementeerd met uw toepassing, gebruikt u het alleen tijdens het lokaal testen van de toepassing. Houd deze informatie zo veilig mogelijk om schadelijke toegang tot de gegevens in de cache te voorkomen.

#### <a name="to-update-the-webconfig-file"></a>Het bestand *web.config* bijwerken
1. Dubbelklik in **Solution Explorer** op het bestand *web.config* om het te openen.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. Ga in het bestand *web.config* naar het element `<appSetting>`. Voeg vervolgens het volgende `file`-kenmerk toe. Als u een andere bestandsnaam of -locatie gebruikt, vervangt u deze waarden door de waarden die in het voorbeeld worden weergegeven.

* Voor: `<appSettings>`
* Na:  `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

De ASP.NET-runtime voegt de inhoud van het externe bestand samen met de opmaak van het element `<appSettings>`. Als het opgegeven bestand niet kan worden gevonden, negeert de runtime het bestandskenmerk. Uw geheimen (de verbindingsreeks naar uw cache) worden niet opgenomen in de broncode van de toepassing. Wanneer u de web-app implementeert in Azure, wordt het bestand *CacheSecrets.config* niet geïmplementeerd.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>De toepassing configureren voor het gebruik van StackExchange.Redis

1. Als u de app wilt configureren voor gebruik van het NuGet-pakket [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) voor Visual Studio, selecteert u **Tools > NuGet Package Manager > Package Manager Console**.

2. Voer de volgende opdracht uit vanuit het venster `Package Manager Console`:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. Met het NuGet-pakket downloadt u de vereiste assembly-verwijzingen voor de clienttoepassing en voegt u deze toe om met de StackExchange.Azure Cache voor Redis-client toegang te krijgen tot Azure Cache voor Redis. Als u een versie met een sterke naam van de `StackExchange.Redis`-clientbibliotheek verkiest, installeert u het `StackExchange.Redis.StrongName`-pakket.

### <a name="to-update-the-homecontroller-and-layout"></a>De HomeController en lay-out bijwerken

1. Vouw in **Solution Explorer** de map **Controllers** uit en open vervolgens het bestand *HomeController.cs*.

2. Voeg `using` boven aan het bestand de volgende instructies toe om de cache-client, app-instellingen en opbouw functie voor teken reeksen te ondersteunen.

    ```csharp
    using System.Configuration;
    using System.Text;
    using StackExchange.Redis;
    ```

3. Voeg de volgende methode voor de `HomeController`-klasse toe ter ondersteuning van een nieuwe `RedisCache`-actie waarmee bepaalde opdrachten voor de nieuwe cache worden uitgevoerd.

    ```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";
            
        IDatabase cache = Connection.GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demonstrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        ViewBag.command5 = "CLIENT LIST";
        StringBuilder sb = new StringBuilder();

        var endpoint = (System.Net.DnsEndPoint)Connection.GetEndPoints()[0];
        var server = Connection.GetServer(endpoint.Host, endpoint.Port);
        var clients = server.ClientList();

        sb.AppendLine("Cache response :");
        foreach (var client in clients)
        {
            sb.AppendLine(client.Raw);
        }

        ViewBag.command5Result = sb.ToString();

        return View();
    }
                
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

4. Vouw in **Solution Explorer** de map **Views** > **Shared** uit. Open vervolgens het bestand *_Layout.cshtml*.

    Vervang:
    
    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    door:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>Een nieuwe RedisCache-weergave toevoegen

1. Vouw in **Solution Explorer** de map **Views** uit en klik met de rechtermuisknop op de map **Home**. Kies **Add** > **View...** .

2. Voer in het dialoogvenster **Add View** als weergavenaam in: **RedisCache**. Selecteer vervolgens **Toevoegen**.

3. Vervang de code in het bestand *RedisCache.cshtml* door de volgende code:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
        <tr>
            <th>Command</th>
            <th>Result</th>
        </tr>
        <tr>
            <td>@ViewBag.command1</td>
            <td><pre>@ViewBag.command1Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command2</td>
            <td><pre>@ViewBag.command2Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command3</td>
            <td><pre>@ViewBag.command3Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command4</td>
            <td><pre>@ViewBag.command4Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command5</td>
            <td><pre>@ViewBag.command5Result</pre></td>
        </tr>
    </table>
    ```

## <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Het project is standaard geconfigureerd voor het lokaal hosten van de app in [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) voor testen en foutopsporing.

### <a name="to-run-the-app-locally"></a>De app lokaal uitvoeren
1. Selecteer in Visual Studio **Debug** > **Start Debugging** om de app lokaal te bouwen en te starten voor testen en foutopsporing.

2. Selecteer in de browser **Azure Cache voor Redis Test** op de navigatiebalk.

3. In het volgende voorbeeld ziet u dat de `Message`-sleutel eerder een waarde in de cache had, die was ingesteld met behulp van de Azure Cache voor Redis-console in de portal. De app heeft die waarde in de cache bijgewerkt. De app heeft ook de opdrachten `PING` en `CLIENT LIST` uitgevoerd.

    ![Eenvoudige test lokaal voltooid](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Publiceren en uitvoeren in Azure

Nadat het lokaal testen van de app is geslaagd, implementeert u de app in Azure en voert u deze uit in de cloud.

### <a name="to-publish-the-app-to-azure"></a>De app publiceren in Azure

1. Klik in Visual Studio met de rechtermuisknop op het projectknooppunt in Solution Explorer. Selecteer vervolgens **Publiceren**.

    ![Publiceren](./media/cache-web-app-howto/cache-publish-app.png)

2. Selecteer **Microsoft Azure App Service**, selecteer **Nieuwe maken** en selecteer vervolgens **Publiceren**.

    ![Publiceren naar App Service](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. Breng in het dialoogvenster **App Service maken** de volgende wijzigingen aan:

    | Instelling | Aanbevolen waarde | Beschrijving |
    | ------- | :---------------: | ----------- |
    | **Naam van app** | Gebruik de standaard. | De app-naam is de hostnaam voor de app wanneer deze is geïmplementeerd in Azure. Aan de naam is mogelijk een tijdstempel als achtervoegsel toegevoegd om deze uniek te maken, indien nodig. |
    | **Abonnement** | Kies uw Azure-abonnement. | Voor dit abonnement worden eventuele gerelateerde hostingkosten in rekening gebracht. Als u meerdere Azure-abonnementen hebt, controleert u of het gewenste abonnement is geselecteerd.|
    | **Resourcegroep** | Gebruik dezelfde resourcegroep waar u de cache hebt gemaakt. (Bijvoorbeeld, *TestResourceGroup*.) | Met een resourcegroep kunt u alle resources als een groep beheren. Als u de app later wilt verwijderen, verwijdert u gewoon de groep. |
    | **App Service-plan** | Selecteer **Nieuw** en maak vervolgens een nieuw App Service-plan met de naam *TestingPlan*. <br />Gebruik dezelfde **locatie** die u hebt gebruikt bij het maken van uw cache. <br />Kies **Vrij** voor de grootte. | Een App Service-plan definieert een set van rekenresources waarmee een web-app wordt uitgevoerd. |

    ![Dialoogvenster App Service](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. Nadat u de instellingen voor App Service-hosting hebt geconfigureerd, selecteert u **Maken**.

5. Controleer het venster **Uitvoer** in Visual Studio om de publicatiestatus te zien. Nadat de app is gepubliceerd, wordt de URL voor de app geregistreerd:

    ![Publicatie-uitvoer](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>De app-instelling voor de cache toevoegen

Nadat de nieuwe app is gepubliceerd, voegt u een nieuwe app-instelling toe. Deze instelling wordt gebruikt om de verbindingsgegevens van de cache op te slaan. 

#### <a name="to-add-the-app-setting"></a>De app-instelling toevoegen 

1. Typ de app-naam in de zoekbalk boven aan Azure Portal om te zoeken naar de nieuwe app die u hebt gemaakt.

    ![App zoeken](./media/cache-web-app-howto/cache-find-app-service.png)

2. Voeg een nieuwe app instelling met de naam **CacheConnection** toe die de app kan gebruiken om verbinding te maken met de cache. Gebruik dezelfde waarde die u hebt geconfigureerd voor `CacheConnection` in het bestand *CacheSecrets.config*. De waarde bevat de hostnaam en toegangssleutel van de cache.

    ![App-instelling toevoegen](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>De app in Azure uitvoeren

Ga in de browser naar de URL voor de app. De URL wordt weergegeven in de resultaten van de publicatiebewerking in het uitvoervenster van Visual Studio. Deze is ook beschikbaar in Azure Portal op de overzichtspagina van de app die u hebt gemaakt.

Selecteer op de navigatiebalk **Azure Cache voor Redis Test** om toegang tot de cache te testen.

![Eenvoudige test voltooid in Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die u in deze snelstart hebt gemaakt, behouden en opnieuw gebruiken.

Als klaar bent met de voorbeeldtoepassing uit de snelstart, kunt u de Azure-resources die u in deze snelstart hebt gemaakt, verwijderen om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. Wanneer u een resourcegroep verwijdert, worden alle resources in de groep definitief verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.

### <a name="to-delete-a-resource-group"></a>Een resourcegroep verwijderen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer vervolgens **Resourcegroepen**.

2. Typ in het vak **Filteren op naam...** de naam van de resourcegroep. In de instructies voor dit artikel is een resourcegroep met de naam *TestResources* gebruikt. Selecteer in de resourcegroep, in de resultatenlijst, de optie **...** . Selecteer vervolgens **Resourcegroep verwijderen**.

    ![Verwijderen](./media/cache-web-app-howto/cache-delete-resource-group.png)

U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ ter bevestiging de naam van de resourcegroep. Selecteer vervolgens **Verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle bijbehorende resources verwijderd.

## <a name="next-steps"></a>Volgende stappen

In de volgende zelfstudie gebruikt u Azure Cache voor Redis in een realistischer scenario om de prestaties van een app te verbeteren. U werkt deze toepassing bij zodat leaderboardresultaten in de cache worden geplaatst met behulp van het cache-aside-patroon met ASP.NET en een database.

> [!div class="nextstepaction"]
> [Een 'cache-aside' leaderboard maken in ASP.NET](cache-web-app-cache-aside-leaderboard.md)