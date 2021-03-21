---
title: 'VPN Gateway: Azure AD-Tenant voor P2S VPN-verbindingen: Azure AD-verificatie'
description: Meer informatie over het instellen van een Azure AD-Tenant voor P2S open VPN-verificatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: bff1eec0152ab0f57edd212adf6b14f7b588fb51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390161"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-tenant maken voor P2S OpenVPN-protocolverbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S open VPN-verificatie.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD-Tenant verifiëren

Controleer of u een Azure AD-Tenant hebt. Als u geen Azure AD-Tenant hebt, kunt u er een maken met behulp van de stappen in het artikel [een nieuwe Tenant maken](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisatie naam
* Oorspronkelijke domeinnaam

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Nieuwe Azure AD-Tenant" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. gebruikers van Azure AD-tenants maken

Uw Azure AD-Tenant heeft de volgende accounts nodig: een globaal beheerders account en een hoofd gebruikers account. Het hoofd gebruikers account wordt gebruikt als uw hoofd account voor insluiten (Service-account). Wanneer u een Azure AD-Tenant gebruikers account maakt, past u de Directory-rol aan voor het type gebruiker dat u wilt maken.

Volg de stappen in [gebruikers toevoegen of verwijderen-Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) om ten minste twee gebruikers te maken voor uw Azure AD-Tenant. Zorg ervoor dat u de **Directory-rol** wijzigt om de volgende account typen te maken:

* Globale beheerder
* Gebruiker

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Azure AD-verificatie inschakelen op de VPN-gateway

1. Zoek de Directory-ID van de map die u voor verificatie wilt gebruiken. Deze wordt weer gegeven in de sectie eigenschappen van de pagina Active Directory.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="Scherm opname van de mapeigenschappen" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Kopieer de Map-id.

1. Meld u aan bij de Azure Portal als gebruiker aan wie de rol van **globale beheerder** is toegewezen.

1. Geef vervolgens toestemming voor de beheerder. Kopieer en plak de URL die betrekking heeft op uw implementatie locatie in de adres balk van uw browser:

   Openbaar

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Microsoft Cloud Duitsland

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Als u een globaal beheerders account gebruikt dat niet systeem eigen is voor de Azure AD-Tenant om toestemming te geven, vervangt u ' common ' door de Azure AD-Directory-id in de URL. Mogelijk moet u ook ' common ' vervangen door uw directory-id in bepaalde andere gevallen.
   >

1. Selecteer het account van de **globale beheerder** als u hierom wordt gevraagd.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Een account kiezen" border="false":::
1. Selecteer **accepteren** wanneer u hierom wordt gevraagd.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="Scherm afbeelding toont de bericht machtigingen die voor uw organisatie zijn aangevraagd en de optie voor het accepteren van gegevens." border="false":::
1. Onder uw Azure AD, in **bedrijfs toepassingen**, wordt **Azure VPN** weer gegeven.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="Scherm opname van de pagina alle toepassingen." lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Als u nog geen werkende Point-to-site-omgeving hebt, volgt u de instructie om er een te maken. Zie [een punt-naar-site-VPN maken](vpn-gateway-howto-point-to-site-resource-manager-portal.md) om een punt-naar-site-VPN-gateway te maken en te configureren.

    > [!IMPORTANT]
    > De basis-SKU wordt niet ondersteund voor OpenVPN.

1. Schakel Azure AD-verificatie in op de VPN-gateway door te navigeren naar **punt-naar-site-configuratie** en **openvpn (SSL)** te kiezen als het **Tunnel Type**. Selecteer **Azure Active Directory** als **verificatie type** en vul vervolgens de gegevens in onder de sectie **Azure Active Directory** .

   * **Tenant:** TenantID voor de Azure AD-Tenant ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Doel groep:** ApplicationID van de Azure AD Enter prise-app voor Azure VPN ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **Uitgever**: URL van de Secure token service ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="SAzure VPN" border="false":::

   > [!NOTE]
   > Zorg ervoor dat u een afsluitende slash aan het einde van de waarde opneemt `AadIssuerUri` . Anders kan de verbinding mislukken.
   >

1. Maak en down load het profiel door te klikken op de koppeling **VPN-client downloaden** .

1. Pak het gedownloade zip-bestand uit.

1. Blader naar de map ungezipte ' AzureVPN '.

1. Noteer de locatie van het bestand azurevpnconfig.xml. De azurevpnconfig.xml bevat de instelling voor de VPN-verbinding en kan rechtstreeks in de Azure VPN-client toepassing worden geïmporteerd. U kunt dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of een andere manier. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te kunnen maken.

## <a name="next-steps"></a>Volgende stappen

Een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
