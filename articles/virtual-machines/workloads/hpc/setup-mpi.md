---
title: Message Passing Interface (MPI) instellen voor HPC-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het instellen van MPI voor HPC op Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 66de34c43ab1b3a6b4245f77196793bf9ad8530c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606637"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Bericht interface voor het door geven van HPC instellen

De [Message Passing Interface (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) is een open bibliotheek en de meest feitelijke standaard voor gedistribueerde geheugen parallel Lise ring. Dit wordt vaak gebruikt in veel HPC-workloads. HPC-workloads op de [RDMA](../../sizes-hpc.md#rdma-capable-instances) [-](../../sizes-hpc.md) werk belasting en [N-serie-](../../sizes-gpu.md) vm's kunnen mpi gebruiken om te communiceren via het InfiniBand-netwerk met lage latentie en hoge band breedte.
- Met de VM-grootten van SR-IOV in azure kunt u bijna alle MPI gebruiken met Mellanox OFED.
- Op Vm's waarvoor geen SR-IOV is ingeschakeld, wordt in ondersteunde MPI-implementaties de micro soft Network direct (ND)-interface gebruikt voor communicatie tussen Vm's. Daarom worden alleen micro soft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5. x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure RDMA-Stuur Programma's.

Voor met SR-IOV ingeschakelde [RDMA-compatibele vm's](../../sizes-hpc.md#rdma-capable-instances), [CENTOS-HPC VM-installatie kopieën](configure.md#centos-hpc-vm-images) versie 7,6 en hoger zijn geschikt. Deze VM-installatie kopieën worden geoptimaliseerd en vooraf geladen met de OFED-Stuur Programma's voor RDMA en verschillende veelgebruikte MPI-bibliotheken en weten schappelijke computing pakketten. Dit is de eenvoudigste manier om aan de slag te gaan.

Hoewel de voor beelden hier zijn voor RHEL/CentOS, maar de stappen algemeen zijn en kunnen worden gebruikt voor elk compatibel Linux-besturings systeem, zoals Ubuntu (16,04, 18,04 19,04, 20,04) en SLES (12 SP4 en 15). Meer voor beelden voor het instellen van andere MPI-implementaties voor andere distributies bevindt zich op de [azhpc-images opslag plaats](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Voor het uitvoeren van MPI-taken op SR-IOV ingeschakelde Vm's met bepaalde MPI-bibliotheken (zoals platform MPI) is het instellen van partitie sleutels (p-sleutels) in een Tenant vereist voor isolatie en beveiliging. Volg de stappen in de sectie [partitie sleutels detecteren](#discover-partition-keys) voor meer informatie over het bepalen van de p-sleutel waarden en het correct instellen van een mpi-taak met die mpi-bibliotheek.

> [!NOTE]
> De onderstaande code fragmenten zijn voor beelden. We raden u aan om de meest recente stabiele versies van de pakketten te gebruiken of om te verwijzen naar de [azhpc-installatie kopieën opslag plaats](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) is een framework van communicatie-API'S voor HPC. Het is geoptimaliseerd voor MPI-communicatie via InfiniBand en werkt met veel MPI-implementaties zoals OpenMPI en MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Recente builds van UCX hebben een [probleem](https://github.com/openucx/ucx/pull/5965) opgelost waarbij de juiste InfiniBand-interface wordt gekozen in de aanwezigheid van meerdere NIC-interfaces. Meer informatie [vindt u hier](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) over het uitvoeren van MPI via InfiniBand wanneer versneld netwerken zijn ingeschakeld op de VM.

## <a name="hpc-x"></a>HPC-X

De [HPC-X-software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) bevat UCX en HCOLL en kan worden gebouwd op basis van UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

HPC-X uitvoeren

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>MPI collectief optimaliseren

MPI collectieve communicatie primitieven bieden een flexibele, draag bare manier voor het implementeren van groeps communicatie bewerkingen. Ze worden veel gebruikt voor diverse weten schappelijke parallelle toepassingen en hebben een aanzienlijke invloed op de algehele prestaties van de toepassing. Raadpleeg het [artikel TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) voor meer informatie over configuratie parameters voor het optimaliseren van gezamenlijke communicatie prestaties met behulp van de HPC-X-en HCOLL-bibliotheek voor collectieve communicatie.

> [!NOTE] 
> Met HPC-X 2.7.4 + kan het nodig zijn om LD_LIBRARY_PATH expliciet door te geven als de UCX-versie op MOFED vs. die in HPC-X verschilt.

## <a name="openmpi"></a>OpenMPI

Installeer UCX zoals hierboven wordt beschreven. HCOLL maakt deel uit van de [HPC-X-software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) en vereist geen speciale installatie.

OpenMPI kan worden geïnstalleerd vanuit de pakketten die beschikbaar zijn in de opslag plaats.

```bash
sudo yum install –y openmpi
```

We raden u aan een nieuwste, stabiele versie van OpenMPI te bouwen met UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Voor optimale prestaties voert u OpenMPI uit met `ucx` en `hcoll` .

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controleer de hierboven vermelde partitie sleutel.

## <a name="intel-mpi"></a>Intel MPI

Down load de gewenste versie van [Intel mpi](https://software.intel.com/mpi-library/choose-download). Wijzig de I_MPI_FABRICS omgevings variabele, afhankelijk van de versie.
- Intel MPI 2019 en 2021: gebruik `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . De `mlx` provider gebruikt UCX. Het gebruik van termen is onstabiel en minder uitvoerende. Zie het [artikel TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) voor meer informatie.
- Intel MPI 2018: gebruiken `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: gebruiken `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

### <a name="non-sr-iov-vms"></a>Virtuele machines die niet zijn SR-IOV
Voor niet-SR-IOV-Vm's is een voor beeld van het downloaden van de [gratis evaluatie versie](https://registrationcenter.intel.com/en/forms/?productid=1740) van 5. x als volgt:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Zie de installatie handleiding voor de [Intel mpi-bibliotheek](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)voor installatie stappen.
U kunt eventueel ook ptrace inschakelen voor niet-hoofd processen zonder fout opsporing (vereist voor de meest recente versies van Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Voor SUSE Linux Enterprise Server VM-installatie kopie versies-SLES 12 SP3 voor HPC, SLES 12 SP3 voor HPC (Premium), SLES 12 SP1 voor HPC, SLES 12 SP1 voor HPC (Premium), SLES 12 SP4 en SLES 15, worden de RDMA-Stuur Programma's geïnstalleerd en worden Intel MPI-pakketten gedistribueerd op de VM. Installeer Intel MPI door de volgende opdracht uit te voeren:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 bouwen.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

MVAPICH2 uitvoeren.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi"></a>Platform MPI

Installeer de vereiste pakketten voor platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Volg het installatie proces.

De volgende opdrachten zijn voor beelden van het uitvoeren van MPI pingpong en allreduce met platform MPI op HBv3 Vm's met behulp van CentOS-HPC 7,6, 7,8 en 8,1 VM-installatie kopieën.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Installeer UCX zoals hierboven wordt beschreven. MPICH bouwen.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

MPICH uitvoeren.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controleer de hierboven vermelde partitie sleutel.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-benchmarks

Down load [OSU mpi-benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) en untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Benchmarks bouwen met een bepaalde MPI-bibliotheek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-benchmarks bevinden zich in de `mpi/` map.


## <a name="discover-partition-keys"></a>Partitie sleutels detecteren

Detectie van partitie sleutels (p-sleutels) voor communicatie met andere Vm's binnen dezelfde Tenant (Beschikbaarheidsset of virtuele-machine Schaalset).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Hoe groter de twee is, is de Tenant sleutel die moet worden gebruikt met MPI. Voor beeld: als het volgende de p-sleutels zijn, moet 0x800b worden gebruikt met MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Gebruik de partitie sleutel anders dan standaard (0x7fff). UCX vereist dat de MSB van p-sleutel moet worden gewist. Stel bijvoorbeeld UCX_IB_PKEY in als 0x000b voor 0x800b.

Houd er ook rekening mee dat als de Tenant (Beschikbaarheidsset of virtuele-machine Schaalset) bestaat, de PKEYs hetzelfde blijft. Dit geldt ook wanneer er knoop punten worden toegevoegd of verwijderd. Nieuwe tenants krijgen verschillende PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Gebruikers limieten instellen voor MPI

Gebruikers limieten instellen voor MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>SSH-sleutels voor MPI instellen

Stel SSH-sleutels voor MPI-typen in waarvoor deze nodig is.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

De bovenstaande syntaxis gaat ervan uit dat een gedeelde basismap, else. ssh-map, naar elk knoop punt moet worden gekopieerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de met [InfiniBand ingeschakelde](../../sizes-hpc.md#rdma-capable-instances) [H-Series](../../sizes-hpc.md) en [N-Series](../../sizes-gpu.md) vm's
- Bekijk het overzicht van de [HBv3-serie](hbv3-series-overview.md) en de [HC-serie](hc-series-overview.md).
- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
