---
title: DHCPv6 configureren voor Linux-Vm's
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over het configureren van DHCPv6 voor Linux-Vm's.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure load balancer, dual stack, openbaar IP, systeem eigen IPv6, mobiel, IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006743"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>DHCPv6 configureren voor Linux-Vm's


Voor sommige virtuele Linux-machine-installatie kopieën op Azure Marketplace zijn Dynamic Host Configuration Protocol versie 6 (DHCPv6) niet standaard geconfigureerd. Voor de ondersteuning van IPv6 moet DHCPv6 worden geconfigureerd in de Linux-besturingssysteem distributie die u gebruikt. De diverse Linux-distributies configureren DHCPv6 op verschillende manieren omdat ze verschillende pakketten gebruiken.

> [!NOTE]
> Recente SUSE Linux-en CoreOS-installatie kopieën in azure Marketplace zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze installatie kopieën gebruikt.

In dit document wordt beschreven hoe u DHCPv6 inschakelt, zodat uw virtuele Linux-machine een IPv6-adres krijgt.

> [!WARNING]
> Als u de netwerk configuratie bestanden onjuist bewerkt, kunt u de netwerk toegang tot uw VM verliezen. U wordt aangeraden de configuratie wijzigingen op niet-productie systemen te testen. De instructies in dit artikel zijn getest op de nieuwste versies van de Linux-installatie kopieën in de Azure Marketplace. Raadpleeg de documentatie voor uw eigen versie van Linux voor meer gedetailleerde instructies.

## <a name="ubuntu"></a>Ubuntu

1. Bewerk het */etc/DHCP/dhclient6.conf* -bestand en voeg de volgende regel toe:

    ```config
    timeout 10;
    ```

2. Bewerk de netwerk configuratie voor de eth0-interface met de volgende configuratie:

   * Bewerk het */etc/network/interfaces.d/ETH0.cfg* -bestand op **Ubuntu 12,04 en 14,04**. 
   * Bewerk het */etc/network/interfaces.d/50-Cloud-init.cfg* -bestand op **Ubuntu 16,04**.

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

Vanaf Ubuntu 17,10 is de standaard methode voor netwerk configuratie [netplan]( https://netplan.io).  Bij installatie/activerings tijd leest netplan netwerk configuratie van YAML-configuratie bestanden op deze locatie:/{lib, etc, run}/netplan/*. yaml.

Neem een *dhcp6: True* -instructie op voor elke Ethernet-interface in uw configuratie.  Bijvoorbeeld:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

Tijdens het vroegtijdig opstarten schrijft het netplan ' netwerk renderer ' de configuratie naar/run van het beheer van apparaten naar de opgegeven netwerk-daemon voor naslag informatie over netplan https://netplan.io/reference . Zie.
 
## <a name="debian"></a>Debian

1. Bewerk het */etc/DHCP/dhclient6.conf* -bestand en voeg de volgende regel toe:

    ```config
    timeout 10;
    ```

2. Bewerk het */etc/network/interfaces* -bestand en voeg de volgende configuratie toe:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS en Oracle Linux

1. Bewerk het */etc/sysconfig/network* -bestand en voeg de volgende para meter toe:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Bewerk het */etc/sysconfig/network-scripts/ifcfg-eth0* -bestand en voeg de volgende twee para meters toe:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 en openSUSE 13

Recente SUSE Linux Enterprise Server-(SLES) en openSUSE-installatie kopieën in azure zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze installatie kopieën gebruikt. Als u een virtuele machine op basis van een oudere of aangepaste SUSE-installatie kopie hebt, doet u het volgende:

1. Installeer het `dhcp-client` pakket, indien nodig:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Bewerk het */etc/sysconfig/network/ifcfg-eth0* -bestand en voeg de volgende para meter toe:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 en openSUSE Schrikkel

Recente SLES-en openSUSE-installatie kopieën in azure zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze installatie kopieën gebruikt. Als u een virtuele machine op basis van een oudere of aangepaste SUSE-installatie kopie hebt, doet u het volgende:

1. Bewerk het */etc/sysconfig/network/ifcfg-eth0* -bestand en vervang de `#BOOTPROTO='dhcp4'` para meter door de volgende waarde:

    ```config
    BOOTPROTO='dhcp'
    ```

2. Voeg aan het */etc/sysconfig/network/ifcfg-eth0* -bestand de volgende para meter toe:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Recente CoreOS-installatie kopieën in azure zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze installatie kopieën gebruikt. Als u een virtuele machine op basis van een oudere of aangepaste CoreOS-installatie kopie hebt, doet u het volgende:

1. Bewerk het bestand */etc/systemd/network/10_dhcp. Network* :

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Het IPv6-adres vernieuwen:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
