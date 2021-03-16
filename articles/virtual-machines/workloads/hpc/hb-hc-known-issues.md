---
title: Problemen met bekende problemen met HPC-en GPU-Vm's oplossen-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het oplossen van bekende problemen met HPC-en GPU-VM-grootten in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0a0eaa18f5b120fcc9cbf0e4da470ee46772c925
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470401"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Bekende problemen met VM's uit de H-serie en N-serie

In dit artikel vindt u de meest voorkomende problemen en oplossingen bij het gebruik van de [H-serie](../../sizes-hpc.md) en HPC-en GPU-vm's van de [N-serie](../../sizes-gpu.md) .

## <a name="known-issues-on-hbv3"></a>Bekende problemen met HBv3
- InfiniBand wordt momenteel alleen ondersteund op de 120-core VM (Standard_HB120rs_v3). Ondersteuning op andere virtuele machines wordt binnenkort ingeschakeld.
- Versnelde netwerken van Azure worden niet ondersteund in de HBv3-serie in alle regio's. Deze functie wordt binnenkort ingeschakeld.

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Versneld netwerken op HB, HC, HBv2 en NDv2

[Versnelde netwerken van Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is nu beschikbaar op de [HB](../../hb-series.md)RDMA en Infiniband CAPABLE en SR-IOV ingeschakelde VM-grootten, [HC](../../hc-series.md), [HBv2](../../hbv2-series.md)en [NDv2](../../ndv2-series.md). Deze mogelijkheid is nu verbeterd gedurende (Maxi maal 30 Gbps) en wacht tijden via het Azure Ethernet-netwerk. Hoewel dit gescheiden is van de RDMA-mogelijkheden via het InfiniBand-netwerk, kunnen sommige platform wijzigingen voor deze mogelijkheid invloed hebben op het gedrag van bepaalde MPI-implementaties bij het uitvoeren van taken via InfiniBand. De InfiniBand-interface op sommige Vm's heeft mogelijk een iets andere naam (mlx5_1 in plaats van eerdere mlx5_0), en dit kan het aanpassen van de MPI-opdracht regels zijn, met name bij het gebruik van de UCX-interface (meestal met OpenMPI en HPC-X).
Meer informatie hierover vindt u in dit [blog artikel](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) met instructies over het oplossen van eventuele waargenomen problemen.

## <a name="infiniband-driver-installation-on-n-series-vms"></a>Installatie van InfiniBand-stuur programma op Vm's uit de N-serie

NC24r_v3 en ND40r_v2 zijn SR-IOV ingeschakeld terwijl er geen SR-IOV is ingeschakeld voor NC24r en NC24r_v2. [Hier vindt](../../sizes-hpc.md#rdma-capable-instances)u enkele details over de bifurcation.
InfiniBand (IB) kan worden geconfigureerd op de VM-grootten met SR-IOV ingeschakeld met de OFED-Stuur Programma's terwijl voor de VM-grootten van niet-SR-IOV ND-Stuur Programma's zijn vereist. Deze IB-ondersteuning is op de juiste wijze beschikbaar op [CentOS-HPC VMIs](configure.md). Voor Ubuntu raadpleegt u de [volgende instructie](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) voor het installeren van de OFED-en ND-Stuur Programma's, zoals beschreven in de [docs](enable-infiniband.md#vm-images-with-infiniband-drivers).

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Dubbele MAC met Cloud-init met Ubuntu op virtuele machines uit de H-serie en N-serie

Er is een bekend probleem met Cloud-init op Ubuntu VM-installatie kopieën voor het openen van de IB-interface. Dit kan gebeuren bij het opnieuw opstarten van de VM of bij het maken van een VM-installatie kopie na generalisatie. De opstart logboeken van de VM kunnen er als volgt uitzien: "netwerk service starten... RuntimeError: dubbele Mac gevonden. zowel ' eth1 ' als ' ib0 ' hebben Mac '.

Deze dubbele MAC met Cloud-init op Ubuntu is een bekend probleem. De tijdelijke oplossing is:
1) De VM-installatie kopie (Ubuntu 18,04) Marketplace implementeren
2) Installeer de benodigde software pakketten om de IB-functie in te scha kelen ([hier](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Bewerk waagent. conf om EnableRDMA te wijzigen = y
4) Netwerken uitschakelen in Cloud-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Het door Cloud-init gegenereerde netwerk configuratie bestand van het netplan bewerken om de MAC te verwijderen
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series"></a>DRAM on HB-serie

Vm's uit de HB-serie kunnen op dit moment slechts 228 GB RAM-geheugen beschikbaar maken voor gast-vm's. Op dezelfde manier 458 GB op HBv2 en 448 GB op HBv3 Vm's. Dit wordt veroorzaakt door een bekende beperking van Azure Hyper Visor om te voor komen dat pagina's worden toegewezen aan de lokale DRAM van AMD CCX (NUMA-domeinen) die zijn gereserveerd voor de gast-VM.

## <a name="qp0-access-restriction"></a>qp0-toegangs beperking

Om te voor komen dat hardware-toegang op laag niveau kan leiden tot beveiligings problemen, is de wachtrij koppeling 0 niet toegankelijk voor gast-Vm's. Dit geldt alleen voor acties die doorgaans zijn gekoppeld aan het beheer van de verbinding met de Connectx-5-NIC, en voor het uitvoeren van een paar InfiniBand Diagnostics zoals ibdiagnet, maar niet voor eindgebruikers toepassingen zelf.

## <a name="gss-proxy"></a>GSS-proxy

GSS-proxy heeft een bekende fout in CentOS/RHEL 7,5 die kan worden opgedeeld als aanzienlijke prestaties en reactie snelheid bij NFS. Dit kan worden verholpen:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cache reiniging

Op HPC-systemen is het vaak handig het geheugen op te schonen nadat een taak is voltooid voordat aan de volgende gebruiker hetzelfde knoop punt is toegewezen. Nadat u toepassingen in Linux hebt uitgevoerd, is het mogelijk dat uw beschik bare geheugen vermindert terwijl het buffer geheugen toeneemt, ondanks dat er geen toepassingen worden uitgevoerd.

![Scherm opname van opdracht prompt voordat wordt gereinigd](./media/known-issues/cache-cleaning-1.png)

Met `numactl -H` worden de NUMAnode (s) weer gegeven waarin het geheugen wordt gebufferd (mogelijk alle). In Linux kunnen gebruikers de caches op drie manieren opschonen om gebufferde of in de cache geplaatste geheugen te retour neren naar Free. U moet hoofd zijn of over sudo-machtigingen beschikken.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Scherm opname van opdracht prompt na reiniging](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-waarschuwingen

U kunt de volgende kernel-waarschuwings berichten negeren tijdens het opstarten van een virtuele machine in de HB-serie onder Linux. Dit wordt veroorzaakt door een bekende beperking van de Azure-Hyper Visor die na verloop van tijd wordt opgelost.

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
- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) in azure](/azure/architecture/topics/high-performance-computing/)voor een architectuur weergave op een hoger niveau voor het uitvoeren van HPC-workloads.
