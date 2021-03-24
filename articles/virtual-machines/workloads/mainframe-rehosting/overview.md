---
title: Mainframe herhosten op Azure virtual machines
description: Host uw mainframe werkbelastingen zoals op IBM Z gebaseerde systemen met virtual machines (Vm's) op Microsoft Azure.
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.openlocfilehash: 7e4b41497a0ed49ab0b39ab2c93c0142fce6416e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951314"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe herhosten op Azure virtual machines

Als u werk belastingen migreert van mainframe omgevingen naar de Cloud, kunt u uw infra structuur moderniseren en vaak besparen op kosten. Veel workloads kunnen worden overgebracht naar Azure met slechts kleine codewijzigingen, zoals het bijwerken van de namen van databases.

Normaal gesp roken is de term *mainframe* een groot computer systeem. De grote meerderheid die momenteel in gebruik is, zijn IBM-systeem Z-servers of IBM-plug-compatible systemen die MVS, DOS, VSE, OS/390 of Z/OS uitvoeren.

Een virtuele machine (VM) van Azure wordt gebruikt om de resources voor een specifieke toepassing op één exemplaar te isoleren en te beheren. Mainframes zoals IBM z/OS gebruiken logische partities (LPARS) voor dit doel einde. Een mainframe kan een LPAR gebruiken voor een CICS-regio met bijbehorende COBOL-Program ma's en een afzonderlijke LPAR voor de IBM Db2-data base. Een typische [toepassing met n-tier op Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implementeert Azure vm's in een virtueel netwerk dat kan worden gesegmenteerd in subnetten voor elke laag.

Met virtuele machines van Azure kunnen mainframe-emulatie omgevingen en-compilers worden uitgevoerd die ondersteuning bieden voor lift-en Shift-scenario's. Ontwikkeling en testen zijn vaak de eerste workloads die worden gemigreerd van een mainframe naar een ontwikkel-en test omgeving van Azure. Algemene Server onderdelen die u kunt emuleren, zijn OLTP (online Trans Action process), batch-en gegevensopnamesystemen zoals in de volgende afbeelding wordt weer gegeven.

![Met emulatie omgevingen in azure kunt u op z/O'S gebaseerde systemen uitvoeren.](media/01-overview.png)

Sommige mainframe werkbelastingen kunnen naar Azure worden gemigreerd met relatief gemak, terwijl andere kunnen worden gehost op Azure met behulp van een partner oplossing. Voor gedetailleerde informatie over het kiezen van een partner oplossing, kan het [Azure mainframe-migratie centrum](https://azure.microsoft.com/migration/mainframe/) u helpen.

## <a name="mainframe-migration"></a>Mainframe-migratie

Opnieuw hosten, opnieuw bouwen, vervangen of buiten gebruik stellen? IaaS of PaaS? Als u de juiste migratie strategie voor uw mainframe-toepassing wilt bepalen, raadpleegt u de [mainframe-migratie](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  handleiding in het Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>Micro Focus platform voor opnieuw hosten

Micro Focus Enter prise server is een van de grootste platformen voor het hosten van mainframes die beschikbaar zijn. U kunt deze gebruiken om uw z/O'S-workloads uit te voeren op een minder duur x86-platform op Azure.

Aan de slag:

- [Enter prise server en Enter prise Developer installeren op Azure](./microfocus/set-up-micro-focus-azure.md)
- [CICS BankDemo instellen voor Enter prise Developer op Azure](./microfocus/demo.md)
- [Enter prise server uitvoeren in een docker-container in azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft open frame op Azure

TmaxSoft open frame is een populaire mainframe oplossing voor opnieuw hosten die wordt gebruikt in Lift-en Shift-scenario's. Een open frame-omgeving op Azure is geschikt voor ontwikkelings-, demonstratie-, test-of productie werk belastingen.

Aan de slag:

- [Aan de slag met TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Het ebook downloaden](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12,0

De IBM Z-ontwikkelings-en test omgeving (IBM zD&T) stelt een niet-productie omgeving in op Azure die u kunt gebruiken voor het ontwikkelen, testen en demo's van op Z/O'S gebaseerde toepassingen.

De emulatie omgeving van Azure kan verschillende soorten Z-instanties hosten via toepassings ontwikkelaars beheerde distributies (ADCDs). U kunt zD uitvoeren&T Personal Edition, zD&Sysplex en zD&T Enter prise Edition op Azure en Azure Stack.

Aan de slag:

- [IBM zD&T 12,0 op Azure instellen](./ibm/install-ibm-z-environment.md)
- [ADCD instellen op zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale op Azure

De IBM DB2 pureScale-omgeving biedt een database cluster voor Azure. Het is niet identiek aan de oorspronkelijke omgeving, maar biedt vergelijk bare Beschik baarheid en schaal als IBM DB2 voor z/OS dat wordt uitgevoerd in een parallelle Sysplex-installatie.

Zie [IBM DB2 pureScale in azure](.//ibm/ibm-db2-purescale-azure.md)om aan de slag te gaan.

## <a name="considerations"></a>Overwegingen

Wanneer u mainframe-workloads migreert naar Azure Infrastructure as a Service (IaaS), kunt u kiezen uit verschillende soorten, schaal bare computer bronnen op aanvraag, waaronder Azure-Vm's. Azure biedt een scala aan [Linux](../../linux/overview.md) -en [Windows](../../windows/overview.md) -vm's.

### <a name="compute"></a>Compute

Azure Compute-kracht vergelijkt de capaciteit van een mainframe. Als u een mainframe werk belasting wilt verplaatsen naar Azure, moet u de mainframe metriek van 1.000.000 instructies per seconde (MIPS) vergelijken met virtuele Cpu's. 

Meer informatie over het [verplaatsen van mainframe Compute naar Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Hoge Beschik baarheid en failover

Azure biedt op toezegging gebaseerde service overeenkomsten (Sla's). De beschik baarheid van meerdere Nines is de standaard waarde en service overeenkomsten kunnen worden geoptimaliseerd met lokale of geo-gebaseerde replicatie van services. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

Met Azure IaaS, zoals een virtuele machine, bieden specifieke systeem functies failover-ondersteuning, bijvoorbeeld voor instanties voor Failoverclustering en beschikbaarheids sets. Wanneer u Azure Platform as a Service-resources (PaaS) gebruikt, wordt de failover automatisch door het platform verwerkt. Voor beelden zijn [Azure SQL database](../../../azure-sql/database/sql-database-paas-overview.md) en [Azure Cosmos DB](../../../cosmos-db/introduction.md).

### <a name="scalability"></a>Schaalbaarheid

Mainframes worden doorgaans geschaald, terwijl Cloud omgevingen uitschalen. Azure biedt een scala aan [Linux](../../sizes.md) -en [Windows](../../sizes.md) -grootten die aan uw behoeften voldoen. De Cloud wordt ook omhoog of omlaag geschaald, zodat deze overeenkomt met de exacte specificaties van de gebruiker. Reken kracht, opslag en services kunnen op aanvraag worden [geschaald](/azure/architecture/best-practices/auto-scaling) onder een facturerings model op basis van gebruik.

### <a name="storage"></a>Storage

In de Cloud hebt u diverse flexibele, schaal bare opslag opties en betaalt u alleen voor wat u nodig hebt. [Azure Storage](../../../storage/common/storage-introduction.md) biedt een zeer schaal bare object opslag voor gegevens objecten, een File System-Service voor de Cloud, een betrouw bare berichten opslag en een NoSQL-archief. Voor Vm's, beheerde en onbeheerde schijven bieden permanente, beveiligde schijf opslag.

Meer informatie over het [verplaatsen van mainframe-opslag naar Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Back-ups maken en herstellen

Het onderhoud van uw eigen nood herstel site kan een dure toegevoegde zijn. Azure heeft eenvoudig te implementeren en rendabele opties voor [back-up](../../../backup/backup-overview.md), [herstel](../../../site-recovery/site-recovery-overview.md)en [Redundantie](../../../storage/common/storage-redundancy.md) op lokaal of regionaal niveau, of via Geo-redundantie.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government voor mainframe-migraties

Veel open bare sector entiteiten zouden graag hun mainframe-toepassingen willen verplaatsen naar een modern, flexibel platform. Microsoft Azure Government is een fysiek gescheiden exemplaar van het wereld wijde Microsoft Azure platform, verpakt voor federale, regionale en lokale overheids systemen. Het biedt de services voor beveiliging, bescherming en naleving van wereld klasse, specifiek voor Verenigde Staten overheids instanties en hun partners.

Azure Government een voorlopige autoriteit in te voeren (P-ATO) voor FedRAMP hoge impact voor systemen die dit type omgeving nodig hebben.

Als u aan de slag wilt gaan, moet u [Microsoft Azure Government-Cloud voor mainframe-toepassingen](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)downloaden.

## <a name="next-steps"></a>Volgende stappen

Vraag onze [partners](partner-workloads.md) om u te helpen uw mainframe-toepassingen te migreren of te hosten. 

Zie ook:

- [Technische documentatie over mainframe-onderwerpen](mainframe-white-papers.md)
- [Mainframe migratie](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Problemen oplossen](../../troubleshooting/index.yml)
- [Ontrafelen mainframe naar Azure-migratie](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md