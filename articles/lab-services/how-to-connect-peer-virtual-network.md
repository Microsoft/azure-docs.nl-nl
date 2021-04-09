---
title: Verbinding maken met een peer netwerk in Azure Lab Services | Microsoft Docs
description: Informatie over het verbinden van uw Lab-netwerk met een ander netwerk als een peer. Verbind uw on-premises organisatie/Universiteit-netwerk bijvoorbeeld met het virtuele netwerk van het lab in Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 99ea72f11c8f389a15171a1fe3b376646494903a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434240"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Uw Lab-netwerk verbinden met een virtueel peer-netwerk in Azure Lab Services

Dit artikel bevat informatie over het peeren van uw Labs-netwerk met een ander netwerk.

## <a name="overview"></a>Overzicht

Met peering op virtueel netwerk kunt u naadloos verbinding maken met virtuele netwerken van Azure. Nadat de virtuele netwerken zijn gekoppeld via peering, worden ze voor verbindingsdoeleinden weergegeven als één netwerk. Het verkeer tussen virtuele machines in de gekoppelde virtuele netwerken wordt doorgestuurd via de micro soft backbone-infra structuur, vergelijkbaar met het verkeer dat wordt gerouteerd tussen virtuele machines in hetzelfde virtuele netwerk, via alleen particuliere IP-adressen. Zie [peering van virtuele netwerken](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.

Mogelijk moet u uw Lab-netwerk verbinden met een virtueel peer netwerk in sommige scenario's, waaronder de volgende:

- De virtuele machines in de test omgeving hebben software die verbinding maakt met on-premises licentie servers om een licentie aan te schaffen.
- De virtuele machines in het lab moeten toegang hebben tot gegevens sets (of andere bestanden) op de netwerk shares van de University.

Bepaalde on-premises netwerken zijn verbonden met Azure Virtual Network via [ExpressRoute](../expressroute/expressroute-introduction.md) of [Virtual Network gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Deze services moeten buiten Azure Lab Services worden ingesteld. Zie [overzicht van ExpressRoute](../expressroute/expressroute-introduction.md)voor meer informatie over het verbinden van een on-premises netwerk met Azure met behulp van ExpressRoute. Voor on-premises connectiviteit met een Virtual Network gateway, de gateway, het opgegeven virtuele netwerk en het lab-account moeten zich allemaal in dezelfde regio bevinden.

> [!NOTE]
> Bij het maken van een Azure-Virtual Network die wordt gekoppeld aan een Lab-account, is het belang rijk om te begrijpen hoe de regio van het virtuele netwerk van invloed is op de manier waarop de Labs wordt gemaakt.  Zie de sectie over de beheerders handleiding op [regions\locations](./administrator-guide.md#regionslocations)voor meer informatie.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configureren op het moment dat het lab-account wordt gemaakt

Tijdens het maken van het nieuwe [Lab-account](tutorial-setup-lab-account.md)kunt u een bestaand virtueel netwerk kiezen dat wordt weer gegeven in de vervolg keuzelijst van het **virtuele peer netwerk** op het tabblad **Geavanceerd** .  In de lijst worden alleen virtuele netwerken weer gegeven die zich in dezelfde regio bevinden als het lab-account. Het geselecteerde virtuele netwerk is verbonden (gekoppeld aan) met de laboratoria die zijn gemaakt met het lab-account.  Alle virtuele machines in Labs die zijn gemaakt na het maken van deze wijziging, hebben toegang tot de resources in het gekoppelde virtuele netwerk.

![VNet naar peer selecteren](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Adresbereik

Er is ook een optie voor het opgeven van **adres bereik** voor virtuele machines voor de laboratoria.  De eigenschap **adres bereik** is alleen van toepassing als er een **virtueel peer netwerk** is ingeschakeld voor het lab. Als het adres bereik wordt verstrekt, worden alle virtuele machines in de Labs onder het lab-account in dat adres bereik gemaakt. Het adres bereik moet de CIDR-notatie hebben (bijvoorbeeld 10.20.0.0/20) en mag niet overlappen met de bestaande adresbereiken.  Wanneer u een adres bereik opgeeft, is het belang rijk om na te denken over het aantal *Labs* dat wordt gemaakt en een adres bereik op te geven dat u wilt bieden. Voor Lab-Services wordt uitgegaan van een maximum van 512 virtuele machines per Lab.  Een IP-bereik met '/23 ' kan bijvoorbeeld slechts één Lab maken.  Een bereik met een '/21 ' staat het maken van vier Labs toe.

Als het **adres bereik** niet is opgegeven, gebruikt Lab Services het standaard adres bereik dat door Azure wordt verstrekt tijdens het maken van het virtuele netwerk om te worden gekoppeld aan uw virtuele netwerk.  Het bereik is vaak iets zoals 10. x. 0,0/16.  Dit kan leiden tot een overlap ping van het IP-bereik. Zorg er dus voor dat u een adres bereik opgeeft in de Lab-instellingen of Controleer het adres bereik van het virtuele netwerk dat wordt gekoppeld aan een peer.

> [!NOTE]
> Het maken van een Lab kan mislukken als het lab-account is gekoppeld aan een virtueel netwerk, maar te beperkt is over een IP-adres bereik. Er is onvoldoende ruimte beschikbaar in het adres bereik als er te veel Labs aanwezig zijn in het lab-account (elk lab gebruikt 512-adressen). 
> 
> Als het maken van het lab mislukt, neemt u contact op met de eigenaar van uw Lab-account/beheerder en vraagt u het adres bereik te verg root. De beheerder kan het adres bereik verhogen met behulp van de stappen die worden beschreven in de sectie [een adres bereik voor Vm's opgeven in een Lab-account](#specify-an-address-range-for-vms-in-the-lab-account) . 

## <a name="configure-after-the-lab-account-is-created"></a>Configureren nadat het lab-account is gemaakt

Deze eigenschap kan worden ingeschakeld op het tabblad **configuratie** van de **test omgeving** van de pagina Lab-account als u geen peer netwerk hebt ingesteld op het moment dat het account wordt gemaakt. De wijziging van deze instelling is alleen van toepassing op de Labs die na de wijziging zijn gemaakt. Zoals u kunt zien in de afbeelding, kunt u het **virtuele peer netwerk** voor Labs in-of uitschakelen in het lab-account.

![VNet-peering in-of uitschakelen nadat het lab is gemaakt](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Wanneer u een virtueel netwerk voor het veld **virtuele peer netwerk** selecteert, is de optie **Lab maken voor het kiezen van Lab-locatie** is uitgeschakeld. Dat komt omdat Labs in het lab-account zich in dezelfde regio bevinden als het lab-account om verbinding te maken met resources in het virtuele peer netwerk.

> [!IMPORTANT]
> De instelling voor het gekoppelde virtuele netwerk is alleen van toepassing op Labs die zijn gemaakt nadat de wijziging is aangebracht, niet aan de bestaande Labs.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Geef een adres bereik voor Vm's op in het lab-account
De volgende procedure bevat stappen voor het opgeven van een adres bereik voor Vm's in het lab. Als u het bereik bijwerkt dat u eerder hebt opgegeven, is het gewijzigde adres bereik alleen van toepassing op virtuele machines die zijn gemaakt nadat de wijziging is aangebracht. 

Hier volgen enkele beperkingen bij het opgeven van het adres bereik dat u moet onthouden. 

- Het voor voegsel moet kleiner zijn dan of gelijk zijn aan 23. 
- Als een virtueel netwerk is gekoppeld aan het lab-account, kan het gegeven adres bereik niet overlappen met het adres bereik van een gekoppeld virtueel netwerk.

1. Selecteer op de pagina **Lab-account** de optie **Labs-instellingen** in het menu links.
2. Geef in het veld **adres bereik** het adres bereik op voor virtuele machines die in het lab worden gemaakt. Het adres bereik moet in de CIDR-notatie (Classless Inter-Domain Routing) staan (voor beeld: 10.20.0.0/23). Virtuele machines in het lab worden gemaakt in dit adres bereik.
3. Selecteer **Opslaan** op de werkbalk. 

    ![Adres bereik configureren](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Toestaan dat de labmaker de lablocatie kiest](allow-lab-creator-pick-lab-location.md)
- [Een galerie met gedeelde afbeeldingen koppelen aan een Lab](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als een Lab-eigenaar](how-to-add-user-lab-owner.md)
- [Firewall instellingen voor een Lab weer geven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)