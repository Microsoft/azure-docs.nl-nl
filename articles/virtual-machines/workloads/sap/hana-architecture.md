---
title: Architectuur van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Architectuur van het implementeren van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd81139c15e6bba65fe9b9a53a2e8e950ebd754b
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201753"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architectuur van SAP HANA (grote instanties) op Azure

Op hoog niveau heeft de oplossing voor SAP HANA op Azure (grote instanties) de SAP-toepassingslaag op Vm's. De Gegevenstoegangslaag bevindt zich op SAP TDI-geconfigureerde hardware die zich bevindt in een grote instantie stempel in dezelfde Azure-regio die is verbonden met Azure IaaS.

> [!NOTE]
> Implementeer de SAP-toepassingslaag in dezelfde Azure-regio als de SAP-DBMS-laag. Deze regel is goed gedocumenteerd in gepubliceerde informatie over SAP-workloads op Azure. 

De algemene architectuur van SAP HANA op Azure (grote instanties) biedt een SAP-gecertificeerde hardwareconfiguratie van het apparaat. Dit is een niet-gevirtualiseerde, Bare Metal, krachtige server voor de SAP HANA-data base. Het biedt ook de mogelijkheid en flexibiliteit van Azure om resources voor de SAP-toepassingslaag te schalen om te voldoen aan uw behoeften.

![Architectuur overzicht van SAP HANA op Azure (grote exemplaren)](./media/hana-overview-architecture/image1-architecture.png)

De weer gegeven architectuur is onderverdeeld in drie secties:

- **Rechts**: toont een on-premises infra structuur die verschillende toepassingen in data centers uitvoert, zodat eind gebruikers toegang hebben tot LOB-toepassingen, zoals SAP. In het ideale geval is deze on-premises infra structuur verbonden met Azure met [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: hier worden Azure-IaaS weer gegeven. in dit geval gebruikt u vm's voor het hosten van SAP of andere toepassingen die gebruikmaken van SAP Hana als DBMS-systeem. Kleinere HANA-instanties die werken met het geheugen dat Vm's bieden, worden in Vm's geïmplementeerd samen met hun toepassingslaag. Zie [virtuele machines](https://azure.microsoft.com/services/virtual-machines/)voor meer informatie over virtuele machines.

   Azure Network Services worden gebruikt voor het groeperen van SAP-systemen met andere toepassingen in virtuele netwerken. Deze virtuele netwerken maken verbinding met on-premises systemen en SAP HANA op Azure (grote exemplaren).

   Voor SAP NetWeaver-toepassingen en-data bases die worden ondersteund voor uitvoering in azure, raadpleegt u [SAP-ondersteuning opmerking #1928533: SAP-toepassingen op Azure: ondersteunde producten en typen Azure VM](https://launchpad.support.sap.com/#/notes/1928533). Zie voor documentatie over het implementeren van SAP-oplossingen in Azure:

  -  [SAP op virtuele Windows-machines gebruiken](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [SAP-oplossingen gebruiken op virtuele machines van Azure](get-started.md)

- **Links**: toont de SAP Hana TDI-gecertificeerde hardware in de Azure-stempel voor grote instanties. De HANA-eenheden voor grote instanties zijn verbonden met de virtuele netwerken van uw Azure-abonnement met behulp van dezelfde technologie als de connectiviteit van on-premises in Azure. Vanaf mei 2019 werd een Optima Lise ring geïntroduceerd waarmee kan worden gecommuniceerd tussen de HANA-grote exemplaar eenheden en de virtuele Azure-machines zonder betrokkenheid van de ExpressRoute-gateway. Deze optimalisatie met de naam ExpressRoute Fast path wordt weer gegeven in deze architectuur (rode lijnen). 

De Azure large instance-stempel zelf combineert de volgende onderdelen:

- **Computing**: servers die zijn gebaseerd op verschillende generatie Intel Xeon-processors die de benodigde reken capaciteit bieden en SAP Hana gecertificeerd zijn.
- **Netwerk**: een uniforme netwerk infrastructuur met hoge snelheid die de computer-, opslag-en LAN-onderdelen verbindt.
- **Opslag**: een opslag infrastructuur die toegankelijk is via een uniforme netwerk infrastructuur. De specifieke opslag capaciteit die wordt opgegeven, is afhankelijk van de specifieke SAP HANA op de configuratie van Azure (grote instanties) die is geïmplementeerd. Meer opslag capaciteit is beschikbaar tegen een extra maandelijkse prijs.

Binnen de multi tenant-infra structuur van de stempel met grote instanties worden klanten geïmplementeerd als geïsoleerde tenants. Tijdens de implementatie van de Tenant kunt u een Azure-abonnement binnen uw Azure-inschrijving noemen. Dit Azure-abonnement is de versie waarop de HANA grote instantie wordt gefactureerd. Deze tenants hebben een 1:1-relatie met het Azure-abonnement. Voor een netwerk is het mogelijk om toegang te krijgen tot een HANA grote instantie-eenheid die is geïmplementeerd in één Tenant in één Azure-regio vanuit verschillende virtuele netwerken die tot verschillende Azure-abonnementen behoren. Deze Azure-abonnementen moeten deel uitmaken van dezelfde Azure-inschrijving. 

Net als bij virtuele machines wordt SAP HANA op Azure (grote exemplaren) aangeboden in meerdere Azure-regio's. Als u mogelijkheden voor herstel na nood gevallen wilt bieden, kunt u kiezen om u aan te melden. Verschillende stem pels voor grote instanties binnen één geo-politieke regio zijn met elkaar verbonden. Bijvoorbeeld: HANA grote instantie tempels in VS West en VS Oost zijn verbonden via een speciale netwerk koppeling voor herstel na nood gevallen. 

Net zoals u kunt kiezen tussen verschillende VM-typen met Azure Virtual Machines, kunt u kiezen uit verschillende Sku's van HANA grote instanties die zijn afgestemd op verschillende soorten werk belasting van SAP HANA. SAP past geheugen-naar-processor socket-verhoudingen toe voor het variëren van werk belastingen op basis van de Intel-processor generaties. De volgende tabel toont de SKU-typen die worden aangeboden.

U vindt beschik bare sku's [beschik bare sku's voor HLI](hana-available-skus.md).

**Volgende stappen**
- Zie [SAP Hana netwerk architectuur (grote exemplaren)](hana-network-architecture.md)
