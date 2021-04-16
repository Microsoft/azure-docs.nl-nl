---
title: Geneste virtualisatie voor Azure IoT Edge linux in Windows | Microsoft Docs
description: Meer informatie over het navigeren in geneste virtualisatie in Azure IoT Edge voor Linux in Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4f01362fd9342c1f508f165b34e121a11e8d07e2
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496585"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Geneste virtualisatie voor Azure IoT Edge linux in Windows
Er zijn twee vormen van geneste virtualisatie die compatibel zijn met Azure IoT Edge voor Linux in Windows. Gebruikers kunnen ervoor kiezen om te implementeren via een lokale VM of Azure-VM. Dit artikel biedt gebruikers duidelijkheid over welke optie het beste is voor hun scenario en biedt inzicht in de configuratievereisten.

> [!NOTE]
>
> Zorg ervoor dat u één [netwerkoptie inschakelen](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) voor geneste virtualisatie. Als u dit niet doet, leidt dit tot EFLOW-installatiefouten. 

## <a name="deployment-on-local-vm"></a>Implementatie op lokale VM
Dit is de basislijnbenadering voor elke Windows-VM die als host Azure IoT Edge linux in Windows. In dit geval moet geneste virtualisatie worden ingeschakeld voordat u de implementatie start. Lees [Hyper-V uitvoeren in een virtuele machine met geneste virtualisatie](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) voor meer informatie over het configureren van dit scenario.

Als u Windows Server gebruikt, moet u de [Hyper-V-functie installeren.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)

## <a name="deployment-on-azure-vms"></a>Implementatie op Azure-VM's
Als u ervoor kiest om te implementeren op azure-VM's, is er geen externe switch. Azure IoT Edge voor Linux in Windows is ook niet compatibel op een Azure-VM met de Server-SKU, tenzij er een specifiek script wordt uitgevoerd dat een standaardsyteem oplevert. Zie de sectie Windows Server hieronder voor meer informatie over het instellen van een [standaardschakelaar.](#windows-server) 

> [!NOTE]
>
> Azure-VM's die EFLOW moeten hosten, moeten een virtuele machine zijn die [geneste virtualisatie ondersteunt](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Beperkt gebruik van externe switch
In elk scenario waarin de VM geen IP-adres kan verkrijgen via een externe switch, maakt de implementatiefunctionaliteit automatisch gebruik van de interne standaards switch voor de implementatie. Dit betekent dat het beheer van de Azure IoT Edge voor Linux-VM alleen kan worden uitgevoerd op het doelapparaat zelf (dat wil zeggen dat verbinding maken met de Azure IoT Edge voor Linux-VM via de WAC PowerShell SSH-extensie alleen mogelijk is op localhost).

## <a name="windows-server"></a>Windows Server
Voor Windows Server-gebruikers geldt dat Azure IoT Edge voor Linux in Windows niet automatisch ondersteuning biedt voor de standaardschakelaar.

* Gevolgen voor lokale VM: Zorg ervoor dat de EFLOW-VM een IP-adres kan verkrijgen via de externe switch.

* Gevolgen voor azure-VM: Omdat er geen externe switch op virtuele Azure-VM's is, is het niet mogelijk om EFLOW te implementeren voordat u een interne switch op de server inschakelt.

Er is standaard geen standaardschakelaar voor Server-SKU's (ongeacht of de Server-SKU wordt uitgevoerd op een Azure-VM of niet). Wanneer u implementeert op een Azure-VM waarop de externe switch niet kan worden gebruikt, moet de standaardschakelaar handmatig worden ingesteld en geconfigureerd voordat u Azure IoT Edge voor Linux in Windows-implementatie. Onze implementatiefunctionaliteit dekt dit, omdat hiervoor IP-configuratie voor de interne switch, een NAT-configuratie en het installeren en configureren van een DHCP-server is vereist. Onze implementatiefunctionaliteit in de openbare preview geeft aan dat deze instellingen niet worden gebruikt om netwerkconfiguraties op productieve implementaties niet te verslechteren.

* Relevante informatie over het handmatig instellen van de standaardschakelaar vindt u hier: Geneste [virtualisatie inschakelen in Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Documentatie over het instellen van een DHCP-server voor dit scenario vindt u hier: [DHCP implementeren met behulp van Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
