---
title: 'Azure VMware-oplossing: DNS door sturen van privécloud naar on-premises'
description: Hierin wordt beschreven hoe u de DNS-server van uw CloudSimple-Privécloud inschakelt om het opzoeken van on-premises resources te doorstuurten
author: Ajayan1008
ms.author: v-hborys
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3481e21a93fbf5f658c5ca00513ea179c8f95400
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899198"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>DNS-servers voor CloudSimple-Privécloud inschakelen om DNS-Zoek opdrachten van on-premises resources naar uw DNS-servers door te sturen

DNS-servers in de privécloud kunnen DNS-Zoek opdrachten door sturen voor alle on-premises resources naar uw DNS-servers.  Als u de zoek functie inschakelt, kunnen vSphere-onderdelen in de Privécloud worden gebruikt voor het opzoeken van services die in uw on-premises omgeving worden uitgevoerd en met behulp van de volledig gekwalificeerde domein namen (FQDN).

## <a name="scenarios"></a>Scenario's 

Door de DNS-zoek opdracht voor uw on-premises DNS-server door te sturen, kunt u uw Privécloud gebruiken voor de volgende scenario's:

* Een Privécloud gebruiken als nood herstel installatie voor uw on-premises VMware-oplossing
* On-premises Active Directory gebruiken als een identiteits bron voor uw Privécloud vSphere
* HCX gebruiken voor het migreren van virtuele machines van on-premises naar een Privécloud

## <a name="before-you-begin"></a>Voordat u begint

Er moet een netwerk verbinding zijn tussen uw particuliere cloud netwerk en uw on-premises netwerk voor het werken met DNS-door sturen.  U kunt de netwerk verbinding instellen met behulp van:

* [Verbinding maken tussen on-premises en CloudSimple met behulp van ExpressRoute](on-premises-connection.md)
* [Een site-naar-site-VPN-gateway instellen](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

Voor een goede werking van DNS moet u Firewall poorten openen op deze verbinding.  Gebruikte poorten zijn TCP-poort 53 of UDP-poort 53.

> [!NOTE]
> Als u site-naar-site-VPN gebruikt, moet uw on-premises DNS-server subnet worden toegevoegd als onderdeel van on-premises voor voegsels.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Door sturen van DNS aanvragen van een Privécloud naar on-premises

Als u het door sturen van DNS vanuit een Privécloud naar on-premises wilt inschakelen, dient u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)in, waarbij de volgende informatie wordt verstrekt.

* Probleem type: **technisch**
* Abonnement: het **abonnement waar de CloudSimple-service is geïmplementeerd**
* Service: **VMware-oplossing per CloudSimple**
* Probleem type: **advies of hoe kan ik...**
* Subtype van probleem: **hulp nodig met NW**
* Geef de domein naam van uw on-premises domein op in het deel venster Details.
* Geef de lijst met uw on-premises DNS-servers op waarnaar de zoek opdracht wordt doorgestuurd vanuit uw privécloud in het detail venster.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de configuratie van on-premises firewall](on-premises-firewall-configuration.md)
* [Configuratie van on-premises DNS-server](on-premises-dns-setup.md)
