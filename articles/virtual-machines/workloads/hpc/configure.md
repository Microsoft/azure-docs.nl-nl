---
title: Configuratie en optimalisatie van met InfiniBand ingeschakelde H-Series en N-Series Azure Virtual Machines
description: Meer informatie over het configureren en optimaliseren van de met InfiniBand ingeschakelde H-Series en N-Series Vm's voor HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/23/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b1686b08608e4f1c49cd918e86e8149f0fe2a21c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963365"
---
# <a name="configure-and-optimize-vms"></a>VM's configureren en optimaliseren

In dit artikel worden bekende technieken gedeeld voor het configureren en optimaliseren van de met InfiniBand ingeschakelde [H-Series](../../sizes-hpc.md) en [N-Series](../../sizes-gpu.md) vm's voor HPC.

## <a name="vm-images"></a>VM-installatiekopieën
Op InfiniBand ingeschakelde Vm's zijn de juiste Stuur Programma's vereist om RDMA in te scha kelen. Op Linux worden de CentOS-HPC-VM-installatie kopieën in de Marketplace vooraf geconfigureerd met de juiste Stuur Programma's. De Ubuntu-VM-installatie kopieën kunnen worden geconfigureerd met de juiste Stuur Programma's met behulp van de [instructies hier](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Het is ook raadzaam om [aangepaste VM-installatie kopieën](../../linux/tutorial-custom-images.md) te maken met de juiste Stuur Programma's en configuratie en deze herhaaldelijk te hergebruiken.

> [!NOTE]
> Op met GPU ingeschakelde vm's uit de [N-serie](../../sizes-gpu.md) zijn de juiste GPU-Stuur Programma's ook vereist, die kunnen worden toegevoegd via de [VM-extensies](../../extensions/hpccompute-gpu-linux.md) of [hand matig](../../linux/n-series-driver-setup.md). Sommige VM-installatie kopieën op de Marketplace worden ook vooraf geïnstalleerd met de NVIDIA GPU-Stuur Programma's.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM-installatie kopieën

#### <a name="non-sr-iov-enabled-vms"></a>Vm's waarvoor geen SR-IOV is ingeschakeld
Voor [RDMA-compatibele](../../sizes-hpc.md#rdma-capable-instances), op SR-IOV geschikte vm's, CENTOS-HPC-versie 6,5 of hoger, is er een geschikte versie van 7,5 in Marketplace. Een voor beeld: voor [vm's uit de H16-serie](../../h-series.md)wordt versie 7,1 tot 7,5 aanbevolen. Deze VM-installatie kopieën worden vooraf geladen met de netwerk directe Stuur Programma's voor RDMA en Intel MPI versie 5,1.

> [!NOTE]
> Op deze CentOS-gebaseerde HPC-installatie kopieën voor virtuele machines die niet zijn beveiligd met SR-IOV, worden kernel-updates uitgeschakeld in het configuratie bestand **yum** . Dit komt doordat de Network direct Linux RDMA-Stuur Programma's worden gedistribueerd als een RPM-pakket, en updates van Stuur Programma's kunnen mogelijk niet worden uitgevoerd als de kernel wordt bijgewerkt.

#### <a name="sr-iov-enabled-vms"></a>Vm's met SR-IOV ingeschakeld
  Voor met SR-IOV ingeschakelde [RDMA-compatibele vm's](../../sizes-hpc.md#rdma-capable-instances), [CentOS-HPC-versie 7,6 of een latere](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) versie van VM-installatie kopieën in de Marketplace, zijn geschikt. Deze VM-installatie kopieën worden geoptimaliseerd en vooraf geladen met de OFED-Stuur Programma's voor RDMA en verschillende veelgebruikte MPI-bibliotheken en weten schappelijke computing pakketten. Dit is de eenvoudigste manier om aan de slag te gaan.

  Voor beeld van scripts die worden gebruikt bij het maken van de VM-installatie kopieën CentOS-HPC versie 7,6 en hoger van een basis installatie kopie voor CentOS Marketplace, vindt u op de [azhpc-images opslag plaats](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE] 
  > De nieuwste Azure HPC Marketplace-installatie kopieën hebben Mellanox OFED 5,1 en hoger, die geen ondersteuning bieden voor ConnectX3-Pro InfiniBand-kaarten. Met SR-IOV ingeschakelde VM-grootten van N-serie met FDR InfiniBand (bijvoorbeeld NCv3) kunnen de volgende CentOS van de HPC-VM-installatie kopie worden gebruikt, of ouder:
  >- Open Logic: CentOS-HPC: 7,6:7.6.2020062900
  >- Open Logic: CentOS-HPC: 7_6gen2:7.6.2020062901
  >- Open Logic: CentOS-HPC: 7,7:7.7.2020062600
  >- Open Logic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
  >- Open Logic: CentOS-HPC: 8_1:8.1.2020062400
  >- Open Logic: CentOS-HPC: 8_1-Gen2:8.1.2020062401


### <a name="rhelcentos-vm-images"></a>VM-installatie kopieën van RHEL/CentOS
Installatie kopieën op basis van niet-HPC-RHEL of CentOS op de Marketplace kunnen worden geconfigureerd voor gebruik op [virtuele machines met RDMA-functionaliteit](../../sizes-hpc.md#rdma-capable-instances)voor SR-IOV. Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) en het instellen van [mpi](setup-mpi.md) op de vm's.

  Voor beeld van scripts die worden gebruikt bij het maken van de VM-installatie kopieën CentOS-HPC versie 7,6 en hoger van een basis installatie kopie voor CentOS Marketplace, vindt u op de [azhpc-images opslag plaats](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE]
  > Mellanox OFED 5,1 en hoger bieden geen ondersteuning voor ConnectX3-Pro InfiniBand-kaarten op SR-IOV waarvoor VM-grootten van N-serie zijn ingeschakeld met FDR InfiniBand (bijvoorbeeld NCv3). Gebruik LTS Mellanox OFED versie 4.9-0.1.7.0 of ouder op de virtuele machines uit de N-serie met ConnectX3-Pro kaarten. Lees [hier](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)meer informatie.

### <a name="ubuntu-vm-images"></a>Ubuntu VM-installatie kopieën
Ubuntu Server 16,04 LTS, 18,04 LTS en 20,04 LTS VM-installatie kopieën in de Marketplace worden ondersteund voor [virtuele machines](../../sizes-hpc.md#rdma-capable-instances)met SR-IOV en niet-SR-IOV-ondersteuning. Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) en het instellen van [mpi](setup-mpi.md) op de vm's.

  Voor beeld van scripts die kunnen worden gebruikt bij het maken van de Ubuntu 18,04 LTS op basis van HPC VM-installatie kopieën bevinden zich op de [azhpc-installatie kopieën opslag plaats](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>VM-installatie kopieën SUSE Linux Enterprise Server
SLES 12 SP3 voor HPC, SLES 12 SP3 voor HPC (Premium), SLES 12 SP1 voor HPC, SLES 12 SP1 voor HPC (Premium), SLES 12 SP4 en SLES 15 VM-installatie kopieën in de Marketplace worden ondersteund. Deze VM-installatie kopieën worden vooraf geladen met de netwerk directe Stuur Programma's voor RDMA en Intel MPI versie 5,1. Meer informatie over het [instellen van MPI](setup-mpi.md) op de vm's.

## <a name="optimize-vms"></a>Vm's optimaliseren

Hier volgen enkele optionele optimalisatie-instellingen voor verbeterde prestaties van de virtuele machine.

### <a name="update-lis"></a>LIS bijwerken

Indien nodig voor functionaliteit of prestaties, kunnen [Lis-Stuur programma's (Linux Integration Services)](../../linux/endorsed-distros.md) worden geïnstalleerd of bijgewerkt op ondersteunde OS distributies, met name voor de implementatie met behulp van een aangepaste installatie kopie of een oudere versie van het besturings systeem, zoals CENTOS/RHEL 6. x of een eerdere versie van 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Geheugen vrijmaken

Verbeter de prestaties door geheugen automatisch vrij te maken om externe geheugen toegang te voor komen.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Dit permanent maken na het opnieuw opstarten van de VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Firewall-en SELinux uitschakelen

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
U kunt de WALinuxAgent eventueel uitschakelen als een voorbereidende taak en een achterwaartse post-taak inschakelen voor een maximale Beschik baarheid van de VM-resource voor de HPC-workload.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) op de vm's met de [H-Series](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) van Infiniband.
- Meer informatie over het installeren van verschillende [ondersteunde mpi-bibliotheken](setup-mpi.md) en hun optimale configuratie op de vm's.
- Bekijk [Overzicht HB-serie](hb-series-overview.md) en [Overzicht HC-serie](hc-series-overview.md) voor meer informatie over het optimaal configureren van workloads ten behoeve van de prestaties en schaalbaarheid.
- Lees over de laatste aankondigingen en enkele HPC-voorbeelden en -resultaten in de [Azure Compute Tech Community-blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
