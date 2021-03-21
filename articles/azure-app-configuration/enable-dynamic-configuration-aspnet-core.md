---
title: 'Zelfstudie: Dynamische configuratie van App Configuration in ASP.NET Core gebruiken'
titleSuffix: Azure App Configuration
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor ASP.NET Core-apps dynamisch bijwerkt
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 083bd56b2b211d11206a277bf31eea797b37cdb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979926"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Zelfstudie: Dynamische configuratie in een ASP.NET Core-app gebruiken

ASP.NET Core heeft een pluggable configuratiesysteem waarmee configuratiegegevens uit verschillende bronnen kunnen worden gelezen. Het kan dynamisch wijzigingen verwerken zonder dat de toepassing opnieuw moet worden gestart. ASP.NET Core ondersteunt de binding van configuratie-instellingen tot sterk getypeerde .NET-klassen. Ze worden in uw code ingevoegd met behulp van de verschillende `IOptions<T>`-patronen. Een van deze patronen, met name `IOptionsSnapshot<T>`, laadt de configuratie van de toepassing opnieuw wanneer de onderliggende gegevens wijzigen. U kunt `IOptionsSnapshot<T>` in domeincontrollers in uw toepassing invoeren voor toegang tot de meest recente configuratiegegevens die zijn opgeslagen in Azure-app-configuratie.

U kunt ook de ASP.NET Core-clientbibliotheek van App Configuration instellen om een set configuratie-instellingen dynamisch te vernieuwen met behulp van middleware. De configuratie-instellingen worden elke keer bijgewerkt met het configuratiearchief zolang de web-app aanvragen ontvangt.

App Configuration slaat elke instelling automatisch in de cache op om te veel aanroepen naar het configuratiearchief te voorkomen. De vernieuwingsbewerking wacht totdat de waarde van een instelling in de cache verloopt om deze instelling bij te werken, zelfs wanneer de waarde ervan in het configuratiearchief wordt gewijzigd. De standaardwaarde voor de vervaltijd van de cache is 30 seconden. U kunt deze vervaltijd, indien nodig, overschrijven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Volg eerst [Een ASP.NET Core-app maken met App Configuration](./quickstart-aspnet-core-app.md) voordat u verder gaat.

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw toepassing instellen voor het bijwerken van de configuratie als reactie op wijzigingen in een App Configuration-archief.
> * De meest recente configuratie in de controllers van uw toepassing invoeren.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt uitvoeren, installeert u de [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Volg eerst [Een ASP.NET Core-app maken met App Configuration](./quickstart-aspnet-core-app.md) voordat u verder gaat.

## <a name="add-a-sentinel-key"></a>Een Sentinel-sleutel toevoegen

Een *Sentinel-sleutel* is een speciale sleutel die wordt gebruikt om te signaleren wanneer de configuratie is gewijzigd. Uw app controleert de Sentinel-sleutel op wijzigingen. Wanneer een wijziging wordt gedetecteerd, vernieuwt u alle configuratiewaarden. Deze benadering vermindert het totale aantal aanvragen dat door uw app naar de App Configuration wordt gedaan in vergelijking met het bewaken van alle sleutels voor wijzigingen.

1. Selecteer in de Azure Portal **Configuratieverkenner> Maken > Sleutelwaarde**.
1. Voer bij **Sleutel** *TestApp:Settings:Sentinel* in. Voer 1 in bij **Waarde**. Laat **Label** en **Inhoudstype** leeg.
1. Selecteer **Toepassen**.

> [!NOTE]
> Als u geen Sentinel-sleutel gebruikt, moet u elke sleutel die u wilt bekijken, handmatig registreren.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Voeg een verwijzing aan het NuGet-pakket `Microsoft.Azure.AppConfiguration.AspNetCore` toe door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Open *Program.cs* en werk de methode `CreateWebHostBuilder` bij om de methode `config.AddAzureAppConfiguration()` toe te voegen.

   #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```   
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    De methode `ConfigureRefresh` wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratiegegevens met het app-configuratiearchief wanneer een vernieuwingsbewerking wordt geactiveerd. De parameter `refreshAll` voor de methode `Register` geeft aan dat alle configuratiewaarden moeten worden vernieuwd als de Sentinel-sleutel wordt gewijzigd.

    De methode `SetCacheExpiration` overschrijft ook de standaardvervaltijd van de cache van 30 seconden en geeft in plaats daarvan een tijd van vijf minuten op. Dit beperkt het aantal aanvragen dat wordt gedaan aan App Configuration.

    > [!NOTE]
    > Voor testdoeleinden wilt u de vervaltijd van de cache mogelijk verlagen.

    Als u een vernieuwingsbewerking daadwerkelijk wilt activeren, moet u middleware voor het vernieuwen van de toepassing configureren om de configuratiegegevens te vernieuwen wanneer er wijzigingen optreden. In een latere stap ziet u hoe u dit doet.

2. Voeg in de map controllers een bestand *Settings. cs* toe dat een nieuwe klasse definieert en implementeert `Settings` . Vervang de naam ruimte door de naam van uw project. 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Open *Startup.cs* en gebruik `IServiceCollection.Configure<T>` in de methode `ConfigureServices` bij om configuratiegegevens te binden aan de klasse `Settings`.

    #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---
    > [!Tip]
    > Zie [Optiepatronen in ASP.NET Core](/aspnet/core/fundamentals/configuration/options) voor meer informatie over het optiepatroon bij het lezen van configuratiewaarden.

4. Werk de methode `Configure` bij, waarbij de middleware `UseAzureAppConfiguration` wordt toegevoegd zodat de configuratie-instellingen die voor vernieuwing zijn geregistreerd, kunnen worden bijgewerkt terwijl de ASP.NET Core-web-app aanvragen blijft ontvangen.


    #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    De middleware maakt gebruik van de vernieuwingsconfiguratie die is opgegeven in de methode `AddAzureAppConfiguration` in `Program.cs` om een vernieuwing te activeren voor elke aanvraag die wordt ontvangen door de ASP.NET Core-web-app. Voor elke aanvraag wordt een vernieuwingsbewerking geactiveerd en de clientbibliotheek controleert of de in de cache opgeslagen waarde voor de geregistreerde configuratie-instelling is verlopen. Als de configuratie-instelling is verlopen, wordt deze vernieuwd.

    > [!NOTE]
    > Als u wilt controleren of de configuratie is vernieuwd, voegt u de middleware zp vroeg als kan toe aan de aanvraagpijplijn. Op die manier ontstaat er geen kortsluiting met andere middleware in de toepassing.

## <a name="use-the-latest-configuration-data"></a>De meest recente configuratiegegevens gebruiken

1. Open *HomeController.cs* in de map Controllers en voeg een verwijzing naar het pakket `Microsoft.Extensions.Options` toe.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Werk de klasse `HomeController` bij voor het ontvangen van `Settings` via afhankelijkheidsinjectie en gebruik de waarden ervan.

 #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
```
---



3. Open *Index.cshtml* in de directory Views > Home en vervang de inhoud ervan door het volgende script:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Compileer de app met behulp van de .NET Core CLI door de volgende opdracht uit te voeren in de opdrachtshell:

    ```console
        dotnet build
    ```

1. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```console
        dotnet run
    ```

1. Open een browservenster en ga naar de URL die wordt weergegeven in de `dotnet run`-uitvoer.

    ![Quickstart-app lokaal starten](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Configuratieverkenner** en werk de waarde van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:BackgroundColor | groen |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Gegevens uit Azure-app-configuratie - nu met live updates! |
    | TestApp:Settings:Sentinel | 2 |

1. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien. Mogelijk moet u meer dan één keer vernieuwen voordat de wijzigingen worden doorgevoerd, of de automatische vernieuwings frequentie wijzigen in minder dan 5 minuten. 

    ![Bijgewerkte quickstart-app lokaal starten](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw ASP.NET Core-web-app ingeschakeld voor het dynamisch vernieuwen van configuratie-instellingen vanuit App Configuration. Als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot App Configuration te stroomlijnen, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
