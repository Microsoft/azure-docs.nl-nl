---
title: VMware HCX-netwerksegmenten
description: Er zijn vier netwerken vereist voor VMware HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 1f0fcc1ddeeb26702a297035e6ae2a2f73dd71d6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462288"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Er zijn vier netwerken vereist voor VMware HCX:

- **Beheer netwerk:** Normaal gesp roken is het hetzelfde beheer netwerk dat wordt gebruikt door het Azure VMware-oplossings cluster. Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.

   > [!NOTE]
   > De aanbevolen methode is om een/26-netwerk te maken, omdat u Maxi maal 10 service netten en 60 netwerk extenders (-1 per service-net) kunt gebruiken. U kunt **acht** netwerken per netwerk Extender uitrekken met behulp van persoonlijke Clouds van Azure VMware-oplossingen.
   >
   
- **vMotion-netwerk:** Normaal gesp roken is het hetzelfde netwerk dat wordt gebruikt voor vMotion op het Azure VMware-oplossings cluster.  Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.  

   Het netwerk van vMotion moet zichtbaar zijn op een gedistribueerde virtuele switch of vSwitch0. Als dat niet het geval is, wijzig dan de omgeving.

   > [!NOTE]
   > Dit netwerk kan privé zijn (niet gerouteerd).

- **Uplinknetwerk:** Maak een nieuw netwerk voor VMware HCX Uplink en breidt dit naar uw vSphere-cluster uit via een poortgroep. Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.  

   > [!NOTE]
   > De aanbevolen methode is om een/26-netwerk te maken, omdat u Maxi maal 10 service netten en 60 netwerk extenders (-1 per service-net) kunt gebruiken. U kunt **acht** netwerken per netwerk Extender uitrekken met behulp van persoonlijke Clouds van Azure VMware-oplossingen.
   >
   
- **Replicatienetwerk:** Dit is optioneel. Maak een nieuw netwerk voor VMware HCX Replication en breidt dit naar uw vSphere-cluster uit via een poortgroep. Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.

   > [!NOTE]
   > Deze configuratie is alleen mogelijk als de on-premises clusterhosts een toegewezen VMkernel-netwerk voor replicatie gebruiken.  Als er voor uw on-premises cluster geen toegewezen VMkernel-netwerk voor replicatie is gedefinieerd, hoeft u dit netwerk niet te maken.
