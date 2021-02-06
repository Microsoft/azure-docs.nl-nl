---
title: Overzicht van de voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services
description: Overzicht en architectuur van de voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services. Met dit blauwdrukvoorbeeld kunnen klanten specifieke beheeropties van ISO 27001 bekijken.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: d5ac88fc7a4fe21cef74ee23af2336a5a376471a
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627647"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Overzicht van de voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services

De voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services biedt een set met conforme infrastructuurpatronen en beleidsrichtlijnen die de route naar ISO 27001-attestatie vergemakkelijken. Aan de hand van deze blauwdruk kunnen klanten op de cloud gebaseerde architecturen implementeren die oplossingen bieden voor scenario's met accreditatie- en nalevingsvereisten.

Het blauwdrukvoorbeeld [ISO 27001 conforme App Service Environment-/SQL Database-workloads](../iso27001-ase-sql-workload/index.md) vormt hier een aanvulling op.

## <a name="architecture"></a>Architectuur

Met de voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services wordt in Azure een infrastructureel fundament geïmplementeerd waarmee organisaties meerdere workloads kunnen hosten op basis van de VDC-benadering (Virtual Datacenter).
VDC is een in de praktijk bewezen set referentiearchitecturen, automatiseringshulpmiddelen en interactiemodellen die door Microsoft worden gebruikt voor de grootste enterprise-klanten. Het blauwdrukvoorbeeld voor gedeelde services is gebaseerd op de volledig systeemeigen Azure-VDC-omgeving die hieronder wordt weergegeven.

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="Ontwerp van blauwdrukvoorbeeld voor ISO 27001 conforme gedeelde services" border="false":::

Deze omgeving bestaat uit diverse Azure-services die samen een veilige, volledig bewaakte en direct inzetbare infrastructuur voor gedeelde services bieden op basis van de ISO 27001-standaarden. De omgeving bestaat uit de volgende elementen:

- [Azure-rollen](../../../../role-based-access-control/overview.md) voor scheiding van taken met het oog op optimaal beheer. Vóór de implementatie van de infrastructuur worden er drie rollen gedefinieerd:
  - De rol NetOps beschikt over de rechten die nodig zijn om de netwerkomgeving te beheren, waaronder firewallinstellingen, instellingen voor netwerkbeveiligingsgroepen, routering en andere netwerkfuncties
  - De rol SecOps beschikt over de rechten die nodig zijn om het [Azure Security Center](../../../../security-center/security-center-introduction.md) te implementeren en te beheren, en het [Azure Policy](../../../policy/overview.md)-definities te definiëren, evenals over andere beveiligingsgerelateerde rechten
  - De rol SysOps beschikt over de rechten die nodig zijn om het [Azure Policy](../../../policy/overview.md)-definities binnen het abonnement te definiëren en [Log Analytics](../../../../azure-monitor/overview.md) te beheren voor de volledige omgeving, evenals over andere operationele rechten
- Als eerste Azure-service wordt [Log Analytics](../../../../azure-monitor/overview.md) geïmplementeerd om te garanderen dat er op een centrale locatie logboeken worden bijgehouden voor alle acties en services zodra u begint met de beveiligde implementatie
- Een virtueel netwerk dat ondersteuning biedt voor subnetten voor connectiviteit met een on-premises datacenter, een stack voor inkomend en uitgaand verkeer voor internetconnectiviteit, en een subnet voor gedeelde services waarin gebruik wordt gemaakt van netwerkbeveiligingsgroepen (NSG's) en toepassingsbeveiligingsgroepen (ASG's) voor volledige micro-segmentatie. Het netwerk bevat de volgende elementen:
  - Een jumpbox- of bastionhost die wordt gebruikt voor beheertaken en alleen kan worden benaderd via een [Azure-firewall](../../../../firewall/overview.md) die is geïmplementeerd in het subnet voor de stack voor inkomend verkeer
  - Twee virtuele machines met Azure Active Directory Domain Services (Azure AD DS) en DNS die uitsluitend toegankelijk zijn via de jumpbox en alleen kunnen worden geconfigureerd voor replicatie van AD via een VPN- of [ExpressRoute](../../../../expressroute/expressroute-introduction.md)-verbinding (niet geïmplementeerd door de blauwdruk)
  - [Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) en standaard-DDoS-bescherming
- Een [Azure Key Vault](../../../../key-vault/general/overview.md)-exemplaar om de geheimen te bewaren voor de virtuele machines die in de omgeving met gedeelde services worden geïmplementeerd

Al deze elementen voldoen aan de aanbevolen procedures zoals gepubliceerd in [Azure Architecture Center - Referentiearchitecturen](/azure/architecture/reference-architectures/).

> [!NOTE]
> Met deze infrastructuur voor ISO 27001 conforme gedeelde services implementeert u het infrastructurele fundament voor de uitvoering van workloads.
> Naast dit fundament moet u ook de workloads zelf implementeren.

Raadpleeg voor meer informatie de [documentatie voor Virtual Datacenter](/azure/architecture/vdc/).

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van de voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services doorgenomen.
Lees nu de volgende artikelen voor meer informatie over het toewijzen van beheeropties en het implementeren van dit voorbeeld:

> [!div class="nextstepaction"]
> [Blauwdruk voor ISO 27001 conforme gedeelde services - beheer toewijzen](./control-mapping.md)
> [Blauwdruk voor ISO 27001 conforme gedeelde services - implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).