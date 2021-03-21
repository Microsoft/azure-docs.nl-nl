---
title: Veelvoorkomende Scenario's voor Azure Service Fabric diagnosticeren
description: Meer informatie over het oplossen van veelvoorkomende bewakings-en diagnose scenario's in azure Service Fabric-toepassingen.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3083348948ce97ae9d99098896878d79081a15f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580489"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Veelvoorkomende scenario's met Service Fabric diagnosticeren

In dit artikel ziet u veelvoorkomende scenario's die gebruikers hebben gevonden op het gebied van bewaking en diagnose met Service Fabric. De gepresenteerde scenario's beslaan alle drie de lagen van service Fabric: toepassing, cluster en infra structuur. Elke oplossing maakt gebruik van Application Insights-en Azure Monitor-logboeken, Azure-controle hulpprogramma's, om elk scenario te volt ooien. De stappen in elke oplossing geven gebruikers een inleiding over het gebruik van Application Insights en Azure Monitor-Logboeken in de context van Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Vereisten en aanbevelingen

De oplossingen in dit artikel gebruiken de volgende hulpprogram ma's. U wordt aangeraden deze instellingen in te stellen en te configureren:

* [Application Insights met Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Azure Diagnostics in uw cluster inschakelen](service-fabric-diagnostics-event-aggregation-wad.md)
* [Een Log Analytics-werk ruimte instellen](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics-agent om prestatie meter items bij te houden](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hoe kan ik onverwerkte uitzonde ringen in mijn toepassing zien?

1. Navigeer naar uw Application Insights-resource waarmee uw toepassing is geconfigureerd.
2. Klik op *zoeken* in de linkerbovenhoek. Klik vervolgens op filteren op het volgende deel venster.

    ![Overzicht van AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. U ziet veel typen gebeurtenissen (traceringen, aanvragen, aangepaste gebeurtenissen). Kies uitzonde ring als uw filter.

    ![AI-filter lijst](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Door te klikken op een uitzonde ring in de lijst, kunt u meer informatie bekijken, inclusief de service context als u de Service Fabric Application Insights SDK gebruikt.

    ![AI-uitzonde ring](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hoe kan ik weer geven welke HTTP-aanroepen worden gebruikt in mijn Services?

1. In dezelfde Application Insights resource kunt u filteren op aanvragen in plaats van uitzonde ringen en alle aanvragen weer geven die zijn gemaakt
2. Als u de Service Fabric Application Insights SDK gebruikt, ziet u een visuele weer gave van de services die zijn verbonden met elkaar en het aantal geslaagde en mislukte aanvragen. Klik aan de linkerkant op ' toepassings overzicht '

    ![App-toewijzing Blade AI app-kaart ](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Ga voor meer informatie over het toepassings overzicht naar de [documentatie van het toepassings overzicht](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hoe kan ik een waarschuwing maken wanneer een knoop punt uitvalt

1. Knooppunt gebeurtenissen worden bijgehouden door uw Service Fabric-cluster. Ga naar de Service Fabric-analyse Solution resource met de naam **ServiceFabric (NameofResourceGroup)**
2. Klik op de grafiek aan de onderkant van de Blade met de titel ' samen vatting '

    ![Oplossing voor Azure Monitor-logboeken](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Hier ziet u veel grafieken en tegels die verschillende metrische gegevens weer geven. Klik op een van de grafieken en u gaat naar de zoek opdracht in Logboeken. Hier kunt u een query uitvoeren voor alle cluster gebeurtenissen of prestatie meter items.
4. Voer de volgende query in. Deze gebeurtenis-Id's vindt u in de [verwijzing knooppunt gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Klik bovenaan op ' nieuwe waarschuwings regel ' en nu wanneer een gebeurtenis arriveert op basis van deze query, ontvangt u een waarschuwing in de gekozen communicatie methode.

    ![Nieuwe waarschuwing Azure Monitor logboeken](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hoe kan ik worden gewaarschuwd bij het terugdraaien van de toepassings upgrade?

1. In hetzelfde Zoek venster voor Logboeken als voordat u de volgende query invoert voor het terugdraaien van een upgrade. Deze gebeurtenis-Id's vindt u in de [Naslag informatie over toepassings gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Klik bovenaan op ' nieuwe waarschuwings regel ' en nu wanneer een gebeurtenis arriveert op basis van deze query, ontvangt u een waarschuwing.

## <a name="how-do-i-see-container-metrics"></a>Zie metrische container gegevens Hoe kan ik?

In dezelfde weer gave met alle grafieken ziet u enkele tegels voor de prestaties van uw containers. U hebt de Log Analytics agent en [container bewakings oplossing](service-fabric-diagnostics-oms-containers.md) nodig om deze tegels in te vullen.

![Metrische gegevens van Log Analytics container](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Als u telemetrie **vanuit de** container wilt instrumenteren, moet u het [Application Insights nuget-pakket toevoegen voor containers](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Hoe kan ik prestatie meter items controleren?

1. Nadat u de Log Analytics agent aan uw cluster hebt toegevoegd, moet u de specifieke prestatie meter items toevoegen die u wilt bijhouden. Ga in de portal naar de pagina van de Log Analytics-werk ruimte. op de pagina van de oplossing vindt u het tabblad werk ruimte in het linkermenu.

    ![Tabblad Log Analytics werk ruimte](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Zodra u zich op de pagina van de werk ruimte bevindt, klikt u in hetzelfde linkermenu op geavanceerde instellingen.

    ![Geavanceerde instellingen Log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klik op gegevens > Windows-prestatie meter items (gegevens > Linux-prestatie meter items voor Linux-machines) om te beginnen met het verzamelen van specifieke tellers van uw knoop punten via de Log Analytics agent. Hier volgen enkele voor beelden van de indeling van toe te voegen items

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     In de Snelstartgids zijn VotingData en VotingWeb de proces namen die worden gebruikt, zodat het volgen van deze prestatie meter items eruit zou zien

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Prestatie meter items Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Zo kunt u zien hoe uw-infra structuur uw workloads verwerkt en relevante waarschuwingen instellen op basis van het resource gebruik. Bijvoorbeeld: u wilt een waarschuwing instellen als het totale processor gebruik hoger is dan 90% of lager dan 5%. De item naam die u zou gebruiken voor dit is '% processor tijd '. U kunt dit doen door een waarschuwings regel te maken voor de volgende query:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hoe kan ik de prestaties van mijn Reliable Services en actors volgen?

Als u de prestaties van Reliable Services of actors in uw toepassingen wilt volgen, moet u ook de items van de Service Fabric actor, de actor-methode, de service en service methode verzamelen. Hier volgen enkele voor beelden van betrouw bare prestatie meter items voor services en actors die u kunt verzamelen

>[!NOTE]
>Service Fabric prestatie meter items kunnen momenteel niet worden verzameld door de Log Analytics-agent, maar kunnen worden verzameld door [andere diagnostische oplossingen](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Controleer deze koppelingen voor de volledige lijst met prestatie meter items op reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) en [actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Volgende stappen

* [Veelvoorkomende fouten bij het activeren van code pakketten opzoeken](./service-fabric-diagnostics-code-package-errors.md)
* [Stel waarschuwingen in AI in](../azure-monitor/alerts/alerts-log.md) om een melding te ontvangen over wijzigingen in prestaties of gebruik
* [Slimme detectie in Application Insights](../azure-monitor/app/proactive-diagnostics.md) voert een proactieve analyse uit van de telemetrie die naar AI wordt verzonden om u te waarschuwen over mogelijke prestatie problemen
* Meer informatie over Azure Monitor-logboeken waarin u wordt [gewaarschuwd](../azure-monitor/alerts/alerts-overview.md) voor hulp bij detectie en diagnose.
* Voor on-premises clusters biedt Azure Monitor-Logboeken een gateway (HTTP forward proxy) die kan worden gebruikt om gegevens te verzenden naar Azure Monitor-Logboeken. Meer informatie over dat bij [het verbinden van computers zonder Internet toegang tot Azure monitor-logboeken met behulp van de log Analytics gateway](../azure-monitor/agents/gateway.md)
* Krijg vertrouwd met de functies voor [Zoeken in Logboeken en query's](../azure-monitor/logs/log-query-overview.md) die worden aangeboden als onderdeel van Azure monitor logboeken
* Meer gedetailleerd overzicht van Azure Monitor-logboeken en wat het biedt, Lees [Wat is Azure monitor logboeken?](../azure-monitor/overview.md)
