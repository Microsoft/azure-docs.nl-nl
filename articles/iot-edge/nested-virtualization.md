---
title: Geneste virtualisatie voor Azure IoT Edge voor Linux op Windows | Microsoft Docs
description: Meer informatie over het navigeren in geneste virtualisatie in Azure IoT Edge voor Linux in Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609271"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Geneste virtualisatie voor Azure IoT Edge voor Linux in Windows
Er zijn twee soorten geneste virtualisatie die compatibel is met Azure IoT Edge voor Linux in Windows. Gebruikers kunnen kiezen om te implementeren via een lokale virtuele machine of virtuele Azure-machine. In dit artikel vindt u meer informatie over de optie die het meest geschikt is voor hun scenario en om inzicht te krijgen in de configuratie vereisten.

> [!NOTE]
>
> Zorg ervoor dat u één [netowrking-optie](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) inschakelt voor geneste virtualisatie. Als u dit niet doet, worden er EFLOW-installatie fouten geretourneerd. 

## <a name="deployment-on-local-vm"></a>Implementatie op de lokale virtuele machine
Dit is de basislijn benadering voor een Windows-VM die Azure IoT Edge voor Linux op Windows host. Voor deze aanvraag moet geneste virtualisatie worden ingeschakeld voordat de implementatie wordt gestart. Lees [Hyper-V uitvoeren in een virtuele machine met geneste virtualisatie](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) voor meer informatie over het configureren van dit scenario.

Als u Windows Server gebruikt, moet u ervoor zorgen dat u [de Hyper-V-functie installeert](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Implementatie op virtuele machines in azure
Als u ervoor kiest om te implementeren op virtuele machines in azure, moet u er rekening mee houden dat er geen externe switch is. Azure IoT Edge voor Linux in Windows is ook niet compatibel met een Azure-VM waarop de server-SKU wordt uitgevoerd, tenzij er een specifiek script wordt uitgevoerd dat een standaard Switch oplevert. Zie de [sectie Windows Server](#windows-server) hieronder voor meer informatie over het weer geven van een standaard Switch. 

> [!NOTE]
>
> Virtuele Azure-machines die EFLOW moeten hosten, moeten een VM zijn die [ondersteuning biedt voor geneste virtualisatie](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Beperkt gebruik van een externe switch
In elk scenario waarbij de virtuele machine geen IP-adres kan verkrijgen via een externe switch, gebruikt de implementatie functionaliteit automatisch de interne standaard switch voor de implementatie. Dit betekent dat het beheer van de virtuele machine van de Azure IoT Edge voor Linux alleen kan worden uitgevoerd op het doel apparaat zelf (dat wil zeggen dat er verbinding wordt gemaakt met de Azure IoT Edge voor Linux VM via de WAC Power shell-uitbrei ding is alleen mogelijk op localhost).

## <a name="windows-server"></a>Windows Server
Voor gebruikers van Windows Server houdt u er rekening mee dat Azure IoT Edge voor Linux in Windows niet automatisch de standaard switch ondersteunt.

* Gevolgen voor de lokale VM: Controleer of de EFLOW-VM een IP-adres kan verkrijgen via de externe switch.

* Gevolgen voor Azure VM: omdat er geen externe switch op virtuele machines in Azure is, is het niet mogelijk EFLOW te implementeren voordat u een interne switch op de server instelt.

Er is standaard geen standaard switch op server-Sku's (ongeacht of de server-SKU wordt uitgevoerd op een virtuele machine van Azure of niet). Wanneer u implementeert op een virtuele machine van Azure, waarbij de externe switch niet kan worden gebruikt, moet de standaard Switch hand matig worden ingesteld en geconfigureerd voordat Azure IoT Edge voor Linux op de Windows-implementatie wordt uitgevoerd. Onze implementatie functionaliteit heeft betrekking op dit omdat IP-configuratie voor de interne switch, een NAT-configuratie en het installeren en configureren van een DHCP-server vereist is. Onze implementatie functionaliteit in open bare preview geeft aan dat deze niet in staat is om de netwerk configuraties voor productieve implementaties te fiddle.

* Relevante informatie over hoe u de standaard Switch hand matig kunt instellen, vindt u hier: [geneste virtualisatie inschakelen in Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Documentatie over het instellen van een DHCP-server voor dit scenario vindt u hier: [DHCP implementeren met Windows Power shell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
