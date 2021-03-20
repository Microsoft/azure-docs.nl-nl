---
title: Firewall-instellingen voor Azure Lab Services
description: Informatie over het bepalen van het open bare IP-adres en het poort nummer bereik van virtuele machines in een Lab zodat informatie kan worden toegevoegd aan de firewall regels.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445845"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Firewall-instellingen voor Azure Lab Services

Elke organisatie of school zal hun eigen netwerk instellen op een manier die het beste past bij hun behoeften.  Soms omvat het instellen van firewall regels die Remote Desktop Protocol (RDP) of ssh-verbindingen (Secure Shell) met computers buiten hun netwerk blok keren.  Omdat Azure Lab Services in de open bare Cloud wordt uitgevoerd, is er mogelijk een extra configuratie nodig om studenten toegang te geven tot hun virtuele machine bij het maken van verbinding via het netwerk van de campus.

Elk Lab maakt gebruik van één openbaar IP-adres en meerdere poorten.  Alle Vm's, zowel de virtuele machine als de student-Vm's, gebruiken dit open bare IP-adres.  Het open bare IP-adres wordt niet gewijzigd voor de levens duur van het lab.  Elke VM heeft echter een ander poort nummer.  De poort nummers variëren van 49152 tot 65535.  De combi natie van het open bare IP-adres en het poort nummer wordt gebruikt om docenten en studenten te verbinden met de juiste VM.  In dit artikel wordt beschreven hoe u het specifieke open bare IP-adres kunt vinden dat wordt gebruikt door een lab.  Deze informatie kan worden gebruikt om binnenkomende en uitgaande firewall regels bij te werken zodat studenten toegang hebben tot hun Vm's.

>[!IMPORTANT]
>Elk lab heeft een ander openbaar IP-adres.

## <a name="find-public-ip-for-a-lab"></a>Een openbaar IP-adres voor een Lab zoeken

De open bare IP-adressen voor elk lab worden weer gegeven op de pagina **alle Labs** van het lab-account voor Lab-Services.  Zie [Labs weer geven in een Lab-account](manage-labs.md#view-labs-in-a-lab-account)voor instructies voor het vinden van de pagina **alle Labs** .  

> [!div class="mx-imgBorder"]
> ![Pagina alle Labs](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Het open bare IP-adres wordt niet weer geven als de sjabloon machine voor uw Lab nog niet is gepubliceerd.

## <a name="conclusion"></a>Conclusie

Nu weten we het open bare IP-adres voor het lab.  Er kunnen binnenkomende en uitgaande regels worden gemaakt voor de firewall van de organisatie voor het open bare IP-adres en het poort bereik 49152-65535.  Zodra de regels zijn bijgewerkt, hebben studenten toegang tot hun Vm's zonder dat de netwerk firewall de toegang blokkeert.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Toestaan dat de labmaker de lablocatie kiest](allow-lab-creator-pick-lab-location.md)
- [Uw Lab-netwerk verbinden met een virtueel peer netwerk](how-to-connect-peer-virtual-network.md)
- [Een galerie met gedeelde afbeeldingen koppelen aan een Lab](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als een Lab-eigenaar](how-to-add-user-lab-owner.md)
- [Firewall instellingen voor een Lab weer geven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)
