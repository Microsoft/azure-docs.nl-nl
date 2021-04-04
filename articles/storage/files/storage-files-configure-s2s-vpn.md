---
title: Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files | Microsoft Docs
description: Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0fa3fb8040fd79d68f9260ab520d3b6823ab363d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629288"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Een site-naar-site-VPN configureren voor gebruik met Azure Files
U kunt een S2S-VPN-verbinding (site-naar-site) gebruiken om uw Azure-bestands shares te koppelen via SMB vanaf uw on-premises netwerk, zonder poort 445 te openen. U kunt een site-naar-site-VPN instellen met behulp van [azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), een Azure-resource die VPN-services aanbiedt en wordt geïmplementeerd in een resource groep naast opslag accounts of andere Azure-resources.

![Een topologie diagram van de topologie van een Azure VPN-gateway die een Azure-bestands share verbindt met een on-premises site met behulp van een S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

We raden u ten zeerste aan [Azure files netwerk overzicht](storage-files-networking-overview.md) te lezen voordat u verdergaat met dit artikel voor een volledige bespreking van de beschik bare netwerk opties voor Azure files.

In het artikel worden de stappen beschreven voor het configureren van een site-naar-site-VPN om on-premises Azure-bestands shares rechtstreeks te koppelen. Als u synchronisatie verkeer voor Azure File Sync wilt omleiden via een site-naar-site-VPN, raadpleegt u [Azure file sync proxy-en Firewall instellingen configureren](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Vereisten
- Een Azure-bestands share die u on-premises wilt koppelen. Azure-bestands shares worden geïmplementeerd in opslag accounts. Dit zijn beheer constructies die een gedeelde opslag groep vertegenwoordigen, waarbij u meerdere bestands shares en andere opslag resources, zoals BLOB-containers of wacht rijen, kunt implementeren. Meer informatie over het implementeren van Azure-bestands shares en opslag accounts vindt u in [een Azure-bestands share maken](storage-how-to-create-file-share.md).

- Een persoonlijk eind punt voor het opslag account dat de Azure-bestands share bevat die u on-premises wilt koppelen. Zie [Azure files Network-eind punten configureren](storage-files-networking-endpoints.md?tabs=azure-portal)voor meer informatie over het maken van een persoonlijk eind punt. 

- Een netwerk apparaat of-server in uw on-premises Data Center dat compatibel is met Azure VPN Gateway. Azure Files is het neutraal van het on-premises netwerk apparaat, maar Azure VPN Gateway houdt een [lijst bij van geteste apparaten](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Verschillende netwerk apparaten bieden verschillende functies, prestatie kenmerken en beheer functionaliteit, dus houd er rekening mee bij het selecteren van een netwerk apparaat.

    Als u geen bestaand netwerk apparaat hebt, bevat Windows Server een ingebouwde serverrol, route ring en RAS (RRAS), die kan worden gebruikt als een on-premises netwerk apparaat. Zie voor meer informatie over het configureren van route ring en RAS in Windows Server [RAS-gateway](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Opslag account toevoegen aan VNet
Navigeer in het Azure Portal naar het opslag account met de Azure-bestands share die u on-premises wilt koppelen. Selecteer in de inhouds opgave voor het opslag account de vermelding **firewalls en virtuele netwerken** . Tenzij u een virtueel netwerk aan uw opslag account hebt toegevoegd tijdens het maken hiervan, moet het deel venster **toegang toestaan van** het keuze rondje voor **alle geselecteerde netwerken** zijn ingeschakeld.

Selecteer **geselecteerde netwerken** om uw opslag account toe te voegen aan het gewenste virtuele netwerk. Klik onder de subkoppen **virtuele netwerken** op een **bestaand virtueel netwerk toevoegen** of **+ nieuw virtueel netwerk toevoegen** , afhankelijk van de gewenste status. Als u een nieuw virtueel netwerk maakt, wordt er een nieuwe Azure-resource gemaakt. De nieuwe of bestaande VNet-resource hoeft zich niet in dezelfde resource groep of hetzelfde abonnement als het opslag account te bevinden, maar moet zich in dezelfde regio bevinden als het opslag account en de resource groep en het abonnement waar u uw VNet implementeert, moet overeenkomen met de naam waarop u uw VPN Gateway gaat implementeren. 

![Scherm afbeelding van de Azure Portal waarmee een bestaand of nieuw virtueel netwerk aan het opslag account wordt toegevoegd](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Als u een bestaand virtueel netwerk toevoegt, wordt u gevraagd een of meer subnetten van het virtuele netwerk te selecteren waaraan het opslag account moet worden toegevoegd. Als u een nieuw virtueel netwerk selecteert, maakt u een subnet als onderdeel van het maken van het virtuele netwerk en kunt u later meer toevoegen via de resulterende Azure-resource voor het virtuele netwerk.

Als u nog geen opslag account aan uw abonnement hebt toegevoegd, moet u het service-eind punt van micro soft. Storage toevoegen aan het virtuele netwerk. Dit kan enige tijd duren en totdat deze bewerking is voltooid, hebt u geen toegang meer tot de Azure-bestands shares in dat opslag account, inclusief via de VPN-verbinding. 

## <a name="deploy-an-azure-vpn-gateway"></a>Een Azure-VPN Gateway implementeren
Selecteer in de inhouds opgave voor de Azure Portal **een nieuwe resource maken** en zoek naar *virtuele netwerk gateway*. De gateway van uw virtuele netwerk moet zich in hetzelfde abonnement, dezelfde Azure-regio en resource groep bevinden als het virtuele netwerk dat u in de vorige stap hebt geïmplementeerd (Houd er rekening mee dat de resource groep automatisch wordt geselecteerd wanneer het virtuele netwerk wordt gekozen). 

Voor de implementatie van een Azure VPN Gateway moet u de volgende velden invullen:

- **Naam**: de naam van de Azure-resource voor de VPN gateway. Deze naam kan een naam zijn die u nuttig vindt voor uw beheer.
- **Regio**: de regio waarin de VPN gateway wordt geïmplementeerd.
- **Gateway type**: als u een site-naar-site-VPN wilt implementeren, moet u **VPN** selecteren.
- **VPN-type**: u kunt kiezen voor op *route gebaseerd** of **op basis van beleid** , afhankelijk van uw VPN-apparaat. Op route gebaseerde Vpn's ondersteunen IKEv2, terwijl op beleid gebaseerde Vpn's alleen IKEv1 ondersteunen. Zie [informatie over op beleid gebaseerde en op route gebaseerde VPN-gateways](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about) voor meer informatie over de twee typen VPN-gateways
- **SKU**: de SKU bepaalt het aantal toegestane site-naar-site tunnels en de gewenste prestaties van de VPN. Als u de juiste SKU voor uw use-case wilt selecteren, raadpleegt u de lijst met [Gateway-sku's](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) . De SKU van de VPN Gateway kan indien nodig later worden gewijzigd.
- **Virtueel netwerk**: het virtuele netwerk dat u in de vorige stap hebt gemaakt.
- **Openbaar IP-adres**: het IP-adres van VPN gateway dat wordt blootgesteld aan Internet. Waarschijnlijk moet u een nieuw IP-adres maken, maar u kunt ook een bestaand ongebruikt IP-adres gebruiken, indien van toepassing. Als u selecteert om **nieuwe te maken**, wordt er een nieuw IP-adres Azure-resource gemaakt in dezelfde resource groep als de VPN gateway en is de naam van het  **open bare IP-adres** de naam van het nieuwe IP-adres. Als u **bestaande gebruiken** selecteert, moet u het bestaande ongebruikte IP-adres selecteren.
- **Modus actief-actief inschakelen**: Selecteer **ingeschakeld** als u een configuratie voor Active-Active-gateway maakt, anders **uitgeschakeld laten uitschakelen** geselecteerd. Zie voor meer informatie over de modus actief-actief [Maxi maal beschik bare cross-premises en vnet-naar-vnet-connectiviteit](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **BGP ASN configureren**: Selecteer **ingeschakeld** als deze instelling specifiek is vereist voor uw configuratie. Zie [over BGP met Azure VPN gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie over deze instelling.

Selecteer **controleren + maken** om de VPN gateway te maken. Het kan 45 minuten duren voordat een VPN Gateway volledig te maken en te implementeren.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Een lokale netwerk gateway maken voor uw on-premises gateway 
Een lokale netwerk gateway is een Azure-resource die uw on-premises netwerk apparaat weergeeft. Selecteer in de inhouds opgave voor de Azure Portal **een nieuwe resource maken** en zoeken naar de *lokale netwerk gateway*. De lokale netwerk gateway is een Azure-resource die naast uw opslag account, virtueel netwerk en VPN Gateway wordt geïmplementeerd, maar niet in dezelfde resource groep of hetzelfde abonnement als het opslag account moet zijn. 

Voor de implementatie van de lokale netwerk gateway resource moet u de volgende velden invullen:

- **Naam**: de naam van de Azure-resource voor de lokale netwerk gateway. Deze naam kan een naam zijn die u nuttig vindt voor uw beheer.
- **IP-adres**: het open bare IP-adres van uw lokale gateway on-premises.
- **Adres ruimte**: de adresbereiken voor het netwerk die door deze lokale netwerk gateway worden vertegenwoordigd. U kunt meerdere adresbereiken voor de adres ruimte toevoegen, maar zorg ervoor dat de bereiken die u hier opgeeft, elkaar niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. 
- **BGP-instellingen configureren**: CONFIGUREER alleen BGP-instellingen als deze instelling is vereist voor uw configuratie. Zie [over BGP met Azure VPN gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie over deze instelling.
- **Abonnement**: het gewenste abonnement. Dit hoeft niet overeen te komen met het abonnement dat wordt gebruikt voor de VPN Gateway of het opslag account.
- **Resource groep**: de gewenste resource groep. Dit hoeft niet overeen te komen met de resource groep die wordt gebruikt voor de VPN Gateway of het opslag account.
- **Locatie**: de Azure-regio waarin de lokale netwerk gateway resource moet worden gemaakt. Dit moet overeenkomen met de regio die u hebt geselecteerd voor de VPN Gateway en het opslag account.

Selecteer **maken** om de lokale netwerk gateway resource te maken.  

## <a name="configure-on-premises-network-appliance"></a>On-premises netwerk apparaat configureren
De specifieke stappen voor het configureren van uw on-premises netwerk apparaat zijn afhankelijk van het netwerk apparaat dat door uw organisatie is geselecteerd. Afhankelijk van het apparaat dat uw organisatie heeft gekozen, kan de [lijst met geteste apparaten](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) een koppeling hebben met de instructies van de leverancier van uw apparaat voor het configureren van Azure VPN gateway.

## <a name="create-the-site-to-site-connection"></a>De site-naar-site-verbinding maken
Als u de implementatie van een S2S VPN wilt volt ooien, moet u een verbinding maken tussen uw on-premises netwerk apparaat (vertegenwoordigd door de bron van de lokale netwerk gateway) en de VPN Gateway. Ga hiervoor naar de VPN Gateway die u hierboven hebt gemaakt. Selecteer in de inhouds opgave voor de VPN Gateway **verbindingen** en klik op **toevoegen**. In het deel venster voor het toevoegen van een **verbinding** zijn de volgende velden vereist:

- **Naam**: de naam van de verbinding. Een VPN Gateway kan meerdere verbindingen hosten, dus kies een naam die nuttig is voor uw beheer, zodat deze specifieke verbinding wordt onderscheiden.
- **Verbindings type**: Selecteer sinds deze S2S-verbinding de optie **site-naar-site (IPSec)** in de vervolg keuzelijst.
- **Gateway van virtueel netwerk**: dit veld wordt automatisch geselecteerd voor het VPN gateway u de verbinding maakt en kan niet worden gewijzigd.
- **Lokale netwerk gateway**: dit is de lokale netwerk gateway die u wilt verbinden met uw VPN gateway. Het deel venster resulterende selectie moet de naam hebben van de lokale netwerk gateway die u hierboven hebt gemaakt.
- **Gedeelde sleutel (PSK)**: een combi natie van letters en cijfers die wordt gebruikt om versleuteling voor de verbinding tot stand te brengen. Dezelfde gedeelde sleutel moet zowel in het virtuele netwerk als in de lokale netwerk gateways worden gebruikt. Als uw gateway apparaat er geen biedt, kunt u hier een maken en dit aan uw apparaat geven.

Selecteer **OK** om de verbinding te maken. U kunt controleren of de verbinding tot stand is gebracht via de pagina **verbindingen** .

## <a name="mount-azure-file-share"></a>Azure-bestands share koppelen 
Bij de laatste stap bij het configureren van een S2S-VPN wordt gecontroleerd of deze werkt voor Azure Files. U kunt dit doen door uw Azure-bestands share on-premises te koppelen met uw voorkeurs besturingssysteem. Zie de instructies voor het koppelen van het besturings systeem:

- [Windows](storage-how-to-use-files-windows.md)
- [MacOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Zie ook
- [Overzicht van Azure Files netwerken](storage-files-networking-overview.md)
- [Een punt-naar-site-VPN (P2S) in Windows configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md)