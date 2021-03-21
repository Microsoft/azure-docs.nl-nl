---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Templafy SAML2 | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Templafy SAML2.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d83bf1b6e986f867a3781b5da7d2784e65815520
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515816"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-templafy-saml2"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Templafy SAML2

In deze zelfstudie leert u hoe u Templafy SAML2 kunt integreren met Azure AD (Active Directory). Wanneer u Templafy SAML2 integreert met Azure AD, kunt u:

* In Azure AD regelen wie toegang heeft tot Templafy SAML2.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Templafy SAML2.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Templafy SAML2 SSO-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Templafy SAML2 biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding
* Templafy SAML2 biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-templafy-saml2-from-the-gallery"></a>Templafy SAML2 toevoegen vanuit de galerie

Voor het configureren van de integratie van Templafy SAML2 in Azure AD moet u Templafy SAML2 uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Templafy SAML2** in het zoekvak in de sectie **Toevoegen vanuit de galerie**.
1. Selecteer **Templafy SAML2** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-templafy-saml2"></a>Azure AD-eenmalige aanmelding configureren en testen voor Templafy SAML2

Configureer en test eenmalige aanmelding van Azure AD met Templafy SAML2 met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Templafy SAML2.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Templafy SAML2 te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Templafy SAML2 configureren](#configure-templafy-saml2-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Templafy SAML2-testgebruiker maken](#create-templafy-saml2-test-user)** : als u een equivalent van B.Simon in Templafy SAML2 wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Templafy SAML2** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<CLIENTSUBDOMAIN>.templafy.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Templafy SAML2](mailto:support@templafy.com) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Templafy SAML2-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing Templafy SAML2 nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk| Naamruimte  |
    | ---------------| --------------- | --------- |
    | givenname | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | surname | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | emailaddress | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`
    | streetaddress | user.streetaddress | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | city | user.city | `http://schemas.templafy.com/2016/06/identity/claims`|
    | postalcode | user.postalcode | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | stateorprovince | user.state | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | country | user.country | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | jobtitle | user.jobtitle | `http://schemas.templafy.com/2016/06/identity/claims`|
    | department | user.department | `http://schemas.templafy.com/2016/06/identity/claims`|
    | phonenumber | user.telephonenumber | `http://schemas.templafy.com/2016/06/identity/claims` |
    | facsimilenumber | user.facsimiletelephonenumber | `http://schemas.templafy.com/2016/06/identity/claims`|
    | upn | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | nameidentifier | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Templafy SAML2.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Templafy SAML2** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-templafy-saml2-sso"></a>Eenmalige aanmelding voor Templafy SAML2 configureren

Als u eenmalige aanmelding wilt configureren in **Templafy SAML2**, moet u de **App-URL voor federatieve metagegevens** verzenden naar het [ondersteuningsteam voor Templafy SAML2](mailto:support@templafy.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-templafy-saml2-test-user"></a>Testgebruiker voor Templafy SAML2 maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Templafy SAML2. Templafy SAML2 biedt ondersteuning voor just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Templafy SAML2 bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Templafy SAML2 in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Templafy SAML2 waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Templafy SAML2 met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)