---
title: Beschik baarheid van SAP HANA op virtuele machines van Azure-overzicht | Microsoft Docs
description: Hierin worden SAP HANA bewerkingen op Azure native Vm's beschreven.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 757dfc34e3be12d09b8f965a2bb0295adb712c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504232"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA hoge Beschik baarheid voor virtuele machines van Azure

U kunt talloze Azure-mogelijkheden gebruiken om essentiële data bases te implementeren, zoals SAP HANA op virtuele machines van Azure. Dit artikel bevat richt lijnen voor het bezorgen van Beschik baarheid voor SAP HANA exemplaren die worden gehost in virtuele machines van Azure. In het artikel worden verschillende scenario's beschreven die u kunt implementeren met behulp van de Azure-infra structuur om de beschik baarheid van SAP HANA in azure te verg Roten. 

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de basis principes van IaaS (Infrastructure as a Service) in azure, waaronder: 

- Virtuele machines of virtuele netwerken implementeren via de Azure Portal of Power shell.
- Met de Azure platformoverschrijdende opdracht regel interface (Azure CLI), met inbegrip van de optie voor het gebruik van JavaScript Object Notation (JSON)-Sjablonen.

In dit artikel wordt ervan uitgegaan dat u bekend bent met het installeren van SAP HANA-instanties en met beheer-en besturings-SAP HANA exemplaren. Het is vooral belang rijk dat u bekend bent met het instellen en uitvoeren van HANA-systeem replicatie. Dit omvat taken als back-up en herstel voor SAP HANA-data bases.

Deze artikelen bieden een goed overzicht van het gebruik van SAP HANA in Azure:

- [Hand matige installatie van SAP HANA met één exemplaar op virtuele machines van Azure](./hana-get-started.md)
- [SAP HANA systeem replicatie in virtuele Azure-machines instellen](sap-hana-high-availability.md)
- [Back-ups maken van SAP HANA in virtuele Azure-machines](./sap-hana-backup-guide.md)

Het is ook een goed idee om vertrouwd te raken met deze artikelen over SAP HANA:

- [Hoge Beschik baarheid voor SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Veelgestelde vragen: hoge Beschik baarheid voor SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Systeem replicatie voor SAP HANA uitvoeren](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2,0 SPS 01 wat is er nieuw: hoge Beschik baarheid](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Netwerk aanbevelingen voor SAP HANA systeem replicatie](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA systeem replicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Automatisch opnieuw starten van SAP HANA-service](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA systeem replicatie configureren](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Naast het implementeren van Vm's in azure, wordt u aangeraden [de beschik baarheid van virtuele Windows-machines in azure te beheren](../../availability.md)voordat u uw beschikbaarheids architectuur in azure definieert.

## <a name="service-level-agreements-for-azure-components"></a>Service overeenkomst voor Azure-onderdelen

Azure heeft verschillende beschik baarheids overeenkomsten voor verschillende onderdelen, zoals netwerken, opslag en virtuele machines. Alle service overeenkomsten worden gedocumenteerd. Zie [Microsoft Azure service overeenkomst](https://azure.microsoft.com/support/legal/sla/)voor meer informatie. 

In de [Sla voor virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) worden drie verschillende sla's beschreven, voor drie verschillende configuraties:

- Eén virtuele machine die gebruikmaakt van [Azure Premium ssd's](../../managed-disks-overview.md) voor de besturingssysteem schijf en alle gegevens schijven. Deze optie biedt een maandelijkse uptime van 99,9 procent.
- Meerdere virtuele machines (ten minste twee) die zijn georganiseerd in een [Azure-beschikbaarheidsset](../../windows/tutorial-availability-sets.md). Deze optie biedt een maandelijkse uptime van 99,95 procent.
- Meerdere virtuele machines (ten minste twee) die zijn georganiseerd in een [Beschik baarheid zone](../../../availability-zones/az-overview.md). Met deze optie wordt een maandelijkse uptime van 99,99 procent gegeven.

Meet uw beschikbaarheids vereisten met betrekking tot de Sla's die Azure-onderdelen kunnen bieden. Kies vervolgens uw scenario's voor SAP HANA om het vereiste niveau van Beschik baarheid te bepalen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Beschik baarheid van SAP Hana binnen een Azure-regio](./sap-hana-availability-one-region.md).
- Meer informatie over de [Beschik baarheid van SAP Hana in azure-regio's](./sap-hana-availability-across-regions.md). 















