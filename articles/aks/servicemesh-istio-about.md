---
title: Overzicht van Istio
description: Een overzicht van Istio verkrijgen
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593897"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Overzicht

[Istio][istio] is een volledig uitgeruste, aanpasbaar en uitbreidbaar service-net.

## <a name="architecture"></a>Architectuur

Istio biedt een gegevensvlak dat bestaat uit op [Envoy][envoy-proxy]gebaseerde zijspanken. Deze intelligente proxy's bepalen al het netwerk verkeer in en uit uw gestuurde apps en workloads.

Het besturings vlak beheert de configuratie, het beleid en de telemetrie via de volgende [onderdelen][what-is-istio]:

- **Mixer** : dwingt toegangs beheer en gebruiks beleid af. Verzamelt telemetrie van de proxy's die in [Prometheus][prometheus]worden gepusht.

- **Pilot** : biedt service detectie en Traffic Management-beleid/-configuratie voor de proxy's.

- **Citadel** : biedt identiteits-en beveiligings mogelijkheden waarmee mTLS tussen services worden toegestaan.

- **Druk proef** -abstracten en biedt configuratie voor onderdelen.

In het volgende architectuur diagram ziet u hoe de verschillende onderdelen binnen het vlak van de gegevens en het besturings element werken.


![Overzicht van Istio-onderdelen en-architectuur.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Selectie criteria

Het is belang rijk dat u de volgende gebieden begrijpt en overweegt om Istio te evalueren voor uw workloads:

- [Ontwerp doelen](#design-goals)
- [Functies](#capabilities)
- [Scenario's](#scenarios)


### <a name="design-goals"></a>Ontwerpdoelen

De volgende ontwerp doelstellingen [begeleiden][design-goals] het Istio-project:

- **Maximale transparantie maximaliseren** : acceptatie met de minimale hoeveelheid werk toestaan om echte waarde van het systeem te verkrijgen.

- **Uitbreid baarheid** : moet kunnen groeien en aanpassen met veranderende behoeften.

- **Portabiliteit** : Voer in verschillende omgevingen-Cloud, on-premises, eenvoudig uit.

- **Uniformiteit van beleid** : consistentie in de beleids definitie over diverse bronnen.


### <a name="capabilities"></a>Functies

Istio biedt de volgende mogelijkheden:

- **Net** -gateways (meerdere clusters), virtuele machines (net-expansie)

- **Verkeers beheer** : route ring, splitsing, time-outs, circuit onderbrekers, nieuwe pogingen, binnenkomend verkeer, uitgang

- **Beleid** : toegangs beheer, frequentie limiet, quota, aangepaste beleids adapters

- **Beveiliging** – verificatie (JWT), autorisatie, versleuteling (mTLS), externe CA (HashiCorp-kluis)

- **Waarneem** bare gegevens: Golden, Mirror, tracering, aangepaste adapters, Prometheus, grafana

### <a name="scenarios"></a>Scenario's

Istio is goed geschikt voor de volgende scenario's:

- Uitbreid baarheid en uitgebreide set mogelijkheden vereisen

- Netexpansie om op virtuele machines gebaseerde workloads op te zetten

- Multi-Cluster service net

## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u Istio kunt installeren in azure Kubernetes service (AKS):

> [!div class="nextstepaction"]
> [Istio installeren in azure Kubernetes service (AKS)][istio-install]

U kunt ook nog verder Istio-concepten en aanvullende implementatie modellen verkennen:

- [Istio-concepten][what-is-istio]
- [Istio-implementatie modellen][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
