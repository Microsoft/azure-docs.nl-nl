---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met Prezi | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Prezi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 4ff8196ff4e30aa6ed036cabe00a72bfa8a5c51f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515318"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Prezi

In deze zelfstudie leert u hoe u Prezi kunt integreren met Azure Active Directory (Azure AD). Wanneer u Prezi integreert met Azure AD, kunt u het volgende:

* Bepalen wie toegang heeft tot Prezi in Azure AD.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Prezi.
* Uw accounts beheren in Azure Portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Er is een Prezi-abonnement ingeschakeld met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Prezi ondersteunt door SP en IDP geïnitieerde eenmalige aanmelding.
* Prezi biedt ondersteuning voor het Just-In-Time inrichten van gebruikers.
* Zodra u Prezi hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Zie [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app) voor meer informatie.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="add-prezi-from-the-gallery"></a>Prezi toevoegen uit de galerie

Voor het configureren van de integratie van Prezi in Azure AD moet u Prezi vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het meest linkse deelvenster **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Ga in het gedeelte **Toevoegen uit de galerie** naar het zoekvak en voer **Prezi** in.
1. Selecteer **Prezi** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Eenmalige aanmelding van Azure AD voor Prezi configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Prezi met behulp van een testgebruiker met de naam B.Simon. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Prezi.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Prezi, gaat u als volgt te werk:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Configureer Prezi SSO](#configure-prezi-sso) om de SSO-instellingen aan de kant van de toepassing te configureren.
    1. [Maak een Prezi-testgebruiker](#create-a-prezi-test-user) zodat u een tegenhanger van B.Simon in Prezi hebt die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Eenmalige aanmelding van Azure AD inschakelen in Azure Portal:

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Prezi** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het pictogram **Bewerken** om de instellingen voor **Standaard SAML-configuratie** te bewerken.

   ![Eenvoudige SAML-configuratie bewerken](common/edit-urls.png)

1. De gebruiker hoeft in de sectie **Standaard SAML-configuratie** geen stappen uit te voeren, omdat de app al vooraf is geïntegreerd met Azure.

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Voer in het vak **Aanmeldings-URL** de URL `https://prezi.com/login/sso/` in.

1. Selecteer **Opslaan**.

1. In de Prezi-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Gebruikerskenmerken en claims](common/default-attributes.png)

1. De toepassing Prezi verwacht ook dat er nog enkele kenmerken moeten worden teruggestuurd via de SAML-respons, zoals hier wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
    
    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | user.surname |

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en zoek in de sectie **SAML-handtekeningcertificaat** de optie **Certificaat (Base64)** . Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Prezi instellen** de juiste URL's op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het meest linkse deelvenster in Azure Portal de optie **Azure Active Directory**. Ga naar **Gebruikers** en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer in de gebruikerseigenschappen de volgende stappen uit:
   1. Voer in het vak **Naam** de naam **B.Simon** in.
   1. Voer in het vak **Gebruikersnaam** `username@companydomain.extension` in, bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weer gegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u B.Simon toestemming voor het gebruik eenmalige aanmelding van Azure door toegang te geven tot Prezi.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Prezi** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in de lijst met gebruikers in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** en klik onderaan het scherm vervolgens op de knop **Selecteren**.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-prezi-sso"></a>Prezi SSO configureren

1. Meld u in een ander browservenster aan bij Prezi met uw team account en ga naar de [Beheerconsole](https://prezi.com/organizations/manage).

1. Selecteer in de **Beheerconsole** het tabblad **Instellingen**.

    ![Tabblad Instellingen](./media/prezi-tutorial/settings-image.png)

1. Ga naar de sectie **Eenmalige aanmelding (SSO)** en schakel met de wisselknop de optie voor eenmalige aanmelding in.
    
    ![Wisselknop Eenmalige aanmelding (SSO)](./media/prezi-tutorial/single-signon.png)

1. Voer in de sectie **Eenmalige aanmelding (SSO)** de volgende stappen uit:

    ![De sectie Eenmalige aanmelding (SSO)](./media/prezi-tutorial/configuration.png)

    1. Plak in het vak **Id of URL-verlener** de waarde van de **Azure Ad-id** die u in Azure Portal hebt gekopieerd.

    1. Plak in het vak **SAML 2.0 Endpoint (HTTP)** de waarde van de **aanmeldings-URL** die u in Azure Portal hebt gekopieerd.

    1. Open het **certificaat (Base64)** dat u vanuit Azure Portal hebt gedownload, in Kladblok. Kopieer de inhoud van het certificaat en plak de inhoud in het vak **Certificaat (X. 509)** .

    1. Selecteer **Opslaan**.

### <a name="create-a-prezi-test-user"></a>Een Prezi-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Prezi. Prezi biedt ondersteuning voor het Just-In-Time inrichten van gebruikers. Deze functie is standaard ingeschakeld. Deze sectie bevat geen actie-item voor u. Als een gebruiker nog niet bestaat in Prezi, wordt er een nieuwe gebruiker gemaakt zodra deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u met behulp van het toegangsvenster de configuratie voor eenmalige aanmelding van Azure AD.

Wanneer u de tegel Prezi selecteert in het toegangsvenster, wordt u automatisch aangemeld bij het Prezi-account waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)
- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
- [Probeer Prezi met Azure AD](https://aad.portal.azure.com/)
- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Prezi beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)