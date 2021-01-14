---
title: Overzicht van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Overzicht van het implementeren van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ec3500c124d3e4f8cb1b46445c28c6a64c93526
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195446"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Wat is SAP HANA on Azure (grote exemplaren)?

SAP HANA op Azure (grote exemplaren) is een unieke oplossing voor Azure. Naast het leveren van virtuele machines voor het implementeren en uitvoeren van SAP HANA, biedt Azure u de mogelijkheid om SAP HANA uit te voeren en te implementeren op bare-metal servers die aan u zijn toegewezen. De oplossing voor het SAP HANA op Azure (grote exemplaren) is gebaseerd op niet-gedeelde host/server bare-metal hardware die aan u is toegewezen. De serverhardware is inge sloten in grotere stem pels die een Compute/Server-, netwerk-en opslag infrastructuur bevatten. Als een combi natie hiervan is de HANA-gecertificeerde Data Center-integratie (TDI) goedgekeurd. SAP HANA op Azure (grote exemplaren) biedt verschillende server-Sku's of-grootten. Eenheden kunnen 36 Intel CPU-kernen en 768 GB aan geheugen hebben en tot Maxi maal 480 Intel CPU-kernen en tot 24 TB aan geheugen beschikken.

De isolatie van de klant binnen het infrastructuur stempel wordt uitgevoerd in de tenants. dit ziet er als volgt uit:

- **Netwerken**: isolatie van klanten binnen infrastructuur stack via virtuele netwerken per door de klant toegewezen Tenant. Een Tenant wordt toegewezen aan één klant. Een klant kan meerdere tenants hebben. De netwerk isolatie van tenants verbiedt netwerk communicatie tussen tenants in het niveau van de infrastructuur stempel, zelfs als de tenants bij dezelfde klant horen.
- **Opslag onderdelen**: isolatie via opslag-virtuele machines waaraan opslag volumes zijn toegewezen. Opslag volumes kunnen alleen worden toegewezen aan één virtuele opslag machine. Een virtuele opslag machine wordt uitsluitend toegewezen aan één enkele Tenant in de SAP HANA TDI Certified Infrastructure-stack. Als gevolg hiervan kunnen opslag volumes die zijn toegewezen aan een virtuele opslag machine alleen worden geopend in een specifieke en gerelateerde Tenant. Ze zijn niet zichtbaar tussen de verschillende geïmplementeerde tenants.
- **Server of host**: een server of host-eenheid wordt niet gedeeld tussen klanten of tenants. Een server of host die is geïmplementeerd voor een klant, is een Atomic bare-metal Compute-eenheid die is toegewezen aan één enkele Tenant. *Er wordt geen* hardware-partitionering of zachte partitionering gebruikt. Dit kan ertoe leiden dat u een host of een server met een andere klant kunt delen. Opslag volumes die zijn toegewezen aan de virtuele opslag machine van de specifieke Tenant, worden aan een dergelijke server gekoppeld. Een Tenant kan één tot veel server eenheden van verschillende Sku's worden toegewezen.
- Binnen een SAP HANA op de infra structuur van Azure (grote instanties) worden veel verschillende tenants geïmplementeerd en geïsoleerd via de Tenant concepten op het niveau van netwerken, opslag en reken kracht. 


Deze bare-metal server eenheden worden alleen ondersteund om SAP HANA uit te voeren. De SAP-toepassingslaag of de werk belasting van de middelste workload-laag worden uitgevoerd in virtuele machines. De infra structuur-stem pels die de SAP HANA op Azure-eenheden (grote exemplaren) uitvoeren, zijn verbonden met de Azure Network Services-backbones. Op deze manier wordt connectiviteit met lage latentie tussen SAP HANA op Azure-eenheden (grote exemplaren) en virtuele machines gegeven.

Vanaf januari 2021 maken we onderscheid tussen twee verschillende revisies van HANA grote instantie stempels en locatie van implementaties:

- Revisie 3 (Rev 3): zijn de stem pels die beschikbaar zijn gesteld voor de klant om te implementeren vóór 2019 juli
- "Revisie 4" (Rev 4): nieuw stempel ontwerp dat is geïmplementeerd in dicht bij Azure VM-hosts en wat tot nu toe wordt vrijgegeven in de Azure-regio's van:
    -  VS - west 2 
    -  VS - oost
    -  Oost-VS2 (tussen twee Beschikbaarheidszones)
    -  VS Zuid-Centraal (tussen twee Beschikbaarheidszones)
    -  Europa -west
    -  Europa - noord


Dit document is een van de documenten die betrekking hebben SAP HANA op Azure (grote exemplaren). In dit document worden de basis architectuur, de verantwoordelijkheden en de services van de oplossing geïntroduceerd. Ook de mogelijkheden van de oplossing op hoog niveau worden besproken. Voor de meeste andere gebieden, zoals netwerken en connectiviteit, hebben vier andere documenten betrekking op Details en inzoomen. De documentatie van SAP HANA op Azure (grote instanties) heeft geen betrekking op de installatie of implementatie van SAP NetWeaver in Vm's. SAP NetWeaver op Azure wordt behandeld in afzonderlijke documenten die zijn gevonden in dezelfde Azure-documentatie container. 


De verschillende documenten van de HANA-ondersteuning voor grote instanties omvatten de volgende gebieden:

- [Overzicht en architectuur van SAP HANA (grote exemplaren) op Azure](hana-overview-architecture.md)
- [Infra structuur en connectiviteit van SAP HANA (grote instanties) op Azure](hana-overview-infrastructure-connectivity.md)
- [SAP HANA (grote exemplaren) installeren en configureren op Azure](hana-installation.md)
- [SAP HANA (grote instanties) hoge Beschik baarheid en herstel na nood geval op Azure](hana-overview-high-availability-disaster-recovery.md)
- [Problemen met SAP HANA (grote instanties) oplossen en controleren op Azure](troubleshooting-monitoring.md)
- [Hoge Beschik baarheid die in SUSE is ingesteld met behulp van de STONITH](./ha-setup-with-stonith.md)
- [Back-up en herstel van het besturings systeem voor de type II Sku's van Revision 3-stem pels](./os-backup-type-ii-skus.md)
- [Besparen op SAP HANA Large Instances met een Azure-reservering](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**Volgende stappen**
- Raadpleeg [de voor waarden](hana-know-terms.md)
