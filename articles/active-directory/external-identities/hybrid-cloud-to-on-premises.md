---
title: B2B-gebruikers toegang verlenen tot uw on-premises apps-Azure AD
description: Laat zien hoe u via Cloud gebruikers toegang kunt bieden tot on-premises apps met Azure AD B2B-samen werking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd91d1d2c9f5a4a413f9ea64cfdef649823d0f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131017"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises toepassingen

Als organisatie die gebruikmaakt van Azure Active Directory (Azure AD) B2B-samenwerkings mogelijkheden om gast gebruikers van partner organisaties te uitnodigen voor uw Azure AD, kunt u deze B2B-gebruikers nu toegang bieden tot on-premises apps. Deze on-premises apps kunnen gebruikmaken van op SAML gebaseerde verificatie of geïntegreerde Windows-verificatie (IWA) met Kerberos-beperkte delegering (KCD).

## <a name="access-to-saml-apps"></a>Toegang tot SAML-apps

Als uw on-premises app op SAML gebaseerde verificatie gebruikt, kunt u deze apps eenvoudig beschikbaar maken voor uw Azure AD B2B-samenwerkings gebruikers via de Azure Portal.

U moet het volgende doen:

- Integreer de app met behulp van SAML zoals beschreven in [Configure SSO op basis van een eenmalige aanmelding](../manage-apps/configure-saml-single-sign-on.md). Zorg ervoor dat u weet wat u gebruikt voor de waarde van de **aanmeldings-URL** .
-  Gebruik Azure AD-toepassingsproxy om de on-premises app te publiceren, met **Azure Active Directory** geconfigureerd als de verificatie bron. Zie [toepassingen publiceren met Azure AD-toepassingsproxy](../manage-apps/application-proxy-add-on-premises-application.md)voor instructies. 

   Wanneer u de instelling **interne URL** configureert, gebruikt u de AANMELDINGS-URL die u hebt opgegeven in de toepassings sjabloon niet-galerie. Op deze manier hebben gebruikers vanaf buiten de grenzen van de organisatie toegang tot de app. Toepassings proxy voert de eenmalige aanmelding voor SAML uit voor de on-premises app.
 
   ![Hiermee worden de interne URL en verificatie van de on-premises app-instellingen weer gegeven](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Toegang tot IWA-en KCD-apps

Om B2B-gebruikers toegang te bieden tot on-premises toepassingen die zijn beveiligd met geïntegreerde Windows-verificatie en beperkte Kerberos-delegering, hebt u de volgende onderdelen nodig:

- **Verificatie via Azure AD-toepassingsproxy**. B2B-gebruikers moeten zich kunnen aanmelden bij de on-premises toepassing. Hiervoor moet u de on-premises app publiceren via de Azure-AD-toepassingsproxy. Zie [zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy](../manage-apps/application-proxy-add-on-premises-application.md)voor meer informatie.
- **Autorisatie via een B2B-gebruikers object in de on-premises Directory**. De toepassing moet gebruikers toegangs controles kunnen uitvoeren en toegang verlenen tot de juiste resources. IWA en KCD vereisen een gebruikers object in het on-premises Windows Server-Active Directory om deze autorisatie te volt ooien. Zoals beschreven in de werking van [eenmalige aanmelding met KCD](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), moet dit gebruikers object door de toepassings proxy worden geïmiteerd en moet er een Kerberos-token bij de app worden ontvangen. 

   > [!NOTE]
   > Wanneer u de Azure AD-toepassingsproxy configureert, moet u ervoor zorgen dat de **gedelegeerde aanmeldings-id** is ingesteld op **User Principal name** (standaard) in de configuratie voor eenmalige aanmelding voor geïntegreerde Windows-verificatie (IWA).

   Voor het B2B-gebruikers scenario zijn er twee methoden beschikbaar die u kunt gebruiken om de gast gebruikers objecten te maken die vereist zijn voor autorisatie in de on-premises Directory:

   - Microsoft Identity Manager (MIM) en de MIM-beheer agent voor Microsoft Graph. 
   - [Een Power shell-script](#create-b2b-guest-user-objects-through-a-script-preview). Het gebruik van het script is een licht gewicht oplossing waarvoor MIM niet is vereist. 

Het volgende diagram bevat een globaal overzicht van hoe Azure AD-toepassingsproxy en de generatie van het B2B-gebruikers object in de on-premises Directory samen werken om B2B-gebruikers toegang te verlenen tot uw on-premises IWA-en KCD-apps. De genummerde stappen worden gedetailleerd beschreven onder het diagram.

![Diagram van MIM-en B2B-script oplossingen](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Een gebruiker van een partner organisatie (de fabrikam-Tenant) wordt uitgenodigd voor de contoso-Tenant.
2.  Er wordt een gast gebruikers object gemaakt in de contoso-Tenant (bijvoorbeeld een gebruikers object met een UPN van guest_fabrikam. com # EXT # @contoso.onmicrosoft.com ).
3.  Fabrikam Guest wordt geïmporteerd van Contoso via MIM of via het B2B Power shell-script.
4.  Een weer gave of footprint van het object fabrikam gast gebruiker (Guest # EXT #) wordt gemaakt in de on-premises Directory, Contoso.com, via MIM of via het B2B Power shell-script.
5.  De gast gebruiker heeft toegang tot de on-premises toepassing, app.contoso.com.
6.  De verificatie aanvraag wordt via een toepassings proxy geautoriseerd met beperkte Kerberos-delegering. 
7.  Omdat het gast gebruikers object lokaal bestaat, is de verificatie geslaagd.

### <a name="lifecycle-management-policies"></a>Levenscyclus beheer beleid

U kunt de on-premises B2B-gebruikers objecten beheren via het levenscyclus beheer beleid. Bijvoorbeeld:

- U kunt beleid voor multi-factor Authentication (MFA) voor de gast gebruiker instellen, zodat MFA wordt gebruikt tijdens de verificatie van de toepassings proxy. Zie [voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md)voor meer informatie.
- Alle sponsoringen, toegangs beoordelingen, account verificaties, enzovoort die worden uitgevoerd op de B2B-gebruiker in de Cloud, zijn van toepassing op de on-premises gebruikers. Als de Cloud gebruiker bijvoorbeeld wordt verwijderd via uw levenscyclus beheer beleid, wordt de on-premises gebruiker ook verwijderd door MIM sync of via Azure AD Connect Sync. Zie [Manage gast Access with Azure AD Access revisies](../governance/manage-guest-access-with-access-reviews.md)(Engelstalig) voor meer informatie.

### <a name="create-b2b-guest-user-objects-through-mim"></a>B2B-gast gebruikers objecten maken via MIM

Voor informatie over het gebruik van MIM 2016 Service Pack 1 en de MIM-beheer agent voor Microsoft Graph om de gast gebruikers objecten te maken in de on-premises Directory, raadpleegt u [Azure AD Business-to-Business (B2B) collaboration with Microsoft Identity Manager (MIM) 2016 SP1 met Azure-toepassing proxy](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>B2B-gebruikers objecten maken met behulp van een script (preview-versie)

Er is een Power shell-voorbeeld script beschikbaar dat u kunt gebruiken als uitgangs punt voor het maken van de gast gebruikers objecten in uw on-premises Active Directory.

U kunt het script en het Leesmij-bestand downloaden van [connectors voor Microsoft Identity Manager 2016 en Forefront Identity Manager 2010 R2](https://www.microsoft.com/download/details.aspx?id=51495). Kies in het Download pakket het **script en Leesmij om Azure AD B2B-gebruikers on-prem.zipbestand te halen** .

Voordat u het script gebruikt, moet u de vereisten en belang rijke overwegingen in het bijbehorende Leesmij-bestand door nemen. Bovendien moet u weten dat het script alleen beschikbaar is gemaakt als voor beeld. Uw ontwikkel team of een partner moet het script aanpassen en controleren voordat u het uitvoert.

## <a name="license-considerations"></a>Licentie overwegingen

Zorg ervoor dat u over de juiste client Access Licenses (Cal's) beschikt voor externe gast gebruikers die toegang hebben tot on-premises apps. Zie de sectie ' externe connectors ' in [licenties voor client toegang en beheer licenties](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx)voor meer informatie. Neem contact op met uw micro soft-vertegenwoordiger of plaatselijke wederverkoper over uw specifieke licentie behoeften.

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samen werking voor hybride organisaties](hybrid-organizations.md)

- Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor een overzicht van Azure AD Connect.