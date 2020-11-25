---
title: 'ExpressRoute-versleuteling configureren: IPsec via ExpressRoute voor Azure Virtual WAN'
description: Informatie over het gebruik van Azure Virtual WAN voor het maken van een site-naar-site-VPN-verbinding via ExpressRoute persoonlijke peering.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6c6f71277c276bed603989774637bd95999de333
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023518"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute-versleuteling: IPsec over ExpressRoute voor virtuele WAN

Dit artikel laat u zien hoe u met behulp van Azure Virtual WAN een IPsec/IKE-VPN-verbinding vanaf uw on-premises netwerk naar Azure kunt maken via de persoonlijke peering van een Azure ExpressRoute-circuit. Deze techniek kan een versleutelde door Voer bieden tussen de on-premises netwerken en Azure Virtual Networks via ExpressRoute, zonder het open bare Internet te passeren of open bare IP-adressen te gebruiken.

## <a name="topology-and-routing"></a>Topologie en route ring

In het volgende diagram ziet u een voor beeld van VPN-connectiviteit via ExpressRoute private-peering:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN via ExpressRoute":::

Het diagram toont een netwerk binnen het on-premises netwerk dat is verbonden met de Azure hub VPN-gateway via ExpressRoute private peering. De connectiviteits inrichting is eenvoudig:

1. Maak een ExpressRoute-verbinding met een ExpressRoute-circuit en privé-peering.
2. Stel de VPN-verbinding in, zoals beschreven in dit artikel.

Een belang rijk aspect van deze configuratie is de route ring tussen de on-premises netwerken en Azure via de ExpressRoute-en VPN-paden.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Verkeer van on-premises netwerken naar Azure

Voor verkeer van on-premises netwerken naar Azure worden de Azure-voor voegsels (met inbegrip van de virtuele hub en alle spoke Virtual Networks die zijn verbonden met de hub) geadverteerd via de ExpressRoute private peering BGP en de VPN BGP. Dit resulteert in twee netwerk routes (paden) naar Azure vanaf de on-premises netwerken:

- Een account via het met IPsec beveiligde pad
- Eén direct over ExpressRoute *zonder* IPSec-beveiliging 

Als u versleuteling wilt Toep assen op de communicatie, moet u ervoor zorgen dat voor het netwerk met VPN-verbinding in het diagram de Azure-routes via on-premises VPN-gateway worden voor rang boven het directe ExpressRoute-pad.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Verkeer van Azure naar on-premises netwerken

Dezelfde vereiste is van toepassing op het verkeer van Azure naar on-premises netwerken. U hebt twee opties om ervoor te zorgen dat het IPsec-pad de voor keur heeft boven het pad voor directe ExpressRoute (zonder IPsec):

- Adverteer meer specifieke voor voegsels op de VPN BGP-sessie voor het VPN-verbonden netwerk. U kunt een groter bereik aankondigen dat het VPN-verbonden netwerk omvat via ExpressRoute private peering, en vervolgens meer specifieke bereiken in de VPN BGP-sessie. Bijvoorbeeld, Adverteer 10.0.0.0/16 via ExpressRoute en 10.0.1.0/24 via VPN.

- Adverteer onafhankelijke voor voegsels adverteren voor VPN en ExpressRoute. Als de netwerkbereiken met VPN-verbindingen worden ontkoppeld van andere ExpressRoute verbonden netwerken, kunt u de voor voegsels in respectievelijk de BGP-sessies VPN en ExpressRoute aankondigen. Bijvoorbeeld, Adverteer 10.0.0.0/24 over ExpressRoute en 10.0.1.0/24 via VPN.

In beide voor beelden verzendt Azure verkeer naar 10.0.1.0/24 via de VPN-verbinding in plaats van rechtstreeks via ExpressRoute zonder VPN-beveiliging.

> [!WARNING]
> Als u *dezelfde* voor voegsels aankondigt voor zowel ExpressRoute-als VPN-verbindingen, wordt het pad ExpressRoute rechtstreeks zonder VPN-beveiliging gebruikt.
>

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Maak een virtueel WAN en een hub met gateways

De volgende Azure-resources en de bijbehorende on-premises configuraties moeten aanwezig zijn voordat u doorgaat:

- Een virtueel WAN van Azure
- Een virtuele WAN-hub met een [ExpressRoute-gateway](virtual-wan-expressroute-portal.md) en een [VPN-gateway](virtual-wan-site-to-site-portal.md)

Zie [een ExpressRoute-koppeling maken met behulp van Azure Virtual WAN](virtual-wan-expressroute-portal.md)voor de stappen voor het maken van een virtuele WAN van Azure en een hub met een ExpressRoute-koppeling. Zie [een site-naar-site-verbinding maken met behulp van Azure Virtual WAN](virtual-wan-site-to-site-portal.md)voor de stappen voor het maken van een VPN-gateway in het virtuele WAN.

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Maak een site voor het on-premises netwerk

De site bron is hetzelfde als de niet-ExpressRoute VPN-sites voor een virtueel WAN. Het IP-adres van het on-premises VPN-apparaat kan nu een privé-IP-adres zijn of een openbaar IP-adres in het on-premises netwerk dat bereikbaar is via ExpressRoute-persoonlijke peering die is gemaakt in stap 1.

> [!NOTE]
> Het IP-adres voor het on-premises VPN-apparaat *moet* deel uitmaken van de adres voorvoegsels die worden geadverteerd naar de virtuele WAN-hub via Azure ExpressRoute private peering.
>

1. Ga naar de Azure Portal in uw browser. 
1. Selecteer de hub die u hebt gemaakt. Selecteer op de pagina virtuele WAN-hub onder **connectiviteit** de optie **VPN-sites**.
1. Selecteer op de pagina **VPN-sites** **+ site maken**.
1. Vul de volgende velden in op de pagina **Site maken**:
   * **Abonnement**: Controleer het abonnement.
   * **Resource groep**: Selecteer of maak de resource groep die u wilt gebruiken.
   * **Regio**: Voer de Azure-regio in voor de bron van de VPN-site.
   * **Naam**: Voer de naam in waarnaar u wilt verwijzen naar uw on-premises site.
   * **Leverancier van apparaat**: Voer de leverancier van het on-PREMISES VPN-apparaat in.
   * **Border Gateway Protocol**: Selecteer ' inschakelen ' als uw on-premises netwerk gebruikmaakt van BGP.
   * **Privé adres ruimte**: Geef de IP-adres ruimte op die zich op uw on-premises site bevindt. Verkeer dat is bestemd voor deze adres ruimte, wordt via de VPN-gateway doorgestuurd naar het on-premises netwerk.
   * **Hubs**: Selecteer een of meer hubs om verbinding te maken met deze VPN-site. Voor de geselecteerde hubs moeten al VPN-gateways zijn gemaakt.
1. Selecteer **volgende: koppelingen >** voor de instellingen van de VPN-koppeling:
   * **Naam van koppeling**: de naam waarmee u naar deze verbinding wilt verwijzen.
   * **Provider naam**: de naam van de Internet serviceprovider voor deze site. Voor een ExpressRoute on-premises-netwerk is dit de naam van de ExpressRoute-service provider.
   * **Snelheid**: de snelheid van de verbinding met de Internet service of het ExpressRoute-circuit.
   * **IP-adres**: het open bare IP-adres van het VPN-apparaat dat zich op uw on-premises site bevindt. Of, voor ExpressRoute on-premises, is het het privé-IP-adres van het VPN-apparaat via ExpressRoute.

   Als BGP is ingeschakeld, wordt dit toegepast op alle verbindingen die zijn gemaakt voor deze site in Azure. Het configureren van BGP op een virtueel WAN is gelijk aan het configureren van BGP op een Azure VPN-gateway. 
   
   Uw on-premises BGP-peer adres *mag niet* gelijk zijn aan het IP-adres van uw VPN naar het apparaat of de adres ruimte van het virtuele netwerk van de VPN-site. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Het kan echter *geen* APIPA zijn (169,254.*x*. *x*)-adres. Specificeer dit adres in de bijbehorende lokale netwerk gateway die de locatie vertegenwoordigt. Zie [Over BGP met Azure VPN-gateway](../vpn-gateway/vpn-gateway-bgp-overview.md) voor BGP-vereisten.

1. Selecteer **volgende: controleren + maken >** om de instellings waarden te controleren en de VPN-site te maken. Als u **hubs** hebt geselecteerd om verbinding te maken, wordt de verbinding tot stand gebracht tussen het on-premises netwerk en de hub VPN-gateway.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. werk de instelling voor de VPN-verbinding voor het gebruik van ExpressRoute

Nadat u de VPN-site hebt gemaakt en verbinding maakt met de hub, gebruikt u de volgende stappen om de verbinding te configureren voor het gebruik van ExpressRoute-persoonlijke peering:

1. Ga terug naar de virtuele WAN-bron pagina en selecteer de hub-resource. Of navigeer van de VPN-site naar de verbonden hub.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="Een hub selecteren":::
1. Onder **connectiviteit** selecteert u **VPN (site-naar-site)**.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="VPN selecteren (site-naar-site)":::
1. Selecteer het beletsel teken (**...**) op de VPN-site via ExpressRoute en selecteer **VPN-verbinding met deze hub bewerken**.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="Configuratie menu invoeren":::
1. Selecteer **Ja** als **u het privé-IP-adres van Azure wilt gebruiken**. Met deze instelling configureert u de hub VPN-gateway voor het gebruik van privé-IP-adressen binnen het hub-adres bereik op de gateway voor deze verbinding, in plaats van de open bare IP-adressen. Dit zorgt ervoor dat het verkeer van het on-premises netwerk het ExpressRoute persoonlijke peering-paden passeert in plaats van het open bare Internet voor deze VPN-verbinding te gebruiken. In de volgende scherm afbeelding ziet u de instelling:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="Instelling voor het gebruik van een privé-IP-adres voor de VPN-verbinding" border="false":::
1. Selecteer **Opslaan**.

Nadat u uw wijzigingen hebt opgeslagen, maakt de hub VPN-gateway gebruik van de privé-IP-adressen op de VPN-gateway om de IPsec/IKE-verbindingen met het on-premises VPN-apparaat via ExpressRoute te maken.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. de privé-IP-adressen voor de hub VPN-gateway ophalen

Down load de configuratie van het VPN-apparaat om de privé-IP-adressen van de hub VPN-gateway op te halen. U hebt deze adressen nodig voor het configureren van het on-premises VPN-apparaat.

1. Selecteer op de pagina voor uw hub **VPN (site-naar-site)** onder **connectiviteit**.
1. Selecteer boven aan de pagina **overzicht** de optie **VPN-configuratie downloaden**. 

   In azure wordt een opslag account gemaakt in de resource groep ' micro soft-Network-[locatie] ', waarbij *locatie* de locatie van het WAN is. Nadat u de configuratie hebt toegepast op uw VPN-apparaten, kunt u dit opslag account verwijderen.
1. Nadat het bestand is gemaakt, selecteert u de koppeling om het te downloaden.
1. Pas de configuratie toe op uw VPN-apparaat.

### <a name="vpn-device-configuration-file"></a>Configuratie bestand voor VPN-apparaat

Het apparaatconfiguratie-bestand bevat de instellingen die moeten worden gebruikt bij het configureren van uw on-premises VPN-apparaat. Wanneer u dit bestand bekijkt, ziet u de volgende informatie:

* **vpnSiteConfiguration**: in deze sectie worden de apparaatgegevens aangegeven die zijn ingesteld als een site die verbinding maakt met het virtuele WAN. Hierin zijn de naam en het open bare IP-adres van het vertakkings apparaat opgenomen.
* **vpnSiteConnections**: in deze sectie vindt u informatie over de volgende instellingen:

    * De adres ruimte van het virtuele netwerk van de virtuele hub.<br/>Voorbeeld:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * De adres ruimte van de virtuele netwerken die zijn verbonden met de hub.<br>Voorbeeld:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * IP-adressen van de VPN-gateway van de virtuele hub. Omdat elke verbinding van de VPN-gateway bestaat uit twee tunnels in actief/actief-configuratie, ziet u beide IP-adressen die in dit bestand staan vermeld. In dit voor beeld ziet u `Instance0` en `Instance1` voor elke site, en ze zijn privé-IP-adressen in plaats van open bare IP-adressen.<br>Voorbeeld:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Configuratie gegevens voor de VPN-gateway verbinding, zoals BGP en vooraf gedeelde sleutel. De vooraf gedeelde sleutel wordt automatisch voor u gegenereerd. U kunt de verbinding altijd bewerken op de **overzichts** pagina voor een aangepaste vooraf gedeelde sleutel.
  
### <a name="example-device-configuration-file"></a>Voorbeeld van een apparaatconfiguratiebestand

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Uw VPN-apparaat configureren

Als u instructies nodig hebt voor het configureren van uw apparaat, kunt u de instructies op de [pagina met configuratiescripts voor VPN-apparaten](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) gebruiken. Houd in dat geval wel rekening met de volgende dingen:

* De instructies op de pagina VPN-apparaat worden niet geschreven voor een virtueel WAN. Maar u kunt de virtuele WAN-waarden uit het configuratie bestand gebruiken om het VPN-apparaat hand matig te configureren. 
* De Download bare configuratie scripts voor het configureren van de VPN-gateway werken niet voor het virtuele WAN, omdat de configuratie verschilt.
* Een nieuw virtueel WAN kan zowel IKEv1 als IKEv2 ondersteunen.
* Een virtueel WAN kan alleen op route gebaseerde VPN-apparaten en-instructies gebruiken.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. uw virtuele WAN weer geven

1. Ga naar het virtuele WAN.
1. Op de pagina **Overzicht** vertegenwoordigt elk punt op de kaart een hub.
1. In de sectie **hubs en verbindingen** kunt u de status van de hub, site, regio en VPN-verbinding weer geven. U kunt ook bytes weer geven in en uit.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. een verbinding bewaken

Maak een verbinding om de communicatie tussen een virtuele machine van Azure en een externe site te bewaken. Zie voor meer informatie over het instellen van een verbindingscontrole de pagina [Netwerkcommunicatie bewaken](~/articles/network-watcher/connection-monitor.md). Het Bron veld is het VM-IP-adres in Azure en het doel-IP-adres is het IP-adres van de site.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. resources opschonen

Wanneer u deze resources niet meer nodig hebt, kunt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resource groep en alle resources die deze bevat, te verwijderen. Voer de volgende Power shell-opdracht uit en vervang door `myResourceGroup` de naam van uw resource groep:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel helpt u bij het maken van een VPN-verbinding via ExpressRoute private peering met behulp van Virtual WAN. Zie voor meer informatie over Virtual WAN en gerelateerde functies het [virtuele WAN-overzicht](virtual-wan-about.md).
