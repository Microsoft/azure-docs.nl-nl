---
title: Bedrijfs continuïteit en herstel na nood gevallen (BCDR) configureren op Azure Stack Edge VPN (virtueel particulier netwerk)
description: Hierin wordt beschreven hoe u BCDR configureert op Azure Stack Edge VPN.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367687"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Bedrijfs continuïteit en herstel na nood gevallen voor Azure Stack Edge VPN configureren

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u bedrijfs continuïteit en herstel na nood gevallen (BCDR) configureert op een virtueel particulier netwerk (VPN) dat is geconfigureerd op een Azure Stack edge-apparaat.

Dit artikel is van toepassing op zowel Azure Stack Edge Pro R als Azure Stack Edge mini-R-apparaat.

## <a name="configure-failover-to-a-paired-region"></a>Failover naar een gekoppelde regio configureren

Uw Azure Stack edge-apparaat maakt gebruik van andere Azure-Services, bijvoorbeeld Azure Storage. U kunt BCDR configureren voor een specifieke Azure-service die wordt gebruikt door het Azure Stack edge-apparaat. Als een Azure-service die wordt gebruikt door Azure Stack Edge een failover naar de gekoppelde regio, wordt het Azure Stack edge-apparaat nu verbonden met de nieuwe IP-adressen en wordt de communicatie niet dubbel versleuteld. 

Het Azure Stack edge-apparaat gebruikt gesplitste tunneling en alle gegevens en services die zijn geconfigureerd in de regio thuis (de regio die is gekoppeld aan uw Azure Stack edge-apparaat), gaan via de VPN-tunnel. Als er een failover wordt uitgevoerd voor de Azure-Services naar een gepaard gebied dat zich buiten de regio Home bevindt, worden de gegevens niet langer via VPN en daarom niet versleuteld. 

In dit scenario worden meestal slechts een aantal Azure-Services beïnvloed. Om dit probleem op te lossen, moeten de volgende wijzigingen worden aangebracht in de VPN-configuratie Azure Stack Edge:

1. Voeg de IP-adres bereik (en) van de failover-Azure-service toe in de inclusieve routes voor VPN op Azure Stack Edge. De services worden vervolgens via de VPN-verbinding gestart.

    Als u de inclusieve routes wilt toevoegen, moet u het JSON-bestand met de servicespecifieke routes downloaden. Zorg ervoor dat u dit bestand bijwerkt met de nieuwe routes.
2. Voeg het bijbehorende IP-bereik (en) van de Azure-service toe aan de Azure-route tabel.
3. Voeg de routes toe aan de firewall.

> [!NOTE]
>
> 1. De failover van een Azure VPN-gateway en Azure Virtual Network (VNET) wordt beschreven in de sectie [herstellen vanuit een Azure-regio die is mislukt vanwege een nood geval](#recover-from-a-failed-azure-region).
> 2. De IP-bereiken die in de Azure route tabel worden toegevoegd, kunnen de limiet van 400 overschrijden. Als dit het geval is, moet u de instructies in het gedeelte volgen, [van de ene Azure-regio naar een andere Azure-regio gaan](#move-from-an-azure-region-to-another).

## <a name="recover-from-a-failed-azure-region"></a>Herstellen van een mislukte Azure-regio

In het geval dat de hele Azure-regio een failover veroorzaakt door een onherstelbare gebeurtenis, zoals aard beving, zullen alle Azure-Services in die regio, met inbegrip van de Azure Stack Edge-service, een failover uitvoeren. Omdat er meerdere services zijn, kunnen de inclusieve routes gemakkelijk in een paar honderden liggen. Azure heeft een limiet van 400 routes. 

Als er een failover wordt uitgevoerd voor de regio, wordt er door het virtuele netwerk (Vnet) ook een failover naar de nieuwe regio uitgevoerd. Dit betekent dat de virtuele netwerk gateway (VPN-gateway). U kunt deze wijziging oplossen door de volgende wijzigingen aan te brengen in de VPN-configuratie Azure Stack Edge:

1. Verplaats uw Vnet naar de doel regio. Zie voor meer informatie: [een virtueel Azure-netwerk verplaatsen naar een andere regio via de Azure Portal](../virtual-network/move-across-regions-vnet-portal.md).
2. Implementeer een nieuwe Azure VPN-gateway in de doel regio waar u het Vnet hebt verplaatst. Zie [een virtuele netwerk gateway maken](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)voor meer informatie.
3. Werk Azure Stack Edge VPN-configuratie om de bovenstaande VPN-gateway in de VPN-verbinding te gebruiken en selecteer vervolgens de doel regio om routes toe te voegen die gebruikmaken van de VPN-gateway.
4. Werk de binnenkomende Azure-route tabel bij als de client adres groep ook wordt gewijzigd. 

## <a name="move-from-an-azure-region-to-another"></a>Van een Azure-regio naar een andere verplaatsen

U kunt uw Azure Stack edge-apparaat verplaatsen van de ene locatie naar een andere locatie. Als u een regio wilt gebruiken die het dichtst bij de implementatie van uw apparaat ligt, moet u het apparaat configureren voor een nieuwe regio. Breng de volgende wijzigingen aan:

1. U kunt Azure Stack Edge VPN-configuratie bijwerken om de VPN-gateway van een nieuwe regio te gebruiken en de nieuwe regio selecteren om routes toe te voegen die gebruikmaken van de VPN-gateway.

## <a name="next-steps"></a>Volgende stappen

[Maak een back-up van uw Azure stack edge-apparaat](azure-stack-edge-gpu-prepare-device-failure.md).