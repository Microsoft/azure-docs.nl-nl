---
title: Een .NET-Linux-toepassing maken voor Service Fabric in Azure
description: In deze zelfstudie vindt u informatie over het maken van een toepassing met een ASP.NET Core front-end en een betrouwbare stateful back-endservice en het implementeren van de toepassing in een cluster.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-js, devx-track-csharp
ms.openlocfilehash: a783f5338dbc7ce0832b346492490525da332e39
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96297114"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Zelfstudie: Een toepassing met een ASP.NET Core web-API front-end service en een stateful back-endservice maken en implementeren

Deze zelfstudie is deel één van een serie.  U leert hoe u een Azure Service Fabric-toepassing met een front-end van ASP.NET Core web-API en een stateful back-endservice maakt voor het opslaan van uw gegevens. Wanneer u klaar bent, hebt u een stemtoepassing met een ASP.NET Core-web-front-end die stemresultaten opslaat in een stateful back-endservice in het cluster. In deze reeks zelfstudies is een Windows-ontwikkelaarscomputer vereist. Als u de stemtoepassing niet handmatig wilt maken, kunt u [de broncode downloaden](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) voor de voltooide toepassing en verdergaan met [Het voorbeeld van een stemtoepassing doorlopen](#walkthrough_anchor).  Als u dat liever doet, kunt u ook een [video](https://channel9.msdn.com/Events/Connect/2017/E100) van deze zelfstudie bekijken.

![AngularJS + ASP. NET API front-end, verbinding maken met een stateful back-end-service op Service Fabric](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Een ASP.NET Core web-API-service als een betrouwbare stateful service maken
> * Een ASP.NET Core-webtoepassingsservice als een stateless webservice maken
> * De omgekeerde proxy gebruiken om te communiceren met de stateful service

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een .NET Service Fabric-toepassing bouwen
> * [De toepassing implementeren in een extern cluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Een HTTPS-eindpunt toevoegen aan een front-endservice van ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [CI/CD configureren met behulp van Azure-pijplijnen](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:
* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer [Visual Studio 2019](https://www.visualstudio.com/) versie 15.5 of hoger met de **Azure-ontwikkelworkload** en de **ASP.NET-ontwikkelings- en webontwikkelingsworkloads**.
* [Installeer de Service Fabric-SDK](service-fabric-get-started.md).

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Een ASP.NET web-API-service als een betrouwbare service maken

Maak eerst de webfront-end van de stemtoepassing met behulp van ASP.NET Core. ASP.NET Core is een lichtgewicht, platformoverschrijdend webontwikkelingsframework dat u kunt gebruiken voor het maken van moderne webgebruikersinterface en web-API's. Voor een completer inzicht in hoe u ASP.NET Core integreert met Service Fabric, raden we u sterk aan het artikel [ASP.NET Core in Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) te lezen. Op dit moment kunt u deze zelfstudie volgen om snel aan de slag te gaan. Zie de [Documentatie bij ASP.NET Core](/aspnet/core/) voor meer informatie over ASP.NET Core.

1. Start Visual Studio als **beheerder**.

2. Maak een project met **File**->**New**->**Project**.

3. Kies in het dialoogvenster **Nieuw Project** de optie **Cloud > Service Fabric-toepassing**.

4. Noem de toepassing **Voting** en klik op **OK**.

   ![Dialoogvenster voor nieuw project in Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Kies op de pagina **New Service Fabric Service** de optie **Stateless ASP.NET Core** en noem uw service **VotingWeb**. Klik daarna op **OK**.
   
   ![ASP.NET-webservice kiezen in het dialoogvenster voor een nieuwe service](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. De volgende pagina bevat een set van ASP.NET Core-projectsjablonen. Kies voor deze zelfstudie, kies **Web Application (Model-View-Controller)** en klik op **OK**.
   
   ![ASP.NET-projecttype kiezen](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio maakt een toepassing en een serviceproject en geeft deze weer in Solution Explorer.

   ![Solution Explorer na het maken van de toepassing met de ASP.NET Core web-API-service]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>Het bestand site.js bijwerken
Open **wwwroot/js/site.js**.  Vervang de inhoud ervan door het volgende JavaScript dat wordt gebruikt door de beginweergaven en sla uw wijzigingen op.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Het bestand Index.cshtml bijwerken

Open **Views/Home/Index.cshtml**, de weergave die specifiek is voor de begincontroller.  Vervang de inhoud door het volgende en sla uw wijzigingen op.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.Key)">
                            <span class="pull-left">
                                {{vote.Key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.Value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.Key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>Het bestand _Layout.cshtml bijwerken

Open **Views/Shared/_Layout.cshtml**, de standaardindeling voor de ASP.NET-app.  Vervang de inhoud door het volgende en sla uw wijzigingen op.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Het bestand VotingWeb.cs bijwerken

Open het bestand *VotingWeb.cs* dat de ASP.NET Core WebHost maakt binnen de stateless service met behulp van de WebListener-webserver.

Plaats de instructie `using System.Net.Http;` boven aan het bestand.

Vervang de functie `CreateServiceInstanceListeners()` door de volgende code en sla uw wijzigingen op.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Voeg ook de volgende `GetVotingDataServiceName`-methode `CreateServiceInstanceListeners()` toe en sla uw wijzigingen op. `GetVotingDataServiceName` retourneert de servicenaam wanneer gepolld.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Het bestand VotesController.cs toevoegen

Voeg een controller toe, waarmee stemacties worden gedefinieerd. Klik met de rechtermuisknop op de map **Controllers** en selecteer **Add->New item->Visual C#->ASP.NET Core->Class**. Noem het bestand **VotesController.cs** en klik op **Add**.  

Vervang de inhoud van het bestand *VotesController.cs* door het volgende en sla uw wijzigingen op.  Verderop, in [Het bestand VotesController.cs bijwerken](#updatevotecontroller_anchor), wordt dit bestand gewijzigd voor het lezen en schrijven van stemgegevens vanaf de back-endservice.  Voor nu retourneert de controller statische tekenreeksgegevens naar de weergave.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>De luisterpoort configureren

Wanneer de front-endservice VotingWeb is gemaakt, selecteert Visual Studio willekeurig een poort voor de service om op te luisteren.  De VotingWeb-service fungeert als de front-end voor deze toepassing en accepteert extern verkeer, dus gaan we die service met een vaste en bekende poort verbinden.  Het [servicemanifest](service-fabric-application-and-service-manifests.md) declareert de service-eindpunten.

Open in Solution Explorer *VotingWeb/PackageRoot/ServiceManifest.xml*.  Zoek het element **Endpoint** in de sectie **Resources** en wijzig de waarde van **Port** in **8080**. Als u de toepassing lokaal wilt implementeren en uitvoeren, moet de luisterende poort van de toepassing open zijn en beschikbaar zijn op uw computer.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Werk ook de eigenschapswaarde van de toepassings-URL in het stemproject bij, zodat een webbrowser op de juiste poort opent wanneer u fouten in de toepassing opspoort.  Selecteer in Solution Explorer het project **Voting** en wijzig de eigenschap **Application URL** in **8080**.

### <a name="deploy-and-run-the-voting-application-locally"></a>De stemtoepassing lokaal implementeren en uitvoeren
U kunt nu verder gaan en de stemtoepassing uitvoeren voor foutopsporing. Druk in Visual Studio op **F5** om de toepassing in de foutopsporingsmodus te implementeren in uw lokale Service Fabric-cluster. De toepassing mislukt als u Visual Studio niet eerder **als administrator** hebt geopend.

> [!NOTE]
> De eerste keer dat u de toepassing lokaal uitvoert en implementeert, wordt door Visual Studio een lokaal Service Fabric-cluster voor foutopsporing gemaakt.  Het maken van een cluster kan enige tijd duren. De status van het maken van het cluster wordt weergegeven in het Visual Studio-uitvoervenster.

Wanneer de stemtoepassing is geïmplementeerd in uw lokale Service Fabric-cluster, wordt de webtoepassing automatisch in een browsertabblad geopend. Deze ziet er als volgt uit:

![ASP.NET Core front-end](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Als u wilt stoppen met de foutopsporing voor de toepassing, gaat u terug naar Visual Studio en drukt u op **Shift + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Een stateful back-endservice toevoegen aan de toepassing

Nu een ASP.NET Web API-service wordt uitgevoerd in de toepassing, gaat u verder en voegt u een stateful betrouwbare service toe voor het opslaan van sommige gegevens in de toepassing.

Service Fabric biedt u de mogelijkheid om uw gegevens consistent en betrouwbaar rechtstreeks in uw service op te slaan met behulp van betrouwbare verzamelingen. Betrouwbare verzamelingen zijn een set van maximaal beschikbare en betrouwbare verzamelingsklassen die bekend zijn voor iedereen die wel eens C#-verzamelingen heeft gebruikt.

In deze zelfstudie maakt u een service die een itemwaarde in een betrouwbare verzameling opslaat.

1. Klik in Solution Explorer met de rechtermuisknop op **Services** in het toepassingsproject Voting en kies **Add > New Service Fabric Service...** .
    
2. Kies in het dialoogvenster **New Service Fabric Service** de optie **Stateful ASP.NET Core**, noem de service **VotingData** en druk op **OK**.

    Nadat uw serviceproject is gemaakt, hebt u twee services in uw toepassing. Als u doorgaat met het bouwen van uw toepassing, kunt u op dezelfde manier meer services toevoegen. Elk kan onafhankelijke versies en upgrades hebben.

3. De volgende pagina bevat een set van ASP.NET Core-projectsjablonen. Voor deze zelfstudie kiest u **API**.

    Visual Studio maakt het serviceproject VotingData en geeft het weer in Solution Explorer.

    ![Solution Explorer](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Het bestand VoteDataController.cs toevoegen

Klik in het project **VotingData** met de rechtermuisknop op de map **Controllers** en selecteer **Add->New item->Class**. Noem het bestand **VoteDataController.cs** en klik op **Add**. Vervang de bestandsinhoud door het volgende en sla uw wijzigingen op.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>De services verbinden

In deze stap verbindt u de twee services en zorgt u dat de front-endwebtoepassing stemgegevens ophaalt en instelt vanuit de back-endservice.

Service Fabric biedt volledige flexibiliteit in hoe u met betrouwbare services communiceert. Binnen één toepassing hebt u mogelijk services die toegankelijk zijn via TCP. Andere services die mogelijk toegankelijk zijn via een HTTP REST-API en nog andere services kunnen toegankelijk zijn via websockets. Zie [Communiceren met services](service-fabric-connect-and-communicate-with-services.md) voor achtergrondinformatie over de beschikbare opties en betrokken afwegingen.

In deze zelfstudie worden [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md) en de [omgekeerde proxy van Service Fabric](service-fabric-reverseproxy.md) gebruikt, zodat de front-endwebservice VotingWeb kan communiceren met de back-endgegevensservice VotingData. De omgekeerde proxy is standaard geconfigureerd voor gebruik van poort 19081 en werkt voor deze zelfstudie. De omgekeerde-proxypoort is ingesteld in het Azure Resource Manager-sjabloon dat is gebruikt voor het instellen van het cluster. Om uit te zoeken welke poort wordt gebruikt, kijkt u in de clustersjabloon in de resource **Microsoft.ServiceFabric/clusters**: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
Als u de omgekeerde proxy-poort wilt zoeken die wordt gebruikt in uw lokale ontwikkelcluster, raadpleegt u het element **HttpApplicationGatewayEndpoint** in het manifest van het lokale Service Fabric-cluster:
1. Open een browservenster en ga naar http:\//localhost:19080 om het hulpprogramma Service Fabric Explorer te openen.
2. Selecteer **Cluster -> Manifest**.
3. Noteer de poort van het element HttpApplicationGatewayEndpoint. Deze is standaard 19081. Als de poort niet 19081 is, moet u de poort in de methode GetProxyAddress van de volgende VotesController.cs-code wijzigen.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Het bestand VotesController.cs bijwerken

Open in het project **VotingWeb** het bestand *Controllers/VotesController.cs*.  Vervang de inhoud van de `VotesController`-klassedefinitie door het volgende en sla uw wijzigingen op. Als de poort van de omgekeerde proxy, die u in de vorige stap hebt ontdekt, niet 19081 is, wijzigt u de poort die wordt gebruikt in de methode GetProxyAddress van 19081 in de poort die u hebt ontdekt.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Stapsgewijs door de voorbeeldstemtoepassing gaan

De stemtoepassing bestaat uit twee services:

* Web-front-endservice (VotingWeb): een ASP.NET Core web-front-endservice die de webpagina ondersteunt en web-API's beschikbaar stelt om te communiceren met de back-endservice.
* Back-endservice (VotingData): een ASP.NET Core-webservice, die een API beschikbaar stelt om de stemresultaten in een betrouwbare woordenlijst op schijf op te slaan.

![Diagram van de toepassing](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Wanneer u in de toepassing stemt, vinden de volgende gebeurtenissen plaats:

1. Een JavaScript verzendt de stemaanvraag als een HTTP PUT-aanvraag naar de web-API in de web-front-endservice.

2. De web-front-endservice gebruikt een proxyserver om een HTTP PUT-aanvraag te vinden en door te sturen naar de back-endservice.

3. De back-endservice neemt de binnenkomende aanvraag aan en slaat het bijgewerkte resultaat op in een betrouwbare woordenlijst die wordt gerepliceerd naar meerdere knooppunten binnen het cluster en wordt opgeslagen op schijf. Gegevens van de toepassing worden opgeslagen in het cluster, zodat er geen database nodig is.

## <a name="debug-in-visual-studio"></a>Fouten opsporen met Visual Studio

Als u met Visual Studio fouten opspoort in de toepassing, gebruikt u daarvoor een lokaal ontwikkelingscluster van Service Fabric. U hebt de mogelijkheid om uw foutopsporingservaring aan uw specifieke scenario aan te passen. In deze toepassing slaat u gegevens in de back-endservice op met behulp van een betrouwbare woordenlijst. Visual Studio verwijdert standaard de toepassing wanneer u het foutopsporingsprogramma stopt. Door de toepassing te verwijderen, worden de gegevens in de back-endservice ook verwijderd. Als u de gegevens tussen de foutopsporingssessies wilt kunnen behouden, kunt u de **foutopsporingsmodus van de toepassing** als eigenschap op het **Voting**-project in Visual Studio wijzigen.

Als u wilt zien wat er in de code gebeurt, moet u de volgende stappen uitvoeren:

1. Open het bestand **VotingWeb\VotesController.cs** en stel in de methode **Put** van de web-API (regel 72) een onderbrekingspunt in.

2. Open het bestand **VotingData\VoteDataController.cs** en stel in de methode **Put** van de web-API (regel 54) een onderbrekingspunt in.

3. Druk op **F5** om de toepassing te starten in de foutopsporingsmodus.

4. Ga terug naar de browser en klik op een stemoptie of voeg een nieuwe stemoptie toe. U komt uit bij het eerste onderbrekingspunt in de API-controller van de web-front-end.
    

   1. Dit is het punt waarop door JavaScript in de browser een aanvraag wordt verzonden naar de web-API-controller in de front-endservice.

      ![Front-endservice van Vote toevoegen](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. Maak eerst de URL naar de ReverseProxy voor de back-endservice **(1)** .
   3. Verzend vervolgens de HTTP PUT-aanvraag naar de ReverseProxy **(2)** .
   4. Tot slot wordt het antwoord van de back-endservice naar de client geretourneerd **(3)** .

5. Druk op **F5** om verder te gaan.
   1. U bent nu op het onderbrekingspunt in de back-endservice aanbeland.

      ![Back-endservice Vote toevoegen](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. Gebruik in de eerste regel in de methode **(1)** de `StateManager` om een betrouwbare woordenlijst met de naam `counts` op te halen of toe te voegen.
   3. Alle interacties met waarden in een betrouwbare woordenlijst vereisen een transactie, met deze instructie **(2)** wordt die transactie gemaakt.
   4. Werk in de transactie de waarde bij van de relevante sleutel voor de stemoptie en voer de bewerking **(3)** door. Zodra de doorvoermethode resultaten retourneert, worden de gegevens bijgewerkt in de woordenlijst en gerepliceerd naar andere knooppunten in het cluster. De gegevens worden nu veilig opgeslagen in het cluster en de back-endservice kan een failover-overschakeling uitvoeren naar andere knooppunten, waarbij de gegevens beschikbaar blijven.
6. Druk op **F5** om verder te gaan.

Als u de foutopsporingssessie wilt stoppen, drukt u op **Shift+F5**.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een ASP.NET Core web-API-service als een betrouwbare stateful service maken
> * Een ASP.NET Core-webtoepassingsservice als een stateless webservice maken
> * De omgekeerde proxy gebruiken om te communiceren met de stateful service

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [De toepassing implementeren in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
