---
title: VMware HCX-netwerksegmenten
description: Er zijn vier netwerken vereist voor VMware HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622097"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Er zijn verschillende manieren om VMware HCX-netwerk segmenten on-premises te configureren. Hieronder wordt een eenvoudige configuratie beschreven die ondersteuning biedt voor een proef-of kleine productie-use case.  Bij het ontwerpen voor honderden of duizenden werk belastingen moet deze configuratie mogelijk worden gewijzigd, afhankelijk van de behoeften van de migratie.  

Bij de voor bereiding van de VMware HCX-implementatie voor de ondersteuning van de pilot-of kleine productie use-case, moet u het volgende identificeren:

- **Beheer netwerk:** Wanneer u VMware HCX on-premises implementeert, moet u een beheer netwerk definiëren.  Normaal gesp roken is het hetzelfde beheer netwerk dat wordt gebruikt door uw on-premises VMware-cluster.  Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. U hebt mogelijk grotere aantallen nodig, afhankelijk van de schaal van uw implementatie na de proef of kleine use-cases.

> [!NOTE]
   > De aanbevolen methode is om een/26-netwerk te maken, omdat u Maxi maal 10 service netten en 60 netwerk extenders (-1 per service-net) kunt gebruiken. U kunt **acht** netwerken per netwerk Extender uitrekken met behulp van persoonlijke Clouds van Azure VMware-oplossingen.
   >
   
- **vMotion-netwerk:** Wanneer u on-premises VMware-HCX implementeert, moet u een vMotion-netwerk definiëren.  Normaal gesp roken is het hetzelfde netwerk dat wordt gebruikt voor vMotion door uw on-premises VMware-cluster.  Identificeer ten minste **twee** IP-adressen op dit netwerk segment voor VMware-HCX. U hebt mogelijk grotere aantallen nodig, afhankelijk van de schaal van uw implementatie na de proef of kleine use-cases.

   Het netwerk van vMotion moet zichtbaar zijn op een gedistribueerde virtuele switch of vSwitch0. Als dat niet het geval is, wijzigt u de omgeving zodat deze past.

   > [!NOTE]
   > Veel VMware-omgevingen gebruiken niet-gerouteerde netwerk segmenten voor vMotion, waardoor er geen problemen zijn.

- **Uplinkpoortprofiel:** Wanneer u VMware HCX on-premises implementeert, moet u een Uplinkpoortprofiel definiëren. Gebruik het beheer netwerk dat is gedefinieerd als uw uplink-netwerk.
   
- **Replicatie netwerk:** Wanneer u VMware HCX on-premises implementeert, moet u een replicatie netwerk definiëren. Gebruik het beheer netwerk dat is gedefinieerd als uw replicatie netwerk.  Als de on-premises clusterhost hosts een toegewezen replicatie VMkernel-netwerk gebruiken, reserveert u **twee** IP-adressen in dit netwerk segment en gebruikt u het replicatie VMkernel-netwerk voor het replicatie netwerk.
