---
title: Metrische gegevens in realtime weer geven met container Insights | Microsoft Docs
description: In dit artikel wordt een overzicht gegeven van de real-time weergave van metrische gegevens zonder kubectl te gebruiken met container Insights.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2e3a8a417a934374c2c0b256bb65a471d98fdebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731847"
---
# <a name="how-to-view-metrics-in-real-time"></a>Metrische gegevens weer geven in realtime

Met de functie container Insights live data (preview) kunt u metrische gegevens over de status van het knoop punt en de pod in realtime visualiseren. Het emuleert directe toegang tot de `kubectl top nodes` -, `kubectl get pods –all-namespaces` -en- `kubectl get nodes` opdrachten voor het aanroepen, parseren en visualiseren van de gegevens in prestatie grafieken die deel uitmaken van dit inzicht.

Dit artikel bevat een gedetailleerd overzicht en helpt u inzicht te krijgen in het gebruik van deze functie.

>[!NOTE]
>AKS-clusters die zijn ingeschakeld als [persoonlijke clusters](https://azure.microsoft.com/updates/aks-private-cluster/) , worden niet ondersteund met deze functie. Deze functie is afhankelijk van directe toegang tot de Kubernetes-API vanuit de browser via een proxyserver. Als u netwerkbeveiliging inschakelt om de Kubernetes-API te blokkeren voor deze proxy, wordt dit verkeer geblokkeerd.

Raadpleeg de [installatie handleiding](container-insights-livedata-setup.md)voor hulp bij het instellen of oplossen van problemen met de functie Live data (preview).

## <a name="how-it-works"></a>Hoe werkt het?

De functie voor Live gegevens (preview) heeft rechtstreeks toegang tot de Kubernetes-API en aanvullende informatie over het verificatie model vindt u [hier](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Deze functie voert een polling bewerking uit op basis van de metrische gegevens (inclusief `/api/v1/nodes` , `/apis/metrics.k8s.io/v1beta1/nodes` en `/api/v1/pods` ), die standaard elke vijf seconden zijn. Deze gegevens worden in de cache opgeslagen in uw browser en gegrafeerd in de vier prestatie grafieken die zijn opgenomen in container Insights op het tabblad **cluster** door **Go Live (preview-versie)** te selecteren. Elke volgende poll wordt gediagrameerd in een visualisatie venster van vijf minuten.

![De optie Live go in de cluster weergave](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Het polling-interval wordt geconfigureerd in de vervolg keuzelijst **ingesteld interval** zodat u elke 1, 5, 15 en 30 seconden polling voor nieuwe gegevens kunt instellen.

![Live vervolg polling-interval](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>We raden u aan het polling-interval in te stellen op één seconde terwijl u een probleem gedurende korte tijd oplost. Deze aanvragen kunnen van invloed zijn op de beschik baarheid en beperking van de Kubernetes-API op uw cluster. Daarna kunt u de configuratie opnieuw configureren naar een langer polling-interval.

>[!IMPORTANT]
>Er worden geen gegevens permanent opgeslagen tijdens de werking van deze functie. Alle informatie die tijdens deze sessie is vastgelegd, wordt onmiddellijk verwijderd wanneer u uw browser sluit of van de functie verlaat. De gegevens blijven in het venster van vijf minuten aanwezig voor visualisatie. alle metrische gegevens die ouder zijn dan vijf minuten, worden ook permanent verwijderd.

Deze grafieken kunnen niet worden vastgemaakt aan het laatste Azure-dash board dat u in de Live-modus hebt bekeken.

## <a name="metrics-captured"></a>Vastgelegde metrische gegevens

### <a name="node-cpu-utilization---node-memory-utilization-"></a>CPU-gebruik van het knoop punt%/het geheugen gebruik van het knoop punt%

Deze twee prestatie grafieken zijn gekoppeld aan een equivalent van het aanroepen `kubectl top nodes` en vastleggen van de resultaten van het **CPU-percentage** en het **geheugen%** van de desbetreffende grafiek.

![Voorbeeld resultaten van Kubectl bovenste knoop punten](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Grafiek percentage CPU-gebruik van knoop punten](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Diagram percentage geheugen gebruik van knoop punt](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

De percentiel berekeningen worden in grotere clusters gebruikt om uitschieter-knoop punten in uw cluster te identificeren. Bijvoorbeeld om te begrijpen of knoop punten worden gebruikt voor schaal baarheid. Met de **minimum** aggregatie kunt u zien welke knoop punten een laag gebruik in het cluster hebben. Als u verder wilt onderzoeken, selecteert u het tabblad **knoop punten** en sorteert u het raster op CPU-of geheugen gebruik.

Dit helpt u ook te begrijpen welke knoop punten worden gepusht naar hun limieten en of uitschalen mogelijk vereist is. Door zowel de aggregaties **Max** en **P95** te gebruiken, kunt u zien of er knoop punten in het cluster zijn met een hoog bron gebruik. Voor nader onderzoek gaat u weer naar het tabblad **knoop punten** .

### <a name="node-count"></a>Aantal knooppunten

Deze prestatie grafiek is gekoppeld aan een equivalent van het aanroepen `kubectl get nodes` en toewijzen van de kolom **status** aan een grafiek, gegroepeerd op status typen.

![Voorbeeld resultaten van Kubectl Get-knoop punten](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Grafiek met aantal knoop punten](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Knoop punten worden gerapporteerd in de status **gereed** of **niet gereed** . Deze worden geteld (en er wordt een totaal aantal gemaakt) en de resultaten van deze twee aggregaties worden in een grafiek opgenomen.
Als u bijvoorbeeld wilt weten of de knoop punten in mislukte statussen vallen. Als u de samen voeging **niet gereed** hebt, kunt u snel het aantal knoop punten in het cluster zien die momenteel **niet gereed** zijn.

### <a name="active-pod-count"></a>Aantal actieve pod

Deze prestatie grafiek wordt toegewezen aan een equivalent van aanroepen `kubectl get pods –all-namespaces` en wijst de kolom **status** toe aan de grafiek gegroepeerd op status typen.

![Kubectl ophalen van een Peul voorbeeld resultaat](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Grafiek pod aantal knoop punten](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>De namen van de status, zoals geïnterpreteerd door, `kubectl` komen mogelijk niet exact overeen in de grafiek.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken om waarschuwingen, visualisaties of verdere analyse van uw clusters te maken.
