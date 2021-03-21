---
title: 'Zelfstudie: Azure Active Directory-integratie met 123FormBuilder SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en 123FormBuilder SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: aa4bab2f7ecb90c61e22de46b01a5ed81342a408
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92319187"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met 123FormBuilder SSO

In deze zelfstudie leert u hoe u 123FormBuilder SSO integreert met Azure Active Directory (Azure AD). Wanneer u 123FormBuilder SSO integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot 123FormBuilder SSO.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij 123FormBuilder SSO.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op 123FormBuilder SSO waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* 123FormBuilder SSO ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding.
* 123FormBuilder SSO ondersteunt het **Just-In-Time** inrichten van gebruikers.
* Zodra u eenmalige aanmelding voor 123FormBuilder SSO hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>123FormBuilder SSO toevoegen vanuit de galerie

Voor de configuratie van de integratie van 123FormBuilder SSO in Azure Active Directory, moet u 123FormBuilder SSO vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **123FormBuilder SSO** in het zoekvak.
1. Selecteer **123FormBuilder SSO** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen voor 123FormBuilder SSO

Configureer en test eenmalige aanmelding van Azure AD met 123FormBuilder SSO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in 123FormBuilder SSO.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met 123FormBuilder SSO, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met 123FormBuilder SSO configureren](#configure-123formbuilder-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    * **[Een testgebruiker maken voor 123FormBuilder SSO](#create-123formbuilder-sso-test-user)** : als u een equivalent van B.Simon in 123FormBuilder SSO wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **123FormBuilder SSO**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL's en id, zoals later in de zelfstudie wordt uitgelegd.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden en vervolgens op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **123FormBuilder SSO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot 123FormBuilder SSO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **123FormBuilder SSO** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-123formbuilder-sso"></a>Eenmalige aanmelding configureren in 123FormBuilder SSO

1. Als u eenmalige aanmelding wilt configureren in **123FormBuilder SSO**, gaat u naar [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) en voert u de volgende stappen uit:

    ![Schermopname van het scherm voor de configuratie van id-provider voor SSO SAML.](./media/123formbuilder-tutorial/submit.png) 

    a. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld `B.Simon@Contoso.com`.

    b. Klik op **Upload** en blader naar het gedownloade XML-bestand met metagegevens dat u hebt gedownload vanuit de Azure-portal.

    c. Klik op **SUBMIT FORM**.

2. Voer in **Microsoft Azure AD - Eenmalige aanmelding - App-instellingen configureren** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/123formbuilder-tutorial/url3.png)

    a. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **IDENTIFIER** voor uw exemplaar en plakt u deze in het tekstvak **Id** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    b. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **REPLY URL** voor uw exemplaar en plakt u deze in het tekstvak **Antwoord-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    c. Als u de toepassing wilt configureren in de **door SP geïnitieerde modus**, kopieert u de waarde van **SIGN ON URL** voor uw exemplaar en plakt u deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

### <a name="create-123formbuilder-sso-test-user"></a>Een testgebruiker maken voor 123FormBuilder SSO

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in 123FormBuilder SSO. 123FormBuilder SSO ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in 123FormBuilder SSO, wordt er na verificatie een gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel 123FormBuilder SSO klikt, wordt u automatisch aangemeld bij de instantie van 123FormBuilder SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [123FormBuilder SSO uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)