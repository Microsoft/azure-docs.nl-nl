---
title: De voorwaarden van Azure BareMetal Infrastructure kennen
description: De voorwaarden van Azure BareMetal Infrastructure kennen.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: aa7d9693b3417ff0bb6c6a61800aee72cd416c48
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536769"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>De voorwaarden voor BareMetal Infrastructure kennen

In dit artikel worden enkele belangrijke termen met betrekking tot de BareMetal-infrastructuur beschreven.

- **Revisie:** Er is een oorspronkelijke zegelrevisie die Revisie 3 (Rev 3) wordt genoemd en twee extra zegelrevisies voor BareMetal-instantiestempels. Elke zegel verschilt in architectuur en de nabijheid van hosts voor virtuele Azure-machines:
    - **Revisie 4** (Rev 4): Een nieuwer ontwerp dat de VM-hosts (virtuele Azure-machines) dichter bij elkaar brengt en de latentie tussen Azure-VM's en SAP HANA-exemplaren verlaagt. 
    - **Revisie 4.2** (Rev 4.2): de meest recente BareMetal-infrastructuur met de bestaande Rev 4-architectuur. Rev 4 is dichter bij de hosts van de virtuele Azure-machines (VM's). De netwerklatentie tussen Azure-VM's en BareMetal-exemplaren die zijn geïmplementeerd in Rev 4-stempels of -rijen, is aanzienlijk verbeterd. U kunt uw BareMetal-exemplaren openen en beheren via de Azure Portal.    

- **Zegel:** hiermee definieert u de interne implementatiegrootte van BareMetal-exemplaren van Microsoft. Voordat exemplaren kunnen worden geïmplementeerd, moet een BareMetal-instantiestempel die bestaat uit reken-, netwerk- en opslagrekken worden geïmplementeerd op een datacenterlocatie. Een dergelijke implementatie wordt een BareMetal-instantiestempel genoemd.

- **Tenant:** een klant die een BareMetal-instantiestempel implementeert, wordt geïsoleerd als *tenant.* Een tenant is geïsoleerd in de netwerk-, opslag- en rekenlaag van andere tenants. Opslag- en rekeneenheden die zijn toegewezen aan de verschillende tenants kunnen elkaar niet zien of met elkaar communiceren op het zegelniveau van het BareMetal-exemplaar. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Zelfs dan is er geen communicatie tussen tenants op het zegelniveau van het BareMetal-exemplaar.

## <a name="next-steps"></a>Volgende stappen

Nu u belangrijke terminologie van de BareMetal-infrastructuur hebt geïntroduceerd, kunt u het volgende leren:
- Meer informatie over [de BareMetal-infrastructuur](concepts-baremetal-infrastructure-overview.md).
- [BareMetal Infrastructure-exemplaren verbinden in Azure](connect-baremetal-infrastructure.md).

