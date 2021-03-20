---
title: Overzicht van Azure VMware-oplossing per CloudSimple-knoop punten
description: Meer informatie over CloudSimple-concepten, met inbegrip van knoop punten, ingerichte knoop punten, een Privécloud en VMware-oplossing door de CloudSimple-knoop punten-Sku's.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140833"
---
# <a name="cloudsimple-nodes-overview"></a>Overzicht van CloudSimple-knoop punten

Knoop punten zijn de bouw stenen van een Privécloud. Een knoop punt is:

* Een toegewezen bare-metal Compute-host waarop een VMware ESXi Hyper Visor is geïnstalleerd  
* Een reken eenheid die u kunt inrichten of reserveren voor het maken van persoonlijke Clouds
* Beschikbaar voor het inrichten of reserveren van een regio waar de CloudSimple-service beschikbaar is

U kunt een Privécloud maken op basis van de ingerichte knoop punten. Als u een Privécloud wilt maken, hebt u Mini maal drie knoop punten van dezelfde SKU nodig. Als u een Privécloud wilt uitbreiden, voegt u extra knoop punten toe.  U kunt knoop punten toevoegen aan een bestaand cluster of een nieuw cluster maken door knoop punten in de Azure Portal in te richten en deze te koppelen aan de CloudSimple-service.  Alle ingerichte knoop punten zijn zichtbaar in de CloudSimple-service.  

## <a name="provisioned-nodes"></a>Ingerichte knoop punten

Ingerichte knoop punten bieden een betalen naar gebruik-capaciteit. Met het inrichten van knoop punten kunt u uw VMware-cluster op aanvraag snel schalen. U kunt indien nodig knoop punten toevoegen of een ingericht knoop punt verwijderen om uw VMware-cluster omlaag te schalen. Ingerichte knoop punten worden maandelijks gefactureerd en worden in rekening gebracht voor het abonnement waar ze zijn ingericht.

* Als u betaalt voor uw Azure-abonnement per credit card, wordt de kaart onmiddellijk gefactureerd.
* Als u per factuur factureert, worden de kosten op uw volgende factuur weer gegeven.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-oplossing op CloudSimple knoop punten SKU

De volgende typen knoop punten zijn beschikbaar voor inrichting of reserve ring.

| SKU           | CS28-knoop punt                 | CS36-knoop punt                 | CS36m-knoop punt                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | VS-Oost, VS-West            | VS-Oost, VS-West            | Europa -west                 |
| CPU           | 2x 2,2 GHz, 28 kernen (56 HT) | 2x 2,3 GHz, 36 kern geheugens (72 HT) | 2x 2,3 GHz, 36 kern geheugens (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cache schijf    | 1,6-TB NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Capaciteits schijf | 5,625 TB onbewerkt                | 11,25 TB onbewerkt                | 15,36 TB onbewerkt                |
| Opslagtype  | Alle Flash                   | Alle Flash                   | Alle Flash                   |

## <a name="limits"></a>Limieten

De volgende limieten voor knoop punten zijn van toepassing op persoonlijke Clouds.

| Resource | Limiet |
|----------|-------|
| Minimum aantal knoop punten voor het maken van een Privécloud | 3 |
| Maximum aantal knoop punten in een cluster in een Privécloud | 16 |
| Maximum aantal knoop punten in een Privécloud | 64 |
| Minimum aantal knoop punten op een nieuw cluster | 3 |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inrichten van knoop punten](create-nodes.md)
* Meer informatie over [persoonlijke Clouds](cloudsimple-private-cloud.md)
