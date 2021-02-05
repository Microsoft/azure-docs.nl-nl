---
title: Prestatiequery's aan serverzijde
description: Hoe kunt u prestatie query's aan de server zijde uitvoeren via API-aanroepen
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594092"
---
# <a name="server-side-performance-queries"></a>Prestatiequery's aan serverzijde

Goede weergave prestaties op de server zijn essentieel voor stabiele frame snelheden en een goede gebruikers ervaring. Het is belang rijk om de prestatie kenmerken van de server zorgvuldig te controleren en waar nodig te optimaliseren. Prestatie gegevens kunnen worden opgevraagd via speciale API-functies.

De meest impact op de weergave prestaties zijn de gegevens van het model invoer. U kunt de invoer gegevens aanpassen, zoals wordt beschreven in [de model conversie configureren](../../how-tos/conversion/configure-model-conversion.md).

Prestaties van toepassingen aan client zijde kunnen ook een knel punt zijn. Voor een diep gaande analyse van prestaties aan client zijde is het raadzaam om een te maken [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Tijd lijn van client/server

Voordat u meer informatie krijgt over de verschillende latentie waarden, is het de moeite waard om de synchronisatie punten van de client en de server op de tijd lijn te bekijken:

![Pipeline-tijd lijn](./media/server-client-timeline.png)

In de afbeelding ziet u hoe:

* de *schatting* van een pose wordt gestart door de client bij constante 60-Hz frame snelheid (elke 16,6 MS)
* de server wordt vervolgens weer gegeven op basis van de pose
* de server stuurt de gecodeerde video-afbeelding terug
* de client decodeert de installatie kopie, voert een aantal CPU-en GPU-werkzaamheden uit en geeft vervolgens de installatie kopie weer

## <a name="frame-statistics-queries"></a>Query's voor frame statistieken

Frame statistieken bieden gegevens op hoog niveau voor het laatste frame, zoals latentie. De gegevens die in de `FrameStatistics` structuur worden gegeven, worden gemeten aan de client zijde, zodat de API een synchrone aanroep is:

```cs
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

Het opgehaalde `FrameStatistics` object bevat de volgende leden:

| Lid | Uitleg |
|:-|:-|
| LatencyPoseToReceive | Latentie van camera is een schatting op het client apparaat totdat een server frame voor deze pose volledig beschikbaar is voor de client toepassing. Deze waarde omvat netwerk retour, Server weergave tijd, video decoderen en jitter-compensatie. Zie **interval 1 in de bovenstaande afbeelding.**|
| LatencyReceiveToPresent | Latentie van de beschik baarheid van een ontvangen extern frame totdat de client-app PresentFrame op de CPU aanroept. Zie **interval 2 in de bovenstaande afbeelding.**|
| LatencyPresentToDisplay  | Latentie van het presen teren van een frame op de CPU totdat de lampjes omhoog worden weer gegeven. Deze waarde omvat de GPU-tijd van de client, alle frame buffers die worden uitgevoerd door het besturings systeem, de hardwareconfiguratie en de weer gave van het apparaat afhankelijk van het scannen. Zie **interval 3 in de bovenstaande afbeelding.**|
| TimeSinceLastPresent | De tijd tussen de volgende aanroepen naar PresentFrame op de CPU. Waarden die groter zijn dan de weergave duur (bijvoorbeeld 16,6 MS op een 60-Hz-client apparaat) geven aan dat er problemen zijn veroorzaakt doordat de client toepassing niet de CPU-werk belasting in de tijd heeft voltooid.|
| VideoFramesReceived | Het aantal frames dat in de laatste seconde van de server is ontvangen. |
| VideoFrameReusedCount | Aantal ontvangen frames in de laatste seconde dat meermaals is gebruikt op het apparaat. Waarden die niet gelijk zijn aan nul geven aan dat frames opnieuw moeten worden gebruikt en moeten worden geprojecteerd als gevolg van netwerk-jitter of overmatige rendering van de server. |
| VideoFramesSkipped | Het aantal ontvangen frames in de laatste seconde dat is gedecodeerd, maar niet weer gegeven in de weer gave omdat een nieuw frame is aangekomen. Waarden die niet gelijk zijn aan nul geven aan dat netwerk-jittering meerdere frames heeft veroorzaakt en vervolgens in een burst op het client apparaat arriveert. |
| VideoFramesDiscarded | Vergelijkbaar met **VideoFramesSkipped**, maar de reden voor het weglaten van een frame is dat er een kader is dat het niet zelfs kan worden gecorreleerd met een in behandeling zijnde pose. Als dit probleem optreedt, is er sprake van een ernstige netwerk conflict.|
| VideoFrameMinDelta | Minimale hoeveelheid tijd tussen twee opeenvolgende frames die in de afgelopen seconde arriveren. Samen met VideoFrameMaxDelta geeft dit bereik een indicatie van de jitter die is veroorzaakt door de netwerk-of videocodec. |
| VideoFrameMaxDelta | Maximale tijd tussen twee opeenvolgende frames die in de afgelopen seconde arriveren. Samen met VideoFrameMinDelta geeft dit bereik een indicatie van de jitter die is veroorzaakt door de netwerk-of videocodec. |

De som van alle latentie waarden is doorgaans veel groter dan de beschik bare frame tijd bij 60 Hz. Dit is OK, omdat meerdere frames in de vlucht parallel zijn en er nieuwe frame aanvragen worden afgesteld op de gewenste frame snelheid, zoals wordt weer gegeven in de afbeelding. Als latentie echter te groot wordt, is dit van invloed op de kwaliteit van de [vertraagde fase](../../overview/features/late-stage-reprojection.md)van het project en kan de algehele ervaring worden aangetast.

`VideoFramesReceived`, `VideoFrameReusedCount` en `VideoFramesDiscarded` kunnen worden gebruikt om de prestaties van het netwerk en de server te meten. Een combi natie van een lage `VideoFramesReceived` waarde en een hoge `VideoFrameReusedCount` waarde kan duiden op netwerk congestie of slechte server prestaties. Een hoge `VideoFramesDiscarded` waarde duidt ook op netwerk congestie.

Ten slotte,, `TimeSinceLastPresent` `VideoFrameMinDelta` en `VideoFrameMaxDelta` geeft u een idee van de variantie van binnenkomende video frames en lokale huidige aanroepen. Hoge variantie betekent een instabiele frame frequentie.

Geen van de bovenstaande waarden geeft een duidelijke indicatie van de zuivere netwerk latentie (de rode pijlen in de afbeelding), omdat de exacte tijd die de server bezet is, moet worden afgetrokken van de retour waarde `LatencyPoseToReceive` . Het gedeelte aan de server zijde van de totale latentie is informatie die niet beschikbaar is voor de client. In de volgende alinea wordt echter uitgelegd hoe deze waarde wordt benaderd via extra invoer van de server en wordt weer gegeven via de `NetworkLatency` waarde.

## <a name="performance-assessment-queries"></a>Query's voor prestatie beoordeling

*Query's voor prestatie beoordeling* bieden meer gedetailleerde informatie over de CPU en GPU-workload op de-server. Omdat de gegevens van de server worden opgevraagd, volgt het uitvoeren van een query op een prestatie momentopname het gebruikelijke asynchrone patroon:

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

Het object bevat in tegens telling tot het `FrameStatistics` object gegevens aan de `PerformanceAssessment` server zijde:

| Lid | Uitleg |
|:-|:-|
| TimeCPU | Gemiddelde CPU-tijd van de server per frame in milliseconden |
| TimeGPU | Gemiddelde server GPU-tijd per frame in milliseconden |
| UtilizationCPU | Totaal CPU-verbruik van server in procenten |
| UtilizationGPU | Totale server GPU-gebruik in procenten |
| MemoryCPU | Totaal server hoofd geheugen gebruik in procenten |
| MemoryGPU | Totaal toegewezen video geheugen gebruik als percentage van de server-GPU |
| NetworkLatency | De gemiddelde latentie bij benadering van het retour netwerk in milliseconden. In de bovenstaande afbeelding komt deze waarde overeen met de som van de rode pijlen. De waarde wordt berekend door het aftrekken van de werkelijke server rendering-tijd van de `LatencyPoseToReceive` waarde van `FrameStatistics` . Hoewel deze benadering niet nauw keurig is, geeft deze een indicatie van de netwerk latentie, geïsoleerd van de latentie waarden die op de client zijn berekend. |
| PolygonsRendered | Het aantal drie hoeken dat in één frame wordt weer gegeven. Dit aantal bevat ook de drie hoeken die later tijdens de rendering worden geruimen. Dit betekent dat dit aantal niet veel verschilt tussen verschillende camera posities, maar de prestaties kunnen aanzienlijk variëren, afhankelijk van het berekenings niveau van de drie hoek.|

Om u te helpen bij het beoordelen van de waarden, wordt elk deel geleverd met een kwaliteits classificatie zoals **geweldig**, **goed**, **mediocre** of **slecht**.
Deze beoordelings metriek biedt een ruwe indicatie van de status van de server, maar mag niet worden gezien als absoluut. Stel dat u een ' mediocre-score voor de GPU-tijd ziet. Het wordt aangemerkt als mediocre omdat de limiet voor het totale frame tijd budget bijna wordt bereikt. In uw geval kan dit echter een goede waarde zijn, omdat u een complex model rendert.

## <a name="statistics-debug-output"></a>Statistieken debug-uitvoer

De klasse `ServiceStatistics` is een C#-klasse die omloopt rond de frame statistieken en prestatie beoordelings query's en biedt handige functionaliteit om statistieken te retour neren als geaggregeerde waarden of als een vooraf gemaakte teken reeks. De volgende code is de eenvoudigste manier om statistieken aan de server zijde in uw client toepassing weer te geven.

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Met de bovenstaande code wordt het tekst label gevuld met de volgende tekst:

![ArrServiceStats teken reeks uitvoer](./media/arr-service-stats.png)

`GetStatsString`Met de API wordt een teken reeks van alle waarden opgemaakt, maar elke enkele waarde kan ook via een programma worden opgevraagd vanuit het `ServiceStatistics` exemplaar.

Er zijn ook varianten van de leden, waarmee de waarden in de loop van de tijd worden geaggregeerd. Zie leden met achtervoegsel `*Avg` , `*Max` of `*Total` . Het lid `FramesUsedForAverage` geeft aan hoeveel frames er voor deze aggregatie zijn gebruikt.

## <a name="api-documentation"></a>API-documentatie

* [C# RenderingConnection. QueryServerPerformanceAssessmentAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection:: QueryServerPerformanceAssessmentAsync ()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Volgende stappen

* [Prestatie traceringen maken](../../how-tos/performance-tracing.md)
* [De model conversie configureren](../../how-tos/conversion/configure-model-conversion.md)