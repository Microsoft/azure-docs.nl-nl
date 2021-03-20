---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met AwardSpring | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AwardSpring.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: a92bf7a10861658ddc3a60154708de266de8904d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92457460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-awardspring"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met AwardSpring

In deze zelfstudie leert u hoe u AwardSpring kunt integreren met Azure Active Directory (Azure AD). Wanneer u AwardSpring integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang heeft tot AwardSpring.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij AwardSpring.
* Uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op AwardSpring waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AwardSpring biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* AwardSpring biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-awardspring-from-the-gallery"></a>AwardSpring uit de galerie toevoegen

Om de integratie van AwardSpring in Azure AD te configureren, moet u AwardSpring uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **AwardSpring**.
1. Selecteer **AwardSpring** in het resultatenvenster, en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-awardspring"></a>Eenmalige aanmelding van Azure AD configureren en testen voor AwardSpring

Configureer en test eenmalige aanmelding van Azure AD met AwardSpring met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AwardSpring.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met AwardSpring te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij AwardSpring configureren](#configure-awardspring-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    * **[Testgebruiker voor AwardSpring maken](#create-awardspring-test-user)** : als u een tegenhanger van B.Simon in AwardSpring wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **AwardSpring** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.awardspring.com/SignIn/SamlMetaData`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.awardspring.com/SignIn/SamlAcs`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.awardspring.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem voor deze waarden contact op met [het clientondersteuningsteam van AwardSpring](mailto:support@awardspring.com). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de AwardSpring-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing AwardSpring nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    |  Naam | Bronkenmerk |
    | ---------------| --------------- |
    | Voornaam | user.givenname |
    | Achternaam | user.surname |
    | Email | user.mail |
    | Gebruikersnaam | user.userprincipalname |
    | StudentID | < Student ID > |

    > [!NOTE]
    > Het kenmerk StudentID is toegewezen met de werkelijke Student-ID die moet worden teruggestuurd in claims. Neem voor deze waarde contact op met [het clientondersteuningsteam van AwardSpring](mailto:support@awardspring.com).

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **AwardSpring instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot AwardSpring.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **AwardSpring**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-awardspring-sso"></a>Eenmalige aanmelding voor AwardSpring configureren

Als u eenmalige aanmelding aan de zijde van **AwardSpring** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Microsoft Azure-portal gekopieerde URL's naar het [AwardSpring-ondersteuningsteam](mailto:support@awardspring.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-awardspring-test-user"></a>Testgebruiker voor AwardSpring maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in AwardSpring. AwardSpring biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in AwardSpring bestaat, wordt er een nieuwe gemaakt na de verificatie.

> [!Note]
> Om een gebruiker handmatig te maken, neemt u contact op met het [ondersteuningsteam van AwardSpring](mailto:support@awardspring.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel AwardSpring klikt, wordt u automatisch aangemeld bij de instantie van AwardSpring waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [AwardSpring uitproberen met Azure AD](https://aad.portal.azure.com/)