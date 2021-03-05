---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SharingCloud | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en instant Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 7ae447a9577feba8b43b5b03a757ec4095ee2cb4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177892"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharingcloud"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SharingCloud

In deze zelf studie leert u hoe u SharingCloud integreert met Azure Active Directory (Azure AD). Wanneer u SharingCloud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SharingCloud.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij SharingCloud met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md)als u meer wilt weten over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* SharingCloud-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SharingCloud ondersteunt SSO die door **SP en IDP** is geïnitieerd
* SharingCloud ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-sharingcloud-from-the-gallery"></a>SharingCloud toevoegen uit de galerie

Als u de integratie van SharingCloud in azure AD wilt configureren, moet u SharingCloud uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)
    
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)
    
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.

    ![De knop Nieuwe toepassing](common/add-new-app.png)
    
1. Typ in de sectie **toevoegen vanuit de galerie** **SharingCloud** in het zoekvak.

    ![SharingCloud in de lijst met resultaten](common/search-new-app.png)
    
1. Selecteer **SharingCloud** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharingcloud"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SharingCloud

Azure AD SSO met SharingCloud configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SharingCloud.

Als u Azure AD SSO wilt configureren en testen met SharingCloud, voltooit u de volgende bouw stenen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SHARINGCLOUD SSO configureren](#configure-sharingcloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een SharingCloud-test gebruiker](#create-sharingcloud-test-user)** -om een equivalent van B. Simon in SharingCloud te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **SharingCloud** de sectie **beheren** en selecteer **eenmalige aanmelding**.
    
    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)
    
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram **bewerken** voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    Upload het meta gegevensbestand met XML-bestand dat is verschaft door SharingCloud. Neem contact op met het [ondersteunings team van SharingCloud](mailto:support@sharingcloud.com) om het bestand op te halen.

    ![image](common/upload-metadata.png)
    
    Selecteer het door gegeven meta gegevensbestand en klik op **uploaden**.

    ![image](common/browse-upload-metadata.png)

1. De SharingCloud-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit_attribute.png)

1. Daarnaast verwacht SharingCloud toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------- |
    | urn: sharingcloud: SSO: FirstName | user.givenname |
    | urn: sharingcloud: SSO: LastName | user.surname |
    | urn: sharingcloud: SSO: e-mail | user.mail |

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op pictogram **kopiëren** om de URL van de **federatieve meta gegevens** te kopiëren van de opgegeven opties volgens uw vereiste.

    ![De URL van de meta gegevens die moet worden gekopieerd](common/copy_metadataurl.png)

## <a name="configure-sharingcloud-sso"></a>SharingCloud SSO configureren

Als u eenmalige aanmelding wilt configureren op **SharingCloud** , moet u de gekopieerde **URL voor federatieve meta gegevens** van Azure Portal naar het [ondersteunings team van SharingCloud](mailto:support@sharingcloud.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SharingCloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SharingCloud**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sharingcloud-test-user"></a>SharingCloud-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in SharingCloud. SharingCloud ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in SharingCloud, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

* Ga rechtstreeks naar uw SharingCloud-URL en begin met de aanmeldings stroom.

## <a name="next-steps"></a>Volgende stappen

Nadat u SharingCloud hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

