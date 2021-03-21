---
title: Azure Firewall ondersteuning voor externe werk
description: In dit artikel wordt beschreven hoe Azure Firewall uw vereisten voor externe werk kracht kan ondersteunen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658312"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall ondersteuning voor externe werk

Azure Firewall is een beheerde, Cloud service voor netwerk beveiliging die de resources van uw Azure-netwerk beveiligt. Het is een volledige stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Ondersteuning voor VDI (Virtual Desktop Infrastructure)-implementatie

Voor werk van het beleid voor thuis gebruik moeten veel IT-organisaties essentiële wijzigingen in capaciteit, netwerk, beveiliging en governance aanpakken. Werk nemers worden niet beveiligd door het gelaagde beveiligings beleid dat is gekoppeld aan on-premises Services terwijl u vanuit huis werkt. Met VDI-implementaties (Virtual Desktop Infrastructure) op Azure kunnen organisaties snel reageren op deze veranderende omgeving. U hebt echter een manier nodig om inkomende/uitgaande internet toegang tot en van deze VDI-implementaties te beveiligen. U kunt Azure Firewall [DNAT regels](rule-processing.md) samen met de op [bedreigingen](threat-intel.md) gebaseerde filter functies gebruiken om uw VDI-implementaties te beveiligen.

## <a name="azure-windows-virtual-desktop-support"></a>Ondersteuning voor virtueel bureau blad van Azure

Virtueel bureau blad van Windows is een allesomvattende Desktop-en app Virtualization-service die wordt uitgevoerd in Azure. Het is de enige virtuele desktop infrastructuur (VDI) die vereenvoudigd beheer, meerdere sessies Windows 10, optimalisaties voor Microsoft 365 apps voor bedrijven en ondersteuning voor Extern bureaublad-services (RDS)-omgevingen levert. U kunt binnen enkele minuten uw Windows-Bureau bladen en-apps in azure implementeren en schalen en ingebouwde beveiligings-en nalevings functies aanschaffen. Voor het virtuele bureau blad van Windows hoeft u geen inkomende toegang te openen voor uw virtuele netwerk. U moet echter een reeks uitgaande netwerk verbindingen toestaan voor de virtuele Windows-bureau blad-machines die worden uitgevoerd in uw virtuele netwerk. Zie [Azure Firewall gebruiken om Windows Virtual Desktop-implementaties te beveiligen](protect-windows-virtual-desktop.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [virtueel bureau blad van Windows](../virtual-desktop/index.yml).