---
title: Een Message Passing Interface (MPI) instellen voor HPC - Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het instellen van MPI voor HPC in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f43fc94174ebdcfdf447d3635a696193959849fa
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600286"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Een Message Passing Interface hpc instellen

De [Message Passing Interface (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) is een open bibliotheek en de-feitelijke standaard voor gedistribueerde geheugenlalisatie. Het wordt vaak gebruikt voor veel HPC-workloads. HPC-workloads op de [voor RDMA](../../sizes-hpc.md#rdma-capable-instances) geschikte VM's uit [de H-serie](../../sizes-hpc.md) en N-serie kunnen MPI gebruiken om te communiceren via het InfiniBand-netwerk met lage latentie en hoge bandbreedte. [](../../sizes-gpu.md)
- Met de VM-grootten met SR-IOV in Azure kan vrijwel elke MPI-smaak worden gebruikt met Mellanox OFED.
- Op niet-SR-IOV-VM's gebruiken ondersteunde MPI-implementaties de Microsoft Network Direct-interface (ND) om te communiceren tussen VM's. Daarom worden alleen Microsoft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5.x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI-runtimebibliotheek zijn al dan niet compatibel met de Azure RDMA-stuurprogramma's.

Voor VM's met SR-IOV die geschikt zijn voor [RDMA,](../../sizes-hpc.md#rdma-capable-instances)zijn [CentOS-HPC VM-afbeeldingen](configure.md#centos-hpc-vm-images) versie 7.6 en hoger geschikt. Deze VM-afbeeldingen zijn geoptimaliseerd en vooraf geladen met de OFED-stuurprogramma's voor RDMA en verschillende veelgebruikte MPI-bibliotheken en wetenschappelijke computingpakketten. Dit is de eenvoudigste manier om aan de slag te gaan.

Hoewel de voorbeelden hier voor RHEL/CentOS zijn, maar de stappen algemeen zijn en kunnen worden gebruikt voor elk compatibel Linux-besturingssysteem zoals Ubuntu (16.04, 18.04 19.04, 20.04) en SLES (12 SP4 en 15). Meer voorbeelden voor het instellen van andere MPI-implementaties op andere distributies is op de [azhpc-images-repo.](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)

> [!NOTE]
> Voor het uitvoeren van MPI-taken op VM's met SR-IOV met bepaalde MPI-bibliotheken (zoals Platform MPI) moet u mogelijk partitiesleutels (p-sleutels) instellen in een tenant voor isolatie en beveiliging. Volg de stappen [](#discover-partition-keys) in de sectie Partitiesleutels ontdekken voor meer informatie over het bepalen van de p-key-waarden en het correct instellen van deze waarden voor een MPI-taak met die MPI-bibliotheek.

> [!NOTE]
> De onderstaande codefragmenten zijn voorbeelden. We raden u aan de nieuwste stabiele versies van de pakketten te gebruiken of te verwijzen naar de [azhpc-images-repo](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="choosing-mpi-library"></a>MPI-bibliotheek kiezen
Als een HPC-toepassing een bepaalde MPI-bibliotheek aanbeveelt, probeert u die versie eerst. Als u flexibiliteit hebt met betrekking tot welke MPI u kunt kiezen en u de beste prestaties wilt, kunt u HPC-X proberen. Over het algemeen presteert de HPC-X MPI het beste met behulp van het UCX-framework voor de InfiniBand-interface en maakt gebruik van alle hardware- en softwaremogelijkheden van Mellanox InfiniBand. Daarnaast zijn HPC-X en OpenMPI compatibel met ABI, zodat u dynamisch een HPC-toepassing kunt uitvoeren met HPC-X die is gebouwd met OpenMPI. Op dezelfde manier zijn Intel MPI, MVAPICH en MPICH compatibel met ABI.

In de volgende afbeelding ziet u de architectuur voor de populaire MPI-bibliotheken.

![Architectuur voor populaire MPI-bibliotheken](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) is een framework van communicatie-API's voor HPC. Het is geoptimaliseerd voor MPI-communicatie via InfiniBand en werkt met veel MPI-implementaties zoals OpenMPI en MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Recente builds van UCX [](https://github.com/openucx/ucx/pull/5965) hebben een probleem opgelost waarbij de juiste InfiniBand-interface wordt gekozen in de aanwezigheid van meerdere NIC-interfaces. Meer informatie [over het](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) uitvoeren van MPI via InfiniBand wanneer versneld netwerken is ingeschakeld op de VM.

## <a name="hpc-x"></a>HPC-X

De [HPC-X-softwaretoolkit](https://www.mellanox.com/products/hpc-x-toolkit) bevat UCX en HCOLL en kan worden gebouwd met UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

De volgende opdracht illustreert enkele aanbevolen mpirun-argumenten voor HPC-X en OpenMPI.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
Hierbij

|Parameter|Beschrijving                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |Hiermee geeft u het aantal MPI-processen. Bijvoorbeeld: `-n 16`.|
|`$HOSTFILE`|Hiermee geeft u een bestand met de hostnaam of HET IP-adres om aan te geven waar de MPI-processen worden uitgevoerd. Bijvoorbeeld: `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Hiermee geeft u het aantal MPI-processen op dat wordt uitgevoerd in elk NUMA-domein. Als u bijvoorbeeld vier MPI-processen per NUMA wilt opgeven, gebruikt u `--map-by ppr:4:numa:pe=1` .|
|`$NUMBER_THREADS_PER_PROCESS`  |Hiermee geeft u het aantal threads per MPI-proces op. Als u bijvoorbeeld één MPI-proces en vier threads per NUMA wilt opgeven, gebruikt u `--map-by ppr:1:numa:pe=4` .|
|`-report-bindings` |Hiermee wordt de toewijzing van MPI-processen aan kernen afgedrukt. Dit is handig om te controleren of het vastmaken van uw MPI-proces juist is.|
|`$MPI_EXECUTABLE`  |Hiermee geeft u het uitvoerbare MPI-bestand op dat is gebouwd om te koppelen in MPI-bibliotheken. MPI-compiler-wrappers doen dit automatisch. Bijvoorbeeld: `mpicc` of `mpif90` .|

Een voorbeeld van het uitvoeren van de OSU-latentieversleutelingsmark is als volgt:
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>MPI-collectieven optimaliseren

MPI Collectieve communicatieprim primitieven bieden een flexibele, draagbare manier om groepscommunicatiebewerkingen te implementeren. Ze worden veel gebruikt in verschillende wetenschappelijke parallelle toepassingen en hebben een aanzienlijke invloed op de algehele prestaties van toepassingen. Raadpleeg het [TechCommunity-artikel](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) voor meer informatie over configuratieparameters voor het optimaliseren van collectieve communicatieprestaties met hpc-x en HCOLL-bibliotheek voor collectieve communicatie.

Als u bijvoorbeeld vermoedt dat uw nauw gekoppelde MPI-toepassing een overmatige hoeveelheid collectieve communicatie aan het doen is, kunt u proberen hiërarchische collectieven (HCOLL) in te stellen. Als u deze functies wilt inschakelen, gebruikt u de volgende parameters.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> Met HPC-X 2.7.4+ kan het nodig zijn om expliciet LD_LIBRARY_PATH door te geven als de UCX-versie op MOFED verschilt van die in HPC-X.

## <a name="openmpi"></a>Openmpi

Installeer UCX zoals hierboven wordt beschreven. HCOLL maakt deel uit van de [HPC-X-softwaretoolkit](https://www.mellanox.com/products/hpc-x-toolkit) en vereist geen speciale installatie.

OpenMPI kan worden geïnstalleerd vanuit de pakketten die beschikbaar zijn in de repo.

```bash
sudo yum install –y openmpi
```

We raden u aan om een nieuwste, stabiele versie van OpenMPI met UCX te bouwen.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Voer OpenMPI uit met en voor `ucx` optimale `hcoll` prestaties. Zie ook het voorbeeld met [HPC-X.](#hpc-x)

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controleer de partitiesleutel zoals hierboven wordt vermeld.

## <a name="intel-mpi"></a>Intel MPI

Download uw keuzeversie van [Intel MPI](https://software.intel.com/mpi-library/choose-download). De Release van Intel MPI 2019 is overgeschakeld van het Open Fabrics Alliance (OFA)-framework naar het Open Fabrics Interfaces-framework (OFI) en ondersteunt momenteel libfabric. Er zijn twee providers voor InfiniBand-ondersteuning: mlx en werkwoorden.
Wijzig de I_MPI_FABRICS omgevingsvariabele, afhankelijk van de versie.
- Intel MPI 2019 en 2021: `I_MPI_FABRICS=shm:ofi` gebruik , `I_MPI_OFI_PROVIDER=mlx` . De `mlx` provider gebruikt UCX. Het gebruik van werkwoorden is instabiel en minder goed presterend. Zie het [TechCommunity-artikel](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) voor meer informatie.
- Intel MPI 2018: gebruiken `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: gebruiken `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

Hier volgen enkele voorgestelde mpirun-argumenten voor Intel MPI 2019 update 5+.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
Hierbij

|Parameter|Beschrijving                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Hiermee geeft u op welke libfabric-provider moet worden gebruikt, wat van invloed is op de GEBRUIKTE API, het protocol en het netwerk. werkwoorden is een andere optie, maar over het algemeen biedt mlx betere prestaties.|
|`I_MPI_DEBUG`|Hiermee geeft u het niveau van extra foutopsporingsuitvoer op, dat details kan bieden over waar processen worden vastgemaakt en welk protocol en netwerk worden gebruikt.|
|`I_MPI_PIN_DOMAIN` |Hiermee geeft u op hoe u uw processen wilt vastmaken. U kunt bijvoorbeeld vastmaken aan kernen, sockets of NUMA-domeinen. In dit voorbeeld stelt u deze omgevingsvariabele in op numa, wat betekent dat processen worden vastgemaakt aan NUMA-knooppuntdomeinen.|

### <a name="optimizing-mpi-collectives"></a>MPI-collectieven optimaliseren

Er zijn enkele andere opties die u kunt proberen, met name als collectieve bewerkingen veel tijd in beslag nemen. Intel MPI 2019 update 5+ ondersteunt mlx en maakt gebruik van het UCX-framework om te communiceren met InfiniBand. Het biedt ook ondersteuning voor HCOLL.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>Niet-SR-IOV-VM's

Voor niet-SR-IOV-VM's [is](https://registrationcenter.intel.com/en/forms/?productid=1740) een voorbeeld van het downloaden van de gratis evaluatieversie van runtime 5.x als volgt:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Zie de Installatiehandleiding voor [de Intel MPI-bibliotheek voor de installatiestappen.](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)
Desgewenst kunt u ptrace inschakelen voor niet-root processen zonder debugger (nodig voor de meest recente versies van Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Voor SUSE Linux Enterprise Server VM-installatiekopieversies : SLES 12 SP3 voor HPC, SLES 12 SP3 for HPC (Premium), SLES 12 SP1 for HPC, SLES 12 SP1 for HPC (Premium), SLES 12 SP4 en SLES 15, worden de RDMA-stuurprogramma's geïnstalleerd en worden Intel MPI-pakketten gedistribueerd op de VM. Installeer Intel MPI door de volgende opdracht uit te voeren:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Hier volgt een voorbeeld van het bouwen van MVAPICH2. Er zijn mogelijk nieuwere versies beschikbaar dan wat hieronder wordt gebruikt.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Een voorbeeld van het uitvoeren van de OSU-latentieversleutelingsmark is als volgt:
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

De volgende lijst bevat verschillende aanbevolen `mpirun` argumenten.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
Hierbij

|Parameter|Beschrijving                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Hiermee geeft u op welk bindingsbeleid moet worden gebruikt, wat van invloed is op de manier waarop processen worden vastgemaakt aan kern-ID's. In dit geval geeft u spreiding op, zodat processen gelijkmatig worden verdeeld over de NUMA-domeinen.|
|`MV2_CPU_BINDING_LEVEL`|Hiermee geeft u waar processen vastmaken. In dit geval stelt u deze in op numanode, wat betekent dat processen worden vastgemaakt aan eenheden van NUMA-domeinen.|
|`MV2_SHOW_CPU_BINDING` |Hiermee geeft u op of u foutopsporingsinformatie wilt krijgen over waar de processen zijn vastgemaakt.|
|`MV2_SHOW_HCA_BINDING` |Hiermee geeft u op of u foutopsporingsinformatie wilt krijgen over welke hostkanaaladapter elk proces gebruikt.|

## <a name="platform-mpi"></a>Platform-MPI

Installeer de vereiste pakketten voor Platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Volg het installatieproces.

De volgende opdrachten zijn voorbeelden van het uitvoeren van MPI-pingpong en allreduce met behulp van Platform MPI op HBv3-VM's met behulp van VM-afbeeldingen van CentOS-HPC 7.6, 7.8 en 8.1.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Installeer UCX zoals hierboven wordt beschreven. Bouw MPICH.

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

Controleer de partitiesleutel zoals hierboven wordt vermeld.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-benchmarks

Download [OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) en untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Bouw benchmarks met behulp van een bepaalde MPI-bibliotheek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-benchmarks staan in de `mpi/` map .


## <a name="discover-partition-keys"></a>Partitiesleutels ontdekken

Partitiesleutels (p-sleutels) ontdekken voor communicatie met andere VM's binnen dezelfde tenant (beschikbaarheidsset of virtuele-machineschaalset).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

De grootste van de twee is de tenantsleutel die moet worden gebruikt met MPI. Voorbeeld: als de volgende p-sleutels zijn, moet 0x800b worden gebruikt met MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Gebruik de partitie anders dan de standaardpartitiesleutel (0x7fff). UCX vereist dat de MSB van p-key wordt geweerd. Stel bijvoorbeeld UCX_IB_PKEY in als 0x000b voor 0x800b.

Houd er ook rekening mee dat zolang de tenant (beschikbaarheidsset of virtuele-machineschaalset) bestaat, de PKEY's hetzelfde blijven. Dit geldt ook wanneer knooppunten worden toegevoegd/verwijderd. Nieuwe tenants krijgen verschillende PKEY's.


## <a name="set-up-user-limits-for-mpi"></a>Gebruikerslimieten instellen voor MPI

Gebruikerslimieten instellen voor MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>SSH-sleutels instellen voor MPI

Stel SSH-sleutels in voor MPI-typen waarvoor dit nodig is.

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

De bovenstaande syntaxis gaat uit van een gedeelde basismap, anders moet de .ssh-map naar elk knooppunt worden gekopieerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [voor InfiniBand ingeschakelde](../../sizes-hpc.md#rdma-capable-instances) [VM's uit de H-serie](../../sizes-hpc.md) [en N-serie](../../sizes-gpu.md)
- Bekijk het [overzicht van de HBv3-serie](hbv3-series-overview.md) en [overzicht van de HC-serie.](hc-series-overview.md)
- Lees meer over de meest recente aankondigingen, HPC-workloadvoorbeelden en prestatieresultaten op de [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
