---
title: Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files | Microsoft Docs
description: Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a08ca4142876a5a92adbe8b1c3fce9ec7953019
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778009"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Een site-naar-site-VPN configureren voor gebruik met Azure Files
U kunt een site-naar-site-VPN-verbinding (S2S) gebruiken om uw Azure-bestands shares via SMB te verbinden vanaf uw on-premises netwerk, zonder poort 445 te openen. U kunt een site-naar-site-VPN instellen met behulp van [Azure VPN Gateway.](../../vpn-gateway/vpn-gateway-about-vpngateways.md)Dit is een Azure-resource die VPN-services aanbiedt en wordt geïmplementeerd in een resourcegroep naast opslagaccounts of andere Azure-resources.

![Een topologiegrafiek met de topologie van een Azure VPN-gateway die een Azure-bestands share verbindt met een on-premises site met behulp van een S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

We raden u [](storage-files-networking-overview.md) ten zeerste aan Azure Files netwerkoverzicht te lezen voordat u verdergaat met dit artikel voor een volledige bespreking van de netwerkopties die beschikbaar zijn voor Azure Files.

In het artikel worden de stappen beschreven voor het configureren van een site-naar-site-VPN om Azure-bestands shares rechtstreeks on-premises te mounten. Als u synchronisatieverkeer voor Azure File Sync via een site-naar-site-VPN wilt omgeleid, raadpleegt u De proxy Azure File Sync en [firewallinstellingen configureren.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Vereisten
- Een Azure-bestands share die u on-premises wilt toevoegen. Azure-bestands shares worden geïmplementeerd in opslagaccounts. Dit zijn beheersin constructies die een gedeelde opslaggroep vertegenwoordigen waarin u meerdere bestands shares kunt implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen. Meer informatie over het implementeren van Azure-bestands shares en opslagaccounts vindt u in [Een Azure-bestands share maken.](storage-how-to-create-file-share.md)

- Een privé-eindpunt voor het opslagaccount met de Azure-bestands share die u on-premises wilt toevoegen. Zie Configuring Azure Files network endpoints (Netwerk Azure Files eindpunten configureren) voor meer informatie over het maken [van een privé-eindpunt.](storage-files-networking-endpoints.md?tabs=azure-portal) 

- Een netwerkapparaat of -server in uw on-premises datacenter dat compatibel is met Azure VPN Gateway. Azure Files is agnostisch van het gekozen on-premises netwerkapparaat, maar Azure VPN Gateway houdt een lijst met [geteste apparaten bij.](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) Verschillende netwerkapparaten bieden verschillende functies, prestatiekenmerken en beheerfuncties, dus houd hier rekening mee wanneer u een netwerkapparaat selecteert.

    Als u geen bestaand netwerkapparaat hebt, bevat Windows Server een ingebouwde serverfunctie, routering en externe toegang (RRAS), die kan worden gebruikt als het on-premises netwerkapparaat. Zie voor meer informatie over het configureren van routering en externe toegang in Windows Server [RAS-Gateway.](/windows-server/remote/remote-access/ras-gateway/ras-gateway)

## <a name="add-storage-account-to-vnet"></a>Opslagaccount toevoegen aan VNet
Navigeer Azure Portal het opslagaccount met de Azure-bestands share die u on-premises wilt toevoegen. Selecteer in de inhoudsopgave voor het opslagaccount de **vermelding Firewalls en virtuele netwerken.** Tenzij u een virtueel netwerk aan uw opslagaccount hebt toegevoegd toen u het maakte, moet in het deelvenster dat wordt weergegeven, het radioknop Toegang **vanuit** toestaan voor **Alle netwerken zijn** geselecteerd.

Selecteer Geselecteerde netwerken om uw opslagaccount toe te voegen aan **het gewenste virtuele netwerk.** Klik onder **de** subkop Virtuele netwerken op **+ Bestaand** virtueel netwerk toevoegen of **+Nieuw** virtueel netwerk toevoegen, afhankelijk van de gewenste status. Als u een nieuw virtueel netwerk maakt, wordt er een nieuwe Azure-resource gemaakt. De nieuwe of bestaande VNet-resource hoeft zich niet in dezelfde resourcegroep of hetzelfde abonnement als het opslagaccount te hebben, maar moet zich wel in dezelfde regio als het opslagaccount en de resourcegroep en het abonnement waarin u uw VNet implementeert, moeten overeenkomen met de resourcegroep waarin u uw VPN Gateway wilt implementeren. 

![Schermopname van de Azure Portal de optie om een bestaand of nieuw virtueel netwerk toe te voegen aan het opslagaccount](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Als u een bestaand virtueel netwerk toevoegt, wordt u gevraagd een of meer subnetten van dat virtuele netwerk te selecteren waaraan het opslagaccount moet worden toegevoegd. Als u een nieuw virtueel netwerk selecteert, maakt u een subnet als onderdeel van het maken van het virtuele netwerk en kunt u later meer toevoegen via de resulterende Azure-resource voor het virtuele netwerk.

Als u nog geen opslagaccount aan uw abonnement hebt toegevoegd, moet het service-eindpunt Microsoft.Storage worden toegevoegd aan het virtuele netwerk. Dit kan enige tijd duren en totdat deze bewerking is voltooid, hebt u geen toegang tot de Azure-bestands shares binnen dat opslagaccount, ook niet via de VPN-verbinding. 

## <a name="deploy-an-azure-vpn-gateway"></a>Een Azure-VPN Gateway
Selecteer in de inhoudsopgave Azure Portal nieuwe **resource** maken en zoek naar *Virtuele netwerkgateway.* Uw virtuele netwerkgateway moet zich in hetzelfde abonnement, dezelfde Azure-regio en dezelfde resourcegroep als het virtuele netwerk hebben geplaatst dat u in de vorige stap hebt geïmplementeerd (houd er rekening mee dat resourcegroep automatisch wordt geselecteerd wanneer het virtuele netwerk wordt gekozen). 

Voor het implementeren van een Azure-VPN Gateway, moet u de volgende velden in vullen:

- **Naam:** de naam van de Azure-resource voor de VPN Gateway. Deze naam kan elke naam zijn die u nuttig vindt voor uw beheer.
- **Regio:** de regio waarin de VPN Gateway worden geïmplementeerd.
- **Gatewaytype:** voor het implementeren van een site-naar-site-VPN moet u **VPN selecteren.**
- **VPN-type:** u kunt op *route* gebaseerd * of op beleid **gebaseerd** kiezen, afhankelijk van uw VPN-apparaat. Op route gebaseerde VPN's ondersteunen IKEv2, terwijl op beleid gebaseerde VPN's alleen IKEv1 ondersteunen. Zie About [policy-based and route-based VPN gateways](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about) (Informatie over op beleid gebaseerde en op route gebaseerde VPN-gateways) voor meer informatie over de twee typen VPN-gateways.
- **SKU:** de SKU bepaalt het aantal toegestane site-naar-site-tunnels en de gewenste prestaties van het VPN. Als u de juiste SKU voor uw use-case wilt selecteren, raadpleegt u de [lijst gateway-SKU.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) De SKU van de VPN Gateway indien nodig later worden gewijzigd.
- **Virtueel netwerk:** het virtuele netwerk dat u in de vorige stap hebt gemaakt.
- **Openbaar IP-adres:** het IP-adres van VPN Gateway die worden blootgesteld aan internet. Waarschijnlijk moet u een nieuw IP-adres maken, maar u kunt ook een bestaand ongebruikt IP-adres gebruiken als dat van toepassing is. Als u Nieuwe maken selecteert, wordt er een nieuw IP-adres gemaakt in dezelfde resourcegroep als de VPN Gateway en wordt de naam van het openbare **IP-adres** de naam van het zojuist gemaakte IP-adres. Als u Bestaande **gebruiken selecteert,** moet u het bestaande ongebruikte IP-adres selecteren.
- **Modus actief-actief inschakelen:** selecteer Alleen Ingeschakeld als u een gatewayconfiguratie actief-actief maakt, anders laat u **Uitgeschakeld** geselecteerd.  Zie Highly [available cross-premises and VNet-to-VNet connectivity (Cross-premises en VNet-naar-VNet-connectiviteit)](../../vpn-gateway/vpn-gateway-highlyavailable.md)voor meer informatie over de modus actief-actief.
- **BGP ASN configureren:** selecteer **Alleen Ingeschakeld als** deze instelling specifiek is vereist voor uw configuratie. Zie Over BGP met Azure-VPN Gateway voor meer informatie [over deze VPN Gateway.](../../vpn-gateway/vpn-gateway-bgp-overview.md)

Selecteer **Beoordelen en maken om** de VPN Gateway. Het VPN Gateway tot 45 minuten duren voordat een implementatie volledig is geïmplementeerd.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Een lokale netwerkgateway maken voor uw on-premises gateway 
Een lokale netwerkgateway is een Azure-resource die uw on-premises netwerkapparaat vertegenwoordigt. Selecteer In de inhoudsopgave voor de Azure Portal selecteert **u Een nieuwe resource maken** en zoekt u naar lokale *netwerkgateway.* De lokale netwerkgateway is een Azure-resource die wordt geïmplementeerd naast uw opslagaccount, virtuele netwerk en VPN Gateway, maar niet in dezelfde resourcegroep of hetzelfde abonnement hoeft te zijn als het opslagaccount. 

Voor het implementeren van de lokale netwerkgatewayresource moet u de volgende velden in vullen:

- **Naam:** de naam van de Azure-resource voor de lokale netwerkgateway. Deze naam kan elke naam zijn die u nuttig vindt voor uw beheer.
- **IP-adres:** het openbare IP-adres van uw lokale gateway on-premises.
- **Adresruimte:** de adresbereiken voor het netwerk waar deze lokale netwerkgateway voor staat. U kunt meerdere adresruimtebereiken toevoegen, maar zorg ervoor dat de hier opgegeven adresbereiken niet overlappen met de adresbereiken van andere netwerken die u wilt verbinden. 
- **BGP-instellingen configureren:** configureer alleen BGP-instellingen als deze instelling vereist is voor uw configuratie. Zie Over BGP met Azure-VPN Gateway voor meer informatie [over deze VPN Gateway.](../../vpn-gateway/vpn-gateway-bgp-overview.md)
- **Abonnement:** Het gewenste abonnement. Dit hoeft niet overeen te komen met het abonnement dat wordt gebruikt voor het VPN Gateway of het opslagaccount.
- **Resourcegroep:** de gewenste resourcegroep. Dit hoeft niet overeen te komen met de resourcegroep die wordt gebruikt voor de VPN Gateway of het opslagaccount.
- **Locatie:** de Azure-regio waarin de lokale netwerkgatewayresource moet worden gemaakt. Dit moet overeenkomen met de regio die u hebt geselecteerd voor VPN Gateway opslagaccount.

Selecteer **Maken om** de lokale netwerkgatewayresource te maken.  

## <a name="configure-on-premises-network-appliance"></a>On-premises netwerkapparaat configureren
De specifieke stappen voor het configureren van uw on-premises netwerkapparaat zijn afhankelijk van het netwerkapparaat dat uw organisatie heeft geselecteerd. Afhankelijk van het apparaat dat uw [](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) organisatie heeft gekozen, bevat de lijst met geteste apparaten mogelijk een koppeling naar de instructies van de leverancier van uw apparaat voor het configureren met Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>De site-naar-site-verbinding maken
Als u de implementatie van een S2S VPN wilt voltooien, moet u een verbinding maken tussen uw on-premises netwerkapparaat (vertegenwoordigd door de lokale netwerkgatewayresource) en de VPN Gateway. Als u dit wilt doen, gaat u naar de VPN Gateway u hierboven hebt gemaakt. Selecteer in de inhoudsopgave VPN Gateway de optie **Verbindingen** en klik op **Toevoegen.** Voor het **resulterende deelvenster** Verbinding toevoegen zijn de volgende velden vereist:

- **Naam:** de naam van de verbinding. Een VPN Gateway kan meerdere verbindingen hosten, dus kies een naam die nuttig is voor uw beheer die deze specifieke verbinding onderscheidt.
- **Verbindingstype:** omdat dit een S2S-verbinding is, selecteert u **Site-naar-site (IPSec)** in de vervolgkeuzelijst.
- **Virtuele netwerkgateway:** dit veld wordt automatisch geselecteerd voor de VPN Gateway u de verbinding maakt en kan niet worden gewijzigd.
- **Lokale netwerkgateway:** dit is de lokale netwerkgateway die u wilt verbinden met uw VPN Gateway. Het resulterende selectiedeelvenster moet de naam hebben van de lokale netwerkgateway die u hierboven hebt gemaakt.
- **Gedeelde sleutel (PSK)**: een combinatie van letters en cijfers, die wordt gebruikt om versleuteling voor de verbinding tot stand te brengen. Dezelfde gedeelde sleutel moet worden gebruikt in zowel het virtuele netwerk als de lokale netwerkgateways. Als uw gatewayapparaat er geen biedt, kunt u er hier een maken en het aan uw apparaat verstrekken.

Selecteer **OK** om de verbinding te maken. U kunt controleren of de verbinding is gemaakt via de **pagina** Verbindingen.

## <a name="mount-azure-file-share"></a>Een Azure-bestands share toevoegen 
De laatste stap bij het configureren van een S2S VPN is controleren of het werkt voor Azure Files. U kunt dit doen door uw Azure-bestands share on-premises te maken met het besturingssysteem van uw voorkeur. Zie de instructies voor het aan het besturingssysteem toe te staan hier:

- [Windows](storage-how-to-use-files-windows.md)
- [MacOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Zie ook
- [Azure Files netwerkoverzicht](storage-files-networking-overview.md)
- [Een punt-naar-site-VPN (P2S) configureren in Windows voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Een punt-naar-site-VPN (P2S) configureren in Linux voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md)