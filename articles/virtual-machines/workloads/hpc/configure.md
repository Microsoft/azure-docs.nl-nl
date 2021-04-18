---
title: Configuratie en optimalisatie van Azure-Virtual Machines voor de voor InfiniBand ingeschakelde H-serie Virtual Machines
description: Meer informatie over het configureren en optimaliseren van de voor InfiniBand ingeschakelde VM's uit de H-serie en N-serie voor HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 470d5efae68366b5cc96243bab4ebb8552771650
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600876"
---
# <a name="configure-and-optimize-vms"></a>VM's configureren en optimaliseren

In dit artikel worden enkele richtlijnen gegeven voor het [](../../sizes-hpc.md) configureren en optimaliseren van de VM's uit de InfiniBand-serie en [N-serie](../../sizes-gpu.md) voor HPC.

## <a name="vm-images"></a>VM-installatiekopieën
Op VM's met InfiniBand zijn de juiste stuurprogramma's vereist om RDMA in te kunnenschakelen.
- De [CentOS-HPC VM-afbeeldingen](#centos-hpc-vm-images) in de Marketplace zijn vooraf geconfigureerd met de juiste IB-stuurprogramma's en zijn de eenvoudigste manier om aan de slag te gaan.
- De [Ubuntu VM-installatie afbeeldingen](#ubuntu-vm-images) kunnen worden geconfigureerd met de juiste IB-stuurprogramma's. Het wordt aanbevolen om aangepaste [VM-installatiebestanden te maken](../../linux/tutorial-custom-images.md) met de juiste stuurprogramma's en configuratie en deze regelmatig opnieuw te gebruiken.

Op VM's [uit de N-serie](../../sizes-gpu.md) met GPU zijn bovendien de juiste GPU-stuurprogramma's vereist die kunnen worden toegevoegd via de [VM-extensies](../../extensions/hpccompute-gpu-linux.md) [of handmatig.](../../linux/n-series-driver-setup.md) Sommige VM-installatie foto's op de Marketplace worden ook vooraf geïnstalleerd met de Nvidia GPU-stuurprogramma's, waaronder enkele VM-installatie afbeeldingen van Nvidia.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM-afbeeldingen

#### <a name="sr-iov-enabled-vms"></a>VM's met SR-IOV
Voor VM's met SR-IOV die geschikt zijn voor [RDMA,](../../sizes-hpc.md#rdma-capable-instances)zijn [CentOS-HPC VM-afbeeldingen](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) op Marketplace versie 7.6 en hoger geschikt. Deze VM-afbeeldingen zijn geoptimaliseerd en vooraf geladen met de OFED-stuurprogramma's voor RDMA en verschillende veelgebruikte MPI-bibliotheken en wetenschappelijke computingpakketten. Dit is de eenvoudigste manier om aan de slag te gaan.
- Meer informatie over wat is opgenomen in de VM-afbeeldingen van CentOS-HPC versie 7.6 en hoger vindt u in het [TechCommunity-artikel](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557).
- Scripts die worden gebruikt bij het maken van de VM-afbeeldingen van CentOS-HPC versie 7.6 en hoger van een basis-CentOS Marketplace-afbeelding, staan in de [azhpc-images-repo.](https://github.com/Azure/azhpc-images/tree/master/centos)
  
> [!NOTE] 
> De nieuwste Azure HPC Marketplace-afbeeldingen hebben Mellanox OFED 5.1 en hoger, die geen ondersteuning ConnectX3-Pro InfiniBand-kaarten. VM-grootten uit de N-serie met SR-IOV met FDR InfiniBand (bijvoorbeeld NCv3 en ouder) kunnen gebruikmaken van de volgende CentOS-HPC VM-afbeelding of oudere versies van de Marketplace:
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>VM's die niet zijn ingeschakeld voor SR-IOV
Voor niet-SR-IOV-VM's die geschikt zijn voor [RDMA,](../../sizes-hpc.md#rdma-capable-instances)zijn CentOS-HPC versie 6.5 of een latere versie, maximaal 7.4 in de Marketplace geschikt. Voor VM's uit de [H16-serie](../../h-series.md)wordt bijvoorbeeld versie 7.1 tot en met 7.4 aanbevolen. Deze VM-afbeeldingen worden vooraf geladen met de Network Direct-stuurprogramma's voor RDMA en Intel MPI versie 5.1.

> [!NOTE]
> Op deze HpC-installatiebestanden op basis van CentOS voor niet-SR-IOV ingeschakelde VM's worden kernelupdates uitgeschakeld in het **yum-configuratiebestand.** Dit komt doordat de NetworkDirect Linux RDMA-stuurprogramma's worden gedistribueerd als een RPM-pakket en stuurprogramma-updates mogelijk niet werken als de kernel wordt bijgewerkt.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM-afbeeldingen
Op RHEL of CentOS gebaseerde niet-HPC VM-VM-afbeeldingen op de Marketplace kunnen [](../../sizes-hpc.md#rdma-capable-instances)worden geconfigureerd voor gebruik op de VM's die geschikt zijn voor SR-IOV. Meer informatie over het [inschakelen van InfiniBand](enable-infiniband.md) [en het instellen van MPI](setup-mpi.md) op de VM's.
- Scripts die worden gebruikt bij het maken van de centOS-HPC versie 7.6 en latere VM-afbeeldingen van een basis centOS Marketplace-afbeelding uit de [azhpc-images-repo](https://github.com/Azure/azhpc-images/tree/master/centos) kunnen ook worden gebruikt.
  
> [!NOTE]
> Mellanox OFED 5.1 en hoger biedt geen ondersteuning voor ConnectX3-Pro InfiniBand-kaarten op VM-grootten uit de N-serie met SR-IOV met FDR InfiniBand (bijvoorbeeld NCv3). Gebruik LTS Mellanox OFED versie 4.9-0.1.7.0 of ouder op de VM's uit de N-serie met ConnectX3-Pro kaarten. U kunt hier meer informatie [bekijken.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)

### <a name="ubuntu-vm-images"></a>Ubuntu-VM-installatie afbeeldingen
Ubuntu Server 16.04 LTS, 18.04 LTS en 20.04 LTS VM-installatie images in de Marketplace worden ondersteund voor VM's die geschikt zijn voor zowel SR-IOV als niet-SR-IOV [RDMA-ondersteuning.](../../sizes-hpc.md#rdma-capable-instances) Meer informatie over het [inschakelen van InfiniBand](enable-infiniband.md) [en het instellen van MPI](setup-mpi.md) op de VM's.
- Instructies voor het inschakelen van InfiniBand op de Ubuntu VM-installatie afbeeldingen staan in een [TechCommunity-artikel.](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)
- Scripts die worden gebruikt bij het maken van op Ubuntu 18.04 en 20.04 LTS gebaseerde HPC-VM-installatie images op basis van een basis-Ubuntu Marketplace-installatiebestand, staan in de [azhpc-images-repo](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM-afbeeldingen maken
SLES 12 SP3 for HPC, SLES 12 SP3 for HPC (Premium), SLES 12 SP1 for HPC, SLES 12 SP1 for HPC (Premium), SLES 12 SP4- en SLES 15 VM-afbeeldingen in de Marketplace worden ondersteund. Deze VM-afbeeldingen worden vooraf geladen met de Network Direct-stuurprogramma's voor RDMA en Intel MPI versie 5.1. Meer informatie over het [instellen van MPI](setup-mpi.md) op de VM's.

## <a name="optimize-vms"></a>VM's optimaliseren

Hier volgen enkele optionele optimalisatie-instellingen voor verbeterde prestaties op de VM.

### <a name="update-lis"></a>LIS bijwerken

Indien nodig voor functionaliteit of prestaties kunnen LIS-stuurprogramma's [(Linux Integration Services)](../../linux/endorsed-distros.md) worden geïnstalleerd of bijgewerkt op ondersteunde versies van het besturingssysteem, met name wordt geïmplementeerd met behulp van een aangepaste installatie-installatier of een oudere versie van het besturingssysteem, zoals CentOS/RHEL 6.x of een eerdere versie van 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Geheugen vrij maken

Verbeter de prestaties door geheugen automatisch vrij te maken om externe geheugentoegang te voorkomen.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

U kunt dit als volgende doen nadat de VM opnieuw is opgestart:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Firewall en SELinux uitschakelen

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Cpupower uitschakelen

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>WALinuxAgent configureren

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Optioneel kan de WALinuxAgent worden uitgeschakeld als een stap vóór de taak en na de taak worden ingeschakeld voor maximale beschikbaarheid van VM-resources voor de HPC-workload.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het inschakelen van InfiniBand](enable-infiniband.md) op de VM's uit de Voor InfiniBand ingeschakelde [H-serie](../../sizes-hpc.md) en [N-serie.](../../sizes-gpu.md)
- Meer informatie over het installeren en uitvoeren van verschillende [ondersteunde MPI-bibliotheken](setup-mpi.md) op de VM's.
- Bekijk het [overzicht van de HBv3-serie](hbv3-series-overview.md) en [het overzicht van de HC-serie.](hc-series-overview.md)
- Lees meer over de meest recente aankondigingen, HPC-workloadvoorbeelden en prestatieresultaten op de [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
