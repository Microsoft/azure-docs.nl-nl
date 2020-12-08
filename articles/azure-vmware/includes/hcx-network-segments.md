---
title: VMware HCX-netwerksegmenten
description: Er zijn vier netwerken vereist voor VMware HCX.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999622"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Er zijn vier netwerken vereist voor VMware HCX:

- **Beheernetwerk:** Meestal is dit hetzelfde beheernetwerk dat wordt gebruikt voor het vSphere-cluster. Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.

   > [!NOTE]
   > De aanbevolen methode is het maken van een /26-netwerk. In een/26-netwerk kunt u maximaal 10 service-meshes en 60 netwerk-extenders (-1 per service-mesh) gebruiken. U kunt acht netwerken per netwerk-extender stretchen met behulp van privéclouds van de Azure VMware-oplossing.
   >
   
- **vMotion-netwerk:** Meestal is dit hetzelfde netwerk dat wordt gebruikt voor vMotion in de vSphere-cluster.  Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.  

   Het netwerk van vMotion moet zichtbaar zijn op een gedistribueerde virtuele switch of vSwitch0. Als dat niet het geval is, wijzig dan de omgeving.

   > [!NOTE]
   > Dit netwerk kan privé zijn (niet gerouteerd).

- **Uplinknetwerk:** Maak een nieuw netwerk voor VMware HCX Uplink en breidt dit naar uw vSphere-cluster uit via een poortgroep. Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.  

   > [!NOTE]
   > De aanbevolen methode is het maken van een /26-netwerk. In een/26-netwerk kunt u maximaal 10 service-meshes en 60 netwerk-extenders (-1 per service-mesh) gebruiken. U kunt acht netwerken per netwerk-extender stretchen met behulp van privéclouds van de Azure VMware-oplossing.
   >
   
- **Replicatienetwerk:** Dit is optioneel. Maak een nieuw netwerk voor VMware HCX Replication en breidt dit naar uw vSphere-cluster uit via een poortgroep. Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.

   > [!NOTE]
   > Deze configuratie is alleen mogelijk als de on-premises clusterhosts een toegewezen VMkernel-netwerk voor replicatie gebruiken.  Als er voor uw on-premises cluster geen toegewezen VMkernel-netwerk voor replicatie is gedefinieerd, hoeft u dit netwerk niet te maken.
