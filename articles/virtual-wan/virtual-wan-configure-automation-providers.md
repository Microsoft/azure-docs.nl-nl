---
title: Automatiserings richtlijnen voor Azure Virtual WAN partners | Microsoft Docs
description: Stel een automatiserings omgeving in om verbinding te maken en een on-premises VPN-of SD-WAN-of-vertakkings apparaat te configureren voor Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566466"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Automatiseringsrichtlijnen voor Virtual WAN-partners

In dit artikel wordt uitgelegd hoe u de automatiserings omgeving kunt instellen om verbinding te maken en een vertakkings apparaat (een klant on-premises VPN-apparaat of SDWAN CPE) te configureren voor Azure Virtual WAN. Als u een provider bent die vertakkings apparaten biedt die VPN-verbindingen via IPsec/IKEv2 of IPsec/IKEv1 kunnen bevatten, is dit artikel voor u.

Een vertakkings apparaat (een on-premises VPN-apparaat of SDWAN CPE) van een klant gebruikt meestal een controller/apparaat-dash board dat moet worden ingericht. Beheerders van SD-WAN-oplossingen kunnen vaak een beheer console gebruiken om een apparaat vooraf in te richten voordat het wordt aangesloten op het netwerk. Op dit apparaat dat geschikt is voor VPN, wordt de besturings vlak logica van een controller opgehaald. Het VPN-apparaat of de SD-WAN-controller kan gebruikmaken van Azure Api's om de connectiviteit met Azure Virtual WAN te automatiseren. Voor dit type verbinding moet op het on-premises apparaat een extern gericht openbaar IP-adres worden toegewezen.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Voordat u begint met het automatiseren van

* Controleer of het apparaat IPsec-IKEv1/IKEv2 ondersteunt. Zie [standaard beleidsregels](#default).
* Bekijk de [rest-api's](#additional) die u gebruikt om de verbinding met Azure Virtual WAN te automatiseren.
* De portal-ervaring van Azure Virtual WAN testen.
* Bepaal vervolgens welk deel van de verbindings stappen u wilt automatiseren. We raden u aan ten minste het automatiseren van:

  * Toegangsbeheer
  * Gegevens van Branch-apparaten uploaden naar virtuele WAN van Azure
  * Azure-configuratie downloaden en connectiviteit van het vertakkings apparaat instellen in azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Aanvullende informatie

* [Rest API](/rest/api/virtualwan/virtualhubs) om het maken van virtuele hub te automatiseren
* [Rest API](/rest/api/virtualwan/vpngateways) voor het automatiseren van Azure VPN-gateway voor virtueel WAN
* [Rest API](/rest/api/virtualwan/vpnconnections) om een VPNSite te verbinden met een Azure VPN-hub
* [Standaard IPsec-beleid](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Gebruikers ervaring

Inzicht in de verwachte ervaring van de klant in combi natie met Azure Virtual WAN.

  1. Normaal gesp roken start een virtuele WAN-gebruiker het proces door een virtuele WAN-resource te maken.
  2. De gebruiker stelt een service op basis van de toegang tot de resource groep in voor het on-premises systeem (uw vertakkings controller of het inrichten van het VPN-apparaat) om vertakkings gegevens te schrijven naar een virtueel WAN van Azure.
  3. De gebruiker kan op dit moment besluiten om u aan te melden bij uw gebruikers interface en de referenties voor de Service-Principal in te stellen. Als dat is voltooid, moet de controller vertakkings informatie kunnen uploaden met de Automation die u gaat bieden. Het hand matige equivalent van dit aan de Azure-zijde is ' site maken '.
  4. Zodra de gegevens van de site (vertakkings apparaat) beschikbaar zijn in azure, wordt de gebruiker verbonden met een hub. Een virtuele hub is een virtueel netwerk dat door micro soft wordt beheerd. De hub bevat verschillende service-eindpunten die verbindingen vanaf uw on-premises netwerk (vpnsite) mogelijk maken. De hub is de kern van uw netwerk in een regio. Er kan slechts één hub per Azure-regio en het VPN-eind punt (vpngateway) worden gemaakt tijdens dit proces. De VPN-gateway is een schaal bare gateway die op de juiste manier grootten is gebaseerd op de band breedte en de verbindings behoeften. U kunt ervoor kiezen om de virtuele hub te automatiseren en vpngateway te maken op basis van het dash board van uw vertakkings controller.
  5. Zodra de virtuele hub aan de site is gekoppeld, wordt een configuratie bestand gegenereerd voor de gebruiker om het hand matig te downloaden. Dit is de plaats waar uw automatisering is ingebouwd en de gebruikers ervaring naadloos maakt. In plaats van de gebruiker hoeft het vertakkings apparaat niet hand matig te downloaden en te configureren, kunt u de automatisering instellen en minimale click-through-ervaring bieden op uw gebruikers interface, waardoor typische verbindings problemen, zoals gedeelde sleutels niet overeenkomen, de IPSec-para meter niet overeenkomen, de Lees baarheid van het configuratie bestand, enzovoort.
  6. Aan het eind van deze stap in uw oplossing heeft de gebruiker een naadloze site-naar-site-verbinding tussen het vertakkings apparaat en de virtuele hub. U kunt ook extra verbindingen instellen tussen andere hubs. Elke verbinding is een actief/actief-tunnel. Uw klant kan ervoor kiezen om een andere ISP te gebruiken voor elk van de koppelingen voor de tunnel.
  7. Overweeg probleemoplossings-en bewakings mogelijkheden te bieden in de CPE-beheer interface. Typische scenario's zijn onder andere ' klant kan geen toegang krijgen tot Azure-resources vanwege een CPE-probleem ', ' IPsec-para meters weer geven aan de CPE-zijde ' etc.

## <a name="automation-details"></a><a name ="understand"></a>Details van automatisering

###  <a name="access-control"></a><a name="access"></a>Toegangsbeheer

Klanten moeten het juiste toegangs beheer voor virtuele WAN-verbindingen kunnen instellen in de gebruikers interface van het apparaat. U wordt aangeraden een Azure-Service-Principal te gebruiken. Toegang op basis van de Service-Principal zorgt voor de juiste verificatie voor het uploaden van vertakkings informatie. Zie [Create Service Principal](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)(Engelstalig) voor meer informatie. Hoewel deze functionaliteit buiten de Azure Virtual WAN-aanbieding valt, vermelden we de gebruikelijke stappen voor het instellen van de toegang in azure, waarna de relevante details worden gegenereerd in het dash board voor Apparaatbeheer

* Maak een Azure Active Directory-toepassing voor uw on-premises apparaat controller.
* Toepassings-ID en verificatie sleutel ophalen
* Tenant-id ophalen
* Toepassing toewijzen aan rol ' bijdrager '

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Gegevens van vertakkings apparaten uploaden

U moet de gebruikers ervaring ontwerpen om vertakkings gegevens (on-premises site) naar Azure te uploaden. U kunt [rest-api's](/rest/api/virtualwan/vpnsites) voor VPNSite gebruiken om de site-informatie in virtuele WAN te maken. U kunt alle Branch SDWAN/VPN-apparaten opgeven of zo nodig aanpassingen van het apparaat selecteren.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Apparaatconfiguratie downloaden en connectiviteit

Deze stap omvat het downloaden van de Azure-configuratie en het instellen van connectiviteit vanuit het vertakkings apparaat in azure Virtual WAN. In deze stap zal een klant die geen provider gebruikt, de Azure-configuratie hand matig downloaden en Toep assen op hun on-premises SDWAN/VPN-apparaat. Als provider kunt u deze stap automatiseren. Bekijk de [rest-api's](/rest/api/virtualwan/vpnsitesconfiguration/download) voor downloaden voor meer informatie. De apparaat-controller kan ' GetVpnConfiguration ' aanroepen REST API de Azure-configuratie te downloaden.

**Configuratie notities**

  * Als Azure VNets zijn gekoppeld aan de virtuele hub, worden ze weer gegeven als ConnectedSubnets.
  * VPN-connectiviteit maakt gebruik van op route gebaseerde configuratie en ondersteunt zowel IKEv1-als IKEv2-protocollen.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Configuratie bestand voor het apparaat

Het apparaatconfiguratiebestand bevat de instellingen die u dient te gebruiken om uw on-premises VPN-apparaat te configureren. Wanneer u dit bestand bekijkt, ziet u de volgende informatie:

* **vpnSiteConfiguration -** in deze sectie vindt u de apparaatgegevens, ingesteld als een site die verbinding maakt met het virtuele WAN. Hier vindt u ook de naam en het openbare ip-adres van het branch-apparaat.
* **vpnSiteConnections -** deze sectie bevat informatie over het volgende:

    * **Adres ruimte** van de virtuele hub (s) VNet.<br>Voorbeeld:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * De **adres ruimte** van de VNets die zijn verbonden met de hub.<br>Voorbeeld:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP-adressen** van de VPN-gateway van de virtuele hub. Omdat voor de VPN-gateway elke verbinding is opgebouwd uit 2 tunnels in een actief-/actief-configuratie, worden beide IP-adressen in dit bestand vermeld. In dit voorbeeld ziet u voor elke site 'Instance0' en 'Instance1'.<br>Voorbeeld:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Informatie over verbindingsconfiguratie van VPN-gateway**, zoals BGP, vooraf-gedeelde sleutels, enzovoort. De PSK is de vooraf gedeelde sleutel die automatisch voor u wordt gegenereerd. U kunt altijd de verbinding bewerken op de pagina Overzicht om een aangepaste PSK in te stellen.
  
**Voorbeeld van een apparaatconfiguratiebestand**

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

## <a name="connectivity-details"></a><a name="default"></a>Connectiviteits gegevens

Uw on-premises SDWAN/VPN-apparaat of de configuratie van de SD-WAN moeten overeenkomen met de volgende algoritmen en para meters, die u opgeeft in het Azure IPsec/IKE-beleid.

* IKE-versleutelingsalgoritme
* IKE-integriteitsalgoritme
* DH-groep
* IPsec-versleutelingsalgoritme
* IPsec-integriteitsalgoritme
* PFS-groep

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Standaardbeleid voor IPsec-connectiviteit

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Aangepast beleid voor IPsec-connectiviteit

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [informatie over Azure Virtual WAN](virtual-wan-about.md) en de [Veelgestelde vragen over Azure Virtual WAN](virtual-wan-faq.md)voor meer informatie over Virtual WAN.

Voor aanvullende informatie kunt u een e-mail sturen naar <azurevirtualwan@microsoft.com> . Vermeld uw bedrijfsnaam tussen '[ ]' in de onderwerpregel.