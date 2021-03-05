---
title: DPDK in een Azure Linux-VM | Microsoft Docs
description: Meer informatie over de voor delen van de data vliegtuig Development Kit (DPDK) en het instellen van de DPDK op een virtuele Linux-machine.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: 3b4d66525ec52ef2382dfbe97bc09278e35b31fb
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124666"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>DPDK instellen op een virtuele Linux-machine

Data Pace Development Kit (DPDK) in Azure biedt een sneller Framework voor pakket verwerking voor de gebruikers ruimte voor prestatie-intensieve toepassingen. Dit framework omzeilt de kernel netwerk stack van de virtuele machine.

In een typische pakket verwerking die gebruikmaakt van de kernel-netwerk stack, wordt het proces onderbroken. Wanneer de netwerk interface binnenkomende pakketten ontvangt, is er een kernel-interrupt voor het verwerken van het pakket en een context schakelaar van de kernel-ruimte naar de gebruikers ruimte. DPDK elimineert de context wisseling en de op interrupts gebaseerde methode voor een implementatie van de gebruikers ruimte die gebruikmaakt van Stuur Programma's voor poll modus voor snelle pakket verwerking.

DPDK bestaat uit sets gebruikers ruimte bibliotheken die toegang bieden tot bronnen op lagere niveaus. Deze resources kunnen bestaan uit hardware, logische kernen, geheugen beheer en stuur Programma's voor poll modus voor netwerk interface kaarten.

DPDK kan worden uitgevoerd op virtuele machines van Azure die ondersteuning bieden voor meerdere distributies van besturings systemen. DPDK biedt de belangrijkste prestatie differentiatie in de implementatie van de functies voor de virtualisatie van een netwerk functie. Deze implementaties kunnen de vorm hebben van virtuele netwerk apparaten (Nva's), zoals virtuele routers, firewalls, Vpn's, load balancers, ontwikkelde pakket kernen en denial-of-service-toepassingen (DDoS).

## <a name="benefit"></a>Voordeel

**Hogere pakketten per seconde (PPS)**: door de kernel over te slaan en pakketten in de gebruikers ruimte te beheren, wordt het aantal cycli verminderd door context switches te elimineren. Ook wordt het aantal pakketten dat per seconde wordt verwerkt in virtuele machines van Azure Linux verbeterd.


## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende distributies van Azure Marketplace worden ondersteund:

| Linux-besturings systeem     | Kernelversie               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-Azure +           | 
| Ubuntu 18.04 | 4.15.0-1014-Azure +           |
| SLES 15 SP1  | 4.12.14-8.19-Azure +          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64 +  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64 +  | 

**Aangepaste kernel-ondersteuning**

Zie [patches voor het bouwen van een door Azure afgestemde Linux-kernel](https://github.com/microsoft/azure-linux-kernel)voor een Linux-kernel-versie die niet wordt vermeld. U kunt ook contact opnemen met voor meer informatie [aznetdpdk@microsoft.com](mailto:aznetdpdk@microsoft.com) . 

## <a name="region-support"></a>Ondersteuning voor regio

Alle Azure-regio's bieden ondersteuning voor DPDK.

## <a name="prerequisites"></a>Vereisten

Versnelde netwerken moeten zijn ingeschakeld op een virtuele Linux-machine. De virtuele machine moet ten minste twee netwerk interfaces hebben, met één interface voor beheer. Het inschakelen van versnelde netwerken op de beheer interface wordt niet aanbevolen. Meer informatie over het [maken van een virtuele Linux-machine met versneld netwerken ingeschakeld](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>DPDK-afhankelijkheden installeren

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL 7.5/CentOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure-kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Standaard kernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>De virtuele-machine omgeving instellen (eenmaal)

1. [Down load de meest recente DPDK](https://core.dpdk.org/download). Versie 18,11 LTS of 19,11 LTS is vereist voor Azure.
2. Bouw de standaard configuratie met `make config T=x86_64-native-linuxapp-gcc` .
3. Schakel de Mellanox PMDs in de gegenereerde configuratie in met `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` .
4. Compileren met `make` .
5. Installeren met `make install DESTDIR=<output folder>` .

## <a name="configure-the-runtime-environment"></a>De runtime-omgeving configureren

Nadat de computer opnieuw is opgestart, voert u de volgende opdrachten eenmaal uit:

1. Hugepages

   * Configureer hugepage door de volgende opdracht uit te voeren voor elk NUMA-knoop punt:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Maak een map om te koppelen aan `mkdir /mnt/huge` .
   * Hugepages koppelen aan `mount -t hugetlbfs nodev /mnt/huge` .
   * Controleer of hugepages zijn gereserveerd voor `grep Huge /proc/meminfo` .

     > ERAAN Er is een manier om het grub-bestand te wijzigen zodat hugepages tijdens het opstarten worden gereserveerd door de [instructies](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) voor de DPDK te volgen. De instructies bevinden zich onder aan de pagina. Wanneer u een virtuele machine van Azure Linux gebruikt, wijzigt u in plaats daarvan bestanden onder **/etc/config/grub.d** om hugepages te reserveren tijdens het opnieuw opstarten.

2. MAC-& IP-adressen: gebruik `ifconfig –a` om het Mac-en IP-adres van de netwerk interfaces weer te geven. De *VF* -netwerk interface en de *NETVSC* -netwerk interface hebben hetzelfde MAC-adres, maar alleen de *NETVSC* -netwerk interface heeft een IP-adres. *VF* -interfaces worden uitgevoerd als onderliggende interfaces van *NETVSC* -interfaces.

3. PCI-adressen

   * Gebruik `ethtool -i <vf interface name>` om te bepalen welk PCI-adres voor *VF* moet worden gebruikt.
   * Als *eth0* versneld netwerken is ingeschakeld, zorgt u ervoor dat testpmd niet per ongeluk het *VF* -PCI-apparaat neemt voor *eth0*. Als de DPDK-toepassing per ongeluk de beheer netwerk interface overneemt en u uw SSH-verbinding verliest, gebruikt u de seriële console om de DPDK-toepassing te stoppen. U kunt de seriële console ook gebruiken om de virtuele machine te stoppen of te starten.

4. Laad *ibuverbs* bij elke herstart met `modprobe -a ib_uverbs` . Voor SLES alleen 15, laadt u ook *mlx4_ib* met `modprobe -a mlx4_ib` .

## <a name="failsafe-pmd"></a>Failsafe PMD

DPDK-toepassingen moeten worden uitgevoerd via de failsafe-PMD die wordt weer gegeven in Azure. Als de toepassing rechtstreeks wordt uitgevoerd via de *VF* -PMD, ontvangt deze niet **alle** pakketten die bestemd zijn voor de virtuele machine, aangezien sommige pakketten worden weer gegeven via de synthetische interface. 

Als u een DPDK-toepassing uitvoert via de failsafe-PMD, zorgt dit ervoor dat de toepassing alle pakketten ontvangt die ernaar zijn bestemd. Het zorgt er ook voor dat de toepassing wordt uitgevoerd in de DPDK-modus, zelfs als het VF wordt ingetrokken wanneer de host wordt verwerkt. Zie voor meer informatie over failsafe PMD [fail-safe veilige poll modus driver bibliotheek](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Testpmd uitvoeren

Als u testpmd in de hoofd modus wilt uitvoeren, gebruikt u `sudo` vóór de opdracht *testpmd* .

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Sanity-controle, initialisatie van de failsafe-adapter

1. Voer de volgende opdrachten uit om een testpmd-toepassing met één poort te starten:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Voer de volgende opdrachten uit om een testpmd-toepassing met twee poorten te starten:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Als u testpmd met meer dan twee Nic's gebruikt, volgt het `--vdev` argument dit patroon: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` .

3.  Nadat de taak is gestart, voert `show port info all` u uit om de poort gegevens te controleren. U moet een of twee DPDK-poorten zien die net_failsafe (niet *net_mlx4*) zijn.
4.  Gebruiken `start <port> /stop <port>` om verkeer te starten.

De vorige opdrachten starten *testpmd* in de interactieve modus. dit wordt aanbevolen voor het proberen van testpmd-opdrachten.

### <a name="basic-single-sendersingle-receiver"></a>Basic: één afzender/één ontvanger

Met de volgende opdrachten worden periodiek de statistieken van pakketten per seconde afgedrukt:

1. Op de TX-zijde voert u de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Op de RX-zijde voert u de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Wanneer u de vorige opdrachten op een virtuele machine uitvoert, wijzigt u *IP_SRC_ADDR* en *IP_DST_ADDR* in, `app/test-pmd/txonly.c` zodat deze overeenkomen met het werkelijke IP-adres van de virtuele machines voordat u compileert. Anders worden de pakketten verwijderd voordat de ontvanger wordt bereikt.

### <a name="advanced-single-sendersingle-forwarder"></a>Geavanceerd: Eén afzender/één doorstuur server
Met de volgende opdrachten worden periodiek de statistieken van pakketten per seconde afgedrukt:

1. Op de TX-zijde voert u de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Voer op de FWD-zijde de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Wanneer u de vorige opdrachten op een virtuele machine uitvoert, wijzigt u *IP_SRC_ADDR* en *IP_DST_ADDR* in, `app/test-pmd/txonly.c` zodat deze overeenkomen met het werkelijke IP-adres van de virtuele machines voordat u compileert. Anders worden de pakketten verwijderd voordat de doorstuur server wordt bereikt. Het is niet mogelijk om een derde computer doorgestuurd verkeer te ontvangen, omdat de *testpmd* -doorstuur server de Layer 3-adressen niet wijzigt, tenzij u een aantal code wijzigingen aanbrengt.

## <a name="references"></a>Referenties

* [Opties voor EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd-opdrachten](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
