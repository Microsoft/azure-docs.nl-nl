---
title: Certificering van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Certificering van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8d4781ad9ca2a30e8523d0e2446dc3903a7cb8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668865"
---
# <a name="certification"></a>Certificering

Naast de certificering voor het geweven, vereist SAP een speciale certificering voor SAP Hana om SAP Hana te ondersteunen op bepaalde infra structuren, zoals Azure IaaS.

De belangrijkste SAP-opmerking op NetWeaver en een diploma SAP HANA certificering is [SAP Note #1928533 – SAP-toepassingen op Azure: ondersteunde producten en typen Azure VM](https://launchpad.support.sap.com/#/notes/1928533).

De certificerings records voor SAP HANA op Azure-eenheden (grote exemplaren) zijn te vinden op de [SAP Hana Certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) -site. 

De SAP HANA van de typen Azure (grote instanties), waarnaar wordt verwezen in de site SAP HANA Certified IaaS platforms, biedt micro soft-en SAP-klanten de mogelijkheid om grote SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA of andere SAP HANA workloads in azure te implementeren. De oplossing is gebaseerd op de SAP-HANA-gecertificeerde specifieke hardware-stempel ([SAP Hana aangepaste datacenter integratie – TDI](https://scn.sap.com/docs/DOC-63140)). Als u een SAP HANA TDI-geconfigureerde oplossing uitvoert, werken alle op SAP HANA gebaseerde toepassingen (zoals SAP Business Suite op SAP HANA, SAP BW op SAP HANA, S4/HANA en BW4/HANA) in de hardware-infra structuur.

Vergeleken met het uitvoeren van SAP HANA in Vm's heeft deze oplossing een voor deel. Het biedt veel grotere geheugen volumes. Als u deze oplossing wilt inschakelen, moet u bekend zijn met de volgende belang rijke aspecten:

- De SAP-toepassingslaag en niet-SAP-toepassingen worden uitgevoerd op Vm's die worden gehost in de gebruikelijke Azure-hardware-tempels.
- De on-premises infra structuur van de klant, data centers en toepassings implementaties zijn verbonden met het Cloud platform via ExpressRoute (aanbevolen) of een virtueel particulier netwerk (VPN). Active Directory en DNS worden ook uitgebreid naar Azure.
- Het SAP HANA data base-exemplaar voor HANA-werk belasting wordt uitgevoerd op SAP HANA op Azure (grote exemplaren). De stempel van een grote instantie is verbonden met Azure-netwerken, zodat software die in Vm's wordt uitgevoerd, kan communiceren met het HANA-exemplaar dat wordt uitgevoerd in HANA grote instantie.
- Hardware van SAP HANA op Azure (grote exemplaren) is specifieke hardware die is opgenomen in een IaaS met SUSE Linux Enterprise Server of Red Hat Enterprise Linux vooraf geïnstalleerd. Net als bij virtuele machines zijn verdere updates en onderhoud van het besturings systeem uw verantwoordelijkheid.
- De installatie van HANA of extra onderdelen die nodig zijn voor het uitvoeren van SAP HANA op eenheden van HANA grote instanties is uw verantwoordelijkheid. Alle respectieve operationele activiteiten en het beheer van SAP HANA op Azure zijn ook uw verantwoordelijkheid.
- Naast de oplossingen die hier worden beschreven, kunt u andere onderdelen in uw Azure-abonnement installeren die verbinding maken met SAP HANA op Azure (grote exemplaren). Voor beelden hiervan zijn onderdelen die communicatie mogelijk maken met of rechtstreeks naar de SAP HANA-data base, zoals Jump servers, RDP-servers, SAP HANA Studio, SAP Data Services voor SAP BI-scenario's of oplossingen voor netwerk bewaking.
- Net als in Azure biedt HANA grote instanties ondersteuning voor hoge Beschik baarheid en herstel na een nood geval.

**Volgende stappen**
- [Beschik bare sku's voor HLI](hana-available-skus.md) 