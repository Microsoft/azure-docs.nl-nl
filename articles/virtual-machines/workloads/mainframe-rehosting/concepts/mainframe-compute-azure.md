---
title: Mainframe Compute verplaatsen naar Azure Virtual Machines
description: Azure Compute-resources vergelijken met de mainframe capaciteit, zodat u IBM z14-toepassingen kunt migreren en moderniseren.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 84682514d83de9d6a613581d68301efe33cd44ee
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954391"
---
# <a name="move-mainframe-compute-to-azure"></a>Mainframe Compute verplaatsen naar Azure

Mainframes hebben een reputatie voor hoge betrouw baarheid en beschik baarheid en blijven de vertrouwde backbone van veel ondernemingen. Het is vaak een goed idee om bijna onbeperkte schaal baarheid en reken kracht te hebben. Sommige ondernemingen hebben echter de mogelijkheid om de grootste beschik bare mainframes te verg Roten. Als dit zo klinkt als u, biedt Azure flexibiliteit, bereiken en infrastructuur besparingen.

Als u mainframe-workloads op Microsoft Azure wilt uitvoeren, moet u weten hoe de reken mogelijkheden van uw mainframe worden vergeleken met Azure. In dit artikel wordt beschreven hoe u op basis van een IBM z14 mainframe (het meest recente model van deze schrijf bewerking) een vergelijk bare resultaten krijgt op Azure.

Als u aan de slag wilt gaan, moet u rekening houden met de omgevingen naast elkaar. In de volgende afbeelding wordt een mainframe-omgeving vergeleken voor het uitvoeren van toepassingen op een Azure-hosting omgeving.

![Azure-Services en-emulatie omgevingen bieden vergelijk bare ondersteuning en kunnen de migratie stroom lijnen](media/mainframe-compute-azure.png)

De kracht van mainframes wordt vaak gebruikt voor OLTP-systemen (online Trans Action processing) die miljoenen updates voor duizenden gebruikers verwerken. Deze toepassingen gebruiken vaak software voor transactie verwerking, scherm verwerking en formulier invoer. Ze kunnen gebruikmaken van een klant Information Control System (CICS), een IMS (Information Management System) of een transactie interface pakket (TIP).

Zoals in de afbeelding wordt weer gegeven, kan een TPM-emulator op Azure de werk belasting van CICS en IMS verwerken. Een batch systeem emulator in azure voert de rol van taak besturings taal (JCL). Mainframe gegevens worden gemigreerd naar Azure-data bases, zoals Azure SQL Database. Azure-Services of andere software die worden gehost in azure Virtual Machines kunnen worden gebruikt voor systeem beheer.

## <a name="mainframe-compute-at-a-glance"></a>Mainframe Compute in één oogopslag

In de z14-mainframe worden de processors in Maxi maal vier *bakken* geordend. Een *lade* is gewoon een cluster met processors en chip sets. Elke lade kan zes actieve Central processor-chips (CP) hebben en elke CP heeft 10 systeem controller (SC)-chips. In de Intel x86-terminologie zijn er zes sockets per lade, 10 kernen per socket en vier lade. Deze architectuur biedt het ruwe equivalent van 24 sockets en 240 kernen, maximum, voor een z14.

De snelle z14 CP heeft een klok snelheid van 5,2 GHz. Normaal gesp roken wordt een z14 met alle CPs in het vak geleverd. Ze worden naar behoefte geactiveerd. Een klant wordt doorgaans gefactureerd voor ten minste vier uur reken tijd per maand, ondanks het werkelijke gebruik.

Een mainframe-processor kan worden geconfigureerd als een van de volgende typen:

- Algemeen-processor (GP)
- Systeem z Integrated Information processor (zIIP)
- Integrated Facility for Linux (IFL)-processor
- Systeem assistent-processor (SAP)
- De processor voor geïntegreerde koppelings faciliteit (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Mainframe-Compute omhoog en uitschalen

IBM-mainframes bieden de mogelijkheid om tot 240 kernen te schalen (de huidige z14-grootte voor één systeem). Daarnaast kan IBM-mainframes worden uitgeschaald via een functie die de koppelings faciliteit (CF) wordt genoemd. Met CF kunnen meerdere mainframe systemen tegelijkertijd toegang hebben tot dezelfde gegevens. Met de CF, de mainframe parallel Sysplex Technology, worden mainframe-processors in clusters gegroepeerd. Wanneer deze hand leiding is geschreven, wordt met de functie parallel Sysplex 32 groepen van 64 processors elk ondersteund. Maxi maal 2.048 processors kunnen op deze manier worden gegroepeerd om reken capaciteit uit te schalen.

Met een CF kunnen compute-clusters gegevens delen met directe toegang. Dit wordt gebruikt voor het vergren delen van informatie, het opslaan van gegevens in de cache en de lijst met gedeelde gegevens bronnen. Een parallelle Sysplex die gebruikmaakt van een of meer CFs, kan worden beschouwd als een scale-out-berekenings cluster dat wordt gedeeld met alles. Zie [parallel Sysplex op IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) op de IBM-website voor meer informatie over deze functies.

Toepassingen kunnen deze functies gebruiken om zowel uitbreid bare prestaties als hoge Beschik baarheid te bieden. Voor informatie over hoe CICS parallelle Sysplex met CF kan gebruiken, moet u de [IBM CICS en de koppelings faciliteit downloaden: meer dan de basis principes](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) van Redbook.

## <a name="azure-compute-at-a-glance"></a>Azure Compute in één oogopslag

Sommige mensen denken er per ongeluk voor dat op Intel gebaseerde servers niet zo krachtig zijn als mainframes. De nieuwe grootschalige, op Intel gebaseerde systemen hebben echter zoveel reken capaciteit als mainframes. In deze sectie worden de Azure Infrastructure-as-a-Service-opties (IaaS) voor computing en opslag beschreven. Azure biedt ook PaaS-opties (platform-as-a-Service), maar dit artikel richt zich op de IaaS-keuzes die vergelijk bare mainframe capaciteit bieden.

Azure Virtual Machines bieden reken kracht in een bereik van grootten en typen. In Azure is een virtuele CPU (vCPU) ruwweg gelijk aan een kern op een mainframe.

Op dit moment is het bereik van de groottes van Azure virtual machines van 1 tot 128 Vcpu's. VM-typen (virtuele machine) zijn geoptimaliseerd voor bepaalde werk belastingen. De volgende lijst bevat bijvoorbeeld de VM-typen (huidige op basis van dit schrijven) en de aanbevolen toepassingen:

| Grootte     | Type en beschrijving                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-serie | Algemeen gebruik met 64 vCPU en Maxi maal 3,5 GHz klok snelheid                           |
| E-serie | Geheugen dat is geoptimaliseerd met Maxi maal 64 Vcpu's                                                 |
| F-serie | Compute Optimized with 64 Vcpu's en 3.. 7 GHz klok snelheid                       |
| H-serie | Geoptimaliseerd voor HPC-toepassingen (High-Performance Computing)                          |
| L-serie | Opslag geoptimaliseerd voor toepassingen met hoge door Voer die worden ondersteund door data bases zoals NoSQL |
| M-serie | Grootste Vm's met reken kracht en geoptimaliseerd voor geheugen met Maxi maal 128 Vcpu's                        |

Zie [reeksen virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/series/)voor meer informatie over beschik bare vm's.

Een z14-mainframe kan Maxi maal 240 kernen hebben. Z14-mainframes gebruiken echter bijna nooit alle kernen voor één toepassing of werk belasting. In plaats daarvan worden workloads gescheiden in logische partities (LPARs) en de LPARs hebben beoordelingen: MIPS (miljoenen instructies per seconde) of MSU (miljoen service-eenheid). Bij het bepalen van de vergelijk bare VM-grootte die nodig is om een mainframe werk belasting uit te voeren op Azure, wordt de waarde van de MIPS-classificatie (of MSU) bepaald.

Hieronder vindt u algemene schattingen:

-   150 MIPS per vCPU

-   1.000 MIPS per processor

Als u de juiste VM-grootte voor een bepaalde werk belasting in een LPAR wilt bepalen, optimaliseert u eerst de virtuele machine voor de werk belasting. Bepaal vervolgens het aantal Vcpu's dat nodig is. Een conservatieve schatting is 150 MIPS per vCPU. Op basis van deze schatting kan bijvoorbeeld een virtuele machine met een F-serie met 16 Vcpu's een IBM Db2-workload die afkomstig is van een LPAR met 2.400 MIPS, eenvoudig ondersteunen.

## <a name="azure-compute-scale-up"></a>Schaal van Azure Compute-up

De virtuele machines uit de M-serie kunnen tot 128 Vcpu's worden geschaald (op het moment dat dit artikel is geschreven). Met behulp van de conservatieve schatting van 150 MIPS per vCPU is de VM uit de M-serie gelijk aan ongeveer 19.000 MIPS. De algemene regel voor het schatten van MIPS voor een mainframe is 1.000 MIPS per processor. Een z14-mainframe kan Maxi maal 24 processors hebben en biedt ongeveer 24.000 MIPS voor één mainframe systeem.

De grootste single z14 mainframe heeft ongeveer 5.000 MIPS meer dan de grootste virtuele machine die beschikbaar is in Azure. Het is echter belang rijk om te vergelijken hoe workloads worden geïmplementeerd. Als een mainframe systeem zowel een toepassing als een relationele data base heeft, worden deze doorgaans geïmplementeerd op hetzelfde fysieke mainframe, elk in een eigen LPAR. Dezelfde oplossing op Azure wordt vaak geïmplementeerd met behulp van één virtuele machine voor de toepassing en een afzonderlijke, een VM-grootte voor de data base.

Als bijvoorbeeld een M64 vCPU-systeem de toepassing ondersteunt en er een M96-vCPU wordt gebruikt voor de data base, zijn ongeveer 150 Vcpu's nodig, of ongeveer 24.000 MIPS, zoals in de volgende afbeelding wordt weer gegeven.

![Implementaties van workloads van 24.000 MIPS vergelijken](media/mainframe-compute-mips.png)

De benadering is het migreren van LPARs naar afzonderlijke Vm's. Vervolgens schaalt Azure eenvoudig tot de grootte die nodig is voor de meeste toepassingen die op één mainframe systeem worden geïmplementeerd.

## <a name="azure-compute-scale-out"></a>Azure Compute scale-out

Een van de voor delen van een Azure-gebaseerde oplossing is de mogelijkheid om uit te schalen. Schaling maakt bijna onbeperkte reken capaciteit beschikbaar voor een toepassing. Azure ondersteunt meerdere methoden om reken kracht uit te schalen:

- **Taak verdeling over een cluster.** In dit scenario kan een toepassing een [Load Balancer](../../../../load-balancer/load-balancer-overview.md) of Resource Manager gebruiken om de werk belasting over meerdere vm's in een cluster uit te breiden. Als er meer reken capaciteit nodig is, worden er extra Vm's aan het cluster toegevoegd.

- **Schaal sets voor virtuele machines.** In dit burst-scenario kan een toepassing worden geschaald naar extra [reken resources](../../../../virtual-machine-scale-sets/overview.md) op basis van het gebruik van de virtuele machine. Wanneer de vraag daalt, kan het aantal Vm's in een schaalset ook worden uitgeschakeld, waardoor het gebruik van reken kracht efficiënt kan worden benut.

- **PaaS schalen.** Azure PaaS-aanbiedingen schaal Compute-resources. Zo wijst [Azure service Fabric](../../../../service-fabric/service-fabric-overview.md) reken bronnen toe om te voldoen aan de toename van het aantal aanvragen.

- **Kubernetes-clusters.** Toepassingen op Azure kunnen gebruikmaken van [Kubernetes-clusters](../../../../aks/concepts-clusters-workloads.md) voor Compute-Services voor opgegeven resources. Azure Kubernetes service (AKS) is een beheerde service die Kubernetes-knoop punten, Pools en clusters in azure organiseert.

Als u de juiste methode voor het schalen van reken resources wilt kiezen, is het belang rijk om te begrijpen hoe Azure en mainframes verschillen. De sleutel is hoe, of als — gegevens worden gedeeld door reken bronnen. In azure wordt gegevens (standaard) doorgaans niet gedeeld door meerdere Vm's. Als het delen van gegevens vereist is voor meerdere Vm's in een scale-out Compute-Cluster, moeten de gedeelde gegevens zich in een resource bevinden die deze functionaliteit ondersteunt. Voor het delen van gegevens in Azure is opslag vereist, zoals in de volgende sectie wordt beschreven.

## <a name="azure-compute-optimization"></a>Optimalisatie van Azure compute

U kunt elke verwerkings lagen optimaliseren in een Azure-architectuur. Gebruik het meest geschikte type Vm's en functies voor elke omgeving. In de volgende afbeelding ziet u één mogelijk patroon voor het implementeren van Vm's in azure ter ondersteuning van een CICS-toepassing die gebruikmaakt van Db2. Op de primaire site worden de Vm's productie, preproductie en testen geïmplementeerd met hoge Beschik baarheid. De secundaire site is voor back-up en herstel na nood gevallen.

Elke laag kan ook geschikte nood herstel services bieden. Voor productie-en data base-Vm's is mogelijk een dynamisch of warme herstel vereist, terwijl de ontwikkel-en test-Vm's een koud herstel ondersteunen.

![Hoge beschik bare implementatie die ondersteuning biedt voor herstel na nood gevallen](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Volgende stappen

- [Mainframe migratie](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe herhosten op Azure Virtual Machines](../overview.md)
- [Mainframe-opslag verplaatsen naar Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM-bronnen

- [Parallelle Sysplex op IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS en de koppelings faciliteit: meer dan de basis beginselen](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [De vereiste gebruikers voor een installatie van een Db2 pureScale-functie maken](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [De opdracht Db2icrt-instantie maken](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Geclusterde database oplossing voor Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government Cloud voor mainframe-toepassingen](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Micro soft en FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Meer migratie resources

- [Azure Virtual Data Center lift-en Shift-gids](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
