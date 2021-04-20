---
title: De voorwaarden van Azure BareMetal Infrastructure kennen
description: De voorwaarden van Azure BareMetal Infrastructure kennen.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725454"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>De voorwaarden voor BareMetal Infrastructure kennen

In dit artikel worden enkele belangrijke termen met betrekking tot de BareMetal-infrastructuur beschreven.

- **Revisie:** er zijn twee verschillende zegelrevisies voor BareMetal Infrastructure-stempels (HANA Large Instance). Deze verschillen in architectuur en nabijheid van virtuele Azure-machinehosts:
    - Revisie 3 (Rev 3): het oorspronkelijke ontwerp is halverwege 2016 geïmplementeerd.
    - Revision 4.2 (Rev 4.2): Een nieuw ontwerp dat de nabijheid van hosts van virtuele Azure-machines dichter bij elkaar brengt, met een ultra lage netwerklatentie tussen Azure-VM's en HANA Large Instances. Resources in de Azure Portal worden aangeduid als 'BareMetal Infrastructure' en klanten hebben toegang tot hun resources als BareMetal-exemplaren van de Azure Portal.

- **Zegel:** hiermee definieert u de interne implementatiegrootte van BareMetal-exemplaren van Microsoft. Voordat exemplaren kunnen worden geïmplementeerd, moet een BareMetal-instantiestempel die bestaat uit reken-, netwerk- en opslagrekken worden geïmplementeerd op een datacenterlocatie. Een dergelijke implementatie wordt een BareMetal-instantiestempel genoemd.

- **Tenant:** een klant die een BareMetal-instantiestempel implementeert, wordt geïsoleerd als *tenant.* Een tenant is geïsoleerd in de netwerk-, opslag- en rekenlaag van andere tenants. Opslag- en rekeneenheden die zijn toegewezen aan de verschillende tenants kunnen elkaar niet zien of met elkaar communiceren op het zegelniveau van het BareMetal-exemplaar. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Zelfs dan is er geen communicatie tussen tenants op het zegelniveau van het BareMetal-exemplaar.

## <a name="next-steps"></a>Volgende stappen

Nu u belangrijke terminologie van de BareMetal-infrastructuur hebt geïntroduceerd, kunt u het volgende leren:
- Meer informatie over [de BareMetal-infrastructuur.](concepts-baremetal-infrastructure-overview.md)
- [BareMetal Infrastructure-instanties verbinden in Azure](connect-baremetal-infrastructure.md).

