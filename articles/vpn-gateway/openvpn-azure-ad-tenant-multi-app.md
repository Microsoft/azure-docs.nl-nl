---
title: 'VPN Gateway: Azure AD-Tenant voor verschillende gebruikers groepen: Azure AD-verificatie'
description: U kunt P2S VPN gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91819674"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Een Active Directory-Tenant (AD) maken voor P2S OpenVPN-protocol verbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat een andere set gebruikers verbinding kan maken met verschillende VPN-gateways, kunt u meerdere apps in AD registreren en deze koppelen aan verschillende VPN-gateways. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S OpenVPN-verificatie en het maken en registreren van meerdere apps in azure AD voor het toestaan van verschillende toegang voor verschillende gebruikers en groepen.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. verificatie inschakelen op de gateway

In deze stap schakelt u Azure AD-verificatie in op de VPN-gateway.

1. Schakel Azure AD-verificatie in op de VPN-gateway door te navigeren naar **punt-naar-site-configuratie** en **openvpn (SSL)** te kiezen als het **Tunnel Type**. Selecteer **Azure Active Directory** als **verificatie type** en vul vervolgens de gegevens in onder de sectie **Azure Active Directory** .

    ![Azure Portal weer gave](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Gebruik de toepassings-ID van de Azure VPN-client niet: Hiermee worden alle gebruikers toegang verleend tot de VPN-gateway. Gebruik de ID van de toepassing (en) die u hebt geregistreerd.

2. Maak en down load het profiel door te klikken op de koppeling **VPN-client downloaden** .

3. Pak het gedownloade zip-bestand uit.

4. Blader naar de map ungezipte ' AzureVPN '.

5. Noteer de locatie van het bestand azurevpnconfig.xml. De azurevpnconfig.xml bevat de instelling voor de VPN-verbinding en kan rechtstreeks in de Azure VPN-client toepassing worden geïmporteerd. U kunt dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of een andere manier. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te kunnen maken.

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
