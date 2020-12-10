---
title: Azure VM-grootten-HPC | Microsoft Docs
description: Geeft een lijst van de verschillende beschik bare grootten voor High Performance Computing virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 5559e491be15f8f848329405736fc977267c70cc
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938593"
---
# <a name="high-performance-computing-vm-sizes"></a>High Performance Computing VM-grootten

Virtuele machines (Vm's) van de H-serie van Azure zijn ontworpen voor het leveren van prestaties, schaal baarheid en kosten efficiëntie van een toonaangevend niveau voor een groot aantal HPC-workloads.

[HBv2-serie](hbv2-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een eindige element analyse en reservoir simulatie. HBv2 Vm's feature 120 AMD EPYC 7742-processor kernen, 4 GB RAM per CPU-kern en zonder gelijktijdige multi threading. Elke HBv2-VM biedt tot 340 GB/sec. geheugen bandbreedte en Maxi maal 4 teraFLOPS van FP64 compute.

HBv2 Vm's feature 200 GB/sec Mellanox HDR InfiniBand, terwijl zowel de virtuele machines van de HB-als de HC-serie 100 GB/sec Mellanox EDR InfiniBand zijn. Elk van deze VM-typen is verbonden met een niet-blokkerende Fat-structuur voor geoptimaliseerde en consistente RDMA-prestaties. HBv2 Vm's ondersteunen adaptieve route ring en het dynamisch verbonden Trans Port (DCT, in aanvulling op standaard-RC-en UD-transporten). Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.

[HB-serie](hb-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een expliciete, beperkte element analyse en weer modellen. HB Vm's feature 60 AMD EPYC 7551-processor kernen, 4 GB RAM per CPU-kern en geen hyperthreading. Het AMD EPYC-platform biedt meer dan 260 GB/sec. geheugen bandbreedte.

[HC-serie](hc-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door een compacte reken kracht, zoals impliciete, geeindigd element analyse, moleculaire dynamiek en reken kundige schei kunde. HC Vm's feature 44 Intel Xeon Platinum 8168-processor kernen, 8 GB aan RAM per CPU-kern en geen hyperthreading. Het Intel Xeon Platinum-platform biedt ondersteuning voor een uitgebreid ecosysteem van Intel-software Programma's zoals de Intel math-kernelmodus.

[H-serie](h-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3 processor cores, 7 of 14 GB RAM per CPU-kern en zonder hyperthreading. Functies van de H-serie 56 GB/sec Mellanox FDR InfiniBand in een niet-blokkerende Fat-structuur configuratie voor consistente RDMA-prestaties. Virtuele machines uit de H-serie ondersteunen Intel MPI 5. x en MS-MPI.

> [!NOTE]
> Alle virtuele machines uit de HBv2-, HB-en HC-serie hebben exclusieve toegang tot de fysieke servers. Er is slechts één virtuele machine per fysieke server en er is geen gedeeld multitenancy met andere virtuele machines voor deze VM-grootten.

> [!NOTE]
> De [A8-A11-vm's](./sizes-previous-gen.md#a-series---compute-intensive-instances) worden gepland voor buiten gebruiks telling op 3/2021. Zie voor meer informatie [HPC-migratie handleiding](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Met RDMA compatibele exemplaren

De meeste HPC VM-grootten (HBv2, HB, HC, H16r, H16mr, A8 en A9) beschikken over een netwerk interface voor RDMA-verbindingen (Remote Direct Memory Access). De geselecteerde [N-serie-](./nc-series.md) grootten die zijn aangewezen met r (ND40rs_v2, ND24rs, NC24rs_v3, NC24rs_v2 en NC24r) zijn ook geschikt voor RDMA. Deze interface is een aanvulling op de standaard Azure Ethernet-netwerk interface die beschikbaar is in de andere VM-grootten.

Met deze interface kunnen de RDMA-compatibele instanties communiceren via een InfiniBand-netwerk (IB), op basis van de HDR-tarieven voor HBv2, EDR-tarieven voor HB, HC, NDv2, FDR-tarieven voor H16r, H16mr en andere RDMA-compatibele virtuele machines van de N-serie, en QDR tarieven voor A8 en A9 Vm's. Deze RDMA-mogelijkheden kunnen de schaal baarheid en prestaties van bepaalde MPI-toepassingen (Message Passing Interface) verhogen.

> [!NOTE]
> In azure HPC zijn er twee klassen Vm's, afhankelijk van het feit of SR-IOV is ingeschakeld voor InfiniBand. Op dit moment zijn voor bijna alle nieuwere, RDMA-compatibele of InfiniBand-Vm's in azure SR-IOV ingeschakeld, met uitzonde ring van H16r, H16mr, NC24r, A8, A9.
> RDMA is alleen ingeschakeld via het InfiniBand-netwerk (IB) en wordt ondersteund voor alle virtuele machines met RDMA-functionaliteit.
> IP over IB wordt alleen ondersteund op Vm's met SR-IOV-functionaliteit.
> RDMA is niet ingeschakeld via het Ethernet-netwerk.

- **Besturings systeem** : Linux wordt zeer goed ondersteund voor HPC-vm's. distributies zoals CentOS, RHEL, Ubuntu, SUSE worden meestal gebruikt. Met betrekking tot Windows-ondersteuning worden Windows Server 2016 en nieuwere versies ondersteund op alle virtuele machines van de HPC-serie. Windows Server 2012 R2, Windows Server 2012 wordt ook ondersteund op virtuele machines die niet zijn beveiligd met SR-IOV (H16r, H16mr, A8 en A9). Houd er rekening mee dat [Windows Server 2012 R2 niet wordt ondersteund voor HBv2 en andere vm's met meer dan 64 (virtuele of fysieke) kernen](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Zie [VM-installatie kopieën](./workloads/hpc/configure.md) voor een lijst met ondersteunde VM-installatie kopieën op de Marketplace en hoe deze op de juiste wijze kunnen worden geconfigureerd.

- **Infiniband en stuur Programma's** : op InfiniBand ingeschakelde vm's zijn de juiste Stuur Programma's vereist om RDMA in te scha kelen. In Linux wordt voor zowel SR-IOV als niet-SR-IOV ingeschakelde Vm's de CentOS-VM-installatie kopieën in de Marketplace vooraf geconfigureerd met de juiste Stuur Programma's. De Ubuntu-VM-installatie kopieën kunnen worden geconfigureerd met de juiste Stuur Programma's met behulp van de [instructies hier](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Zie [vm's voor Linux-besturings systeem configureren en optimaliseren](./workloads/hpc/configure.md) voor meer informatie over kant-en-klare VM Linux-installatie kopieën van het besturings systeem.

   In Linux kan de [InfiniBandDriverLinux-VM-extensie](./extensions/hpc-compute-infiniband-linux.md) worden gebruikt voor het installeren van de Mellanox OFED-Stuur Programma's en het inschakelen van Infiniband op de op SR-IOV ingeschakelde H-en N-Series vm's. Meer informatie over het inschakelen van InfiniBand op virtuele machines met RDMA in [HPC-workloads](./workloads/hpc/enable-infiniband.md).

   In Windows installeert de [InfiniBandDriverWindows-VM-extensie](./extensions/hpc-compute-infiniband-windows.md) Windows-netwerk directe Stuur programma's (op niet-SR-IOV-vm's) of Mellanox OFED-Stuur programma's (op SR-IOV-vm's) voor RDMA-connectiviteit. In bepaalde implementaties van A8-en A9-instanties wordt de uitbrei ding HpcVmDrivers automatisch toegevoegd. Houd er rekening mee dat de HpcVmDrivers VM-extensie wordt afgeschaft. het wordt niet bijgewerkt.

   U kunt [Azure PowerShell](/powershell/azure/) -cmdlets gebruiken om de VM-extensie toe te voegen aan een virtuele machine. Zie [extensies en functies van virtuele machines](./extensions/overview.md)voor meer informatie. U kunt ook werken met uitbrei dingen voor virtuele machines die zijn geïmplementeerd in het [klassieke implementatie model](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Mpi** : met de VM-grootten van SR-IOV in azure kunt u bijna alle mpi gebruiken met Mellanox OFED. Op Vm's waarvoor geen SR-IOV is ingeschakeld, wordt in ondersteunde MPI-implementaties de micro soft Network direct (ND)-interface gebruikt voor communicatie tussen Vm's. Daarom worden alleen micro soft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5. x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure RDMA-Stuur Programma's. Zie [Setup mpi voor HPC](./workloads/hpc/setup-mpi.md) voor meer informatie over het instellen van MPI op HPC-Vm's in Azure.

- **RDMA-netwerk adres ruimte** : het RDMA-netwerk in azure behoudt de adres ruimte 172.16.0.0/16. Als u MPI-toepassingen wilt uitvoeren op instanties die zijn geïmplementeerd in een virtueel Azure-netwerk, moet u ervoor zorgen dat de adres ruimte van het virtuele netwerk het RDMA-netwerk niet overlapt.

## <a name="cluster-configuration-options"></a>Opties voor cluster configuratie

Azure biedt verschillende opties voor het maken van clusters van Windows HPC-Vm's die kunnen communiceren met behulp van het RDMA-netwerk, waaronder: 

- **Virtuele machines**  : implementeer de met RDMA compatibele HPC-vm's in dezelfde schaalset of beschikbaarheidsset (wanneer u het Azure Resource Manager-implementatie model gebruikt). Als u het klassieke implementatie model gebruikt, implementeert u de virtuele machines in dezelfde Cloud service.

- **Virtuele-machine schaal sets** : in een schaalset voor virtuele machines zorgt u ervoor dat u de implementatie beperkt tot één plaatsings groep voor InfiniBand-communicatie binnen de schaalset. Stel bijvoorbeeld in een resource manager-sjabloon de eigenschap in `singlePlacementGroup` op `true` . Houd er rekening mee dat de maximale grootte van de schaalset die met de eigenschap kan worden ingesteld `singlePlacementGroup` , standaard wordt afgesteld `true` op 100 vm's. Als uw HPC-taken schaal behoeften hoger zijn dan 100 Vm's in één Tenant, kunt u een verhoging aanvragen, [een online klant ondersteunings aanvraag openen](../azure-portal/supportability/how-to-create-azure-support-request.md) . De limiet voor het aantal virtuele machines in één schaalset kan worden verhoogd tot 300. Houd er rekening mee dat bij het implementeren van Vm's met beschikbaarheids sets de maximum limiet is 200 Vm's per Beschikbaarheidsset.

- **Mpi tussen virtuele machines** : als RDMA (bijvoorbeeld met MPI-communicatie) is vereist tussen virtuele machines (vm's), moet u ervoor zorgen dat de vm's zich in dezelfde virtuele-machine schaalset of beschik bare set bevinden.

- **Azure CycleCloud** : Maak een HPC-cluster in [Azure CycleCloud](/azure/cyclecloud/) om mpi-taken uit te voeren.

- **Azure batch** : maak een [Azure batch](../batch/index.yml) groep voor het uitvoeren van MPI-workloads. Als u computerintensieve instanties wilt gebruiken bij het uitvoeren van MPI-toepassingen met Azure Batch, raadpleegt u [taken met meerdere instanties gebruiken om mpi-toepassingen (Message Passing Interface) uit te voeren in azure batch](../batch/batch-mpi.md).

- **Micro soft HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) bevat een runtime-omgeving voor MS-mpi die gebruikmaakt van het Azure RDMA-netwerk wanneer het is GEÏMPLEMENTEERD op RDMA-compatibele Linux-vm's. Zie voor beelden van implementaties [een Linux RDMA-cluster met HPC Pack instellen voor het uitvoeren van MPI-toepassingen](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- **Azure-abonnement** : als u meer dan een aantal computerintensieve exemplaren wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- **Prijzen en beschik baarheid** : deze VM-grootten worden alleen aangeboden in de prijs categorie Standard. Controleer of de beschik [bare producten per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/) in azure-regio's.

- **Quotum voor kernen** : u moet mogelijk het quotum voor kernen in uw Azure-abonnement verhogen van de standaard waarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-grootte families, inclusief de H-serie. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../azure-portal/supportability/how-to-create-azure-support-request.md) . (De standaard limieten kunnen variëren, afhankelijk van de categorie abonnement.)

  > [!NOTE]
  > Neem contact op met de ondersteuning van Azure als er grootschalige capaciteits behoeften zijn. Azure-quota zijn krediet limieten, geen capaciteits garanties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  
- **Virtueel netwerk** : een virtueel Azure- [netwerk](https://azure.microsoft.com/documentation/services/virtual-network/) is niet vereist voor het gebruik van de compute-intensieve exemplaren. Voor veel implementaties hebt u echter mini maal een Azure Virtual Network op basis van de Cloud of een site-naar-site-verbinding nodig als u toegang nodig hebt tot on-premises resources. Als dat nodig is, maakt u een nieuw virtueel netwerk om de exemplaren te implementeren. Het toevoegen van Compute-intensieve Vm's aan een virtueel netwerk in een affiniteits groep wordt niet ondersteund.

- **Grootte wijzigen** : vanwege hun gespecialiseerde hardware kunt u alleen Compute-intensieve instanties binnen dezelfde grootte familie (H-serie of N-serie) wijzigen. U kunt bijvoorbeeld alleen het formaat van een VM van de H-serie wijzigen van de grootte van de h-serie in een andere. Aanvullende overwegingen met betrekking tot InfiniBand-stuur programma-ondersteuning en NVMe-schijven moeten mogelijk worden overwogen voor bepaalde Vm's.


## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het configureren van uw vm's, het](./workloads/hpc/configure.md) [inschakelen van Infiniband](./workloads/hpc/enable-infiniband.md), het [instellen van MPI](./workloads/hpc/setup-mpi.md) en het optimaliseren van HPC-toepassingen voor Azure bij [HPC-workloads](./workloads/hpc/overview.md).
- Lees over de laatste aankondigingen en enkele HPC-voorbeelden en -resultaten in de [Azure Compute Tech Community-blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
