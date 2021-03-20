---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding voor GreenLight Enterprise Business Controls Platform | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en het GreenLight Enterprise Business Controls-platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: e1e15c3fdf9900d016581f848fdfdba5d91b0997
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92447062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-greenlight-enterprise-business-controls-platform"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding voor GreenLight Enterprise Business Controls Platform

In deze zelfstudie leert u hoe u Datava Enterprise Service Platform integreert met Azure Active Directory (Azure AD). Wanneer u GreenLight Enterprise Business Controls Platform integreert met Azure AD, kunt u het volgende doen:

* Controleer in Azure AD wie toegang heeft tot GreenLight Enterprise Business Controls Platform.
* Zorg ervoor dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Greenlight Enterprise Business Controls Platform.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* GreenLight Enterprise Business Controls Platform-abonnement met eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GreenLight EnterpriseBusiness Controls Platform ondersteunt **SP en IDP** geïnitieerde eenmalige aanmelding

* Zodra u Greenlight Enterprise Business Controls Platform hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-greenlight-enterprise-business-controls-platform-from-the-gallery"></a>Greenlight Enterprise Business Controls Platform toevoegen vanuit de galerie

Als u de integratie van Greenlight Enterprise Business Controls Platform wilt configureren in Azure AD, moet u Greenlight Enterprise Business Controls Platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Greenlight Enterprise Business Controls Platform** in het zoekvak in het gedeelte **Toevoegen vanuit de galerie**.
1. Selecteer **Greenlight Enterprise Business Controls Platform** uit de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-greenlight-enterprise-business-controls-platform"></a>Eenmalige aanmelding met Azure AD configureren en testen voor Greenlight Enterprise Business Controls Platform

Configureer en test eenmalige aanmelding met Azure AD voor Greenlight Enterprise Business Controls Platform met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Greenlight Enterprise Business Controls Platform.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Greenlight Enterprise Business Controls Platform te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Greenlight Enterprise Business Controls Platform](#configure-greenlight-enterprise-business-controls-platform-sso)** configureren: configuratie van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Greenlight Enterprise Business Controls Platform-testgebruiker maken](#create-greenlight-enterprise-business-controls-platform-test-user)** - om een equivalent van B. Simon te hebben in Greenlight Enterprise Business Controls Platform die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Greenlight Enterprise Business Controls Platform** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<CUSTOMER_NAME>.gltcloud.com/ebcpplatform/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<CUSTOMER_NAME>.gltcloud.com/ebcpplatform/saml`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<CUSTOMER_NAME>.gltcloud.com/ebcpplatform/saml`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [de klantenondersteuning van Greenlight Enterprise Business Controls Platform](mailto:support@greenlightcorp.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de gewenste URL('s) in de sectie **Greenlight Enterprise Business Controls Platform instellen**.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Greenlight Enterprise Business Controls Platform.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Greenlight Enterprise Business Controls Platform**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-greenlight-enterprise-business-controls-platform-sso"></a>Eenmalige aanmelding voor Greenlight Enterprise Business Controls Platform instellen

Als u eenmalige aanmelding wilt configureren aan de kant van **Greenlight Enterprise Business Controls Platform** , moet u de gedownloade **Federatieve metagegevens-XML** en de juiste gekopieerde URL's verzenden van Azure Portal naar [het ondersteuningsteam van Greenlight Enterprise Business Controls Platform](mailto:support@greenlightcorp.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-greenlight-enterprise-business-controls-platform-test-user"></a>Testgebruiker maken voor Greenlight Enterprise Business Controls Platform

In deze sectie maakt u een gebruiker met de naam B.Simon in Greenlight Enterprise Business Controls Platform. Neem contact op met het [ondersteuningsteam van Greenlight Enterprise Business Controls Platform](mailto:support@greenlightcorp.com) om de gebruikers toe te voegen in het platform van Greenlight Enterprise Business Controls Platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Greenlight Enterprise Business Controls Platform klikt, moet u automatisch worden aangemeld bij het Greenlight Enterprise Business Controls Platform waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Greenlight Enterprise Business Controls Platform met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [GreenLight Enterprise Business Controls Platform beveiligen met geavanceerde zichtbaarheid en controles](/cloud-app-security/proxy-intro-aad)