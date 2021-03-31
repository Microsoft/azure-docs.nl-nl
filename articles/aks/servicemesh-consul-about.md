---
title: Overzicht van consul
description: Een overzicht van consul verkrijgen
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83774007"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Overzicht

[Consul][consul] is een service netwerk oplossing met meerdere Data Centers om verbinding te maken met services tussen runtime platforms en deze te beveiligen. [Connect][consul-features] is het onderdeel dat Service-Mesh mogelijkheden biedt.

## <a name="architecture"></a>Architectuur

Consul biedt standaard een gegevens vlak dat bestaat uit op [Envoy][envoy-proxy]gebaseerde [zijspanken][consul-sidecar] . Consul heeft een pluggable proxy-architectuur. Deze intelligente proxy's bepalen al het netwerk verkeer in en uit uw gestuurde apps en workloads.

Het besturings vlak beheert de configuratie en het beleid via de volgende [onderdelen][consul-architecture]:

- **Server** : een consul-agent die wordt uitgevoerd in de server modus die de consul-cluster status onderhoudt.

- **Client** -een consul-agent die wordt uitgevoerd in de modus Lightweight client. Voor elk reken knooppunt moet een client agent worden uitgevoerd. Deze client-Brokers configureren en beleid tussen de werk belastingen en de consul-configuratie. 

In het volgende architectuur diagram ziet u hoe de verschillende onderdelen binnen het vlak van de gegevens en het besturings element werken.

![Overzicht van consul-onderdelen en-architectuur.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Selectie criteria

Het is belang rijk dat u de volgende gebieden begrijpt en overweegt om consul te evalueren voor uw workloads:

- [Consul-principes](#consul-principles)
- [Functies](#capabilities)
- [Scenario's](#scenarios)


### <a name="consul-principles"></a>Consul-principes

De volgende principes [begeleiden][consul-principles] het consul-project:

- **API-aangedreven** -Codify alle configuratie en beleid.

- Voer Anywhere-Connect-workloads uit op verschillende runtime platforms (Kubernetes, Vm's, Serverloos) **en maak er verbinding mee** .

- **Breid en integreer** veilig werk belastingen over de infra structuur.


### <a name="capabilities"></a>Functies

Consul biedt de volgende mogelijkheden:

- **Net** -gateway (Data Center met meerdere gegevens), virtuele machines (buiten cluster knooppunten), service synchronisatie, ingebouwd in de optie fout opsporing

- **Proxy's** – Envoy, ingebouwde proxy, pluggable, N4-proxy beschikbaar voor Windows-workloads

- **Verkeers beheer** – route ring, splitsen, oplossing

- **Beleid** : bedoelt, acl's

- **Beveiliging** : autorisatie, authenticatie, versleuteling, op SPIFFE gebaseerde identiteiten, externe CA (kluis), certificaat beheer en rotatie

- **Waarneem** bare statistieken, UI-dash board, Prometheus, grafana


### <a name="scenarios"></a>Scenario's

Consul is goed geschikt voor de volgende scenario's:

- Bestaande consul Connected workloads uitbreiden

- Nalevings vereisten rond certificaat beheer

- Multi Cluster service net

- Werk belastingen op basis van VM'S die moeten worden opgenomen in het service-net



## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u consul kunt installeren in azure Kubernetes service (AKS):

> [!div class="nextstepaction"]
> [Consul installeren in azure Kubernetes service (AKS)][consul-install]

U kunt ook nog verder consul-functies en-architectuur verkennen:

- [Zelf studies voor consul aan de slag][consul-getting-started]
- [Consul-functies][consul-features]
- [Consul-architectuur][consul-architecture]
- [Consul-hoe Connect werkt][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
