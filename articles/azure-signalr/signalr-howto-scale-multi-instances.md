---
title: Schalen met meerdere exemplaren-Azure signalerings service
description: In veel schaal scenario's moet de klant vaak meerdere instanties inrichten en configureren om ze samen te gebruiken om een grootschalige implementatie te maken. Voor sharding is bijvoorbeeld ondersteuning voor meerdere instanties vereist.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89439211"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hoe kan de signaal service met meerdere exemplaren schalen?
De nieuwste signalerings Service-SDK ondersteunt meerdere eind punten voor de seingevings service-exemplaren. U kunt deze functie gebruiken om de gelijktijdige verbindingen te schalen of deze te gebruiken voor berichten in meerdere regio's.

## <a name="for-aspnet-core"></a>Voor ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hoe kan ik meerdere eind punten toevoegen vanuit de configuratie?

Config met Key `Azure:SignalR:ConnectionString` of `Azure:SignalR:ConnectionString:` voor de Signa lering-Service Connection String.

Als de sleutel begint met `Azure:SignalR:ConnectionString:` , moet deze de indeling hebben `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` , waar `Name` en `EndpointType` Eigenschappen van het object zijn en `ServiceEndpoint` toegankelijk zijn vanuit code.

U kunt meerdere teken reeksen voor instanties toevoegen met behulp van de volgende `dotnet` opdrachten:

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hoe kan ik meerdere eind punten toevoegen vanuit code?

Er `ServicEndpoint` wordt een klasse geïntroduceerd om de eigenschappen van een Azure signalerings service-eind punt te beschrijven.
U kunt meerdere instantie-eind punten configureren wanneer u de Azure signalerings Service-SDK gebruikt via:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Hoe kan ik de eindpunt router aanpassen?

De SDK gebruikt standaard de [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) om eind punten op te halen.

#### <a name="default-behavior"></a>Standaardgedrag 
1. Route ring van client aanvragen

    Wanneer `/negotiate` de client met de app-server. Standaard **selecteert** SDK één eind punt uit de set beschik bare service-eind punten.

2. Server bericht routering

    Wanneer * een bericht verzendt naar een specifieke * *-verbinding * * * en de doel verbinding wordt doorgestuurd naar de huidige server, gaat het bericht rechtstreeks naar dat verbonden eind punt. Anders worden de berichten verzonden naar elk eind punt van Azure signalering.

#### <a name="customize-routing-algorithm"></a>Routerings algoritme aanpassen
U kunt uw eigen router maken wanneer u speciale kennis hebt om te identificeren tot welke eind punten de berichten moeten gaan.

Hieronder wordt een aangepaste router gedefinieerd als voor beeld wanneer groepen beginnen met `east-` altijd naar het eind punt met de naam `east` :

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Een ander voor beeld hieronder, waarbij het standaard onderhandelings gedrag wordt overschreven om de eind punten te selecteren, is afhankelijk van waar de app-server zich bevindt.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Vergeet niet om de router te registreren bij de DI-container met:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Voor ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hoe kan ik meerdere eind punten toevoegen vanuit de configuratie?

Config met Key `Azure:SignalR:ConnectionString` of `Azure:SignalR:ConnectionString:` voor de Signa lering-Service Connection String.

Als de sleutel begint met `Azure:SignalR:ConnectionString:` , moet deze de indeling hebben `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` , waar `Name` en `EndpointType` Eigenschappen van het object zijn en `ServiceEndpoint` toegankelijk zijn vanuit code.

U kunt meerdere exemplaar verbindings reeksen toevoegen aan `web.config` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hoe kan ik meerdere eind punten toevoegen vanuit code?

Er `ServicEndpoint` wordt een klasse geïntroduceerd om de eigenschappen van een Azure signalerings service-eind punt te beschrijven.
U kunt meerdere instantie-eind punten configureren wanneer u de Azure signalerings Service-SDK gebruikt via:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Hoe kan ik een router aanpassen?

Het enige verschil tussen ASP.NET Signalr en ASP.NET Core Signalr is het HTTP-context type voor `GetNegotiateEndpoint` . Voor ASP.NET-Signa lering is het van het type [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) .

Hieronder ziet u het aangepaste voor beeld van onderhandelen voor ASP.NET-Signa lering:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Vergeet niet om de router te registreren bij de DI-container met:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Configuratie in scenario's met meerdere regio's

Het `ServiceEndpoint` object heeft een `EndpointType` eigenschap met waarde `primary` of `secondary` .

`primary` eind punten zijn voorkeurs eindpunten voor het ontvangen van client verkeer en worden beschouwd als een betrouwbaardere netwerk verbinding. `secondary` eind punten worden beschouwd als een minder betrouw bare netwerk verbinding en worden alleen gebruikt voor het maken van de server naar client verkeer, bijvoorbeeld het uitzenden van berichten, niet voor het nemen van client-naar-server verkeer.

In verschillende regio's kan het netwerk Insta Biel zijn. Voor één app-server die zich in *VS-Oost* bevindt, kan het eind punt van de seingevings service die zich in dezelfde regio *VS-Oost* bevindt, worden geconfigureerd als `primary` en eind punten in andere regio's die als zijn gemarkeerd `secondary` . In deze configuratie kunnen service-eind punten in andere regio's berichten **ontvangen** van deze *VS* -server voor Amerikaanse apps, maar er zijn geen clients voor **meerdere regio's** die naar deze app-server worden doorgestuurd. De architectuur wordt weer gegeven in het onderstaande diagram:

![Kruis geografische infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Wanneer een client `/negotiate` met de app-server probeert, met de standaard router, **selecteert** SDK één eind punt uit de set beschik bare `primary` eind punten. Wanneer het primaire eind punt niet beschikbaar is, **selecteert SDK wille keurig** uit alle beschik bare `secondary` eind punten. Het eind punt is gemarkeerd als **beschikbaar** wanneer de verbinding tussen de server en het service-eind punt actief is.

Wanneer een client `/negotiate` met de app-server in *VS-Oost* wordt gehost, wordt het `primary` eind punt dat zich in dezelfde regio bevindt, standaard altijd in het scenario voor meerdere regio's weer gegeven. Wanneer alle *VS-Oost* -eind punten niet beschikbaar zijn, wordt de client omgeleid naar eind punten in andere regio's. In het gedeelte failover over wordt het scenario in detail beschreven.

![Normaal onderhandelen](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Failover

Wanneer alle `primary` eind punten niet beschikbaar zijn, worden `/negotiate` de keuze van de client van de beschik bare `secondary` eind punten. Voor dit failover-mechanisme moet elk eind punt fungeren als een `primary` eind punt naar ten minste één app-server.

![Failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u meerdere exemplaren in dezelfde toepassing kunt configureren voor schalen, sharding en scenario's voor meerdere regio's.

Meerdere endpoints ondersteunen kan ook worden gebruikt in scenario's met hoge Beschik baarheid en herstel na nood gevallen.

> [!div class="nextstepaction"]
> [De signalerings service instellen voor herstel na nood gevallen en hoge Beschik baarheid](./signalr-concept-disaster-recovery.md)
