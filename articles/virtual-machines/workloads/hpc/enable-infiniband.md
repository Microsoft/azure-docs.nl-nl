---
title: InifinBand inschakelen op HPC-VM's - Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het inschakelen van InfiniBand op Azure HPC-VM's.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: dba336c8690bba2bb388a8b9ab2d52b651166da5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599601"
---
# <a name="enable-infiniband"></a>InfiniBand inschakelen

[RDMA-compatibele](../../sizes-hpc.md#rdma-capable-instances) VM's uit de [H-serie](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) communiceren via het InfiniBand-netwerk met lage latentie en hoge bandbreedte. De RDMA-functionaliteit via een dergelijke verbinding is essentieel om de schaalbaarheid en prestaties van HPC- en AI-workloads op gedistribueerde knooppunten te vergroten. De VM's uit de voor InfiniBand geschikte H-serie en N-serie zijn verbonden binnen een niet-blokkerende FAT-structuur met een ontwerp met een lage diameter voor optimale en consistente RDMA-prestaties.

Er zijn verschillende manieren om InfiniBand in teschakelen op de VM-grootten die geschikt zijn.

## <a name="vm-images-with-infiniband-drivers"></a>VM-afbeeldingen met InfiniBand-stuurprogramma's
Zie [VM-afbeeldingen](configure.md#vm-images) voor een lijst met ondersteunde VM-afbeeldingen op de Marketplace, die vooraf zijn geladen met InfiniBand-stuurprogramma's (voor SR-IOV- of niet-SR-IOV-VM's) of die kunnen worden geconfigureerd met de juiste stuurprogramma's voor VM's die geschikt zijn voor [RDMA.](../../sizes-hpc.md#rdma-capable-instances)
- De [CentOS-HPC](configure.md#centos-hpc-vm-images) VM-afbeeldingen in marketplace zijn de eenvoudigste manier om aan de slag te gaan.
- De [Ubuntu](configure.md#ubuntu-vm-images) VM-installatie afbeeldingen kunnen worden geconfigureerd met de juiste IB-stuurprogramma's.

## <a name="infiniband-driver-vm-extensions"></a>VM-extensies voor InfiniBand-stuurprogramma
In Linux kan de [VM-extensie InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) worden gebruikt om de Mellanox OFED-stuurprogramma's te installeren en InfiniBand in teschakelen op de VM's uit de H- en N-serie die zijn ingeschakeld voor SR-IOV.

In Windows installeert [de VM-extensie InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) Windows Network Direct-stuurprogramma's (op niet-SR-IOV-VM's) of Mellanox OFED-stuurprogramma's (op SR-IOV-VM's) voor RDMA-connectiviteit. In bepaalde implementaties van A8- en A9-exemplaren wordt de HpcVmDrivers-extensie automatisch toegevoegd. Houd er rekening mee dat de VM-extensie HpcVmDrivers wordt afgeschaft; deze wordt niet bijgewerkt.

Als u de VM-extensie wilt toevoegen aan een VM, kunt u [Azure PowerShell](/powershell/azure/) cmdlets. Zie Extensies en functies [van virtuele machines voor meer informatie.](../../extensions/overview.md) U kunt ook werken met extensies voor VM's die zijn geïmplementeerd in het [klassieke implementatiemodel](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Handmatige installatie
[Mellanox OpenFabrics-stuurprogramma's (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) kunnen handmatig worden geïnstalleerd op de VM's uit de [H-serie](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) met [SR-IOV.](../../sizes-hpc.md#rdma-capable-instances)

### <a name="linux"></a>Linux
De [OFED-stuurprogramma's voor Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) kunnen worden geïnstalleerd met het onderstaande voorbeeld. Hoewel het voorbeeld hier voor RHEL/CentOS is, maar de stappen algemeen zijn en kunnen worden gebruikt voor elk compatibel Linux-besturingssysteem zoals Ubuntu (16.04, 18.04 19.04, 20.04) en SLES (12 SP4 en 15). Meer voorbeelden voor andere distributies is op de [azhpc-images-repo](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). De postvak IN-stuurprogramma's werken ook, maar de Mellanox OFED-stuurprogramma's bieden meer functies.

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
Voor Windows downloadt en installeert u [Mellanox OFED voor Windows-stuurprogramma's.](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)

## <a name="enable-ip-over-infiniband-ib"></a>IP via InfiniBand (IB) inschakelen
Als u van plan bent MPI-taken uit te voeren, hebt u doorgaans geen IPoIB nodig. De MPI-bibliotheek maakt gebruik van de werkwoordinterface voor IB-communicatie (tenzij u expliciet het TCP/IP-kanaal van de MPI-bibliotheek gebruikt). Maar als u een app hebt die gebruikmaakt van TCP/IP voor communicatie en u wilt uitvoeren via IB, kunt u IPoIB gebruiken via de IB-interface. Gebruik de volgende opdrachten (voor RHEL/CentOS) om IP via InfiniBand in te stellen.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het installeren en uitvoeren van verschillende [ondersteunde MPI-bibliotheken](setup-mpi.md) op de VM's.
- Bekijk het [overzicht van de HBv3-serie en](hbv3-series-overview.md) overzicht van de [HC-serie.](hc-series-overview.md)
- Lees meer over de meest recente aankondigingen, HPC-workloadvoorbeelden en prestatieresultaten op [de Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
