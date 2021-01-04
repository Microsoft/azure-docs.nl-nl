---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3fe1b966b56142fd312850ac5d77839b7d9db434
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97706225"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Inbegrepen opslag<sup>1</sup> (GiB) | 10 | 100 | 500 |
| Limiet gegevensopslag (TiB) | 20| 20 | 20 |
| Maximale grootte van de installatiekopielaag (GiB) | 200 | 200 | 200 |
| ReadOps per minuut<sup>2, 3</sup> | 1000 | 3000 | 10.000 |
| WriteOps per minuut<sup>2, 4</sup> | 100 | 500 | 2.000 |
| Downloadbandbreedte Mbps<sup>2</sup> | 30 | 60 | 100 |
| Uploadbandbreedte Mbps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replicatie | N.v.t. | N.v.t. | [Ondersteund][geo-replication] |
| Beschikbaarheidszones | N.v.t. | N.v.t. | [Preview][zones] |
| Inhoud vertrouwen | N.v.t. | N.v.t. | [Ondersteund][content-trust] |
| Privékoppeling met privé-eindpunten | N.v.t. | N.v.t. | [Ondersteund][plink] |
| &bull; Privé-eindpunten | N.v.t. | N.v.t. | 10 |
| VNet-toegang voor service-eindpunt | N.v.t. | N.v.t. | [Preview][vnet] |
| Door klant beheerde sleutels | N.v.t. | N.v.t. | [Ondersteund][cmk] |
| Machtigingen voor opslagplaatsen | N.v.t. | N.v.t. | [Preview][token]|
| &bull; Tokens | N.v.t. | N.v.t. | 20.000 |
| &bull; Bereiktoewijzingen | N.v.t. | N.v.t. | 20.000 |
| &bull; Opslagplaatsen per bereiktoewijzing | N.v.t. | N.v.t. | 500 |


<sup>1</sup> Opslag inbegrepen in het dagelijkse tarief voor elke laag. Voor extra opslag wordt een extra dagelijks tarief per GiB in rekening gebracht, tot aan de opslaglimiet. Zie voor meer tariefinformatie [Prijzen voor Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps* en *bandbreedte* zijn minimale schattingen. Azure Container Registry streeft ernaar de prestaties te verbeteren op basis van de gebruiksvereisten.

<sup>3</sup>Een [Docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) bestaat uit meerdere leesbewerkingen, afhankelijk van het aantal lagen in de installatiekopie, plus het ophalen van het manifest.

<sup>4</sup>Een [Docker-push](https://docs.docker.com/registry/spec/api/#pushing-an-image) bestaat uit meerdere schrijfbewerkingen, afhankelijk van het aantal lagen dat moet worden gepusht. Een `docker push` omvat de *ReadOps* om een manifest voor een bestaande installatiekopie op te halen.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
