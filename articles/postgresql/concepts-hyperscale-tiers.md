---
title: Preview van Basic-laag-grootschalige (Citus)-Azure Database for PostgreSQL
description: De basis tier met één knoop punt voor Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023988"
---
# <a name="basic-tier-preview"></a>Basic-laag (preview-versie)

> [!IMPORTANT]
> De grootschalige (Citus) Basic-laag is momenteel beschikbaar als preview-versie.  Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

De laag basis in Azure Database for PostgreSQL-grootschalige (Citus) is een eenvoudige manier om een kleine Server groep te maken die u later kunt schalen. Hoewel Server groepen in de laag standaard een coördinator knooppunt hebben en ten minste twee worker-knoop punten, voert de laag basis alles uit in één database knooppunt.

Met uitzonde ring van het gebruik van minder knoop punten, hebben de laag basis alle functies van de Standard-laag. Net als bij de standaard-laag ondersteunt deze ondersteuning voor hoge Beschik baarheid, het lezen van replica's en het opslaan in kolom vorm, onder andere functies.

## <a name="choosing-basic-vs-standard-tier"></a>Basis versus standaard laag kiezen

De Basic-laag kan een voordelige en handige implementatie optie zijn voor de eerste ontwikkeling, testen en continue integratie. Er wordt één database knooppunt gebruikt en dezelfde SQL-API wordt weer gegeven als de laag standaard. U kunt toepassingen testen met de laag basis en deze later afstellen [op de laag standaard](howto-hyperscale-scale-grow.md#add-worker-nodes) , met het vertrouwen dat de interface hetzelfde blijft.

De laag basis is ook geschikt voor kleinere werk belastingen in de productie omgeving (zodra het een preview-versie van de algemene Beschik baarheid heeft). Er is ruimte om verticaal *binnen* de laag basis te schalen door het aantal vCores van de server te verhogen.

Als u meteen een grotere schaal nodig hebt, gebruikt u de laag standaard. De kleinste toegestane Server groep heeft één coördinator knooppunt en twee werk rollen. U kunt ervoor kiezen om meer knoop punten te gebruiken op basis van uw gebruiks voorbeeld, zoals beschreven in onze [oorspronkelijke grootte](howto-hyperscale-scale-initial.md) .

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het inrichten van de Basic-laag](quickstart-create-hyperscale-basic-tier.md)
* Wanneer u klaar bent, kunt u zien [hoe u](howto-hyperscale-scale-grow.md#add-worker-nodes) kunt aflopen van de Basic-laag naar de Standard-laag
* De optie voor [kolom opslag](concepts-hyperscale-columnar.md) is beschikbaar in de lagen basis en standaard.
