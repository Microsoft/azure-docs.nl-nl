---
title: Bekende problemen met HPC- en GPU-VM's oplossen - Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het oplossen van bekende problemen met HPC- en GPU-VM-grootten in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f5bdae17126048da153f70bf27609bcc4b92fe21
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599584"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Bekende problemen met VM's uit de H-serie en N-serie

In dit artikel wordt geprobeerd recente veelvoorkomende problemen en hun oplossingen weer te geven bij het gebruik van de [HPC-](../../sizes-hpc.md) en GPU-VM's uit de H- en [N-serie.](../../sizes-gpu.md)

## <a name="qp0-access-restriction"></a>qp0-toegangsbeperking

Wachtrijpaar 0 is niet toegankelijk voor gast-VM's om hardwaretoegang op laag niveau te voorkomen die beveiligingsproblemen kan tot gevolg hebben. Dit mag alleen van invloed zijn op acties die doorgaans zijn gekoppeld aan het beheer van de ConnectX InfiniBand-NIC en het uitvoeren van bepaalde InfiniBand-diagnoses, zoals ibdiaagnot, maar niet op toepassingen van eindgebruikers.

## <a name="mofed-installation-on-ubuntu"></a>MOFED-installatie op Ubuntu
Op op de Marketplace gebaseerde VM-installatie van Ubuntu-18.04 met kernelversie en nieuwer zijn sommige oudere Mellanox OFED incompatibel, waardoor de opstarttijd van de VM in sommige gevallen tot `5.4.0-1039-azure #42` 30 minuten toeneemt. Dit is gerapporteerd voor zowel Mellanox OFED versie 5.2-1.0.4.0 als 5.2-2.2.0.0. Het probleem is opgelost met Mellanox OFED 5.3-1.0.0.1.
Als het nodig is om de niet-compatibele OFED te gebruiken, is een oplossing het gebruik van de Marketplace VM-installatiekopie **Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** marketplace of ouder en niet om de kernel bij te werken.

## <a name="mpi-qp-creation-errors"></a>Fouten bij het maken van MPI QP
Als er bij het uitvoeren van MPI-workloads fouten optreden bij het maken van InfiniBand QP, zoals hieronder weergegeven, raden we u aan de VM opnieuw op te starten en de workload opnieuw uit te proberen. Dit probleem wordt in de toekomst opgelost.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

U kunt de waarden van het maximum aantal wachtrijparen controleren wanneer het probleem als volgt wordt waargenomen.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Versneld netwerken op HB, HC, HBv2 en NDv2

[Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is nu beschikbaar op de voor RDMA en InfiniBand geschikte VM-grootten [HB,](../../hb-series.md) [HC,](../../hc-series.md) [HBv2](../../hbv2-series.md)en [NDv2](../../ndv2-series.md)met SR-IOV. Met deze mogelijkheid kunnen nu overal (tot 30 Gbps) en latentie via het Azure Ethernet-netwerk worden verbeterd. Hoewel dit los staat van de RDMA-mogelijkheden via het InfiniBand-netwerk, kunnen sommige platformwijzigingen voor deze mogelijkheid invloed hebben op het gedrag van bepaalde MPI-implementaties bij het uitvoeren van taken via InfiniBand. Met name de InfiniBand-interface op sommige VM's kan een iets andere naam hebben (mlx5_1 in tegenstelling tot eerdere mlx5_0). Hiervoor moet u mogelijk de MPI-opdrachtregels aanpassen, met name wanneer u de UCX-interface gebruikt (meestal met OpenMPI en HPC-X). De eenvoudigste oplossing op dit moment kan zijn om de nieuwste HPC-X te gebruiken op de CentOS-HPC VM-afbeeldingen of versneld netwerken uit te schakelen als dat niet nodig is.
Meer informatie over dit artikel is beschikbaar in dit [TechCommunity-artikel](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) met instructies voor het oplossen van eventuele waargenomen problemen.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Installatie van InfiniBand-stuurprogramma op niet-SR-IOV-VM's

Momenteel zijn H16r, H16mr en NC24r niet ingeschakeld voor SR-IOV. Hier staan enkele details over de infiniBand-stack-bilocatie. [](../../sizes-hpc.md#rdma-capable-instances)
InfiniBand kan worden geconfigureerd op de VM-grootten waarvoor SR-IOV is ingeschakeld met de OFED-stuurprogramma's, terwijl voor VM-grootten zonder SR-IOV ND-stuurprogramma's zijn vereist. Deze IB-ondersteuning is op de juiste wijze [beschikbaar voor CentOS, RHEL en Ubuntu.](configure.md)

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>MAC dupliceren met cloud-init met Ubuntu op VM's uit de H-serie en N-serie

Er is een bekend probleem met cloud-init op Ubuntu VM-installatie afbeeldingen terwijl wordt geprobeerd de IB-interface te gebruiken. Dit kan gebeuren bij het opnieuw opstarten van de VM of bij het maken van een VM-afbeelding na de generalisatie. In de VM-opstartlogboeken kan een fout als deze worden weergegeven:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Dit 'dubbele MAC met cloud-init op Ubuntu' is een bekend probleem. Dit wordt opgelost in nieuwere kernels. Als het probleem is aangetroffen, is de tijdelijke oplossing:
1) De marketplace-VM-installatie afbeelding (Ubuntu 18.04) implementeren
2) Installeer de benodigde softwarepakketten om IB in te kunnenschakelen ([instructies hier](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Bewerk waagent.conf om EnableRDMA=y te wijzigen
4) Netwerken uitschakelen in cloud-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Bewerk het netwerkconfiguratiebestand van netplan dat is gegenereerd door cloud-init om de MAC te verwijderen
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM op VM's uit de HB-serie

VM's uit de HB-serie kunnen op dit moment slechts 228 GB RAM beschikbaar maken voor gast-VM's. Op dezelfde manier 458 GB op HBv2 en 448 GB op HBv3-VM's. Dit wordt veroorzaakt door een bekende beperking van De Azure-hypervisor om te voorkomen dat pagina's worden toegewezen aan de lokale DRAM van AMD CCX-domeinen (NUMA-domeinen) die zijn gereserveerd voor de gast-VM.

## <a name="gss-proxy"></a>GSS-proxy

De GSS-proxy heeft een bekende fout in CentOS/RHEL 7.5 die kan optreden als een aanzienlijke prestatie- en reactiesnelheidsstraf bij gebruik met NFS. Dit kan worden vereend met:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cache Cleaning

In HPC-systemen is het vaak handig om het geheugen op te schonen nadat een taak is voltooid voordat aan de volgende gebruiker hetzelfde knooppunt wordt toegewezen. Nadat u toepassingen hebt uitgevoerd in Linux, is het mogelijk dat uw beschikbare geheugen afneemt terwijl uw buffergeheugen toeneemt, ondanks dat er geen toepassingen worden uitgevoerd.

![Schermopname van de opdrachtprompt voordat u opschoont](./media/known-issues/cache-cleaning-1.png)

Met `numactl -H` wordt laten zien met welke NUMAnode(s) het geheugen wordt gebufferd (mogelijk alle). In Linux kunnen gebruikers de caches op drie manieren ops schonen om het in de buffer opgeslagen of in de cache opgeslagen geheugen te retourneren naar 'gratis'. U moet de hoofdmap zijn of sudo-machtigingen hebben.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Schermopname van de opdrachtprompt na het opsnafdrukken](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernelwaarschuwingen

U kunt de volgende kernelwaarschuwingsberichten negeren bij het opstarten van een VM uit de HB-serie onder Linux. Dit komt door een bekende beperking van de Azure-hypervisor die na een periode wordt verholpen.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Volgende stappen

- Bekijk [Overzicht HB-serie](hb-series-overview.md) en [Overzicht HC-serie](hc-series-overview.md) voor meer informatie over het optimaal configureren van workloads ten behoeve van de prestaties en schaalbaarheid.
- Lees meer over de meest recente aankondigingen, HPC-workloadvoorbeelden en prestatieresultaten op [de Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie High Performance Computing (HPC) op Azure voor een architectuurweergave op een hoger niveau van het uitvoeren van [HPC-workloads.](/azure/architecture/topics/high-performance-computing/)
