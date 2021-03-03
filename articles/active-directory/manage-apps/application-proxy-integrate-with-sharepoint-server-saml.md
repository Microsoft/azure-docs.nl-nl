---
title: Publiceren op = premises share point met toepassings proxy-Azure AD
description: Behandelt de basis principes voor het integreren van een on-premises share Point-server met Azure AD-toepassingsproxy voor SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cadf5b7d92e26e561e570f824295e69ca421e16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644511"
---
# <a name="integrate-with-sharepoint-saml"></a>Integreren met share point (SAML)

In deze stapsgewijze hand leiding wordt uitgelegd hoe u de toegang tot de [Azure Active Directory geïntegreerde on-premises share point (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) kunt beveiligen met behulp van Azure AD-toepassingsproxy, waar gebruikers in uw organisatie (Azure AD, B2B) via Internet verbinding maken met share point.

> [!NOTE] 
> Als u geen ervaring hebt met Azure AD-toepassingsproxy en meer informatie wilt, raadpleegt u [externe toegang tot on-premises toepassingen via Azure AD-toepassingsproxy](./application-proxy.md).

Er zijn drie belang rijke voor delen van deze installatie:

- Azure AD-toepassingsproxy zorgt ervoor dat geverifieerd verkeer uw interne netwerk en de share Point-server kan bereiken.
- Uw gebruikers hebben op normale wijze toegang tot de share point-sites zonder VPN te gebruiken.
- U kunt de toegang beheren op basis van de gebruikers toewijzing op Azure AD-toepassingsproxy niveau en u kunt de beveiliging verhogen met Azure AD-functies zoals voorwaardelijke toegang en Multi-Factor Authentication (MFA).

Dit proces vereist twee bedrijfs toepassingen. Een share point on-premises-exemplaar dat u vanuit de galerie publiceert naar uw lijst met beheerde SaaS-apps. De tweede is een on-premises toepassing (niet-galerie toepassing) die u gebruikt om de eerste toepassing voor de Enter prise Gallery te publiceren.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om deze configuratie te volt ooien:
 - Een SharePoint 2013-farm of nieuwer. De share point-farm moet worden [geïntegreerd met Azure AD](../saas-apps/sharepoint-on-premises-tutorial.md).
 - Een Azure AD-Tenant met een abonnement dat toepassings proxy bevat. Meer informatie over [Azure AD-abonnementen en-prijzen](https://azure.microsoft.com/pricing/details/active-directory/).
 - Een [aangepast, geverifieerd domein](../fundamentals/add-custom-domain.md) in de Azure AD-Tenant. Het geverifieerde domein moet overeenkomen met het URL-achtervoegsel van share point.
 - Er is een SSL-certificaat vereist. Bekijk de details bij het [publiceren van aangepaste domeinen](./application-proxy-configure-custom-domain.md).
 - On-premises Active Directory gebruikers moeten worden gesynchroniseerd met Azure AD Connect en moeten worden geconfigureerd om [zich aan te melden bij Azure](../hybrid/plan-connect-user-signin.md). 
 - Voor alleen Cloud-en B2B-gast gebruikers moet u [toegang tot een gast account verlenen aan share point on-premises in de Azure Portal](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - Een toepassings proxy connector is geïnstalleerd en wordt uitgevoerd op een computer binnen het bedrijfs domein.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Stap 1: share point on-premises integreren met Azure AD 

1. De on-premises share point-app configureren. Zie [zelf studie: Azure Active Directory de integratie van eenmalige aanmelding met share point op locatie](../saas-apps/sharepoint-on-premises-tutorial.md)voor meer informatie.
2. Valideer de configuratie voordat u verdergaat met de volgende stap. Als u wilt valideren, probeert u de share point on-premises te openen vanuit het interne netwerk en te controleren of het intern toegankelijk is. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Stap 2: de share point on-premises-toepassing publiceren met toepassings proxy

In deze stap maakt u een toepassing in uw Azure AD-Tenant die gebruikmaakt van de toepassings proxy. U stelt de externe URL in en geeft de interne URL op die u later in share point gebruikt.

> [!NOTE] 
> De interne en externe Url's moeten overeenkomen met de **aanmeldings-URL** in de configuratie van op SAML gebaseerde toepassing in stap 1.

   ![Scherm afbeelding met de waarde voor de aanmeldings-URL.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Maak een nieuwe Azure AD-toepassingsproxy-toepassing met een aangepast domein. Zie [aangepaste domeinen in Azure AD-toepassingsproxy](./application-proxy-configure-custom-domain.md)voor stapsgewijze instructies.

    - Interne URL: https://portal.contoso.com/
    - Externe URL: https://portal.contoso.com/
    - Verificatie vooraf: Azure Active Directory
    - Url's vertalen in kopteksten: Nee
    - Url's vertalen in de hoofd tekst van de toepassing: Nee

        ![Scherm opname van de opties die u gebruikt om de app te maken.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Wijs [dezelfde groepen](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) toe die u hebt toegewezen aan de on-premises share point-galerie toepassing.

3. Ga ten **slotte naar de** sectie **Eigenschappen** en stel **zichtbaar voor gebruikers** in. Deze optie zorgt ervoor dat alleen het pictogram van de eerste toepassing wordt weer gegeven in de portal mijn apps ( https://myapplications.microsoft.com) .

   ![Scherm afbeelding die laat zien waar u de zicht baarheid voor gebruikers instelt? selecteert.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Stap 3: de toepassing testen

Als u een browser van een computer in een extern netwerk gebruikt, gaat u naar de URL ( https://portal.contoso.com/) die u tijdens de stap publiceren hebt geconfigureerd). Zorg ervoor dat u zich kunt aanmelden met het test account dat u hebt ingesteld.