---
title: Virtueel WAN van Azure en werken op afstand
description: Op deze pagina wordt beschreven hoe u Azure Virtual WAN kunt gebruiken om extern te werken vanwege de COVID-19-Pandemic.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023484"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Virtueel WAN van Azure en ondersteunende externe werkzaamheden

>[!NOTE]
>In dit artikel wordt beschreven hoe u Azure Virtual WAN, azure, micro soft Network en het Azure-partner ecosysteem kunt gebruiken om op afstand te werken en netwerk problemen te verhelpen die u als gevolg van COVID-19-crisis.
>

Is het versleutelen om connectiviteit te bieden voor externe gebruikers?
Ziet u plotseling dat er een piek van gebruikers wordt ondersteund dan wat u voor het eerst had gepland?
Hebt u de gebruiker nodig om verbinding te maken vanaf thuis en hebt u niet alleen toegang tot de Cloud, maar kunt u ook on-premises bereiken?
Wilt u dat uw externe gebruikers bronnen kunnen bereiken achter een particulier WAN?
Hebt u behoefte aan gebruikers om toegang te krijgen tot de intra Cloud bronnen zonder dat er verbinding hoeft te worden ingesteld tussen regio's?
Omdat deze globale Pandemic ongekende wijzigingen voor ons maakt, is het Azure Virtual WAN-team hier om u te helpen met uw connectiviteits behoeften.

Azure Virtual WAN is een netwerkservice die een groot aantal netwerk-, beveiligings- en routeringsfuncties samenbrengt om één operationele interface te bieden. Deze functies omvatten vertakkings connectiviteit (via connectiviteits automatisering van virtuele WAN-partner apparaten zoals SD-WAN of VPN CPE), site-naar-site-VPN-verbinding, VPN voor externe gebruikers (punt-naar-site) connectiviteit, persoonlijke (ExpressRoute) connectiviteit, intra-Cloud connectiviteit (transitieve connectiviteit voor virtuele netwerken), VPN ExpressRoute interconnectiviteit, route ring, Azure firewall U hoeft niet al deze gebruiks voorbeelden te hebben om virtueel WAN te gaan gebruiken. U kunt aan de slag met slechts één use-case en uw netwerk aanpassen tijdens het ontwikkelen.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Nu kunt u contact met externe gebruikers maken, zodat u op de hoogte bent van wat u nodig hebt om aan de slag te gaan met uw netwerk:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Externe gebruikers connectiviteit instellen

U kunt verbinding maken met uw resources in Azure via een IPsec/IKE (IKEv2)- of OpenVPN-verbinding. Voor dit type verbinding moet een VPN-client worden geconfigureerd voor de externe gebruiker. Deze client kan de [Azure VPN-client](https://go.microsoft.com/fwlink/?linkid=2117554) of de openvpn-client zijn of een wille keurige client die IKEv2 ondersteunt. Raadpleeg [Een site-naar-site-verbinding maken](virtual-wan-point-to-site-portal.md) voor meer informatie.

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Connectiviteit van de externe gebruiker naar on-premises

U hebt nu twee opties:

* Stel de site-naar-site-verbinding in met een bestaand VPN-apparaat. Wanneer u het IPsec VPN-apparaat verbindt met virtuele WAN-hub van Azure, wordt interconnectiviteit tussen de punt-naar-site-VPN-verbinding (externe gebruiker) en de site-naar-site-VPN automatisch. Zie [een site-naar-site-verbinding maken met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)voor meer informatie over het instellen van een site-naar-site-VPN van uw on-PREMISES VPN-apparaat naar Azure Virtual WAN.

* Verbind uw ExpressRoute-circuit met de virtuele WAN-hub. Voor het verbinden van een ExpressRoute-circuit is een ExpressRoute-gateway in een virtueel WAN geïmplementeerd. Zodra u er een hebt geïmplementeerd, wordt interconnectiviteit tussen de punt-naar-site-VPN-gebruiker en de ExpressRoute-gebruiker automatisch. Zie [een ExpressRoute-verbinding maken met behulp van Virtual WAN](virtual-wan-expressroute-portal.md)om de ExpressRoute-verbinding te maken. U kunt een bestaand ExpressRoute-circuit gebruiken om verbinding te maken met een virtueel WAN van Azure.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Bestaande virtuele WAN-basis klant

Een eenvoudig virtueel WAN biedt alleen site-naar-site-VPN. Om externe gebruikers verbinding te laten maken, moet u het virtuele WAN upgraden naar het standaard virtuele WAN. Zie [een virtuele WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md) voor stappen voor het upgraden van een virtueel WAN

## <a name="additional-information"></a><a name="other considerations"></a>Aanvullende informatie

Virtual WAN ondersteunt één hub per regio/locatie. Zie het artikel [Virtual WAN-partners en -locaties](virtual-wan-locations-partners.md) voor informatie over de locatie. Elke hub ondersteunt Maxi maal 10.000 externe gebruikers verbindingen, 1.000 vertakkings verbinding, vier ExpressRoute-circuits en Maxi maal 500 Virtual Network verbindingen. Wanneer u de externe gebruikers opschaalt, kunt u, als u vragen hebt, geen informatie zoeken door een e-mail te verzenden naar azurevirtualwan@microsoft.com . Als u technische ondersteuning nodig hebt, moet u een ondersteunings ticket openen via de Azure Portal en is de Help-informatie aan de weg.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van virtuele WAN-overzichten](virtual-wan-about.md) voor meer informatie over Virtual WAN