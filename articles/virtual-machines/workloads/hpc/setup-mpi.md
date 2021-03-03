---
title: Message Passing Interface instellen voor HPC-Azure-Virtual Machines | Microsoft Docs
description: Meer informatie over het instellen van MPI voor HPC op Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 9804ed23da4cb9ccbb7515cec03fcc9b4147f749
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673256"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Bericht interface voor het door geven van HPC instellen

De [Message Passing Interface (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) is een open bibliotheek en de meest feitelijke standaard voor gedistribueerde geheugen parallel Lise ring. Dit wordt vaak gebruikt in veel HPC-workloads. HPC-workloads op de [RDMA](../../sizes-hpc.md#rdma-capable-instances) [-](../../sizes-hpc.md) werk belasting en [N-serie-](../../sizes-gpu.md) vm's kunnen mpi gebruiken om te communiceren via het InfiniBand-netwerk met lage latentie en hoge band breedte.

Met de VM-grootten van SR-IOV op Azure (HBv2, HB, HC, NCv3, NDv2) kunt u bijna elke MPI-functie gebruiken met Mellanox OFED. Op Vm's waarvoor geen SR-IOV is ingeschakeld, wordt in ondersteunde MPI-implementaties de micro soft Network direct (ND)-interface gebruikt voor communicatie tussen Vm's. Daarom worden alleen micro soft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5. x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure RDMA-Stuur Programma's.

Voor met SR-IOV ingeschakelde [RDMA-compatibele vm's](../../sizes-hpc.md#rdma-capable-instances), [CentOS-HPC-versie 7,6 of een latere](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) versie van de VM-installatie kopieën in de Marketplace zijn geoptimaliseerd en vooraf geladen met de OFED-Stuur Programma's voor RDMA en diverse veelgebruikte mpi-bibliotheken en weten schappelijke computing pakketten. Dit is de eenvoudigste manier om aan de slag te gaan.

Hoewel de voor beelden hier zijn voor RHEL/CentOS, maar de stappen algemeen zijn en kunnen worden gebruikt voor elk compatibel Linux-besturings systeem, zoals Ubuntu (16,04, 18,04 19,04, 20,04) en SLES (12 SP4 en 15). Meer voor beelden voor het instellen van andere MPI-implementaties voor andere distributies bevindt zich op de [azhpc-images opslag plaats](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Voor het uitvoeren van MPI-taken op Vm's met SR-IOV is het instellen van partitie sleutels (p-sleutels) in een Tenant toegestaan voor isolatie en beveiliging. Volg de stappen in de sectie [partitie sleutels detecteren](#discover-partition-keys) voor meer informatie over het bepalen van de p-sleutel waarden en het correct instellen van een mpi-taak.

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) is een framework van communicatie-API'S voor HPC. Het is geoptimaliseerd voor MPI-communicatie via InfiniBand en werkt met veel MPI-implementaties zoals OpenMPI en MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

De [HPC-X-software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) bevat UCX en HCOLL.

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

## <a name="openmpi"></a>OpenMPI

Installeer UCX zoals hierboven wordt beschreven. HCOLL maakt deel uit van de [HPC-X-software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) en vereist geen speciale installatie.

Installeer OpenMPI van de pakketten die beschikbaar zijn in de opslag plaats.

```bash
sudo yum install –y openmpi
```

OpenMPI bouwen.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Voer OpenMPI uit.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controleer de hierboven vermelde partitie sleutel.

## <a name="intel-mpi"></a>Intel MPI

Down load de gewenste versie van [Intel mpi](https://software.intel.com/mpi-library/choose-download). Wijzig de I_MPI_FABRICS omgevings variabele, afhankelijk van de versie. Voor Intel MPI 2018, gebruik `I_MPI_FABRICS=shm:ofa` en voor 2019, gebruikt u `I_MPI_FABRICS=shm:ofi` .

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

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Installeer de vereiste pakketten voor platform MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Volg het installatie proces.

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
- Bekijk [Overzicht HB-serie](hb-series-overview.md) en [Overzicht HC-serie](hc-series-overview.md) voor meer informatie over het optimaal configureren van workloads ten behoeve van de prestaties en schaalbaarheid.
- Lees over de laatste aankondigingen en enkele HPC-voorbeelden en -resultaten in de [Azure Compute Tech Community-blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
