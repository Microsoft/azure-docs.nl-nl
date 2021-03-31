---
title: Aan de slag met de webjobs SDK
description: Inleiding tot de webjobs SDK voor gebeurtenis gerichte achtergrond verwerking. Meer informatie over toegang tot gegevens in Azure-Services en services van derden.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91652037"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Aan de slag met de Azure WebJobs SDK voor gebeurtenisgestuurde verwerking op de achtergrond

Dit artikel laat zien hoe u met Visual Studio 2019 een Azure WebJobs SDK-project kunt maken, het lokaal kunt uitvoeren en vervolgens kunt implementeren op [Azure app service](overview.md). Versie 3. x van de webjobs SDK ondersteunt zowel .NET Core-als .NET Framework-console-apps. Zie [de Azure WEBJOBS SDK gebruiken voor op gebeurtenissen gebaseerde achtergrond verwerking voor](webjobs-sdk-how-to.md)meer informatie over het werken met de webjobs SDK.

In dit artikel wordt beschreven hoe u webjobs kunt implementeren als een .NET Core-Console-app. Als u webjobs wilt implementeren als een .NET Framework-console-app, raadpleegt u [webjobs als .NET Framework console-apps](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Als u geïnteresseerd bent in webjobs SDK versie 2. x, die alleen .NET Framework ondersteunt, raadpleegt u [Webjobs ontwikkelen en implementeren met behulp van Visual Studio-Azure app service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Vereisten

* [Installeer Visual Studio 2019](/visualstudio/install/) met de werk belasting van **Azure Development** . Als u Visual Studio al hebt, maar deze werk belasting niet hebt, voegt u de werk belasting toe door **extra > Hulpprogram ma's en functies ophalen** te selecteren.

* U moet [een Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) hebben om uw WEBjobs SDK-project te publiceren naar Azure.

## <a name="create-a-project"></a>Een project maken

1. Selecteer in Visual Studio **een nieuw project maken**.

2. Selecteer **console-app (.net core)**.

3. Geef het project de naam *WebJobsSDKSample* en selecteer vervolgens **maken**.

   ![Het dialoogvenster Nieuw project](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>NuGet-pakketten voor webjobs

1. Installeer de meest recente stabiele 3. x-versie van het [ `Microsoft.Azure.WebJobs.Extensions` NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/), inclusief `Microsoft.Azure.WebJobs` .

     Hier volgt de opdracht **Package Manager console** :

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Vervang in deze opdracht door `<3_X_VERSION>` een ondersteunde versie van het pakket. 

## <a name="create-the-host"></a>De host maken

De host is de runtime container voor functies die Luis teren naar triggers en aanroepen-functies. Met de volgende stappen maakt u een host die [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) de algemene host in ASP.net core implementeert.

1. Voeg deze instructies toe aan *programma. cs* `using` :

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Vervang de `Main`-methode door de volgende code:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

In ASP.NET Core worden hardwareconfiguraties ingesteld door methoden voor het exemplaar aan te roepen [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . Zie voor meer informatie [.net generic host](/aspnet/core/fundamentals/host/generic-host). De `ConfigureWebJobs` uitbreidings methode initialiseert de host webjobs. In `ConfigureWebJobs` initialiseert u specifieke webjobs-extensies en stelt u de eigenschappen van deze uitbrei dingen in.  

## <a name="enable-console-logging"></a>Logboek registratie van de console inschakelen

In deze sectie stelt u de console logboek registratie in die gebruikmaakt van het [ASP.net core-logboek registratie raamwerk](/aspnet/core/fundamentals/logging).

1. Installeer de nieuwste stabiele versie van het [ `Microsoft.Extensions.Logging.Console` NuGet-pakket](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), inclusief `Microsoft.Extensions.Logging` .

   Hier volgt de opdracht **Package Manager console** :

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   Vervang in deze opdracht door `<3_X_VERSION>` een ondersteunde 3. x-versie van het pakket.

1. Voeg in *Program. cs* een `using` instructie toe:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Roep de [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) methode aan [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . De- [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) methode voegt console logboek registratie toe aan de configuratie.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    De `Main` methode ziet er nu als volgt uit:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Deze update doet het volgende:

    * Hiermee wordt de [logboek registratie van dash board](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)uitgeschakeld. Het dash board is een verouderd controle programma en logboek registratie van Dash boards wordt niet aanbevolen voor productie scenario's met hoge door voer.
    * Voegt de console provider toe met standaard [filters](webjobs-sdk-how-to.md#log-filtering).

U kunt nu een functie toevoegen die wordt geactiveerd door berichten die in een Azure Storage wachtrij arriveren.

## <a name="install-the-storage-binding-extension"></a>De Storage-bindingextensie installeren

Vanaf versie 3. x moet u de opslag bindings extensie expliciet installeren die is vereist voor de webjobs SDK. In eerdere versies zijn de opslag bindingen opgenomen in de SDK.

1. Installeer de nieuwste stabiele versie van het pakket  [micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet, versie 3. x. 

    Hier volgt de opdracht **Package Manager console** :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Vervang in deze opdracht door `<3_X_VERSION>` een ondersteunde versie van het pakket. 

2. In de `ConfigureWebJobs` uitbreidings methode roept u de- `AddAzureStorage` methode op het [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) exemplaar aan om de opslag extensie te initialiseren. Op dit moment lijkt de `ConfigureWebJobs` methode op het volgende voor beeld:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Een functie maken

1. Klik met de rechter muisknop op het project, selecteer  >  **Nieuw item toevoegen...**, kies **klasse**, noem de nieuwe C#-klasse bestands *functies. cs* en selecteer **toevoegen**.

1. Vervang in functions. cs de gegenereerde sjabloon door de volgende code:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   Het `QueueTrigger` kenmerk geeft aan dat de runtime deze functie aanroept wanneer een nieuw bericht wordt geschreven in een Azure Storage wachtrij met de naam `queue` . De inhoud van het wachtrij bericht wordt gegeven aan de methode code in de `message` para meter. De hoofd tekst van de methode is waar u de trigger gegevens verwerkt. In dit voor beeld registreert de code alleen het bericht.

   De `message` para meter moet geen teken reeks zijn. U kunt ook binden aan een JSON-object, een byte matrix of een [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) -object. [Zie het gebruik van wachtrij Triggers](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Elk bindings type (zoals wacht rijen, blobs of tabellen) heeft een andere set parameter typen waarmee u verbinding kunt maken.

## <a name="create-a-storage-account"></a>Een opslagaccount maken

De Azure Storage-emulator die lokaal wordt uitgevoerd, beschikt niet over alle functies die nodig zijn voor de webjobs SDK. In dit gedeelte maakt u een opslag account in Azure en configureert u het project om het te gebruiken. Als u al een opslag account hebt, gaat u verder met stap 6.

1. Open **Server Explorer** in Visual Studio en meld u aan bij Azure. Klik met de rechter muisknop op het **Azure** -knoop punt en selecteer **verbinding maken met Microsoft Azure abonnement**.

   ![Aanmelden bij Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Klik onder het **Azure** -knoop punt in **Server Explorer** met de rechter muisknop op **opslag** en selecteer vervolgens **opslag account maken**.

   ![Menu opslag account maken](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Voer in het dialoog venster **opslag account maken** een unieke naam in voor het opslag account.

1. Kies dezelfde **regio** die u hebt gemaakt voor uw app service-app in of een regio die dicht bij u ligt.

1. Selecteer **Maken**.

   ![Een opslagaccount maken](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Selecteer het nieuwe opslag account onder het knoop punt **opslag** in **Server Explorer**. Selecteer in het venster **Eigenschappen** het weglatings teken (**...**) aan de rechter kant van het veld met de **verbindings reeks** waarde.

   ![Weglatings teken reeks](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Kopieer de connection string en sla deze waarde ergens anders op, zodat u deze eenvoudig kunt kopiëren.

   ![connection string kopiëren](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Opslag configureren om lokaal uit te voeren

De webjobs SDK zoekt naar de opslag connection string in de toepassings instellingen in Azure. Wanneer u lokaal uitvoert, zoekt het naar deze waarde in het lokale configuratie bestand of in omgevings variabelen.

1. Klik met de rechter muisknop op het project, selecteer  >  **Nieuw item toevoegen...**, kies het **JSON-configuratie bestand van Java script**, geef het nieuwe bestand de naam *appsettings.jsop* bestand en selecteer **toevoegen**. 

1. Voeg in het nieuwe bestand een `AzureWebJobsStorage` veld toe, zoals in het volgende voor beeld:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Vervang *{storage Connection String}* door de Connection String die u eerder hebt gekopieerd.

1. Selecteer de *appsettings.js* in het Solution Explorer en stel in het venster **Eigenschappen** de optie **kopiëren naar uitvoer Directory** in op **kopiëren als u nieuwer** wilt.

Later voegt u dezelfde connection string app-instelling in uw app toe in Azure App Service.

## <a name="test-locally"></a>Lokaal testen

In deze sectie maakt en voert u het project lokaal uit en wordt de functie geactiveerd door een wachtrij bericht te maken.

1. Druk op **CTRL + F5** om het project uit te voeren.

   In de-console ziet u dat de runtime uw functie heeft gevonden en wacht op wachtrij berichten om deze te activeren. De volgende uitvoer wordt gegenereerd door de v3. x-host:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Sluit het console venster.

1. Vouw in **Server Explorer** in Visual Studio het knoop punt voor uw nieuwe opslag account uit en klik vervolgens met de rechter muisknop op **wacht rijen**.

1. Selecteer **wachtrij maken**.

1. Geef een *wachtrij* op als naam voor de wachtrij en selecteer **OK**.

   ![Scherm afbeelding die laat zien waar u de wachtrij maakt en de naam wachtrij geeft. ](./media/webjobs-sdk-get-started/create-queue.png)

1. Klik met de rechter muisknop op het knoop punt voor de nieuwe wachtrij en selecteer vervolgens **wachtrij weer geven**.

1. Selecteer het pictogram **bericht toevoegen** .

   ![Scherm afbeelding die het pictogram bericht toevoegen markeert.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Voer in het dialoog venster **bericht toevoegen** *Hallo wereld in.* Als de **tekst** van het bericht en selecteer vervolgens **OK**. Er bevindt zich nu een bericht in de wachtrij.

   ![Wachtrij maken](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Voer het project opnieuw uit.

   Omdat u het `QueueTrigger` kenmerk in de functie hebt gebruikt `ProcessQueueMessage` , luistert de WeJobs SDK-runtime naar wachtrij berichten wanneer deze wordt gestart. Er wordt een nieuw wachtrij bericht gevonden in de wachtrij met de naam *wachtrij* en de functie wordt aangeroepen.

   Als gevolg van het [Navragen van de wachtrij exponentiële uitstel](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm), kan het twee minuten duren voordat de runtime het bericht heeft gevonden en de functie aanroept. Deze wacht tijd kan worden verkleind door in de [ontwikkelings modus](webjobs-sdk-how-to.md#host-development-settings)te worden uitgevoerd.

   De console-uitvoer ziet er als volgt uit:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Sluit het console venster. 

1. Ga terug naar het wachtrij venster en vernieuw het. Het bericht is verwijderd omdat het is verwerkt door de functie die lokaal wordt uitgevoerd. 

## <a name="add-application-insights-logging"></a>Application Insights logboek registratie toevoegen

Wanneer het project wordt uitgevoerd in azure, kunt u de uitvoering van functies niet bewaken door de console-uitvoer weer te geven. De bewakings oplossing die we aanraden, is [Application Insights](../azure-monitor/app/app-insights-overview.md). Zie [Monitor Azure Functions](../azure-functions/functions-monitoring.md) (Azure Functions bewaken) voor meer informatie.

In deze sectie voert u de volgende taken uit om Application Insights logboek registratie in te stellen voordat u naar Azure implementeert:

* Zorg ervoor dat u beschikt over een App Service-app en een Application Insights-exemplaar waarmee u kunt werken.
* Configureer de App Service-app voor het gebruik van het Application Insights exemplaar en het opslag account dat u eerder hebt gemaakt.
* Stel het project in voor het aanmelden bij Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service app-en Application Insights-exemplaar maken

1. Als u nog geen app hebt App Service die u kunt gebruiken, maakt u er [een](quickstart-dotnet-framework.md). Wanneer u uw app maakt, kunt u ook een verbonden Application Insights-resource maken. Wanneer u dit doet, `APPINSIGHTS_INSTRUMENTATIONKEY` wordt de in uw app ingesteld voor u.

1. Als u nog geen Application Insights resource hebt die u kunt gebruiken, maakt u er [een](../azure-monitor/app/create-new-resource.md ). Stel het **toepassings type** in op **Algemeen** en sla de secties die volgen **de instrumentatie sleutel kopiëren** op.

1. Als u al een Application Insights resource hebt die u wilt gebruiken, [kopieert u de instrumentatie sleutel](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>App-instellingen configureren 

1. In **Server Explorer** in Visual Studio, vouwt u het knoop punt **app service** onder **Azure** uit.

1. Vouw de resource groep uit waarop uw App Service-app zich bevindt en klik met de rechter muisknop op uw App Service-app.

1. Selecteer **instellingen weer geven**.

1. Voeg in het vak **verbindings reeksen** de volgende vermelding toe.

   |Name  |verbindings reeks  |Database type|
   |---------|---------|------|
   |AzureWebJobsStorage | {de opslag connection string die u eerder hebt gekopieerd}|Aangepast|

1. Als het vak **Toepassings instellingen** geen Application Insights instrumentatie sleutel heeft, voegt u het toe dat u eerder hebt gekopieerd. (De instrumentatie sleutel is mogelijk al aanwezig, afhankelijk van hoe u de App Service-app hebt gemaakt.)

   |Name  |Waarde  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentatie sleutel} |

1. Vervang *{instrumentatie Key}* door de instrumentatie sleutel van de Application Insights resource die u gebruikt.

1. Selecteer **Opslaan**.

1. Voeg de Application Insights verbinding toe aan het project, zodat u deze lokaal kunt uitvoeren. Voeg in het *appsettings.js* bestand een veld toe `APPINSIGHTS_INSTRUMENTATIONKEY` , zoals in het volgende voor beeld:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Vervang *{instrumentatie Key}* door de instrumentatie sleutel van de Application Insights resource die u gebruikt.

1. Sla uw wijzigingen op.

### <a name="add-application-insights-logging-provider"></a>Application Insights logboek registratie provider toevoegen

Als u wilt profiteren van [Application Insights](../azure-monitor/app/app-insights-overview.md) logboek registratie, werkt u de logboek registratie code bij om het volgende te doen:

* Een Application Insights-logboek provider met standaard [filters](webjobs-sdk-how-to.md#log-filtering)toevoegen. Wanneer lokaal wordt uitgevoerd, worden alle gegevens en logboeken van een hoger niveau naar de console en de Application Insights geschreven.
* Plaats het [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) -object in een `using` blok om ervoor te zorgen dat de logboek uitvoer wordt leeg gemaakt wanneer de host wordt afgesloten.

1. Installeer de meest recente stabiele 3. x-versie van het [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

   Hier volgt de opdracht **Package Manager console** :

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Vervang in deze opdracht door `<3_X_VERSION>` een ondersteunde versie van het pakket.

1. Open *programma. cs* en vervang de code in de- `Main` methode door de volgende code:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Hiermee voegt u de Application Insights provider toe aan de logboek registratie met behulp van de sleutel die u eerder hebt toegevoegd aan de app-instellingen.

## <a name="test-application-insights-logging"></a>Application Insights logboek registratie testen

In deze sectie voert u lokaal opnieuw uit om te controleren of logboek gegevens nu worden Application Insights en naar de-console.

1. Gebruik **Server Explorer** in Visual Studio om een wachtrij bericht te maken zoals u [eerder](#test-locally)hebt gedaan, met uitzonde ring van *Hallo app Insights.* Als de bericht tekst.

1. Voer het project uit.

   De webjobs SDK verwerkt het bericht in de wachtrij en u ziet de logboeken in het console venster.

1. Sluit het console venster.

1. Ga naar de [Azure Portal](https://portal.azure.com/) om uw Application Insights resource weer te geven. Zoek en selecteer **Application Insights**.

1. Kies uw Application Insights-exemplaar.

1. Selecteer **zoeken**.

   ![Zoek opdracht selecteren](./media/webjobs-sdk-get-started/select-search.png)

1. Als u de *Hello app Insights* niet ziet. bericht, selecteert u regel matig **vernieuwen** gedurende enkele minuten. (Logboeken worden niet onmiddellijk weer gegeven, omdat het even duurt voordat de Application Insights-client de logboeken die IT-processen heeft leeg gemaakt.)

   ![Logboeken in Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Sluit het console venster.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Implementeren op Azure

Tijdens de implementatie maakt u een app service-exemplaar waarin u uw functies kunt uitvoeren. Wanneer u een .NET Core-Console-app publiceert naar App Service in azure, wordt deze automatisch uitgevoerd als Webtaak. Zie [Webjobs ontwikkelen en implementeren met Visual Studio](webjobs-dotnet-deploy-vs.md)voor meer informatie over publiceren.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>De functie activeren in azure

1. Zorg ervoor dat u niet lokaal wordt uitgevoerd (sluit het console venster als dit nog geopend is). Anders is het lokale exemplaar mogelijk de eerste voor het verwerken van de wachtrij berichten die u maakt.

1. Voeg op de pagina **wachtrij** in Visual Studio een bericht toe aan de wachtrij.

1. Vernieuw de **wachtrij** pagina en het nieuwe bericht verdwijnt omdat het is verwerkt door de functie die wordt uitgevoerd in Azure.

   > [!TIP]
   > Wanneer u test in azure, moet u de [ontwikkelings modus](webjobs-sdk-how-to.md#host-development-settings) gebruiken om ervoor te zorgen dat de functie voor het activeren van een wachtrij direct wordt geactiveerd en vertragingen voor komen als gevolg van het [Navragen van de wachtrij exponentiële uitstel](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Logboeken in Application Insights weer geven

1. Open de [Azure Portal](https://portal.azure.com/)en ga naar uw Application Insights resource.

1. Selecteer **zoeken**.

1. Als u de *Hello Azure* niet ziet! bericht, selecteert u regel matig **vernieuwen** gedurende enkele minuten.

   U ziet de logboeken van de functie die wordt uitgevoerd in een Webtaak, inclusief *Hello Azure!* de tekst die u in de voor gaande sectie hebt ingevoerd.

## <a name="add-an-input-binding"></a>Een invoer binding toevoegen

Invoer bindingen vereenvoudigen code waarmee gegevens worden gelezen. In dit voor beeld is het wachtrij bericht een BLOB-naam en gebruikt u de naam van de blob om een BLOB in Azure Storage te zoeken en te lezen.

1. Vervang in *functions. cs* de- `ProcessQueueMessage` methode door de volgende code:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   In deze code `queueTrigger` is een [bindings expressie](../azure-functions/functions-bindings-expressions-patterns.md), wat betekent dat deze tijdens runtime wordt omgezet in een andere waarde.  Tijdens runtime heeft het de inhoud van het bericht in de wachtrij.

1. Een toevoegen `using` :

   ```cs
   using System.IO;
   ```

1. Maak een BLOB-container in uw opslag account.

   a. Vouw in **Server Explorer** in Visual Studio het knoop punt voor uw opslag account uit, klik met de rechter muisknop op **blobs** en selecteer vervolgens **BLOB-container maken**.

   b. Voer in het dialoog venster **BLOB-container maken** *container* in als container naam en klik vervolgens op **OK**.

1. Upload het bestand *Program. cs* naar de BLOB-container. (Dit bestand wordt hier als voor beeld gebruikt; u kunt elk tekst bestand uploaden en een wachtrij bericht met de naam van het bestand maken.)

   a. Dubbel klik in **Server Explorer** op het knoop punt voor de container die u hebt gemaakt.

   b. Selecteer de knop **uploaden** in het **container** venster.

   ![Knop voor het uploaden van blobs](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Zoek en selecteer *Program. cs* en selecteer vervolgens **OK**.

1. Maak een wachtrij bericht in de wachtrij die u eerder hebt gemaakt met *Program. cs* als tekst van het bericht.

   ![Wachtrij berichten programma. cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Voer het project lokaal uit.

   In het wachtrij bericht wordt de functie geactiveerd, waarna de BLOB wordt gelezen en de lengte wordt geregistreerd. De console-uitvoer ziet er als volgt uit:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

Uitvoer bindingen vereenvoudigen code waarmee gegevens worden geschreven. In dit voor beeld wordt het vorige gewijzigd door een kopie van de BLOB te schrijven in plaats van de grootte ervan te registreren. Blob Storage-bindingen zijn opgenomen in het Azure Storage-uitbreidings pakket dat eerder is geïnstalleerd.

1. Vervang de `ProcessQueueMessage`-methode door de volgende code:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Maak nog een wachtrij bericht met *programma. cs* als tekst van het bericht.

1. Voer het project lokaal uit.

   In het wachtrij bericht wordt de functie geactiveerd, waarna de BLOB wordt gelezen, de lengte ervan wordt geregistreerd en een nieuwe BLOB wordt gemaakt. De uitvoer van de console is hetzelfde, maar wanneer u naar het venster BLOB container gaat en vervolgens **vernieuwen** selecteert, ziet u een nieuwe blob met de naam *copy-Program. cs.*

## <a name="republish-the-updates-to-azure"></a>De updates opnieuw publiceren naar Azure

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Controleer in het dialoog venster **publiceren** of het huidige profiel is geselecteerd en kies vervolgens **publiceren**. De resultaten van de publicatie worden gedetailleerd beschreven in het **uitvoer** venster.
 
1. Controleer de functie in azure door opnieuw een bestand te uploaden naar de BLOB-container en een bericht toe te voegen aan de wachtrij die de naam is van het geüploade bestand. U ziet dat het bericht wordt verwijderd uit de wachtrij en een kopie van het bestand dat in de BLOB-container is gemaakt. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een webjobs SDK 3. x-project maakt, uitvoert en implementeert.

> [!div class="nextstepaction"]
> [Meer informatie over de webjobs SDK](webjobs-sdk-how-to.md)