---
title: Azure VM-grootten-HPC | Microsoft Docs
description: Geeft een lijst van de verschillende beschik bare grootten voor High Performance Computing virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772430"
---
# <a name="high-performance-computing-vm-sizes"></a>High Performance Computing VM-grootten

Virtuele machines (Vm's) van de H-serie van Azure zijn ontworpen voor het leveren van prestaties, schaal baarheid en kosten efficiëntie van een toonaangevend niveau voor verschillende HPC-workloads.

[HBv3-serie](hbv3-series.md) Vm's zijn geoptimaliseerd voor HPC-toepassingen, zoals vloei bare dynamiek, expliciete en impliciete, beperkte element analyse, weer modellering, seismische verwerking, reservoir simulatie en V.R.N.L.-simulatie. HBv3 Vm's beschikken over Maxi maal 120 AMD EPYC™ 7003-Series (Milaan) CPU-kernen, 448 GB RAM en geen hyperthreading. Vm's uit de HBv3-serie bieden ook 350 GB/sec. geheugen bandbreedte, Maxi maal 32 MB L3-cache per kern, Maxi maal 7 GB/s van de snelheid van de SSD-prestaties van het apparaat en klok frequenties tot 3,675 gigahertz. 

Alle virtuele machines uit de HBv3-serie van 200 GB/sec-HDR InfiniBand van NVIDIA-netwerken voor het inschakelen van MPI-workloads op supercomputer schaal. Deze Vm's zijn verbonden met een niet-blokkerende Fat-structuur voor geoptimaliseerde en consistente RDMA-prestaties. De HDR InfiniBand Fabric biedt ook ondersteuning voor adaptieve route ring en het dynamisch verbonden Trans Port (DCT, naast standaard-RC-en UD-transporten). Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.

[HBv2-serie](hbv2-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een eindige element analyse en reservoir simulatie. HBv2 Vm's feature 120 AMD EPYC 7742-processor kernen, 4 GB RAM per CPU-kern en zonder gelijktijdige multi threading. Elke HBv2-VM biedt tot 340 GB/sec. geheugen bandbreedte en Maxi maal 4 teraFLOPS van FP64 compute.

HBv2 Vm's feature 200 GB/sec Mellanox HDR InfiniBand, terwijl zowel de virtuele machines van de HB-als de HC-serie 100 GB/sec Mellanox EDR InfiniBand zijn. Elk van deze VM-typen is verbonden met een niet-blokkerende Fat-structuur voor geoptimaliseerde en consistente RDMA-prestaties. HBv2 Vm's ondersteunen adaptieve route ring en het dynamisch verbonden Trans Port (DCT, naast de standaard-RC en UD trans porten). Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.

[HB-serie](hb-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een expliciete, beperkte element analyse en weer modellen. HB Vm's feature 60 AMD EPYC 7551-processor kernen, 4 GB RAM per CPU-kern en geen hyperthreading. Het AMD EPYC-platform biedt meer dan 260 GB/sec. geheugen bandbreedte.

[HC-serie](hc-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door een compacte reken kracht, zoals impliciete, geeindigd element analyse, moleculaire dynamiek en reken kundige schei kunde. HC Vm's feature 44 Intel Xeon Platinum 8168-processor kernen, 8 GB aan RAM per CPU-kern en geen hyperthreading. Het Intel Xeon Platinum-platform biedt ondersteuning voor een uitgebreid ecosysteem van Intel-software Programma's zoals de Intel math-kernelmodus.

[H-serie](h-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3 processor cores, 7 of 14 GB RAM per CPU-kern en zonder hyperthreading. Functies van de H-serie 56 GB/sec Mellanox FDR InfiniBand in een niet-blokkerende Fat-structuur configuratie voor consistente RDMA-prestaties. Virtuele machines uit de H-serie ondersteunen Intel MPI 5. x en MS-MPI.

> [!NOTE]
> Alle virtuele machines uit de HBv3-, HBv2-, HB-en HC-serie hebben exclusieve toegang tot de fysieke servers. Er is slechts één virtuele machine per fysieke server en er is geen gedeeld multitenancy met andere virtuele machines voor deze VM-grootten.

> [!NOTE]
> De [A8-A11-vm's](./sizes-previous-gen.md#a-series---compute-intensive-instances) worden buiten gebruik gesteld vanaf 3/2021. Er zijn nu geen nieuwe VM-implementaties van deze grootten mogelijk. Als u bestaande Vm's hebt, raadpleegt u de e-mail meldingen voor de volgende stappen, waaronder migratie naar andere VM-grootten in de [HPC-migratie handleiding](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Met RDMA compatibele exemplaren

De meeste HPC VM-grootten bieden een netwerk interface voor RDMA-verbindingen (Remote Direct Memory Access). De geselecteerde [N-serie-](./nc-series.md) grootten die zijn opgegeven met r, zijn ook geschikt voor RDMA. Deze interface is een aanvulling op de standaard Azure Ethernet-netwerk interface die beschikbaar is in de andere VM-grootten.

Met deze secundaire interface kunnen de RDMA-compatibele instanties via een InfiniBand (IB)-netwerk communiceren, op basis van de HDR-tarieven voor HBv3, HBv2 en EDR-tarieven voor HB, HC, NDv2 en FDR-tarieven voor H16r, H16mr en andere RDMA-compatibele N-Series virtuele machines. Deze RDMA-mogelijkheden kunnen de schaal baarheid en prestaties van op MPI (Message Passing Interface) gebaseerde toepassingen verhogen.

> [!NOTE]
> **Ondersteuning voor SR-IOV**: in azure HPC zijn er momenteel twee klassen vm's, afhankelijk van het feit of SR-IOV is ingeschakeld voor Infiniband. Op dit moment zijn voor bijna alle nieuwere, RDMA-compatibele of InfiniBand-Vm's in azure SR-IOV ingeschakeld, met uitzonde ring van H16r, H16mr en NC24r.
> RDMA is alleen ingeschakeld via het InfiniBand-netwerk (IB) en wordt ondersteund voor alle virtuele machines met RDMA-functionaliteit.
> IP over IB wordt alleen ondersteund op Vm's met SR-IOV-functionaliteit.
> RDMA is niet ingeschakeld via het Ethernet-netwerk.

- **Besturings systeem** -Linux-distributies zoals CENTOS, RHEL, Ubuntu, SuSE worden meestal gebruikt. Windows Server 2016 en nieuwere versies worden ondersteund op alle virtuele machines van de HPC-serie. Windows Server 2012 R2 en Windows Server 2012 worden ook ondersteund op Vm's waarvoor geen SR-IOV is ingeschakeld. Houd er rekening mee dat [Windows Server 2012 R2 niet wordt ondersteund op HBv2 als VM-grootten met meer dan 64 (virtuele of fysieke) kernen](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Zie [VM-installatie kopieën](./workloads/hpc/configure.md) voor een lijst met ondersteunde VM-installatie kopieën op de Marketplace en hoe deze op de juiste wijze kunnen worden geconfigureerd. De respectieve pagina's van de VM-grootte vermelden ook de ondersteuning voor software stacks.

- **Infiniband en stuur Programma's** : op InfiniBand ingeschakelde vm's zijn de juiste Stuur Programma's vereist om RDMA in te scha kelen. Zie [VM-installatie kopieën](./workloads/hpc/configure.md) voor een lijst met ondersteunde VM-installatie kopieën op de Marketplace en hoe deze op de juiste wijze kunnen worden geconfigureerd. Zie ook [InfiniBand inschakelen](./workloads/hpc/enable-infiniband.md) voor meer informatie over VM-extensies of hand matige installatie van Infiniband-Stuur Programma's.

- **Mpi** : met de VM-grootten van SR-IOV in azure kunt u bijna alle mpi gebruiken met Mellanox OFED. Op Vm's waarvoor geen SR-IOV is ingeschakeld, wordt in ondersteunde MPI-implementaties de micro soft Network direct (ND)-interface gebruikt voor communicatie tussen Vm's. Daarom worden alleen Intel MPI 5. x en micro soft MPI (MS-MPI) 2012 R2 of latere versies ondersteund. Latere versies van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure RDMA-Stuur Programma's. Zie [Setup mpi voor HPC](./workloads/hpc/setup-mpi.md) voor meer informatie over het instellen van MPI op HPC-Vm's in Azure.

  > [!NOTE]
  > **RDMA-netwerk adres ruimte**: het RDMA-netwerk in azure behoudt de adres ruimte 172.16.0.0/16. Als u MPI-toepassingen wilt uitvoeren op instanties die zijn geïmplementeerd in een virtueel Azure-netwerk, moet u ervoor zorgen dat de adres ruimte van het virtuele netwerk het RDMA-netwerk niet overlapt.

## <a name="cluster-configuration-options"></a>Opties voor cluster configuratie

Azure biedt verschillende opties voor het maken van clusters van HPC-Vm's die kunnen communiceren met behulp van het RDMA-netwerk, waaronder: 

- **Virtuele machines**  : implementeer de met RDMA compatibele HPC-vm's in dezelfde schaalset of beschikbaarheidsset (wanneer u het Azure Resource Manager-implementatie model gebruikt). Als u het klassieke implementatie model gebruikt, implementeert u de virtuele machines in dezelfde Cloud service.

- **Virtuele-machine schaal sets** : in een schaalset voor virtuele machines zorgt u ervoor dat u de implementatie beperkt tot één plaatsings groep voor InfiniBand-communicatie binnen de schaalset. Stel bijvoorbeeld in een resource manager-sjabloon de eigenschap in `singlePlacementGroup` op `true` . Houd er rekening mee dat de maximale grootte van de schaalset die kan worden opgevolgd `singlePlacementGroup=true` , standaard wordt afgesteld op 100 vm's. Als uw HPC-taken schaal behoeften hoger zijn dan 100 Vm's in één Tenant, kunt u een verhoging aanvragen, [een online klant ondersteunings aanvraag openen](../azure-portal/supportability/how-to-create-azure-support-request.md) . De limiet voor het aantal virtuele machines in één schaalset kan worden verhoogd tot 300. Houd er rekening mee dat bij het implementeren van Vm's met beschikbaarheids sets de maximum limiet is 200 Vm's per Beschikbaarheidsset.

  > [!NOTE]
  > **Mpi tussen virtuele machines**: als RDMA (bijvoorbeeld met MPI-communicatie) is vereist tussen virtuele machines (vm's), moet u ervoor zorgen dat de vm's zich in dezelfde virtuele-machine schaalset of beschik bare set bevinden.

- **Azure CycleCloud** : Maak een HPC-cluster met behulp van [Azure CycleCloud](/azure/cyclecloud/) om mpi-taken uit te voeren.

- **Azure batch** : maak een [Azure batch](../batch/index.yml) groep voor het uitvoeren van MPI-workloads. Als u computerintensieve instanties wilt gebruiken bij het uitvoeren van MPI-toepassingen met Azure Batch, raadpleegt u [taken met meerdere instanties gebruiken om mpi-toepassingen (Message Passing Interface) uit te voeren in azure batch](../batch/batch-mpi.md).

- **Micro soft HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) bevat een runtime-omgeving voor MS-mpi die gebruikmaakt van het Azure RDMA-netwerk wanneer het is GEÏMPLEMENTEERD op RDMA-compatibele Linux-vm's. Zie voor beelden van implementaties [een Linux RDMA-cluster met HPC Pack instellen voor het uitvoeren van MPI-toepassingen](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- **Azure-abonnement** : als u meer dan een aantal computerintensieve exemplaren wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- **Prijzen en beschik baarheid** : Controleer de prijzen en [Beschik baarheid](https://azure.microsoft.com/global-infrastructure/services/) van [vm's](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) door Azure-regio's.

- **Quotum voor kernen** : u moet mogelijk het quotum voor kernen in uw Azure-abonnement verhogen van de standaard waarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-grootte families, inclusief de H-serie. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../azure-portal/supportability/how-to-create-azure-support-request.md) . (De standaard limieten kunnen variëren, afhankelijk van de categorie abonnement.)

  > [!NOTE]
  > Neem contact op met de ondersteuning van Azure als er grootschalige capaciteits behoeften zijn. Azure-quota zijn krediet limieten, geen capaciteits garanties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  
- **Virtueel netwerk** : een virtueel Azure- [netwerk](https://azure.microsoft.com/documentation/services/virtual-network/) is niet vereist voor het gebruik van de compute-intensieve exemplaren. Voor veel implementaties hebt u echter mini maal een Azure Virtual Network op basis van de Cloud of een site-naar-site-verbinding nodig als u toegang nodig hebt tot on-premises resources. Als dat nodig is, maakt u een nieuw virtueel netwerk om de exemplaren te implementeren. Het toevoegen van Compute-intensieve Vm's aan een virtueel netwerk in een affiniteits groep wordt niet ondersteund.

- **Grootte wijzigen** : vanwege hun gespecialiseerde hardware kunt u alleen Compute-intensieve instanties binnen dezelfde grootte familie (H-serie of N-serie) wijzigen. U kunt bijvoorbeeld alleen het formaat van een VM van de H-serie wijzigen van de grootte van de h-serie in een andere. Aanvullende overwegingen met betrekking tot InfiniBand-stuur programma-ondersteuning en NVMe-schijven moeten mogelijk worden overwogen voor bepaalde Vm's.


## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het configureren van uw vm's, het](./workloads/hpc/configure.md) [inschakelen van Infiniband](./workloads/hpc/enable-infiniband.md), het [instellen van MPI](./workloads/hpc/setup-mpi.md) en het optimaliseren van HPC-toepassingen voor Azure bij [HPC-workloads](./workloads/hpc/overview.md).
- Bekijk het overzicht van de [HBv3-serie](./workloads/hpc/hbv3-series-overview.md) en de [HC-serie](./workloads/hpc/hc-series-overview.md).
- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
