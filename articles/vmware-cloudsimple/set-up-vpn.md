---
title: Azure VMware-oplossing door CloudSimple-VPN configureren tussen on-premises en Privécloud
description: Hierin wordt beschreven hoe u een site-naar-site-of punt-naar-site-VPN-verbinding configureert tussen uw on-premises netwerk en uw CloudSimple-Privécloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b7a119b538ee07b961011bfa98d748f92cc76f0e
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899215"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Een VPN-verbinding met uw CloudSimple-Privécloud configureren

Met VPN-gateways kunt u op afstand verbinding maken met CloudSimple-netwerk vanaf uw on-premises netwerk en vanaf een client computer.  In dit artikel vindt u informatie over het instellen van VPN-gateways in de CloudSimple-Portal.  Een VPN-verbinding tussen uw on-premises netwerk en uw CloudSimple-netwerk biedt toegang tot de vCenter en workloads in uw Privécloud. CloudSimple biedt ondersteuning voor Point-to-site VPN-en site-naar-site VPN-gateways.

## <a name="vpn-gateway-types"></a>VPN-gateway typen

* **Punt-naar-site-VPN-** verbinding is de eenvoudigste manier om verbinding te maken met uw privécloud vanaf uw computer. Gebruik een punt-naar-site-VPN-verbinding om extern verbinding te maken met de Privécloud.
* Met **site-naar-site-VPN-** verbinding kunt u uw werk belastingen voor de privécloud instellen voor toegang tot on-premises Services. U kunt ook on-premises Active Directory als een identiteits bron gebruiken voor verificatie bij uw Privécloud.  Op dit moment wordt **op beleid gebaseerd VPN-** type ondersteund.

In een regio kunt u een site-naar-site-VPN-gateway en één punt-naar-site-VPN-gateway maken.

## <a name="point-to-site-vpn"></a>Punt-naar-site-VPN

Zie [Create Point-to-site VPN gateway](vpn-gateway.md#create-point-to-site-vpn-gateway)om een punt-naar-site-VPN-gateway te maken.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Verbinding maken met CloudSimple met behulp van punt-naar-site-VPN

VPN-client is nodig om vanaf uw computer verbinding te maken met CloudSimple.  Down load de [openvpn-client](https://openvpn.net/community-downloads/) voor Windows of [viscositeit](https://www.sparklabs.com/viscosity/download/) voor macOS en OS X.

1. Start de CloudSimple-Portal en selecteer **netwerk**.
2. Selecteer **VPN gateway**.
3. Klik in de lijst met VPN-gateways op de punt-naar-site-VPN-gateway.
4. Selecteer **Gebruikers**.
5. Klik op **mijn VPN-configuratie downloaden**

    ![VPN-configuratie downloaden](media/download-p2s-vpn-configuration.png)

6. De configuratie op uw VPN-client importeren

    * Instructies voor het [importeren van de configuratie op Windows-client](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instructies voor het [importeren van de configuratie in macOS of OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Verbinding maken met CloudSimple VPN-gateway.

Hieronder ziet u een voor beeld van het importeren van een verbinding met de **viscositeit-client**.

#### <a name="import-connection-on-viscosity-client"></a>Verbinding importeren op de viscositeit-client

1. Pak de inhoud van de VPN-configuratie uit naar het gedownloade zip-bestand.

2. Open viscositeit op uw computer.

3. Klik op het **+** pictogram en selecteer **verbinding importeren**  >  **uit bestand**.

    ![VPN-configuratie importeren uit bestand](media/import-p2s-vpn-config.png)

4. Selecteer het OpenVPN-configuratie bestand (. ovpn) voor het protocol dat u wilt gebruiken en klik op **openen**.

    ![Scherm opname van de OpenVPN-configuratie bestanden die u kunt selecteren.](media/import-p2s-vpn-config-choose-ovpn.png)

De verbinding wordt nu weer gegeven in het menu viscositeit.

#### <a name="connect-to-the-vpn"></a>Maak verbinding met het VPN.

Als u verbinding wilt maken met VPN met behulp van de viscositeit OpenVPN-Client, selecteert u de verbinding in het menu. Het menu pictogram wordt bijgewerkt om aan te geven dat de verbinding tot stand is gebracht.

![Scherm opname van de CloudSimple VPN-connectiviteits status.](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Verbinding maken met meerdere persoonlijke Clouds

Met een punt-naar-site-VPN-verbinding worden de DNS-namen van de eerste Privécloud die u maakt, omgezet. Wanneer u toegang wilt krijgen tot andere persoonlijke Clouds, moet u de DNS-server bijwerken op uw VPN-client.

1. Start de [CloudSimple-Portal](access-cloudsimple-portal.md).

2. Navigeer naar   >  **persoonlijke Clouds** van resources en selecteer de privécloud waarmee u verbinding wilt maken.

3. Op de pagina **samen vatting** van de privécloud kopieert u het IP-adres van de DNS-server van de Privécloud onder **basis informatie**.

    ![DNS-servers in privécloud](media/private-cloud-dns-server.png)

4. Klik met de rechter muisknop op het Viscositeits pictogram in de systeem balk van de computer en selecteer **voor keuren**.

    ![VPN](media/vis00.png)

5. Selecteer de CloudSimple-VPN-verbinding.

    ![VPN-verbinding](media/viscosity-client.png)

6. Klik op **bewerken** om de verbindings eigenschappen te wijzigen.

    ![VPN-verbinding bewerken](media/viscosity-edit-connection.png)

7. Klik op het tabblad **netwerken** en voer de IP-adressen van de DNS-server voor de privécloud in, gescheiden door een komma of een spatie en het domein ```cloudsimple.io``` .  Selecteer **DNS-instellingen negeren die worden verzonden door de VPN-server**.

    ![VPN-netwerken](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Als u verbinding wilt maken met uw eerste Privécloud, verwijdert u deze instellingen en maakt u verbinding met de VPN-server.

## <a name="site-to-site-vpn"></a>Site-naar-site-VPN

Zie [site-naar-site-VPN-gateway maken](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)voor het maken van een site-naar-site-VPN-gateway.  Site-naar-site-VPN-verbinding van uw on-premises netwerk naar uw Privécloud biedt deze voor delen.  

* Toegankelijkheid van uw persoonlijke Cloud-vCenter vanaf elk werk station in uw on-premises netwerk
* Gebruik van uw on-premises Active Directory als een vCenter-identiteits bron
* Handige overdracht van VM-sjablonen, Iso's en andere bestanden van uw on-premises bronnen naar uw Privécloud-vCenter
* Toegankelijkheid van werk belastingen die worden uitgevoerd in uw Privécloud vanuit uw on-premises netwerk

Zie [een VPN-verbinding met hoge Beschik baarheid configureren](high-availability-vpn-connection.md)voor het instellen van uw on-PREMISES VPN-gateway in de modus voor hoge Beschik baarheid.

> [!IMPORTANT]
>    1. Stel TCP MSS in op 1200 op uw VPN-apparaat. Als uw VPN-apparaten geen ondersteuning bieden voor MSS-bekleming, kunt u in plaats daarvan ook de MTU op de tunnel interface instellen op 1240 bytes.
> 2. Nadat site-naar-site-VPN is ingesteld, stuurt u de DNS-aanvragen voor *. cloudsimple.io door naar de DNS-servers van de Privécloud.  Volg de instructies in [on-premises DNS-installatie](on-premises-dns-setup.md).
