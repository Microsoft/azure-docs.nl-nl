---
title: ASP.NET Core-services bewaken en er diagnoses op uitvoeren
description: In deze zelfstudie leert u hoe u bewakings- en diagnostische gegevens configureert voor een ASP.NET Core-toepassing in Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e7fe68c2d0c51ffcc67693da722d9243ea3506f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91840792"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Zelfstudie: Bewaking en diagnose van een ASP.NET Core-toepassing in Service Fabric met behulp van Application Insights

Deze zelfstudie is deel vijf van een serie. U doorloopt de stappen om controle en diagnose te configureren met behulp van Application Insights voor een ASP.NET Core-toepassing die wordt uitgevoerd op een Service Fabric-cluster. U verzamelt de telemetrie van de toepassing die in het eerste deel van deze zelfstudie is ontwikkeld: [Een .NET Service Fabric-toepassing bouwen](service-fabric-tutorial-create-dotnet-app.md).

In deel vier van de reeks zelfstudies leert u het volgende:
> [!div class="checklist"]
> * Application Insights configureren voor uw toepassing
> * Antwoordtelemetrie verzamelen om HTTP-gebaseerde communicatie tussen services te traceren
> * De App Map-functie in Application Insights gebruiken
> * Aangepaste gebeurtenissen toevoegen met behulp van de Application Insights-API

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een .NET Service Fabric-toepassing bouwen](service-fabric-tutorial-create-dotnet-app.md)
> * [De toepassing implementeren in een extern cluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Een HTTPS-eindpunt toevoegen aan een front-endservice van ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [CI/CD configureren met behulp van Azure-pijplijnen](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Controle en diagnostische gegevens voor de toepassing instellen

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installeer Visual Studio 2019](https://www.visualstudio.com/) en installeer de workloads **Azure-ontwikkeling** en **ASP.NET-ontwikkeling en webontwikkeling**.
* [Installeer de Service Fabric-SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>De voorbeeldtoepassing om te stemmen downloaden

Als u in [deel één van deze zelfstudiereeks](service-fabric-tutorial-create-dotnet-app.md) niet de voorbeeldtoepassing om te stemmen hebt gemaakt, kunt u deze downloaden. Voer in een opdrachtvenster of terminal de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen op de lokale computer.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Een Application Insights-resource instellen

Application Insights is het beheerplatform voor toepassingsprestaties van Azure en het aanbevolen platform van Service Fabric om toepassingen te controleren en te onderzoeken.

Als u een Application Insights-resource wilt maken, navigeert u naar [Azure Portal](https://portal.azure.com). Selecteer **Een resource maken** in het navigatiemenu links om Azure Marketplace te openen. Selecteer **Bewaking + beheer** en vervolgens op **Application Insights**.

![Een nieuwe AI-resource maken](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

U moet de vereiste informatie over de kenmerken van de te maken resource invullen. Voer een passende *Naam* in, evenals een *Resourcegroep* en een *Abonnement*. Stel de *Locatie* in waar u uw Service Fabric-cluster in de toekomst wilt implementeren. In deze zelfstudie implementeert u de app in een lokaal cluster, dus het veld *Locatie* is niet relevant. Het *Toepassingstype* moet worden behouden als 'ASP.NET-webtoepassing'.

![AI-resourcekenmerken](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Nadat u de vereiste gegevens hebt ingevuld, selecteert u **Maken** om de resource in te richten, wat ongeveer een minuut duurt.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Application Insights toevoegen aan de services van de toepassing

Start Visual Studio 2019 door met de rechtermuisknop op het pictogram van Visual Studio in het menu Start te klikken en **Als administrator uitvoeren** te kiezen. Selecteer **Bestand** > **Openen** > **Project/oplossing** en navigeer naar de toepassing Voting (gemaakt in deel één van de zelfstudie of gekloond van git). Open *Voting.sln*. Als u wordt gevraagd om de NuGet-pakketten van de toepassing te herstellen, selecteert u **Ja**.

Volg deze stappen voor om Application Insights voor zowel VotingWeb- als VotingData-services te configureren:

1. Klik met de rechtermuisknop op de naam van de service en selecteer **Toevoegen > Verbonden Services > Bewaking met Application Insights**.

    ![AI Configureren](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>Afhankelijk van het projecttype moet u, wanneer u met de rechtermuisknop op de naam van de service klikt, mogelijk Toevoegen -> Application Insights Telemetry ... selecteren

2. Selecteer **Aan de slag**.
3. Meld u aan bij het account wat u ook gebruikt voor uw Azure-abonnement en selecteer het abonnement waarin u de Application Insights-resource hebt gemaakt. U vindt de resource onder *Bestaande Application Insights-resource* in de vervolgkeuzelijst 'Resource'. Selecteer **Registreren** om Application Insights toe te voegen aan uw service.

    ![AI registreren](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Klik op **Voltooien** nadat het dialoogvenster dat verschijnt, de actie heeft voltooid.

> [!NOTE]
> Zorg ervoor dat u de bovenstaande stappen uitvoert voor **beide** services in de toepassing om de configuratie van Application Insights voor de toepassing te voltooien.
> Dezelfde Application Insights-resource wordt gebruikt voor beide services om binnenkomende en uitgaande aanvragen en communicatie tussen de services te bekijken.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Voeg de NuGet Microsoft.ApplicationInsights.ServiceFabric.Native toe aan de services

Application Insights heeft twee Service Fabric-specifieke NuGets die afhankelijk van het scenario kunnen worden gebruikt. Eén wordt met de native services van Service Fabric gebruikt, de ander met de containers en uitvoerbare bestanden van derden. In dit geval gebruikt u de NuGet Microsoft.ApplicationInsights.ServiceFabric.Native om het begrip van de servicecontext dat u erdoor krijgt, goed te benutten. Bekijk [Microsoft Application Insights voor Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) om meer te weten te komen over de Application Insights-SDK en de Service Fabric-specifieke NuGet-pakketten.

Hier volgen de stappen om het NuGet-pakket in te stellen:

1. Klik met de rechtermuisknop op **Oplossing 'Voting'** boven in uw Solution Explorer en selecteer **NuGet-pakketten beheren voor Oplossing...** .
2. Selecteer **Bladeren** in het bovenste navigatiemenu van het venster 'NuGet - oplossing' en vink het vakje **Voorlopige versie opnemen** naast de zoekbalk aan.
>[!NOTE]
>Wellicht moet u het pakket Microsoft.ServiceFabric.Diagnostics.Internal op vergelijkbare wijze installeren als het pakket niet vooraf is geïnstalleerd, voordat u het Application Insights-pakket installeert.

3. Zoek naar `Microsoft.ApplicationInsights.ServiceFabric.Native` en selecteer het juiste NuGet-pakket.
4. Vink rechts de twee vakjes aan naast de twee services in de toepassing, **VotingWeb** en **VotingData**, en selecteer **Installeren**.
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Selecteer **OK** in het dialoogvenster *Preview Wijzigingen* dat verschijnt en accepteer de *Licentie-acceptatie*. Hiermee wordt de NuGet aan de services toegevoegd.
6. U moet nu de initialisatiefunctie voor telemetrie in de twee services instellen. Daarvoor opent u *VotingWeb.cs* en *VotingData.cs*. Voor beide volgt u de volgende twee stappen:
    1. Voeg deze twee *met behulp van*-instructies toe aan het begin van elk *\<ServiceName>.cs*, na de bestaande *met behulp van*-instructies:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. In beide bestanden, voeg in de geneste *retourinstructie* van *CreateServiceInstanceListeners()* of *CreateServiceReplicaListeners()* onder *ConfigureServices* > *services*, tussen de twee gedeclareerde singleton-services, het volgende toe:
    ```csharp
    .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
    ```
    Hiermee voegt u de *Servicecontext* toe aan uw telemetrie, zodat u beter inzicht hebt in de bron van uw telemetrie in Application Insights. De geneste *retourinstructie* in *VotingWeb.cs* ziet er als volgt uit:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Zo zou u ook in *VotingData.cs* het volgende moeten kunnen zien:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Controleer of de `UseApplicationInsights()`methode in beide bestanden wordt aangeduid als *VotingWeb.cs* en *VotingData.cs*, zoals hierboven weergegeven.

>[!NOTE]
>Deze voorbeeld-app gebruikt http om de services te laten communiceren. Als u een app ontwikkelt met Service Remoting V2, moet u ook de volgende regels code op dezelfde plaats toevoegen zoals u hierboven hebt gedaan.

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

U bent nu klaar om de toepassing te implementeren. Selecteer bovenaan **Start** (of **F5**), waarna Visual Studio de toepassing bouwt en verpakt, uw lokale cluster instelt en de toepassing erin implementeert.

>[!NOTE]
>Mogelijk wordt er een opbouwfout weergegeven als u niet beschikt over een actuele versie van de .NET Core SDK geïnstalleerd.

Nadat de toepassing is geïmplementeerd, gaat u naar `localhost:8080` waar u de toepassing met één pagina "Stemmingsvoorbeeld" zou moeten zien. Stem op een paar verschillende items naar keuze om voorbeeldgegevens en -telemetrie te maken. Hieronder zijn toetjes gebruikt.

![AI-voorbeeldstemmen](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Voel u ook vrij om een aantal van de stemopties te *verwijderen* nadat u een paar stemmen hebt toegevoegd.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>De telemetrie en het toepassingsoverzicht in Application Insights bekijken

Ga naar uw Application Insights-resource in Azure Portal.

Selecteer **Overzicht** om terug te gaan naar de landingspagina van uw resource. Selecteer vervolgens **Zoeken** in de rechterbovenhoek om de traceringen binnen te zien komen. Het duurt enkele minuten voordat de traceringen in Application Insights verschijnen. In het geval dat u er geen ziet, wacht u even en klikt u bovenaan op de knop **Vernieuwen**.
![Traceringen zien in AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Als u naar beneden scrolt in het venster *Zoeken*, ziet u alle binnenkomende telemetrie die u bij Application Insights meegeleverd krijgt. Voor elke actie die u hebt genomen in de toepassing Voting, zou er een uitgaande PUT-aanvraag van *VotingWeb* (PUT Votes/Put [naam]) moeten zijn, een binnenkomende PUT-aanvraag van *VotingData* (PUT VoteData/Put [naam]), gevolgd door een paar GET-aanvragen om de gegevens die worden weergegeven, te vernieuwen. Bovendien is er een afhankelijkheidstracering voor HTTP op localhost, aangezien er HTTP-aanvragen zijn. Hier volgt een voorbeeld van wat u ziet wanneer er een stem wordt toegevoegd:

![AI-voorbeeld aanvraagtracering](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

U kunt een van deze traceringen selecteren om er meer informatie over te bekijken. Er is nuttige informatie over de aanvraag die wordt geleverd door Application Insights, waaronder de *Reactietijd* en de *Aanvraag-URL*. Aangezien u de Service Fabric-specifieke NuGet hebt toegevoegd, krijgt u bovendien extra gegevens over uw toepassing in de context van een Service Fabric-cluster in de onderstaande sectie *Aangepaste gegevens*. Dit omvat de servicecontext, zodat u *PartitionID* en *ReplicaId* van de bron van de aanvraag kunt zien en problemen beter kunt lokaliseren wanneer u fouten in uw toepassing opspoort.

![AI-traceringsdetails](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Bovendien kunt u *Toepassing toewijzen* selecteren in het linkermenu op de pagina Overzicht of het pictogram **App-kaart** selecteren om de App-kaart weer te geven met de twee verbonden services.

![Schermopname waarin de App-kaart in het linkermenu is gemarkeerd.](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

Met het Toepassingsoverzicht kunt u beter inzicht krijgen in uw toepassingstopologie, vooral wanneer u meerdere verschillende services toevoegt die met elkaar samenwerken. U krijgt ook eenvoudige gegevens over succespercentages van aanvragen en inzicht in mislukte aanvragen om te zien waar iets fout kan zijn gegaan. Bekijk [Application Map in Application Insights](../azure-monitor/app/app-map.md) (Toepassingsoverzicht in Application Insights) voor meer informatie over het gebruik van het toepassingsoverzicht.

## <a name="add-custom-instrumentation-to-your-application"></a>Aangepaste instrumentatie toevoegen aan uw toepassing

Hoewel Application Insights bij de aankoop al veel telemetrie biedt, kunt aanvullende aangepaste instrumentatie toevoegen. Dit kan gebaseerd zijn op de behoeften van uw bedrijf of om de diagnose te verbeteren wanneer er iets fout gaat in uw toepassing. Application Insights heeft een API om aangepaste gebeurtenissen en metrische gegevens op te nemen. [Hier](../azure-monitor/app/api-custom-events-metrics.md) kunt u daar meer over lezen.

Laten we eens wat aangepaste gebeurtenissen aan *VoteDataController.cs* toevoegen (onder *VotingData* > *Controllers*) om bij te houden wanneer er stemmen worden toegevoegd en verwijderd uit de onderliggende *votesDictionary*.

1. Voeg `using Microsoft.ApplicationInsights;` toe aan het einde van de andere using-instructies.
2. Geef een nieuwe *TelemetryClient* op aan het begin van de klasse, bij het maken van de *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. In de functie *Put()* voegt u een gebeurtenis toe waarmee wordt bevestigd dat er een stem is toegevoegd. Voeg `telemetry.TrackEvent($"Added a vote for {name}");` toe nadat de transactie is voltooid, recht voor de retour-instructie *OkResult*.
4. In *Delete()* is er een 'als/anders' gebaseerd op de voorwaarde dat de *votesDictionary* stemmen voor een bepaalde optie bevat.
    1. Voeg een gebeurtenis toe waarmee de verwijdering van een stem wordt bevestigd in de instructie *als*, na de *await tx.CommitAsync()* : `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Voeg een gebeurtenis toe om te demonstreren dat de verwijdering niet plaatsvond in de instructie *anders*, voor de retourinstructie: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Hier is een voorbeeld van hoe uw functies *Put()* en *Delete()* eruit kunnen zien voor u de gebeurtenissen toevoegt:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Nadat u deze wijzigingen hebt doorgevoerd, **Start** u de toepassing zodat deze de meest recente versie ervan bouwt en implementeert. Wanneer de toepassing is geïmplementeerd, gaat u naar `localhost:8080` om wat stemopties toe te voegen en te verwijderen. Ga vervolgens terug naar uw Application Insights-resource om de traceringen van de laatste uitvoering te bekijken (het kan net als eerst 1-2 minuten duren voordat traceringen zijn te zien in Application Insights). Voor alle stemmen die u hebt toegevoegd en verwijderd, zou u nu een 'Aangepaste gebeurtenis'\* moet zien, samen met alle reactietelemetrie.

![aangepaste gebeurtenissen](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:
> [!div class="checklist"]
> * Application Insights configureren voor uw toepassing
> * Antwoordtelemetrie verzamelen om HTTP-gebaseerde communicatie tussen services te traceren
> * De App Map-functie in Application Insights gebruiken
> * Aangepaste gebeurtenissen toevoegen met behulp van de Application Insights-API

Nu u controle en diagnostische gegevens voor uw ASP.NET-toepassing hebt ingesteld, kunt u het volgende proberen:

* [Meer informatie over controle en diagnostische gegevens in Service Fabric](service-fabric-diagnostics-overview.md)
* [Service Fabric event analysis with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Service Fabric-gebeurtenisanalyse met Application Insights)
* Bekijk [Application Insights-documentatie](/azure/application-insights/) om meer te weten te komen over Application Insights
