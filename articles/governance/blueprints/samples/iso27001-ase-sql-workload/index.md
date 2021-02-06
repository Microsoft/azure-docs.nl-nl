---
title: Overzicht van het blauwdrukvoorbeeld voor ISO 27001 conforme ASE-/SQL-workloads
description: Overzicht en architectuur van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 61e62c9a9099b0c84a98f1840743f05469e46b62
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627513"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Overzicht van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads

Het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads biedt een aanvullende infrastructuur voor het blauwdrukvoorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md).
Aan de hand van deze blauwdruk kunnen klanten op de cloud gebaseerde architecturen implementeren die oplossingen bieden voor scenario's met accreditatie- en nalevingsvereisten.

Er zijn twee ISO 27001 conforme blauwdrukvoorbeelden: dit voorbeeld en het voorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md).

> [!IMPORTANT]
> Dit voorbeeld is afhankelijk van de infrastructuur die wordt geïmplementeerd door het blauwdrukvoorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md). Dat voorbeeld moet daarom eerst worden geïmplementeerd.

## <a name="architecture"></a>Architectuur

Het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads implementeert een webomgeving op basis van een Platform-as-a-Service. De omgeving kan worden gebruikt voor het hosten van meerdere webtoepassingen, web-API's en SQL Database-exemplaren die voldoen aan de ISO 27001-standaarden. Dit blauwdrukvoorbeeld is afhankelijk van het blauwdrukvoorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md).

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="Ontwerp van het blauwdrukvoorbeeld voor ISO 27001 conforme ASE-/SQL-workloads" border="false":::

Deze omgeving bestaat uit diverse Azure-services die samen een veilige, volledig bewaakte en direct inzetbare workloadinfrastructuur bieden op basis van de ISO 27001-standaarden. De omgeving bestaat uit de volgende elementen:

- [Azure-rol](../../../../role-based-access-control/overview.md), DevOps genoemd, met rechten om resources te implementeren en te beheren in de [Azure App Service Environment](../../../../app-service/environment/intro.md) die door het blauwdrukvoorbeeld wordt geïmplementeerd
- [Azure Policy](../../../policy/overview.md)-definities om vast te leggen welke services in de omgeving mogen worden geïmplementeerd en om te voorkomen dat er resources op basis van een openbaar IP-adres worden gemaakt
- Een virtueel netwerk met één subnet en een koppeling met een al bestaande omgeving met [gedeelde services](../iso27001-shared/index.md), waarbij al het verkeer wordt gedwongen de firewall voor [gedeelde services](../iso27001-shared/index.md) te passeren. Het virtuele netwerk hosts de volgende resources:
  - Een [Azure App Service Environment](../../../../app-service/environment/intro.md) die kan worden gebruikt voor het hosten van een of meer web-apps, web-API's of functies
  - Een [Azure Key Vault](../../../../key-vault/general/overview.md)-exemplaar met een VNet-service-eindpunt, voor het opslaan van geheimen die worden gebruikt door in de workloadomgeving uitgevoerde toepassingen
  - Een [Azure SQL Database](../../../../azure-sql/database/sql-database-paas-overview.md)-serverexemplaar met een VNet-service-eindpunt, voor het hosten van databases die worden gebruikt voor toepassingen in de workloadomgeving

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads doorgenomen. Lees nu de volgende artikelen voor meer informatie over het toewijzen van beheeropties en het implementeren van dit voorbeeld:

> [!div class="nextstepaction"]
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - beheeropties toewijzen](./control-mapping.md)
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
