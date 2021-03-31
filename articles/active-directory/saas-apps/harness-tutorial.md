---
title: 'Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Harness | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Harness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.openlocfilehash: d6a6c8b49582b34c2603e0ddf78b76736f97c183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Harness

In deze zelfstudie leert u hoe u Harness kunt integreren met Azure Active Directory (Azure AD). Wanneer u Harness integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Harness.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Harness.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Harness-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Harness ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-harness-from-the-gallery"></a>Harness uit de galerie toevoegen

Voor het configureren van de integratie van Harness met Azure AD moet u Harness uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Harness**.
1. Selecteer **Harness** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Harness

Configureer en test eenmalige aanmelding van Azure AD met Harness met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Harness.

Voltooi de volgende stappen om eenmalige aanmelding bij Harness met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Harness configureren](#configure-harness-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Harness maken](#create-harness-test-user)** : als u in Harness een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Harness** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.harness.io/`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. U krijgt de daadwerkelijke antwoord-URL van de sectie **Eenmalige aanmelding voor Harness configureren** die later in deze zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Harness instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Harness.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Harness** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-harness-sso"></a>Eenmalige aanmelding voor Harness configureren

1. Als u de configuratie in Harness wilt automatiseren, moet u de **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Harness instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Harness-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Harness. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Harness handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Harness-bedrijfssite. Voer hierna de volgende stappen uit:

4. Klik rechtsboven op de pagina op **Doorlopende beveiliging** > **Toegangsbeheer** > **Verificatie-instellingen**.

    ![Schermopname van het menu 'Doorlopende beveiliging' met 'Toegangsbeheer' en 'Verificatie-instellingen' geselecteerd.](./media/harness-tutorial/configure01.png)

5. Klik in de sectie **SSO-providers** op **+SSO-providers toevoegen** > **SAML**.

    ![Scherm opname van de 'SSO-providers' met '+ SSO-providers toevoegen - SAML' geselecteerd.](./media/harness-tutorial/configure03.png)

6. Voer de volgende stappen uit in het pop-upvenster **SAML-provider**:

    ![Scherm opname van het pop-upvenster 'SAML provider' met de velden 'URL' en 'Weergavenaam' gemarkeerd, en de knoppen 'Bestand kiezen' en 'Verzenden' geselecteerd.](./media/harness-tutorial/configure02.png)

    a. Kopieer het exemplaar **Schakel in uw SSO-provider op SAML gebaseerde aanmelding in en voer vervolgens de volgende URL in** en plak het in het tekstvak Antwoord-URL in de sectie **Standaard SAML-configuratie** op Azure Portal.

    b. Typ uw weergavenaam in het tekstvak **Weergavenaam**.

    c. Klik op **Bestand kiezen** om het XML-bestand met federatieve metagegevens, dat u hebt gedownload van Azure AD te uploaden.

    d. Klik op **SUBMIT**.

### <a name="create-harness-test-user"></a>Een testgebruiker voor Harness maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Harness, moeten ze worden ingericht in Harness. Het inrichten in Harness is een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij Harness.

1. Klik rechtsboven op de pagina op **Doorlopende beveiliging** > **Toegangsbeheer** > **Gebruikers**.

    ![Schermopname van het menu 'Doorlopende beveiliging' met 'Toegangsbeheer' en 'Gebruikers' geselecteerd.](./media/harness-tutorial/configure04.png)

1. Klik rechtsboven op de pagina op **+Gebruiker toevoegen**.

    ![Schermopname van de pagina 'Gebruikers' met de actie '+ Gebruiker toevoegen' geselecteerd.](./media/harness-tutorial/configure05.png)

1. Voer in het pop-upvenster **Gebruiker toevoegen** de volgende stappen uit:

    ![Configuratie van Harness](./media/harness-tutorial/configure06.png)

    a. Voer in het tekstvak **E-mailadres(sen)** het e-mailadres van de gebruiker in, zoals `B.simon@contoso.com`.

    b. Selecteer uw **Gebruikersgroepen**.

    c. Klik op **Submit**

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Harness klikt, wordt u automatisch aangemeld bij de instantie van Harness waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Harness proberen met Azure AD](https://aad.portal.azure.com/)