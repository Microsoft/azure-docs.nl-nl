---
title: Azure Bastion | Microsoft Docs
description: Meer informatie over Azure Bastion, een veilige en naadloze RDP-/SSH-verbinding met uw virtuele machines zonder dat RDP-/SSH-poorten extern beschikbaar worden.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 18672d76c262f8919489ccf132532c117666abe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492175"
---
# <a name="what-is-azure-bastion"></a>Wat is Azure Bastion?

Azure Bastion is een service die u kunt implementeren om verbinding te maken met een virtuele machine met behulp van uw browser en de Azure-portal. De Azure Bastion-service is een volledig door het platform beheerde PaaS-service die u in uw virtuele netwerk inricht. De service biedt beveiligde en naadloze RDP-/SSH-connectiviteit voor uw virtuele machine, rechtstreeks vanuit de Azure-portal via TLS. Wanneer u verbinding maakt met Azure Bastion, hebben uw virtuele machines geen openbaar IP, agent of speciale clientsoftware nodig.

Bastion biedt veilige RDP- en SSH-connectiviteit voor alle virtuele machines in het virtuele netwerk waarin de service is ingericht. Azure Bastion beschermt uw virtuele machines tegen blootstelling van RDP-/SSH-poorten aan de buitenwereld, terwijl u toch beveiligde toegang krijgt geboden met behulp van RDP/SSH.

## <a name="architecture"></a>Architectuur

De Azure Bastion-implementatie vindt plaats per virtueel netwerk, niet per abonnement/account of virtuele machine. Zodra u een Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de RDP-/SSH-ervaring beschikbaar voor al uw virtuele machines in hetzelfde virtuele netwerk.

RDP en SSH zijn twee van de fundamentele middelen waarmee u verbinding kunt maken met uw werkbelastingen die worden uitgevoerd in Azure. Het beschikbaar maken van RDP-/SSH-poorten via internet is niet gewenst en wordt gezien als een aanzienlijke bedreiging. Dit komt vaak door kwetsbaarheden in protocollen. Om deze dreiging te beheersen, kunt u Bastion-hosts (ook wel jump-servers genoemd) aan de openbare kant van uw perimeternetwerk implementeren. Bastion-hostservers zijn ontworpen en geconfigureerd om aanvallen te weerstaan. Bastion-servers bieden ook RDP- en SSH-connectiviteit voor de werkbelastingen achter Bastion en dieper in het netwerk.

![Azure Bastion-architectuur](./media/bastion-overview/architecture.png)

In deze afbeelding ziet u de architectuur van een Azure Bastion-implementatie. In dit diagram:

* De bastion-host wordt geïmplementeerd in het virtuele netwerk dat het AzureBastionSubnet-subnet bevat met een minimum/27-voor voegsel.
* Maakt de gebruiker verbinding met Azure Portal met behulp van een HTML5-browser.
* Selecteert de gebruiker de virtuele machine waarmee verbinding moet worden gemaakt.
* Wordt de RDP-/SSH-sessie met één klik geopend in de browser.
* Is er geen openbaar IP-adres vereist voor de virtuele Azure-machine.

## <a name="key-features"></a>Belangrijke functies

De volgende functies zijn beschikbaar:

* **Rechtstreeks RDP en SSH in Azure Portal:** U kunt in Azure Portal rechtstreeks naar de RDP- en SSH-sessie gaan met behulp van een naadloze ervaring met één muisklik.
* **Externe sessie via TLS en firewall traversal voor RDP/SSH:** Voor Azure Bastion wordt een op HTML5 gebaseerde webclient gebruikt die automatisch wordt gestreamd naar uw lokale apparaat, zodat u uw RDP-/SSH-sessie via TLS op poort 443 ontvangt en veilig bedrijfsfirewalls kunt doorlopen.
* **Geen openbaar IP-adres vereist voor de virtuele Azure-machine:** Azure Bastion opent de RDP-/SSH-verbinding met uw virtuele Azure-machine met behulp van een privé IP-adres op uw virtuele machine. U hebt geen openbaar IP-adres nodig op uw virtuele machine.
* **Geen gedoe met het beheren van NSG's:** Azure Bastion is een volledig beheerde PaaS-service voor platformen van Azure die intern is beveiligd om u te voorzien van beveiligde RDP-/SSH-connectiviteit. U hoeft geen NSG's toe te passen op het Azure Bastion-subnet. Omdat Azure Bastion verbinding maakt met uw virtuele machines via een particulier IP-adres, kunt u uw NSG's zo configureren dat RDP/SSH alleen van Azure Bastion wordt toegestaan. Hiermee kunt u eenvoudig NSG’s beheren op de momenten waarop u een veilige verbinding met uw virtuele machines moet maken.
* **Bescherming tegen het scannen van poorten:** Omdat u uw virtuele machines niet aan het openbare internet hoeft bloot te stellen, worden uw virtuele machines beschermd tegen poortscans door malafide en kwaadwillende gebruikers buiten uw virtuele netwerk.
* **Bescherming tegen zero day-aanvallen. Beveiliging op slechts één plek:** Azure Bastion is een volledig door een platform beheerde PaaS-service. Omdat deze zich in de omgeving van uw virtuele netwerk bevindt, hoeft u zich geen zorgen te maken over de beveiliging van elke virtuele machine in uw virtuele netwerk. Het Azure-platform beschermt tegen zero day-aanvallen door de Azure Bastion te beveiligen en altijd up-to-date te houden.

## <a name="whats-new"></a><a name="new"></a>Wat is er nieuw?

Abonneer u op de RSS-feed en bekijk de nieuwste updates voor Azure Bastion-onderdelen op de pagina [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion).

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Een Azure Bastion-host maken en verbinding maken met een Windows-VM](tutorial-create-host-portal.md).
* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
