---
title: Problemen met bekende problemen met HPC-en GPU-Vm's oplossen-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het oplossen van bekende problemen met HPC-en GPU-VM-grootten in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f4e93deb40799cbcc9c86aff454e250f1ab71712
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963331"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Bekende problemen met VM's uit de H-serie en N-serie

In dit artikel vindt u de meest voorkomende problemen en oplossingen bij het gebruik van de [H-serie](../../sizes-hpc.md) en HPC-en GPU-vm's van de [N-serie](../../sizes-gpu.md) .

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

Vm's uit de HB-serie kunnen op dit moment slechts 228 GB RAM-geheugen beschikbaar maken voor gast-vm's. Dit wordt veroorzaakt door een bekende beperking van Azure Hyper Visor om te voor komen dat pagina's worden toegewezen aan de lokale DRAM van AMD CCX (NUMA-domeinen) die zijn gereserveerd voor de gast-VM.

## <a name="accelerated-networking"></a>Versneld netwerken

Versnelde netwerken van Azure op HPC-en GPU-Vm's met IB zijn op dit moment niet ingeschakeld. Klanten worden op de hoogte gesteld wanneer deze functie wordt ondersteund.

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
- Lees over de laatste aankondigingen en enkele HPC-voorbeelden en -resultaten in de [Azure Compute Tech Community-blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) in azure](/azure/architecture/topics/high-performance-computing/)voor een architectuur weergave op een hoger niveau voor het uitvoeren van HPC-workloads.
