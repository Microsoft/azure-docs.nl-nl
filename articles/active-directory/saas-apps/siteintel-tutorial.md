---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met SiteIntel | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SiteIntel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92509961"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met SiteIntel

In deze zelfstudie leert u hoe u SiteIntel integreert met Azure Active Directory (Azure AD). Wanneer u SiteIntel integreert met Azure AD, kunt u:

* Bepalen in Azure AD wie er toegang heeft tot SiteIntel.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij SiteIntel.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SiteIntel waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SiteIntel ondersteunt door SP geïnitieerde en door IdP geïnitieerde SSO.
* Zodra u SiteIntel hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>SiteIntel toevoegen vanuit de galerie

Als u de integratie van SiteIntel met Azure AD wilt configureren, moet u SiteIntel vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SiteIntel** in het vak **Toevoegen uit de galerie**.
1. Selecteer in de lijst met resultaten **SiteIntel** en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SiteIntel

Configureer en test eenmalige aanmelding van Azure AD met SiteIntel met behulp van een testgebruiker met de naam *B.Simon*. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SiteIntel.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SiteIntel te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.  

    a. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met de gebruiker B.Simon.  

    b. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat de gebruiker B.Simon eenmalige aanmelding van Azure AD kan gebruiken.

1. **[Eenmalige aanmelding configureren in SiteIntel](#configure-siteintel-sso)** als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.

    * **[Een testgebruiker maken in SiteIntel](#create-a-siteintel-test-user)** zodat u een tegenhanger van B.Simon in SiteIntel hebt die is gekoppeld aan de Azure AD-representatie van de gebruiker.

1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Ga als volgt te werk om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **SiteIntel** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Klik op de pagina **Eenmalige aanmelding met SAML instellen** op het pictogram **Bewerken** (potlood) naast **Standaard SAML-configuratie**.

   ![Schermopname van het deelvenster Eenmalige aanmelding instellen met SAML](common/edit-urls.png)

1. Als u de toepassing in de door de IDP geïnitieerde modus wilt configureren, voert u de volgende stappen uit in de sectie **Standaard SAML-configuratie**:

    a. In het tekstvak **Id** typt u een URL met de volgende indeling: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende indeling: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. In het tekstvak **Relaystatus** typt u een URL met de volgende indeling: `https://<CLIENT>.siteintel.com`

1. Als u de toepassing in de door de SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen** en doet u het volgende:

   * Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende indeling: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en relaystatus. Neem voor deze waarden contact op met het [ondersteuningsteam van SiteIntel](mailto:support@intalytics.com). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** de knop **Kopiëren** om de URL in het vak **App-URL voor federatieve metagegevens** te kopiëren.

    ![Schermopname van de knop Kopiëren voor app-URL van federatieve metagegevens](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Ga als volgt te werk in het eigenschappenvenster van de **gebruiker**:

   a. Voer in het vak **Naam** de naam **B.Simon** in.  

   b. Voer in het vak **Gebruikersnaam** de gebruikersnaam in met de volgende indeling: `username@companydomain.extension` (bijvoorbeeld `B.Simon@contoso.com`).

   c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

   d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u de gebruiker B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door hem of haar toegang te verlenen tot SiteIntel.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **SiteIntel** in de lijst **Toepassingen**.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermopname van de koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Schermopname van de knop Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** **B.Simon** en selecteer vervolgens de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Selecteer vervolgens de knop **Selecteren**.
1. Selecteer in het deelvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-siteintel-sso"></a>Eenmalige aanmelding configureren in SiteIntel

Als u eenmalige aanmelding wilt configureren in SiteIntel, verzendt u de URL die u hebt gekopieerd in het vak **App-URL voor federatieve metagegevens** naar het [ondersteuningsteam van SiteIntel](mailto:support@intalytics.com). Het team stelt de waarde zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-a-siteintel-test-user"></a>Een SiteIntel-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam *B.Simon* in SiteIntel. Neem contact op met het [ondersteuningsteam van SiteIntel](mailto:support@intalytics.com) om de gebruikers toe te voegen in het SiteIntel-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel **SiteIntel** selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij het exemplaar van SiteIntel toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)
- [Single sign-on to applications in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) (Eenmalige aanmelding bij toepassingen met Azure Active Directory)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
- [SiteIntel uitproberen met Azure AD](https://aad.portal.azure.com/)
- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [SiteIntel beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)