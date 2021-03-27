---
title: Aggregatie van Azure Service Fabric-gebeurtenissen met Event flow
description: Meer informatie over het samen voegen en verzamelen van gebeurtenissen met Event flow voor bewaking en diagnose van Azure Service Fabric-clusters.
ms.topic: conceptual
ms.date: 2/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a502fe05a862893d2c4879a6976d8d8d1044cbf
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627758"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Gebeurtenis aggregatie en verzameling met behulp van Event flow

[Micro soft Diagnostics Event flow](https://github.com/Azure/diagnostics-eventflow) kan gebeurtenissen van een knoop punt naar een of meer bewakings bestemmingen routeren. Omdat deze is opgenomen als een NuGet-pakket in uw service project, wordt de Event flow-code en-configuratie met de service getransporteerd, waardoor het configuratie probleem per knoop punt dat eerder wordt vermeld bij Azure Diagnostics. Event flow wordt binnen uw service proces uitgevoerd en maakt rechtstreeks verbinding met de geconfigureerde uitvoer. Vanwege de directe verbinding werkt Event flow voor Azure, containers en on-premises service-implementaties. Wees voorzichtig als u Event flow uitvoert in scenario's met hoge dichtheid, zoals in een container, omdat elke event flow-pijp lijn een externe verbinding maakt. Als u meerdere processen host, krijgt u dus verschillende uitgaande verbindingen! Dit is niet zo veel belang rijk voor Service Fabric toepassingen, omdat alle replica's van een `ServiceType` run in hetzelfde proces worden beperkt, waardoor het aantal uitgaande verbindingen wordt overschreden. Event flow biedt ook gebeurtenis filtering, zodat alleen de gebeurtenissen worden verzonden die overeenkomen met het opgegeven filter.

## <a name="set-up-eventflow"></a>Event flow instellen

Event flow binaire bestanden zijn beschikbaar als een set NuGet-pakketten. Om event flow toe te voegen aan een Service Fabric service project, klikt u met de rechter muisknop op het project in de Solution Explorer en kiest u NuGet-pakketten beheren. Ga naar het tabblad ' Bladeren ' en zoek naar ' `Diagnostics.EventFlow` ':

![Event flow NuGet-pakketten in Visual Studio NuGet Package Manager-gebruikers interface](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

U ziet een lijst met verschillende pakketten die worden weer gegeven met "inputs" en "outputs". Event flow ondersteunt diverse verschillende registratie providers en analyse functies. De service hosting Event flow moet toepasselijke pakketten bevatten, afhankelijk van de bron en bestemming voor de toepassings Logboeken. Naast het kern pakket ServiceFabric hebt u ook ten minste één invoer-en uitvoer configuratie nodig. U kunt bijvoorbeeld de volgende pakketten toevoegen om event source-gebeurtenissen te verzenden naar Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` gegevens van de Event source-klasse van de service vastleggen en van standaard EventSources zoals *micro soft-ServiceFabric-Services* en *micro soft-ServiceFabric-actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (we gaan de logboeken verzenden naar een Azure-toepassing Insights-resource)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(Hiermee wordt de initialisatie van de Event flow-pijp lijn vanuit Service Fabric service configuratie ingeschakeld en worden eventuele problemen met het verzenden van diagnostische gegevens gerapporteerd als Service Fabric status rapporten)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` voor het pakket moet het service project zijn gericht op .NET Framework 4,6 of nieuwer. Zorg ervoor dat u het juiste doel raamwerk in project eigenschappen hebt ingesteld voordat u dit pakket installeert.

Nadat alle pakketten zijn geïnstalleerd, is de volgende stap het configureren en inschakelen van Event flow in de service.

## <a name="configure-and-enable-log-collection"></a>Logboek verzameling configureren en inschakelen
De Event flow-pijp lijn die verantwoordelijk is voor het verzenden van de logboeken wordt gemaakt op basis van een specificatie die is opgeslagen in een configuratie bestand. Het `Microsoft.Diagnostics.EventFlow.ServiceFabric` pakket installeert een start event flow-configuratie bestand in de `PackageRoot\Config` map Solution, met de naam `eventFlowConfig.json` . Dit configuratie bestand moet worden gewijzigd om gegevens op te halen uit de standaard service `EventSource` klasse en alle andere invoer die u wilt configureren, en om gegevens naar de juiste locatie te verzenden.

>[!NOTE]
>Als uw project bestand de Visual Studio 2017-indeling heeft `eventFlowConfig.json` , wordt het bestand niet automatisch toegevoegd. U kunt dit probleem oplossen door het bestand in de map te maken `Config` en de opbouw actie in te stellen op `Copy if newer` . 

Hier volgt een *voor beeldeventFlowConfig.jsop* basis van de hierboven genoemde NuGet-pakketten:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

De naam van de service ServiceEventSource is de waarde van de eigenschap name van de `EventSourceAttribute` toegepast op de klasse ServiceEventSource. Het is allemaal opgegeven in het `ServiceEventSource.cs` bestand, dat deel uitmaakt van de service code. In het volgende code fragment is bijvoorbeeld de naam van de ServiceEventSource *mijn bedrijf-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Houd er rekening mee dat het `eventFlowConfig.json` bestand deel uitmaakt van het service configuratie pakket. Wijzigingen in dit bestand kunnen worden opgenomen in alleen-of configuratie-upgrades van de service, afhankelijk van Service Fabric status controles voor upgrades en automatisch terugdraaien als er een upgrade fout optreedt. Zie [service Fabric Application upgrade](service-fabric-application-upgrade.md)(Engelstalig) voor meer informatie.

In het gedeelte *filters* van de configuratie kunt u de informatie die door de Event flow-pijp lijn wordt door gegeven aan de uitvoer verder aanpassen, zodat u bepaalde gegevens kunt neerzetten of toevoegen, of de structuur van de gebeurtenis gegevens kunt wijzigen. Zie [Event flow filters](https://github.com/Azure/diagnostics-eventflow#filters)voor meer informatie over filteren.

De laatste stap bestaat uit het instantiëren van de Event flow-pijp lijn in de opstart code van uw service, die zich in het bestand bevindt `Program.cs` :

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

De naam die wordt door gegeven als de para meter van de- `CreatePipeline` methode `ServiceFabricDiagnosticsPipelineFactory` is de naam van de *status entiteit* die de pijp lijn van de Event flow-logboek verzameling vertegenwoordigt. Deze naam wordt gebruikt als Event flow stuiten en fout en deze rapporteert via het subsysteem Service Fabric Health.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Service Fabric instellingen en toepassings parameters gebruiken in eventFlowConfig

Event flow ondersteunt het gebruik van Service Fabric instellingen en toepassings parameters voor het configureren van Event flow-instellingen. U kunt de para meters voor Service Fabric-instellingen gebruiken met deze speciale syntaxis voor waarden:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` de naam is van de sectie Service Fabric configuratie, en `<setting-name>` is de configuratie-instelling die de waarde levert die wordt gebruikt om een event flow-instelling te configureren. Ga voor meer informatie over hoe u dit doet naar [ondersteuning voor service Fabric instellingen en toepassings parameters](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verificatie

Start uw service en Bekijk het uitvoer venster voor fout opsporing in Visual Studio. Nadat de service is gestart, moet u eerst bewijzen bekijken dat uw service records verzendt naar de uitvoer die u hebt geconfigureerd. Ga naar het analyse-en visualisatie platform van uw gebeurtenis en controleer of Logboeken zijn gestart om weer te geven (dit kan enkele minuten duren).

## <a name="next-steps"></a>Volgende stappen

* [Gebeurtenis analyse en visualisatie met Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Gebeurtenis analyse en visualisatie met Azure Monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md)
* [Documentatie voor Event flow](https://github.com/Azure/diagnostics-eventflow)
