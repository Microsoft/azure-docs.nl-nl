---
title: Reken intensief Azure-Vm's gebruiken met batch
description: Profiteren van de grootte van HPC en GPU-virtuele machines in Azure Batch groepen. Meer informatie over afhankelijkheden van besturings systemen en een aantal voor beelden van scenario's weer geven.
ms.topic: how-to
ms.date: 12/17/2018
ms.openlocfilehash: 016da7669c9e6a6586a53d379f9665c9ea048b64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86147333"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>RDMA-of GPU-instanties gebruiken in batch-Pools

Als u bepaalde batch taken wilt uitvoeren, kunt u profiteren van de Azure VM-grootten die zijn ontworpen voor grootschalige berekeningen. Bijvoorbeeld:

* Als u [mpi werk belastingen](batch-mpi.md)voor meerdere instanties wilt uitvoeren, kiest u H-serie of andere grootten met een netwerk interface voor RDMA (Remote Direct Memory Access). Deze groottes maken verbinding met een InfiniBand-netwerk voor communicatie tussen knoop punten, waarmee MPI-toepassingen kunnen worden versneld. 

* Kies voor CUDA-toepassingen de grootte van de N-serie met NVIDIA Tesla GPU-kaarten (graphics processing unit).

Dit artikel bevat richt lijnen en voor beelden voor het gebruik van enkele gespecialiseerde grootten van Azure in batch-Pools. Zie voor specificaties en achtergrond:

* High Performance Compute VM-grootten ([Linux](../virtual-machines/sizes-hpc.md), [Windows](../virtual-machines/sizes-hpc.md)) 

* VM-grootten met GPU ([Linux](../virtual-machines/sizes-gpu.md), [Windows](../virtual-machines/sizes-gpu.md)) 

> [!NOTE]
> Bepaalde VM-grootten zijn mogelijk niet beschikbaar in de regio's waar u uw batch-accounts maakt. Als u wilt controleren of er een grootte beschikbaar is, raadpleegt u [beschik bare producten per regio](https://azure.microsoft.com/regions/services/) en [kiest u een VM-grootte voor een batch-pool](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Afhankelijkheden

De RDMA-of GPU-mogelijkheden van Compute-grootten in batch worden alleen ondersteund in bepaalde besturings systemen. (De lijst met ondersteunde besturings systemen is een subset van die worden ondersteund voor virtuele machines die zijn gemaakt in deze grootten.) Afhankelijk van hoe u de batch-pool maakt, moet u mogelijk extra Stuur Programma's of andere software op de knoop punten installeren of configureren. In de volgende tabellen ziet u een overzicht van deze afhankelijkheden. Zie gekoppelde artikelen voor meer informatie. Zie verderop in dit artikel voor opties voor het configureren van batch-Pools.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-Pools-virtuele-machine configuratie

| Grootte | Mogelijkheid | Besturingssystemen | Vereiste software | Groeps instellingen |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16,04 LTS, of<br/>HPC op basis van CentOS<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA-Stuur Programma's | Communicatie tussen knoop punten inschakelen, gelijktijdige taak uitvoering uitschakelen |
| [NC, NCv2, NCv3, NDv2-serie](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (per serie) | Ubuntu 16,04 LTS, of<br/>CentOS 7,3 of 7,4<br/>(Azure Marketplace) | NVIDIA CUDA-of CUDA Toolkit-Stuur Programma's | N.v.t. | 
| [NV, NVv2-serie](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16,04 LTS, of<br/>CentOS 7,3<br/>(Azure Marketplace) | NVIDIA-raster Stuur Programma's | N.v.t. |

<sup>*</sup>RDMA-compatibele N-serie grootten omvatten ook NVIDIA Tesla-Gpu's

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-Pools-virtuele-machine configuratie

| Grootte | Mogelijkheid | Besturingssystemen | Vereiste software | Groeps instellingen |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 of<br/>2012 (Azure Marketplace) | Micro soft MPI 2012 R2 of hoger, of<br/> Intel MPI 5<br/><br/>Windows RDMA-Stuur Programma's | Communicatie tussen knoop punten inschakelen, gelijktijdige taak uitvoering uitschakelen |
| [NC, NCv2, NCv3, ND, NDv2-serie](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (per serie) | Windows Server 2016 of <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA-of CUDA Toolkit-Stuur Programma's| N.v.t. | 
| [NV, NVv2-serie](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 of<br/>2012 R2 (Azure Marketplace) | NVIDIA-raster Stuur Programma's | N.v.t. |

<sup>*</sup>RDMA-compatibele N-serie grootten omvatten ook NVIDIA Tesla-Gpu's

### <a name="windows-pools---cloud-services-configuration"></a>Windows-Pools-Cloud Services configureren

> [!NOTE]
> De grootte van een N-serie wordt niet ondersteund in batch-Pools met de configuratie van de Cloud service.
>

| Grootte | Mogelijkheid | Besturingssystemen | Vereiste software | Groeps instellingen |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012 of<br/>2008 R2 (gast besturingssysteem familie) | Micro soft MPI 2012 R2 of hoger, of<br/>Intel MPI 5<br/><br/>Windows RDMA-Stuur Programma's | Communicatie tussen knoop punten inschakelen,<br/> gelijktijdige taak uitvoering uitschakelen |

## <a name="pool-configuration-options"></a>Opties voor groeps configuratie

Als u een speciale VM-grootte voor de batch-pool wilt configureren, hebt u verschillende opties om vereiste software of stuur Programma's te installeren:

* Kies voor groepen in de configuratie van de virtuele machine een vooraf geconfigureerde VM-installatie kopie van [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) met vooraf geïnstalleerde Stuur Programma's en software. Voorbeelden: 

  * [Op CentOS gebaseerde 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) -bevat RDMA-Stuur Programma's en Intel mpi 5,1

  * [Data Science virtual machine](../machine-learning/data-science-virtual-machine/overview.md) voor Linux of Windows-bevat NVIDIA CUDA-Stuur Programma's

  * Linux-installatie kopieën voor batch-container werkbelastingen die ook GPU-en RDMA-Stuur Programma's bevatten:

    * [CentOS (met GPU-en RDMA-Stuur Programma's) voor Azure Batch container Pools](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu-Server (met GPU-en RDMA-Stuur Programma's) voor Azure Batch container Pools](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Maak een [aangepaste Windows-of Linux-VM-installatie kopie](batch-sig-images.md) waarop u stuur Programma's, software of andere instellingen hebt geïnstalleerd die vereist zijn voor de VM-grootte. 

* Maak een batch- [toepassings pakket](batch-application-packages.md) op basis van een zip-stuur programma of toepassings installatie programma en configureer batch om het pakket te implementeren op pool knooppunten en installeer eenmaal wanneer elk knoop punt is gemaakt. Als het toepassings pakket bijvoorbeeld een installatie programma is, maakt u een opdracht regel voor een [begin taak](jobs-and-tasks.md#start-task) om de app op de achtergrond te installeren op alle groeps knooppunten. Overweeg het gebruik van een toepassings pakket en een taak voor het starten van een pool als uw werk belasting afhankelijk is van een bepaalde versie van het stuur programma.

  > [!NOTE] 
  > De begin taak moet worden uitgevoerd met verhoogde machtigingen (beheerder) en moet wachten op geslaagd. Langlopende taken verg Roten de tijd om een batch-pool in te richten.
  >

* Met [batch Shipyard](https://github.com/Azure/batch-shipyard) worden de GPU-en RDMA-Stuur Programma's automatisch geconfigureerd om transparant te werken met in containers geplaatste workloads op Azure batch. Batch Shipyard wordt volledig gestuurd met configuratie bestanden. Er zijn veel voor beeld van recept configuraties beschikbaar waarmee GPU-en RDMA-workloads kunnen worden ingeschakeld, zoals het [CNTK GPU-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) , waarmee GPU-Stuur Programma's worden geconfigureerd op vm's uit de N-serie en Microsoft Cognitive Toolkit software wordt geladen als docker-installatie kopie.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Voor beeld: NVIDIA GPU-Stuur Programma's in Windows NC VM-groep

Als u CUDA-toepassingen wilt uitvoeren op een groep Windows NC-knoop punten, moet u NVDIA GPU-Stuur Programma's installeren. In de volgende voorbeeld stappen wordt een toepassings pakket gebruikt om de NVIDIA GPU-Stuur Programma's te installeren. U kunt deze optie kiezen als uw werk belasting afhankelijk is van een specifieke versie van het GPU-stuur programma.

1. Down load een installatie pakket voor de GPU-Stuur Programma's op Windows Server 2016 van de [NVIDIA-website](https://www.nvidia.com/Download/index.aspx) , bijvoorbeeld [versie 411,82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Sla het bestand lokaal op met behulp van een korte naam, zoals *GPUDriverSetup.exe*.
2. Maak een zip-bestand van het pakket.
3. Upload het pakket naar uw batch-account. Zie de richt lijnen voor [toepassings pakketten](batch-application-packages.md) voor instructies. Geef een toepassings-ID, zoals *GPUDriver*, en een versie, zoals *411,82*, op.
1. Maak met behulp van de batch-Api's of de Azure Portal een pool in de virtuele-machine configuratie met het gewenste aantal knoop punten en schaal. De volgende tabel bevat voor beelden van instellingen voor het op de achtergrond installeren van de NVIDIA GPU-Stuur Programma's met behulp van een begin taak:

| Instelling | Waarde |
| ---- | ----- | 
| **Type installatiekopie** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Aanbieding** | WindowsServer |
| **SKU** | 2016-Data Center |
| **Knooppuntgrootte** | NC6-standaard |
| **Toepassings pakket verwijzingen** | GPUDriver, versie 411,82 |
| **Taak starten is ingeschakeld** | Waar<br>**Opdracht regel** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Gebruikers identiteit** : groeps beleidsgebruiker, beheerder<br/>**Wachten op geslaagd** -True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Voor beeld: NVIDIA GPU-Stuur Programma's in een Linux NC-VM-groep

Als u CUDA-toepassingen wilt uitvoeren op een groep Linux NC-knoop punten, moet u de benodigde NVIDIA Tesla GPU-Stuur Programma's installeren vanuit de CUDA Toolkit. In de volgende voorbeeld stappen wordt een aangepaste Ubuntu 16,04 LTS-installatie kopie gemaakt en geïmplementeerd met de GPU-Stuur Programma's:

1. Implementeer een Azure NC-Series-VM met Ubuntu 16,04 LTS. Maak bijvoorbeeld de virtuele machine in de regio VS Zuid-Centraal. 
2. Voeg de [uitbrei ding NVIDIA GPU-Stuur Programma's](../virtual-machines/extensions/hpccompute-gpu-linux.md
) toe aan de virtuele machine met behulp van de Azure Portal, een client computer die verbinding maakt met het Azure-abonnement of Azure Cloud shell. U kunt ook de stappen volgen om verbinding te maken met de virtuele machine en [CUDA-Stuur Programma's](../virtual-machines/linux/n-series-driver-setup.md) hand matig te installeren.
3. Volg de stappen voor het maken van een afbeelding voor de [Galerie met gedeelde afbeeldingen](batch-sig-images.md) voor batch.
4. Maak een batch-account in een regio die NC-Vm's ondersteunt.
5. Maak met behulp van de batch-Api's of de Azure Portal een pool [met behulp van de aangepaste installatie kopie](batch-sig-images.md) en met het gewenste aantal knoop punten en schaal. De volgende tabel bevat voor beelden van groeps instellingen voor de installatie kopie:

| Instelling | Waarde |
| ---- | ---- |
| **Type installatiekopie** | Aangepaste installatiekopie |
| **Aangepaste installatie kopie** | *Naam van de afbeelding* |
| **SKU van knoop punt agent** | batch. node. Ubuntu 16,04 |
| **Knooppuntgrootte** | NC6-standaard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Voor beeld: micro soft MPI in een VM-groep van Windows H16r

Als u Windows MPI-toepassingen wilt uitvoeren op een groep Azure H16r VM-knoop punten, moet u de HpcVmDrivers-extensie configureren en [micro soft mpi](/message-passing-interface/microsoft-mpi)installeren. Hier volgen enkele voor beelden van stappen voor het implementeren van een aangepaste installatie kopie van Windows Server 2016 met de benodigde Stuur Programma's en software:

1. Implementeer een Azure H16r-VM met Windows Server 2016. Maak bijvoorbeeld de virtuele machine in de regio vs West. 
2. Voeg de uitbrei ding HpcVmDrivers toe aan de virtuele machine door [een Azure PowerShell opdracht](../virtual-machines/sizes-hpc.md) uit te voeren vanaf een client computer die verbinding maakt met uw Azure-abonnement of Azure Cloud shell gebruikt. 
1. Een Extern bureaublad verbinding maken met de virtuele machine.
1. Down load het [installatie pakket](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) voor de nieuwste versie van micro soft MPI en installeer micro soft MPI.
1. Volg de stappen voor het maken van een afbeelding voor de [Galerie met gedeelde afbeeldingen](batch-sig-images.md) voor batch.
1. Maak met behulp van de batch-Api's of de Azure Portal een pool [met behulp van de galerie met gedeelde afbeeldingen](batch-sig-images.md) en met het gewenste aantal knoop punten en schaal. De volgende tabel bevat voor beelden van groeps instellingen voor de installatie kopie:

| Instelling | Waarde |
| ---- | ---- |
| **Type installatiekopie** | Aangepaste installatiekopie |
| **Aangepaste installatie kopie** | *Naam van de afbeelding* |
| **SKU van knoop punt agent** | batch. node. Windows amd64 |
| **Knooppuntgrootte** | H16r-standaard |
| **Communicatie tussen knoop punten is ingeschakeld** | Waar |
| **Maximum aantal taken per knoop punt** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Voor beeld: Intel MPI op een Linux H16r VM-groep

Voor het uitvoeren van MPI-toepassingen in een groep van Linux-knoop punten van de H-serie, is het gebruik van de op [CentOS 7,4 gebaseerde HPC-](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) installatie kopie op basis van de Azure Marketplace. Linux RDMA-Stuur Programma's en Intel MPI zijn vooraf geïnstalleerd. Deze installatie kopie ondersteunt ook docker-container werkbelastingen.

Maak met behulp van de batch-Api's of de Azure Portal een pool met behulp van deze installatie kopie en met het gewenste aantal knoop punten en schaal. De volgende tabel bevat voor beelden van groeps instellingen:

| Instelling | Waarde |
| ---- | ---- |
| **Type installatiekopie** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Aanbieding** | CentOS-HPC |
| **SKU** | 7.4 |
| **Knooppuntgrootte** | H16r-standaard |
| **Communicatie tussen knoop punten is ingeschakeld** | Waar |
| **Maximum aantal taken per knoop punt** | 1 |

## <a name="next-steps"></a>Volgende stappen

* Zie de [Windows](batch-mpi.md) -of [Linux](/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch) -voor beelden om mpi-taken uit te voeren in een Azure batch groep.

* Zie de [batch Shipyard](https://github.com/Azure/batch-shipyard/) recepten voor voor beelden van GPU-workloads op batch.
