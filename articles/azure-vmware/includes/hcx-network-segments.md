---
title: VMware HCX-netwerksegmenten
description: Er zijn vier netwerken vereist voor VMware HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251298"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Er zijn verschillende manieren om VMware HCX-netwerk segmenten on-premises te configureren. Hieronder wordt een eenvoudige configuratie beschreven die ondersteuning biedt voor een proef-of kleine productie-use case.  Bij het ontwerpen voor honderden of duizenden werk belastingen moet deze configuratie mogelijk worden gewijzigd, afhankelijk van de behoeften van de migratie.  

Bij de voor bereiding van de VMware HCX-implementatie voor de ondersteuning van de pilot-of kleine productie use-case, moet u het volgende identificeren:

- **Beheer netwerk:** Wanneer u on-premises VMware-HCX implementeert, moet u een beheer netwerk voor VMware HCX identificeren.  Normaal gesp roken is het hetzelfde beheer netwerk dat wordt gebruikt door uw on-premises VMware-cluster.  Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. U hebt mogelijk grotere aantallen nodig, afhankelijk van de schaal van uw implementatie na de proef of kleine use-cases.

  > [!NOTE]
  > Voor bereiding voor grote omgevingen, in plaats van het beheer netwerk dat wordt gebruikt voor het on-premises VMware-cluster, maakt u een nieuw/26-netwerk en presenteert u dat netwerk als een poort groep aan uw on-premises VMware-cluster.  U kunt vervolgens tot 10 service netten en 60 netwerk extenders (-1 per service-net) maken. U kunt **acht** netwerken per netwerk Extender uitrekken met behulp van persoonlijke Clouds van Azure VMware-oplossingen.
  >

- **Uplinkpoortprofiel:** Wanneer u on-premises VMware-HCX implementeert, moet u een Uplinkpoortprofiel identificeren voor VMware HCX. Gebruik hetzelfde netwerk dat u wilt gebruiken voor het beheer netwerk. 

- **vMotion-netwerk:** Wanneer u on-premises VMware-HCX implementeert, moet u een vMotion-netwerk voor VMware HCX identificeren.  Normaal gesp roken is het hetzelfde netwerk dat wordt gebruikt voor vMotion door uw on-premises VMware-cluster.  Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. U hebt mogelijk grotere aantallen nodig, afhankelijk van de schaal van uw implementatie na de proef of kleine use-cases.

   Het netwerk van vMotion moet zichtbaar zijn op een gedistribueerde virtuele switch of vSwitch0. Als dat niet het geval is, wijzigt u de omgeving zodat deze past.

   > [!NOTE]
   > Veel VMware-omgevingen gebruiken niet-gerouteerde netwerk segmenten voor vMotion, waardoor er geen problemen zijn.
  
- **Replicatie netwerk:** Wanneer u VMware HCX on-premises implementeert, moet u een replicatie netwerk definiëren. Gebruik hetzelfde netwerk als u gebruikt voor uw beheer-en uplink-netwerken.  Als de on-premises clusterhost hosts een toegewezen replicatie VMkernel-netwerk gebruiken, reserveert u **twee** IP-adressen in dit netwerk segment en gebruikt u het replicatie VMkernel-netwerk voor het replicatie netwerk.
