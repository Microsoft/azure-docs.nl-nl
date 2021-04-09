---
title: InifinBand inschakelen op HPC-Vm's-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het inschakelen van InfiniBand op Azure HPC-Vm's.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 089976f2009e006f53dd2a77f09f57d5090429b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721235"
---
# <a name="enable-infiniband"></a>InfiniBand inschakelen

[RDMA-compatibele](../../sizes-hpc.md#rdma-capable-instances) VM's uit de [H-serie](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) communiceren via het InfiniBand-netwerk met lage latentie en hoge bandbreedte. De RDMA-functionaliteit via een dergelijke verbinding is essentieel om de schaalbaarheid en prestaties van HPC- en AI-workloads op gedistribueerde knooppunten te vergroten. De VM's uit de voor InfiniBand geschikte H-serie en N-serie zijn verbonden binnen een niet-blokkerende FAT-structuur met een ontwerp met een lage diameter voor optimale en consistente RDMA-prestaties.

Er zijn verschillende manieren om InfiniBand in te scha kelen voor de mogelijke VM-grootten.

## <a name="vm-images-with-infiniband-drivers"></a>VM-installatie kopieën met InfiniBand-Stuur Programma's
Zie [VM-installatie kopieën](configure.md#vm-images) voor een lijst met ondersteunde VM-installatie kopieën die vooraf zijn geladen met InfiniBand-Stuur programma's (voor SR-IOV-of niet-SR-IOV-vm's) of kan worden geconfigureerd met de juiste Stuur Programma's voor [virtuele machines met RDMA-functionaliteit](../../sizes-hpc.md#rdma-capable-instances).
- De [CentOS-VM-](configure.md#centos-hpc-vm-images) installatie kopieën in de Marketplace zijn de eenvoudigste manier om aan de slag te gaan.
- De [Ubuntu](configure.md#ubuntu-vm-images) -VM-installatie kopieën kunnen worden geconfigureerd met de juiste IB-Stuur Programma's.

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand-VM-extensies voor Stuur Programma's
In Linux kan de [InfiniBandDriverLinux-VM-extensie](../../extensions/hpc-compute-infiniband-linux.md) worden gebruikt voor het installeren van de Mellanox OFED-Stuur Programma's en het inschakelen van Infiniband op de op SR-IOV ingeschakelde H-en N-Series vm's.

In Windows installeert de [InfiniBandDriverWindows-VM-extensie](../../extensions/hpc-compute-infiniband-windows.md) Windows-netwerk directe Stuur programma's (op niet-SR-IOV-vm's) of Mellanox OFED-Stuur programma's (op SR-IOV-vm's) voor RDMA-connectiviteit. In bepaalde implementaties van A8-en A9-instanties wordt de uitbrei ding HpcVmDrivers automatisch toegevoegd. Houd er rekening mee dat de HpcVmDrivers VM-extensie wordt afgeschaft. het wordt niet bijgewerkt.

U kunt [Azure PowerShell](/powershell/azure/) -cmdlets gebruiken om de VM-extensie toe te voegen aan een virtuele machine. Zie [extensies en functies van virtuele machines](../../extensions/overview.md)voor meer informatie. U kunt ook werken met uitbrei dingen voor virtuele machines die zijn geïmplementeerd in het [klassieke implementatie model](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Handmatige installatie
[Mellanox open fabrics-Stuur programma's (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) kunnen hand matig worden geïnstalleerd op de op [SR-IOV ingeschakelde](../../sizes-hpc.md#rdma-capable-instances) [H-Series](../../sizes-hpc.md) en vm's uit de [N-serie](../../sizes-gpu.md) .

### <a name="linux"></a>Linux
De [OFED-Stuur Programma's voor Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) kunnen met onderstaand voor beeld worden geïnstalleerd. Het voor beeld is hier voor RHEL/CentOS, maar de stappen zijn algemeen en kunnen worden gebruikt voor elk compatibel Linux-besturings systeem, zoals Ubuntu (16,04, 18,04 19,04, 20,04) en SLES (12 SP4 en 15). Meer voor beelden voor andere distributies vindt u op de [azhpc-opslag plaats](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). De Stuur Programma's voor het postvak in werken ook wel, maar de Mellanox OFED-Stuur Programma's bieden meer functies.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Voor Windows downloadt en installeert u de [MELLANOX OFED voor Windows-Stuur Programma's](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>IP via InfiniBand (IB) inschakelen
Als u MPI-taken wilt uitvoeren, hebt u doorgaans geen IPoIB nodig. De MPI-bibliotheek gebruikt de werk-interface voor IB-communicatie (tenzij u expliciet het TCP/IP-kanaal van de MPI-bibliotheek gebruikt). Maar als u een app hebt die gebruikmaakt van TCP/IP voor communicatie en u meer dan IB wilt uitvoeren, kunt u gebruikmaken van IPoIB via de IB-interface. Gebruik de volgende opdrachten (voor RHEL/CentOS) om IP via InfiniBand in te scha kelen.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het installeren van verschillende [ondersteunde mpi-bibliotheken](setup-mpi.md) en hun optimale configuratie op de vm's.
- Bekijk het overzicht van de [HBv3-serie](hbv3-series-overview.md) en de [HC-serie](hc-series-overview.md).
- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
