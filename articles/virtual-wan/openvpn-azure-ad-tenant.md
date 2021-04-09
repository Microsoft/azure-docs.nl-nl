---
title: 'Azure AD-Tenant voor VPN-verbindingen van gebruikers: Azure AD-verificatie'
description: U kunt Azure Virtual WAN User VPN (punt-naar-site) gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91367849"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Azure Active Directory Tenant voorbereiden voor OpenVPN-protocol verbindingen voor gebruikers

Wanneer u via het IKEv2-protocol verbinding maakt met uw virtuele hub, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het OpenVPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor virtuele WAN-gebruikers VPN (punt-naar-site) met behulp van OpenVPN-verificatie.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN- &reg; Protocol verbindingen.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. de Azure AD-Tenant maken

Controleer of u een Azure AD-Tenant hebt. Als u geen Azure AD-Tenant hebt, kunt u er een maken met behulp van de stappen in het artikel [een nieuwe Tenant maken](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisatienaam
* Oorspronkelijke domeinnaam

Voorbeeld:

   ![Nieuwe Azure AD-Tenant](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. gebruikers van Azure AD-tenants maken

Maak vervolgens twee gebruikers accounts in de zojuist gemaakte Azure AD-Tenant, een globaal beheerders account en één gebruikers account. Het gebruikers account kan worden gebruikt om OpenVPN-verificatie te testen en het account van de globale beheerder wordt gebruikt om toestemming te geven voor de registratie van de Azure VPN-app. Nadat u een Azure AD-gebruikers account hebt gemaakt, wijst u aan de gebruiker een **Directory-rol** toe om beheerders machtigingen te delegeren.

Volg de stappen in [dit artikel](../active-directory/fundamentals/add-users-azure-active-directory.md) om de twee gebruikers voor uw Azure AD-Tenant te maken. Zorg ervoor dat u de **Directory-rol** van een van de gemaakte accounts wijzigt in **globale beheerder**.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. toestemming geven voor de registratie van de Azure VPN-app

1. Meld u aan bij de Azure-portal als een gebruiker aan wie de rol **globale beheerder** is toegewezen.

2. Ken vervolgens toestemming van de beheerder voor uw organisatie toe, waardoor de Azure VPN-toepassing zich kan aanmelden en gebruikers profielen kan lezen. Kopieer en plak de URL die betrekking heeft op uw implementatie locatie in de adres balk van uw browser:

    Openbaar

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Duitsland

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. Selecteer het account van de **globale beheerder** als u hierom wordt gevraagd.

    ![Map-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Selecteer **accepteren** wanneer u hierom wordt gevraagd.

    ![Scherm afbeelding toont het dialoog venster met de bericht machtigingen die zijn aangevraagd voor uw organisatie en aanvullende informatie.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Onder uw Azure AD, in **bedrijfs toepassingen**, zou u nu **Azure VPN** moeten weer geven.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerken met behulp van Azure AD-verificatie, moet u een VPN-configuratie voor gebruikers maken en deze koppelen aan een virtuele hub. Zie [Azure AD-verificatie configureren voor punt-naar-site-verbinding met Azure](virtual-wan-point-to-site-azure-ad.md).
