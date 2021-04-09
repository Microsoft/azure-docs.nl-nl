---
title: Azure-regio's
description: Meer informatie over de regio's voor het uitvoeren van virtuele machines in Azure.
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthnn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.openlocfilehash: e874a76cc447158127da8482f23bf03d56961818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504606"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Regio's voor virtuele machines in Azure

Het is belangrijk om inzicht te hebben in hoe en waar uw virtuele machines (VM's) in Azure werken, evenals in wat uw mogelijkheden zijn om de prestaties, beschikbaarheid en redundantie te maximaliseren. Dit artikel biedt een overzicht van de mogelijkheden van Azure op het gebied van beschikbaarheid en redundantie.


## <a name="what-are-azure-regions"></a>Wat zijn Azure-regio's?
Azure werkt vanuit diverse datacentra, op locaties overal wereld. Deze datacenters worden gegroepeerd in geografische regio's, waardoor u flexibiliteit heeft bij het kiezen waar u uw toepassingen ontwikkelt. 

U maakt Azure-resources in gedefinieerde geografische regio's zoals ' vs-West ', ' Europa-noord ' of ' Zuidoost-Azië '. U kunt zelf de [lijst met regio's en bijbehorende locaties](https://azure.microsoft.com/regions/) bekijken. In elke regio bevinden zich meerdere datacenters, om te zorgen voor voldoende redundantie en beschikbaarheid. Deze benadering biedt u de flexibiliteit bij het ontwerpen van toepassingen om virtuele machines te maken die het dichtst bij uw gebruikers staan en om te voldoen aan alle juridische, nalevings-of belasting doeleinden.

## <a name="special-azure-regions"></a>Speciale Azure-regio's
Azure heeft een aantal speciale regio's die u mogelijk wilt gebruiken bij het ontwikkelen van uw toepassingen voor naleving of juridische doel einden. Voorbeelden van dergelijke speciale regio's zijn:

* **US Gov - Virginia** en **US Gov - Iowa**
  * Een fysiek en logisch van netwerken afgeschermd exemplaar van Azure voor de Amerikaanse overheid en zijn partners, bediend door gecontroleerde Amerikaanse staatsburgers. Dit exemplaar beschikt over aanvullende nalevingscertificeringen, zoals [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) en [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Meer informatie over [Azure Government](https://azure.microsoft.com/features/gov/).
* **China - noord** en **China - oost**
  * Deze regio's zijn beschikbaar via een unieke samenwerking tussen Microsoft en 21Vianet, waarbij Microsoft niet rechtstreeks de datacentra onderhoudt. Meer informatie over [Azure China 21vianet](https://www.windowsazure.cn/).
* **Duitsland - centraal** en **Duitsland - noordoost**
  * Deze regio's zijn beschikbaar via een model voor gegevens vertrouwensrelatie waarbij klant gegevens in Duitsland blijven, onder controle van T-Systems, een Deutsche Telekom-bedrijf dat als de Duitse gegevens beheerder optreedt.

## <a name="region-pairs"></a>Regioparen
Elke Azure-regio is gekoppeld aan een andere regio binnen hetzelfde geografische gebied (zoals VS, Europa of Azië). Hierdoor kunnen resources, zoals VM-opslag, worden gerepliceerd op een andere locatie binnen hetzelfde geografische gebied, zodat de kans wordt beperkt dat beide regio’s tegelijkertijd worden beïnvloed door natuurrampen, onrusten, stroomstoringen of fysieke netwerkuitval. Het gebruik van regioparen biedt meer voordelen:

* In het geval van een bredere Azure-storing krijgt van elk paar één regio prioriteit, om te zorgen dat toepassingen zo snel mogelijk weer beschikbaar zijn. 
* Geplande Azure-updates worden voor gepaarde regio’s één voor één geïmplementeerd, om downtime en het risico op niet-beschikbare toepassingen te beperken.
* Gegevens blijven voor juridische en belastingdoeleinden voor ieder paar binnen hetzelfde geografische gebied (met uitzondering van Brazilië - zuid).

Voorbeelden van regioparen zijn:

| Primair | Secundair |
|:--- |:--- |
| VS - west |VS - oost |
| Europa - noord |Europa -west |
| Azië - zuidoost |Azië - oost |

U vindt de volledige [lijst met regioparen hier](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Beschikbaarheid van functies
Sommige services of VM-functies zijn alleen beschikbaar in bepaalde regio's, zoals specifieke VM-grootten of opslagtypen. Er zijn ook een aantal algemene Azure-services waarvoor u geen specifieke regio hoeft te kiezen, zoals [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../traffic-manager/traffic-manager-overview.md) en [Azure DNS](../dns/dns-overview.md). Als hulp bij het ontwerpen van de omgeving van uw toepassing, kunt u de [beschikbaarheid van Azure-services per regio](https://azure.microsoft.com/regions/#services) raadplegen. U kunt ook [programmatisch query's uitvoeren op de ondersteunde VM-grootten en-beperkingen in elke regio](../azure-resource-manager/templates/error-sku-not-available.md).

## <a name="storage-availability"></a>Opslagbeschikbaarheid
Inzicht in Azure-regio's en geografische locaties wordt belangrijk wanneer u gaat kijken naar de verschillende opties voor opslagreplicatie. U hebt verschillende replicatieopties, afhankelijk van het opslagtype.

**Azure Managed Disks**
* Lokaal redundante opslag (LRS)
  * Uw gegevens worden drie keer gerepliceerd in de regio waar u uw storage-account hebt gemaakt.

**Schijven voor opslag op basis van een account**
* Lokaal redundante opslag (LRS)
  * Uw gegevens worden drie keer gerepliceerd in de regio waar u uw storage-account hebt gemaakt.
* Zone-redundante opslag (ZRS)
  * Uw gegevens worden binnen twee of drie faciliteiten in één regio of tussen twee regio's driemaal gerepliceerd.
* Geografisch redundante opslag (GRS)
  * Uw gegevens worden gerepliceerd naar een secundaire regio, honderden kilometers van de primaire regio.
* Geografisch redundante opslag met leestoegang (RA-GRS)
  * Uw gegevens worden gerepliceerd naar een secundaire regio, net als bij GRS, maar vervolgens biedt de secundaire locatie alleen-lezen toegang tot de gegevens.

De volgende tabel geeft een overzicht van de verschillen tussen de typen opslagreplicatie:

| Replicatiestrategie | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Gegevens worden gerepliceerd bij meerdere faciliteiten. |Nee |Ja |Ja |Ja |
| Gegevens kunnen worden gelezen vanaf de secundaire locatie en vanaf de primaire locatie. |Nee |Nee |Nee |Ja |
| Het aantal exemplaren van de gegevens op afzonderlijke knooppunten. |3 |3 |6 |6 |

Meer informatie over [Azure Storage-replicatieopties vindt u hier](../storage/common/storage-redundancy.md). Zie voor meer informatie over beheerde schijven [overzicht Azure Managed Disks](./managed-disks-overview.md).

### <a name="storage-costs"></a>Opslagkosten
De kosten zijn afhankelijk van het opslagtype en de beschikbaarheid die u selecteert.

**Azure Managed Disks**
* Premium-Managed Disks worden ondersteund door Solid-State drives (Ssd's) en standaard Managed Disks worden ondersteund door normale draaiende schijven. Voor zowel Premium als Standard Managed Disks worden kosten in rekening gebracht op basis van de ingerichte capaciteit van de schijf.

**Niet-beheerde schijven**
* Premium-opslag wordt ondersteund door Solid-State drives (Ssd's) en wordt in rekening gebracht op basis van de capaciteit van de schijf.
* Standaard opslag wordt ondersteund door traditionele draaiende schijven en wordt in rekening gebracht op basis van de gebruikte capaciteit en de gewenste opslagbeschikbaarheid.
  * Voor RA-GRS wordt een toeslag in rekening gebracht voor de gegevensoverdracht voor geo-replicatie, voor de bandbreedtekosten van het repliceren van gegevens naar een andere Azure-regio.

Zie [prijzen van Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor informatie over prijzen van de verschillende opslagtypen en beschikbaarheidsopties.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/) voor meer informatie.