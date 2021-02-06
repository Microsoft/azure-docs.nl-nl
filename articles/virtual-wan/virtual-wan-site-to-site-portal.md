---
title: 'Zelfstudie: Azure Virtual WAN gebruiken om site-naar-site-verbindingen te maken'
description: In deze zelfstudie leert u hoe u Azure Virtual WAN kunt gebruiken om een site-naar-site-VPN-verbinding te maken met Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: f3458c3b12b3151fd20531282f56ed2f1fd29b6b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627696"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Zelfstudie: Een site-naar-site-verbinding maken met Azure Virtual WAN

In deze zelfstudie leert u hoe u Virtual WAN kunt gebruiken om verbinding te maken met uw resources in Azure via een VPN-verbinding met IPsec/IKE (IKEv1 en IKEv2). Voor dit type verbinding moet er on-premises een VPN-apparaat aanwezig zijn waaraan een extern openbaar IP-adres is toegewezen. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel WAN maken
> * Een hub maken
> * Een site maken
> * Een site verbinden met een hub
> * Een VPN-site verbinden met een hub
> * Een VNet verbinden met een hub
> * Een configuratiebestand downloaden
> * Uw VPN-gateway configureren

> [!NOTE]
> Als u veel sites hebt, zou u normaal gesproken een [Virtual WAN-partner](https://aka.ms/virtualwan) gebruiken om deze configuratie te maken. U kunt deze configuratie echter ook zelf maken als bekend bent met netwerktechnologie en uw eigen VPN-apparaat kunt configureren.
>

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

## <a name="prerequisites"></a>Vereisten

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Een virtueel WAN maken

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Een hub maken

Een hub is een virtueel netwerk dat gateways kan bevatten voor site-naar-site-, ExpressRoute- of punt-naar-site-functionaliteit. Zodra de hub is gemaakt, worden u kosten in rekening gebracht voor de hub, zelfs als u geen sites hebt toegevoegd. Het duurt dertig minuten om de site-naar-site-VPN-gateway in de virtuele hub te maken.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Een site maken

In deze sectie maakt u een site. Sites komen overeen met uw fysieke locaties. Maak zoveel sites als u nodig hebt. Als u bijvoorbeeld een filiaal in NY, een filiaal in Londen en een filiaal in LA hebt, maakt u drie afzonderlijke sites. Deze sites bevatten de eindpunten voor uw on-premises VPN-apparaat. U kunt Maxi maal 1000 sites per virtuele hub maken in een virtueel WAN. Als u meerdere hubs hebt, kunt u per hub duizend sites maken. Als u een virtueel WAN-partner CPE-apparaat hebt, kunt u hier contact mee vinden voor meer informatie over automatisering naar Azure. Normaal gesp roken impliceert Automation een eenvoudige klik ervaring voor het exporteren van grootschalige vertakkings gegevens naar Azure en het instellen van connectiviteit vanaf de CPE tot Azure Virtual WAN VPN gateway. Zie [Richtlijnen voor automatisering van Azure naar CPE-partners](virtual-wan-configure-automation-providers.md) voor meer informatie.

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>De VPN-site verbinden met de hub

In deze stap verbindt u uw VPN-site met de hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>VNet verbinden met de hub

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>VPN-configuratie downloaden

Gebruik de VPN-apparaatconfiguratie om uw on-premises VPN-apparaat te configureren.

1. Klik op de pagina voor uw virtuele WAN op **Overzicht**.
2. Klik boven aan de pagina **Hub -> VPNSite** op **VPN-configuratie downloaden**. Azure maakt een opslagaccount in de resourcegroep 'microsoft-network-[location]', waarbij 'location' wordt vervangen door de locatie van het WAN. Nadat u de configuratie hebt toegepast op uw VPN-apparaten, kunt u dit storage-account verwijderen.
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Pas de configuratie toe op uw on-premises VPN-apparaat.

### <a name="about-the-vpn-device-configuration-file"></a>Over het configuratie bestand voor het VPN-apparaat

Het apparaatconfiguratiebestand bevat de instellingen die u dient te gebruiken om uw on-premises VPN-apparaat te configureren. Wanneer u dit bestand bekijkt, ziet u de volgende informatie:

* **vpnSiteConfiguration -** in deze sectie vindt u de apparaatgegevens, ingesteld als een site die verbinding maakt met het virtuele WAN. Hier vindt u ook de naam en het openbare ip-adres van het branch-apparaat.
* **vpnSiteConnections -** deze sectie bevat informatie over de volgende instellingen:

    * **Adres ruimte** van de virtuele hub (s) VNet.<br>Voorbeeld:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * De **adres ruimte** van de VNets die zijn verbonden met de hub.<br>Voorbeeld:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP-adressen** van de VPN-gateway van de virtuele hub. Omdat elke verbinding van de VPN-gateway is opgebouwd uit twee tunnels in een actief/actief-configuratie, worden beide IP-adressen in dit bestand vermeld. In dit voorbeeld ziet u voor elke site 'Instance0' en 'Instance1'.<br>Voorbeeld:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Informatie over verbindingsconfiguratie van VPN-gateway**, zoals BGP, vooraf-gedeelde sleutels, enzovoort. De PSK is de vooraf gedeelde sleutel die automatisch voor u wordt gegenereerd. U kunt altijd de verbinding bewerken op de pagina Overzicht om een aangepaste PSK in te stellen.
  
### <a name="example-device-configuration-file"></a>Voorbeeld van een apparaatconfiguratiebestand

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Uw VPN-apparaat configureren

>[!NOTE]
> Als u met een Azure Virtual WAN-oplossing van een partner werkt, wordt het VPN-apparaat automatisch geconfigureerd. De apparaatcontroller haalt het configuratiebestand op uit Azure en past dit toe op het apparaat om zo de verbinding met Azure in te stellen. U hoeft in dat geval dus niet te weten hoe u uw VPN-apparaat handmatig configureert.
>

Als u instructies nodig hebt voor het configureren van uw apparaat, kunt u de instructies op de [pagina met configuratiescripts voor VPN-apparaten](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) gebruiken. Houd in dat geval wel rekening met de volgende dingen:

* De instructies op de pagina voor VPN-apparaten zijn niet geschreven voor Virtual WAN, maar u kunt de waarden voor Virtual WAN in het configuratiebestand gebruiken om uw VPN-apparaat handmatig te configureren. 
* De downloadbare apparaatconfiguratiescripts voor VPN Gateway werken niet voor Virtual WAN, omdat de configuratie anders is.
* Een nieuw virtueel WAN kan ondersteuning bieden voor zowel IKEv1 als IKEv2.
* Virtual WAN kan gebruikmaken van op beleid en op route gebaseerde VPN-apparaten en apparaatinstructies.

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>Uw VPN-gateway configureren

U kunt de instellingen van uw VPN-gateway op elk gewenst moment weergeven en configureren door **Weergeven/configureren** te selecteren.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="U kunt deze configuratie echter ook zelf maken als bekend bent met netwerktechnologie en uw eigen VPN-apparaat kunt configureren." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

Op de pagina **VPN-gateway bewerken** ziet u de volgende instellingen:

* Openbaar IP-adres van VPN-gateway (toegewezen door Azure)
* Privé-IP-adres van VPN-gateway (toegewezen door Azure)
* Standaard BGP-IP-adres van VPN-gateway (toegewezen door Azure)
* Configuratie-optie voor aangepast BGP-IP-adres: Dit veld is voorbehouden voor APIPA (Automatic Private IP Addressing). Azure ondersteunt BGP-IP in het bereik 169.254.21.* en 169.254.22.*. Azure accepteert BGP-verbindingen in deze bereiken maar zal verbinding maken met het standaard BGP-IP-adres.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="Configuratie weergeven" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN:

> [!div class="nextstepaction"]
> * [Veelgestelde vragen over Virtual WAN](virtual-wan-faq.md)
