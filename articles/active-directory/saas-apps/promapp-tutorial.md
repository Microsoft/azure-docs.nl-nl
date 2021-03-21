---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Nintex Promapp | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Nintex Promapp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: fc31195e7f544bdce7fe2f135a39cb9992875d0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92505881"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Nintex Promapp

In deze zelfstudie leert u hoe u Nintex Promapp kunt integreren met Azure Active Directory (Azure AD). Wanneer u Nintex Promapp integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Nintex Promapp.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Nintex Promapp.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Nintex Promapp waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Nintex Promapp biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Nintex Promapp biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-nintex-promapp-from-the-gallery"></a>Nintex Promapp toevoegen vanuit de galerie

Als u de integratie van Nintex Promapp met Azure Active Directory wilt configureren, moet u Nintex Promapp toevoegen vanuit de galerie aan uw lijst van beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Nintex Promapp** in het zoekvak.
1. Selecteer **Nintex Promapp** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Nintex Promapp

Configureer en test eenmalige aanmelding van Azure AD met Nintex Promapp met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Nintex Promapp.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Nintex Promapp te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van Nintex Promapp configureren](#configure-nintex-promapp-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Nintex Promapp-testgebruiker maken](#create-nintex-promapp-test-user)** : als u een equivalent van B.Simon in Nintex Promapp wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Nintex Promapp** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    1. Voer in het vak **Id** een URL met dit patroon in:

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > Azure AD-integratie met Nintex Promapp is momenteel alleen geconfigureerd voor door de service geïnitieerde verificatie. (Dat wil zeggen dat als u naar een Nintex Promapp-URL gaat het verificatieproces wordt gestart.) Maar het veld **Antwoord-URL** is een verplicht veld.

    1. Voer in het vak **Antwoord-URL** een URL met dit patroon in:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Voer in het vak **Aanmeldings-URL** een URL met dit patroon in: `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. Hiervoor moet u de werkelijke id, antwoord-URL en aanmeldings-URL gebruiken. Neem contact op met het [ondersteuningsteam van Nintex Promapp](https://www.promapp.com/about-us/contact-us/) om de waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Nintex Promapp instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Nintex Promapp.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nintex Promapp** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-nintex-promapp-sso"></a>Eenmalige aanmelding bij Nintex Promapp configureren

1. Meld u bij de bedrijfssite van Nintex Promapp aan als beheerder.

2. Selecteer in het menu boven in het venster de optie **Admin**:

    ![Beheerder selecteren][12]

3. Selecteer **Configure**:

    ![Configure selecteren][13]

4. Voer in het dialoogvenster **Security** de volgende stappen uit.

    ![Het dialoogvenster Security][14]

    1. Plak in het vak **SSO-Login URL** de **aanmeldings-URL** die u vanuit Azure Portal hebt gekopieerd.

    1. Selecteer in de lijst **SSO - Single Sign-on Mode** de optie **Optional**. Selecteer **Opslaan**.

       > [!NOTE]
       > De optionele modus is alleen bedoeld voor testdoeleinden. Wanneer u tevreden bent met de configuratie, selecteert u **Required** in de lijst **SSO - Single Sign-on Mode** om alle gebruikers te dwingen om te verifiëren met Azure AD.

    1. Open in Kladblok het certificaat dat u in de vorige sectie hebt gedownload. Kopieer de inhoud van het certificaat zonder de eerste regel ( **-----BEGIN CERTIFICATE-----** ) of de laatste regel ( **-----END CERTIFICATE-----** ). Plak de certificaatinhoud in het vak **SSO-x.509 Certificate** en selecteer **Save**.

### <a name="create-nintex-promapp-test-user"></a>Nintex Promapp-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Nintex Promapp. Nintex Promapp biedt ondersteuning voor Just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Nintex Promapp bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Nintex Promapp klikt, wordt u automatisch aangemeld bij het exemplaar van Nintex Promapp waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Nintex Promapp gebruiken met Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png