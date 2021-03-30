---
title: Geforceerde tunneling Azure Firewall
description: U kunt geforceerde tunneling configureren om Internet-gebonden verkeer te routeren naar een extra firewall of een virtueel netwerk apparaat voor verdere verwerking.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88612589"
---
# <a name="azure-firewall-forced-tunneling"></a>Geforceerde tunneling Azure Firewall

Wanneer u een nieuwe Azure-firewall configureert, kunt u al het internetverkeer routeren naar een aangewezen volgende hop in plaats van rechtstreeks naar internet. U kunt bijvoorbeeld een on-premises edge-firewall of een ander virtueel netwerkapparaat (NVA) hebben om netwerkverkeer te verwerken voordat het wordt doorgegeven aan internet. U kunt echter geen bestaande firewall configureren voor geforceerde tunneling.

Geforceerde tunneling is standaard niet toegestaan op Azure Firewall om ervoor te zorgen dat aan alle uitgaande Azure-afhankelijkheden wordt voldaan. UDR-configuraties (door de gebruiker gedefinieerde route) op de *AzureFirewallSubnet* die een standaard route hebben die niet rechtstreeks naar Internet gaat, worden uitgeschakeld.

## <a name="forced-tunneling-configuration"></a>Configuratie van geforceerde tunneling

Voor de ondersteuning van geforceerde tunneling wordt Service Management-verkeer gescheiden van het verkeer van de klant. Een extra toegewezen subnet met de naam *AzureFirewallManagementSubnet* (minimale subnet-grootte/26) is vereist met een eigen gekoppeld openbaar IP-adres. De enige route die op dit subnet is toegestaan, is een standaard route naar Internet en de doorgifte van BGP-routes moet worden uitgeschakeld.

Als u een standaard route hebt geadverteerd via BGP om verkeer naar on-premises af te dwingen, moet u de *AzureFirewallSubnet* en *AzureFirewallManagementSubnet* maken voordat u uw firewall implementeert en een UDR met een standaard route naar Internet hebt, en **Gateway routes door geven** die zijn uitgeschakeld.

Binnen deze configuratie kan de *AzureFirewallSubnet* nu routes naar een on-premises firewall of NVA bevatten om verkeer te verwerken voordat het wordt door gegeven aan Internet. U kunt deze routes ook publiceren via BGP naar *AzureFirewallSubnet* als het **door sturen van Gateway routes** is ingeschakeld op dit subnet.

U kunt bijvoorbeeld een standaard route maken op het *AzureFirewallSubnet* met uw VPN-gateway als de volgende hop om naar uw on-premises apparaat te gaan. U kunt ook **Gateway routes door geven** inschakelen om de juiste routes naar het on-premises netwerk op te halen.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Doorgifte van route van virtuele netwerkgateway":::

Als u geforceerde tunneling inschakelt, wordt het Internet-gebonden verkeer omgezet naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet, waardoor de bron wordt verborgen via uw on-premises firewall.

Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, Azure Firewall SNATs het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt Azure Firewall echter zodanig configureren dat uw open bare IP-adres bereik **niet** wordt gesnat. Raadpleeg [Azure Firewall SNAT voor privé-IP-adresbereiken](snat-private-range.md) voor meer informatie.

Zodra u Azure Firewall configureert om geforceerde tunneling te ondersteunen, kunt u de configuratie niet ongedaan maken. Als u alle andere IP-configuraties van uw firewall verwijdert, wordt de beheer-IP-configuratie ook verwijderd en wordt de toewijzing van de Firewall ongedaan gemaakt. Het open bare IP-adres dat is toegewezen aan de beheer-IP-configuratie kan niet worden verwijderd, maar u kunt een ander openbaar IP-adres toewijzen.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal](tutorial-hybrid-portal.md)
