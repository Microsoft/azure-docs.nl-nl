---
title: Snelstart - Uw eerste Azure Spring Cloud-toepassing implementeren
description: In deze quickstart implementeren we een Spring Cloud-toepassing in Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a1732f42ea95c16cdec7a1d7569c954667e52cb4
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750897"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Quickstart: Uw eerste Azure Spring Cloud-toepassing implementeren

::: zone pivot="programming-language-csharp"
In deze quickstart wordt uitgelegd hoe u een eenvoudige Azure Spring Cloud-microservicetoepassing kunt implementeren om te worden uitgevoerd in Azure.

>[!NOTE]
> Steeltoe-ondersteuning voor Azure Spring Cloud wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunnen klanten voorafgaand aan de officiële release met nieuwe functies experimenteren.  Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie.  Voor meer informatie over ondersteuning tijdens previews kunt u de [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) raadplegen of een [Ondersteuningsaanvraag](../azure-portal/supportability/how-to-create-azure-support-request.md) indienen.

In deze quickstart leert u het volgende:

> [!div class="checklist"]
> * Een eenvoudig Steeltoe .NET Core-project genereren
> * Een exemplaar van de Azure Spring Cloud-service inrichten
> * De app bouwen en implementeren met een openbaar eindpunt
> * Logboeken in real-time streamen

De toepassingscode die in deze quickstart wordt gebruikt, is een eenvoudige app die wordt ontwikkeld met een .NET Core-projectsjabloon voor web-API’s. Wanneer u dit voorbeeld hebt voltooid, is de toepassing online toegankelijk en kan deze worden beheerd via de Azure-portal en Azure CLI.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). De Azure Spring Cloud-service ondersteunt .NET Core 3.1 en hogere versies.
* [Azure CLI versie 2.0.67 of hoger](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Azure CLI-extensie installeren

Controleer of de Azure CLI-versie 2.0.67 of hoger is:

```azurecli
az --version
```

Voer de volgende opdracht uit om de Azure Spring Cloud-extensie voor Azure CLI te installeren:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

1. Meld u aan bij Azure CLI.

    ```azurecli
    az login
    ```

1. Als u meerdere abonnementen hebt, kiest u het abonnement dat u voor deze quickstart wilt gebruiken.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Een Steeltoe .NET Core-project genereren

Maak in Visual Studio een ASP.NET Core-webtoepassing met de naam ‘hello-world’ met behulp van een projectsjabloon voor API’s. Er wordt automatisch een WeatherForecastController gegenereerd, die verderop ons testeindpunt zal zijn.

1. Maak een map voor de projectbroncode en genereer het project.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Navigeer naar de projectmap.

   ```console
   cd hello-world
   ```

1. Bewerk het bestand *appSettings.json* om de volgende instellingen toe te voegen:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Wijzig in *appsettings.json* het logboekniveau voor de categorie `Microsoft` van `Warning` in `Information`. Deze wijziging verzekert dat er logboeken worden geproduceerd wanneer u streaminglogboeken bekijkt in een latere stap.

   Het bestand *appsettings.json* ziet er nu ongeveer zoals het volgende voorbeeld uit:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Voeg afhankelijkheden en een `Zip`-taak toe aan het bestand *.csproj*:

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   De pakketten zijn voor Steeltoe Service Discovery en de Azure Spring Cloud-clientbibliotheek. De `Zip`-taak is voor implementatie in Azure. Wanneer u de opdracht `dotnet publish` uitvoert, worden de binaire bestanden in de map *publish* gegenereerd, en met deze taak wordt de map *publish* gezipt in een *ZIP*-bestand dat u naar Azure uploadt.

3. In het bestand *Program.cs* voegt u een `using`-instructie en code die gebruikmaakt van de Azure Spring Cloud-clientbibliotheek toe:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .UseAzureSpringCloudService()
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   });
   ```

4. In het bestand *Startup.cs* voegt u een `using`-instructie en code die gebruikmaakt van de Steeltoe Service Discovery toe aan het eind van de methoden `ConfigureServices` en `Configure`:

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Bouw het project om ervoor te zorgen dat er geen compilatiefouten zijn.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Een service-exemplaar inrichten

Met de volgende procedure maakt u een exemplaar van Azure Spring Cloud met behulp van de Azure Portal.

1. Open [Azure Portal](https://ms.portal.azure.com/). 

1. Zoek in het bovenste zoekvak naar *Azure Spring Cloud*.

1. Selecteer *Azure Spring Cloud* in de resultaten.

   ![ASC-pictogram: starten](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Klik op + Toevoegen op de pagina **Azure Spring Cloud**.

   ![ASC-pictogram: toevoegen](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**.  Houd rekening met de volgende richtlijnen:

   * **Abonnement**: Selecteer het abonnement waarvoor u voor deze resource gefactureerd wilt worden.
   * **Resourcegroep**: Maak een nieuwe resourcegroep. De naam die u hier invoert, wordt in latere stappen gebruikt als **\<resource group name\>** .
   * **Servicedetails/naam**: Geef de **\<service instance name\>** op.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
   * **Regio**: Selecteer de regio voor uw service-exemplaar.

   ![ASC-portal starten](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Selecteer **Controleren en maken**.

## <a name="build-and-deploy-the-app"></a>De app compileren en implementeren

Met de volgende procedure wordt het project gebouwd en geïmplementeerd dat u eerder hebt gemaakt.

1. Zorg ervoor dat de opdrachtprompt nog steeds in de projectmap staat.

1. Voer de volgende opdracht uit om het project te bouwen, de binaire bestanden te publiceren en de binaire bestanden op te slaan in een *ZIP*-bestand in de projectmap.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Maak een app in uw Azure Spring Cloud-exemplaar met een openbaar eindpunt eraan toegewezen. Gebruik dezelfde toepassingsnaam ‘hello-world’ die u hebt opgegeven in *appsettings.json*.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public --runtime-version NetCore_31
   ```

1. Implementeer het *ZIP*-bestand in de app.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   De optie `--main-entry` identificeert het *DLL*-bestand dat het beginpunt van de toepassing bevat. Nadat het *ZIP-bestand* is geüpload, worden alle bestanden en mappen geëxtraheerd en wordt geprobeerd het beginpunt in het *DLL*-bestand opgegeven door `--main-entry` uit te voeren.

   Het duurt enkele minuten om de implementatie van de toepassing te voltooien. Als u wilt controleren of de implementatie is geslaagd, gaat u naar de blade **Apps** in de Azure-portal.

## <a name="test-the-app"></a>De app testen

Zodra de implementatie is voltooid, kunt u de app openen op de volgende URL:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

De app retourneert JSON-gegevens die lijken op die in het volgende voorbeeld:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Logboeken in real-time streamen

Gebruik de volgende opdracht om real-time logboeken op te halen in de app.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

Logboeken worden weergegeven in de uitvoer:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> Gebruik `az spring-cloud app logs -h` om meer parameters en functionaliteit van logboekstreaming te verkennen.

Ga naar het tabblad **Logboeken** in het menu van de [Azure-portal](https://portal.azure.com/) voor geavanceerde functies voor logboekanalyse. Logboeken hebben hier een latentie van enkele minuten.
[ ![Logboekanalyse](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
In deze quickstart wordt uitgelegd hoe u een eenvoudige Azure Spring Cloud-microservicetoepassing kunt implementeren om te worden uitgevoerd in Azure. 

De toepassingscode die in deze zelfstudie wordt gebruikt, is een eenvoudige app die is gebouwd met Spring Initializr. Wanneer u dit voorbeeld hebt voltooid, is de toepassing online toegankelijk en kan deze worden beheerd via de Azure-portal.

In deze quickstart wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Een eenvoudig Spring Cloud-project genereren
> * Een service-exemplaar inrichten
> * De app bouwen en implementeren met een openbaar eindpunt
> * Logboeken in real-time streamen

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* [JDK 8 installeren](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI-versie 2.0.67 of hoger](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Een Spring Cloud-project genereren

Begin met [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) om een voorbeeldproject te genereren met aanbevolen afhankelijkheden voor Azure Spring Cloud. In de volgende afbeelding ziet u de Initializr-configuratie voor dit voorbeeldproject.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```

  ![Initializr-pagina](media/spring-cloud-quickstart-java/initializr-page.png)

> [!NOTE]
> We hebben een probleem geïdentificeerd met Spring Boot 2.4 in TLS-verificatie tussen uw apps en Eureka, en werken momenteel samen met de Spring-community om dit op te lossen. Raadpleeg onze [Veelgestelde vragen](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-faq?pivots=programming-language-java#development) voor de tijdelijke oplossing.

1. Klik op **Genereren** wanneer alle afhankelijkheden zijn ingesteld. Download het pakket en pak het uit. Maak vervolgens een webcontroller voor een eenvoudige webtoepassing door `src/main/java/com/example/hellospring/HelloController.java` als volgt toe te voegen:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Een exemplaar van Azure Spring Cloud inrichten

Met de volgende procedure maakt u een exemplaar van Azure Spring Cloud met behulp van de Azure-portal.

1. Open [Azure Portal](https://ms.portal.azure.com/) op een nieuw tabblad. 

2. Zoek in het bovenste zoekvak naar *Azure Spring Cloud*.

3. Selecteer *Azure Spring Cloud* in de resultaten.

    ![ASC-pictogram: starten](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klik op **+ Toevoegen** op de Azure Spring Cloud-pagina.

    ![ASC-pictogram: toevoegen](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**.  Houd rekening met de volgende richtlijnen:
    - **Abonnement**: Selecteer het abonnement waarvoor u voor deze resource gefactureerd wilt worden.
    - **Resourcegroep**: Het is een best practice om nieuwe resourcegroepen te maken voor nieuwe resources. Dit wordt in latere stappen gebruikt als **\<resource group name\>** .
    - **Servicedetails/naam**: Geef de **\<service instance name\>** op.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - **Locatie**: Selecteer de regio voor uw service-exemplaar.

    ![ASC-portal starten](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klik op **Controleren en maken**.

## <a name="build-and-deploy-the-app"></a>De app compileren en implementeren
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
Met de volgende procedure wordt de toepassing gebouwd en geïmplementeerd met behulp van de Azure CLI. Voer de volgende opdracht uit in de hoofdmap van het project.

1. Bouw het project met behulp van Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. Installeer de Azure Spring Cloud-extensie voor de Azure CLI (als u dit nog niet hebt gedaan):

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Maak de app met een toegewezen openbaar eindpunt:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Implementeer het JAR-bestand voor de app (`target\hellospring-0.0.1-SNAPSHOT.jar` in Windows):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Het duurt enkele minuten om de implementatie van de toepassing te voltooien. Als u wilt controleren of de implementatie is geslaagd, gaat u naar de blade **Apps** in de Azure-portal. U ziet hier de status van de toepassing.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

In de volgende procedure wordt gebruikgemaakt van de IntelliJ-invoegtoepassing voor Azure Spring Cloud om de voorbeeld-app te implementeren in de IntelliJ IDEA.  

### <a name="import-project"></a>Project importeren

1. Open het dialoogvenster **Welkom** van IntelliJ, en selecteer **Project importeren** om de wizard Importeren te openen.
1. Selecteer de map `hellospring`.

    ![Project importeren](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>De app implementeren
Als u wilt implementeren in Azure, moet u zich aanmelden met uw Azure-account en uw abonnement kiezen.  Zie [Installatie en aanmelding](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) voor meer informatie over aanmelden.

1. Klik met de rechtermuisknop op uw project in de projectverkenner van IntelliJ en selecteer **Azure** -> **Implementeren in Azure Spring Cloud**.

    [ ![Implementeren in Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Accepteer de naam voor de app in het veld **Naam**. **Naam** verwijst naar de configuratie, niet naar de naam van de app. Gebruikers hoeven deze doorgaans niet te wijzigen.
1. Selecteer in het tekstvak **Artefact** de optie *hellospring-0.0.1-SNAPSHOT.jar*.
1. Controleer uw abonnement in het tekstvak **Abonnement**.
1. Selecteer in het tekstvak **Spring Cloud** het exemplaar van Azure Spring Cloud dat u hebt gemaakt in [Azure Spring Cloud-exemplaar inrichten](./spring-cloud-quickstart-provision-service-instance.md).
1. Stel **Openbaar eindpunt** in op *Inschakelen*.
1. Selecteer in het tekstvak **App:** de optie **App maken...** .
1. Voer *hellospring* in en klik vervolgens op **OK**.

    [ ![Implementeren in Azure OK](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Start de implementatie door onder in het dialoogvenster **Azure Spring Cloud-app implementeren** op de knop **Uitvoeren** te klikken. De invoegtoepassing voert de opdracht `mvn package` uit in de app `hellospring` en implementeert het JAR-bestand dat is gegenereerd met de opdracht `package`.
---

Zodra de implementatie is voltooid, hebt u toegang tot de app op `https://<service instance name>-hellospring.azuremicroservices.io/`.

  [ ![Toegang tot de app vanuit de browser](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Streaminglogboeken in real-time

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Gebruik de volgende opdracht om real-time logboeken op te halen in de app.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Logboeken worden weergegeven in de resultaten:

[ ![Streaminglogboeken](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Gebruik `az spring-cloud app logs -h` om meer parameters en functionaliteit van logboekstreaming te verkennen.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Selecteer **Azure Explorer**  en **Spring Cloud**.
1. Klik met de rechtermuisknop op de app die wordt uitgevoerd.
1. Selecteer **Streaminglogboeken** in de vervolgkeuzelijst.
1. Selecteer het exemplaar.

    [ ![Streaminglogboeken selecteren](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Het streaminglogboek wordt weergegeven in het uitvoervenster.

    [ ![Uitvoer van streaminglogboeken](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Ga naar het tabblad **Logboeken** in het menu van de [Azure-portal](https://portal.azure.com/) voor geavanceerde functies voor logboekanalyse. Logboeken hebben hier een latentie van enkele minuten.

[ ![Logboekanalyse](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

In de vorige stappen hebt u Azure-resources gemaakt waarmee de kosten blijven toenemen zolang de resources in uw abonnement blijven. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep uit de portal of door de volgende opdracht in Azure CLI uit te voeren:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een eenvoudig Azure Spring Cloud-project genereren
> * Een service-exemplaar inrichten
> * De app bouwen en implementeren met een openbaar eindpunt
> * Logboeken in real-time streamen

Als u wilt weten hoe u nog meer Azure Spring-mogelijkheden kunt gebruiken, gaat u verder met de quickstart-reeks waarin een voorbeeldtoepassing wordt geïmplementeerd in Azure Spring Cloud:

> [!div class="nextstepaction"]
> [Microservices bouwen en uitvoeren](spring-cloud-quickstart-sample-app-introduction.md)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
