---
title: Kan geen extern bureau blad naar Azure Virtual Machines vanwege een vast IP-adres | Microsoft Docs
description: Meer informatie over het oplossen van problemen met RDP-problemen dat wordt veroorzaakt door een statisch IP-adres in Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: bf19a6f77a87f2424f9e7b889e48119d57d1e2e5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820979"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Kan geen extern bureau blad naar Azure Virtual Machines vanwege een vast IP-adres

In dit artikel wordt een probleem beschreven waarbij u geen extern bureau blad naar Azure kunt Windows Virtual Machines (Vm's) nadat een statisch IP-adres is geconfigureerd in de VM.


## <a name="symptoms"></a>Symptomen

Wanneer u een RDP-verbinding maakt met een virtuele machine in azure, wordt het volgende fout bericht weer gegeven:

**Extern bureaublad kunt geen verbinding met de externe computer maken om een van de volgende redenen:**

1. **Externe toegang tot de server is niet ingeschakeld**

2. **De externe computer is uitgeschakeld**

3. **De externe computer is niet beschikbaar op het netwerk**

**Zorg ervoor dat de externe computer is ingeschakeld en is verbonden met het netwerk en dat externe toegang is ingeschakeld.**

Wanneer u de scherm opname van de [Diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) in de Azure Portal controleert, ziet u dat de VM normaal wordt opgestart en wacht op referenties in het aanmeldings scherm.

## <a name="cause"></a>Oorzaak

De virtuele machine heeft een statisch IP-adres dat is gedefinieerd voor de netwerk interface in Windows. Dit IP-adres wijkt af van het adres dat is gedefinieerd in de Azure Portal.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, moet u een moment opname maken van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

U kunt dit probleem oplossen door met behulp van seriële controle DHCP in te scha kelen of de [netwerk interface](reset-network-interface.md) voor de virtuele machine opnieuw in te stellen.

### <a name="use-serial-control"></a>Serieel besturings element gebruiken

1. Verbinding maken met de [seriële console en het Open cmd-exemplaar](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële console niet op uw virtuele machine is ingeschakeld, raadpleegt u de [netwerk interface opnieuw instellen](reset-network-interface.md).
2. Controleer of DHCP is uitgeschakeld op de netwerk interface:

    ```console
    netsh interface ip show config
    ```

3. Als de DHCP is uitgeschakeld, keert u de configuratie van de netwerk interface terug voor gebruik van DHCP:

    ```console
    netsh interface ip set address name="<NIC Name>" source=dhcp
    ```

    Als de interwerk-interface bijvoorbeeld ' Ethernet 2 ' is, voert u de volgende opdracht uit:

    ```console
    netsh interface ip set address name="Ethernet 2" source=dhcp
    ```

4. Voer een query uit op de IP-configuratie om ervoor te zorgen dat de netwerk interface nu correct is ingesteld. Het nieuwe IP-adres moet overeenkomen met de naam die wordt opgegeven door Azure.

    ```console
    netsh interface ip show config
    ```

    U hoeft de virtuele machine op dit moment niet opnieuw op te starten. De virtuele machine is terug bereikbaar.

Als u hierna het statische IP-adres voor de virtuele machine wilt configureren, raadpleegt u [statische IP-adressen configureren voor een virtuele machine](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).
