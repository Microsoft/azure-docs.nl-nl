---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627695"
---
1. Selecteer **VPN-sites** in het onderdeel **Connectiviteit** op de portaalpagina voor uw virtuele WAN om de pagina voor VPN-sites te openen.
2. Op de pagina **VPN sites** klikt u op **+Site maken**.

   ![Scherm afbeelding toont het venster site-naar-site met het deel venster VPN-site maken geopend.](./media/virtual-wan-tutorial-site-include/basics.png "Basisbeginselen")
3. Op het tabblad **Basisinstellingen** van de pagina **VPN-sites maken** vult u de volgende velden in:

    * **Regio** - voorheen locatie genoemd. Dit is de locatie waar u deze websiteresource in wilt maken.
    * **Naam** - de naam waarmee u naar uw on-premises site wilt verwijzen.
    * **Leverancier van het apparaat** - de naam van de leverancier van het VPN-apparaat (bijvoorbeeld: Citrix, Cisco, Barracuda). Het toevoegen van de leverancier van het apparaat kan het Azure-team helpen beter inzicht te krijgen in uw omgeving om in de toekomst extra optimalisatie mogelijkheden toe te voegen of om u te helpen bij het oplossen van problemen.
    * **Border Gateway Protocol** : de instelling **inschakelen** impliceert dat voor alle verbindingen van de site BGP is ingeschakeld. Uiteindelijk moet u de BGP-gegevens voor elke koppeling van de VPN-site instellen in de sectie koppelingen. Het configureren van BGP op een virtueel WAN is gelijk aan het configureren van BGP op een virtuele Azure-netwerk gateway VPN. Het adres van uw on-premises BGP-peer mag niet gelijk zijn aan het openbare IP-adres van uw VPN-apparaat of het de VNet-adresruimte van de VPN-site. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Specificeer dit adres in de bijbehorende VPN-site die de locatie vertegenwoordigt. Zie [Over BGP met Azure VPN-gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) voor BGP-vereisten. U kunt altijd een VPN-verbinding bewerken om de BGP-para meters (peering-IP op de koppeling en de als #) bij te werken zodra de BGP-instelling VPN-site is ingeschakeld.
    * **Privié-adresruimte** - de IP-adresruimte op uw on-premises site. Verkeer dat bestemd is voor deze adresruimte wordt doorgestuurd naar uw lokale site. Dit is vereist wanneer BGP niet is ingeschakeld voor de site.
    
      >[!NOTE]
      >Als u de adresruimte bewerkt nadat u de site hebt gemaakt (bijvoorbeeld als u een extra adresruimte toevoegt), kan het 8-10 minuten duren om de efficiënte routes bij te werken terwijl de onderdelen opnieuw worden gemaakt.
      >
    * **Hubs**: de hub waarmee u de site wilt verbinden. Een site kan alleen worden verbonden met hubs die een VPN-gateway hebben. Als u een hub niet ziet, maak dan eerst een VPN-gateway in die hub.
4. Selecteer **koppelingen** om informatie over de fysieke koppelingen op de vertakking toe te voegen. Als u een virtueel WAN-partner CPE-apparaat hebt, kunt u contact opnemen met de apparaten om te zien of deze gegevens worden uitgewisseld met Azure als onderdeel van de gegevens die vanuit hun systemen worden geüpload.

   ![Scherm afbeelding toont het deel venster VPN-site maken met het tabblad koppelingen geselecteerd.](./media/virtual-wan-tutorial-site-include/links.png "Koppelingen")

    * **Koppelingsnaam**: een naam die u wilt opgeven voor de fysieke koppeling op de VPN-site. Voorbeeld: mylink1.
    * **Providernaam**: de naam van de fysieke koppeling op de VPN-site. Voorbeeld: ATT, Verizon.
    * **Snelheid** - Dit is de snelheid van het VPN-apparaat in de filiaallocatie. Voorbeeld: 50, wat betekent dat 50 Mbps de snelheid is van het VPN-apparaat op de vertakkingssite.
    * **IP-adres/FQDN** - Openbaar IP-adres van het on-premises apparaat dat deze koppeling gebruikt. U kunt het privé-IP-adrs van uw on-premises VPN-apparaat dat achter ExpressRoute zit opgeven. U kunt ook een fully qualified somain name opgeven. Bijvoorbeeld *something.contoso.com*. De FQDN moet omzetbaar zijn vanuit de VPN-gateway. Dit is mogelijk als de DNS-server waarop deze FQDN gehost wordt bereikbaar is via internet. IP-adres heeft voorrang wanneer zowel het IP-adres als de FQDN zijn opgegeven.

      >[!NOTE]
      >
      >* Ondersteunt één IPv4-adres per FQDN. Als de FQDN wordt omgezet in meerdere IP-adressen, dan kiest de VPN-gateway het eerste IP4-adres van de lijst. Momenteel worden IPv6-adressen niet ondersteund.
      >
      >* VPN-gateway onderhoudt een DNS-cache die om de vijf minuten wordt vernieuwd. De gateway probeert enkel FQDN's op te lossen voor niet-verbonden tunnels. Een gateway opnieuw instellen of de configuratie ervan wijzigen kan ook FQDN-omzetting activeren.
      >
5. U kunt het selectie vakje gebruiken om meer koppelingen te verwijderen of toe te voegen. Er worden vier koppelingen per VPN-site ondersteund. Als u bijvoorbeeld vier Isp's (Internet provider) op de vertakkings locatie hebt, kunt u vier koppelingen maken, één per provider en de informatie voor elke koppeling opgeven.
6. Wanneer u klaar bent met het invullen van de velden, selecteert u **Beoordelen + maken** om de site te controleren en te maken.
7. Bekijk de status op de pagina met de VPN-sites. De site gaat naar **Verbinding nodig**, omdat deze nog niet is verbonden met de hub.
