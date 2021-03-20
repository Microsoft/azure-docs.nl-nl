---
title: 'Zelfstudie: Integratie van Microsoft Azure Active Directory met Appraisd | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Appraisd.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 79db0a7bb769d6138bfeeb4e765621a1cffb4d14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92457857"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Zelfstudie: Appraisd integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Appraisd kunt integreren met Azure AD (Active Directory). Wanneer u Appraisd integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Appraisd.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Appraisd.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Appraisd-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Appraisd biedt ondersteuning voor met **SP en IDP** geïnitieerde SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Appraisd toevoegen vanuit de galerie

Om de integratie van Appraisd in Microsoft Azure Active Directory te configureren, moet u Appraisd vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Appraisd**.
1. Selecteer **Appraisd** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Appraisd met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Appraisd.

Als u eenmalige aanmelding van Azure AD met Appraisd wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Appraisd configureren](#configure-appraisd)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een testgebruiker voor Appraisd maken](#create-appraisd-test-user)** : als u een tegenhanger van B. Simon in Appraisd wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Appraisd** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop Opslaan te klikken, en de volgende stappen uit te voeren:

    a. Klik op **Extra URL's instellen**.

    b. In het tekstvak **Relaystatus** typt u een URL: `<TENANTCODE>`

    c. Als u de toepassing in de door **SP** geïnitieerde modus wilt configureren, typt u in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > U krijgt de werkelijke waarde van de aanmeldings-URL en de relaystatus op de pagina voor de configuratie van eenmalige aanmelding met Appraisd, wat later in de zelfstudie wordt uitgelegd.

1. In de Appraisd-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de Appraisd-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![Schermopname toont de sectie 'Gebruikerskenmerken', waarin het pictogram 'Bewerken' is gemarkeerd.](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Appraisd instellen** de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Appraisd configureren

1. Als u de configuratie in Appraisd wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Appraisd instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Appraisd-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Appraisd. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Appraisd handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Appraisd. Voer hierna de volgende stappen uit:

4. Klik rechtsboven op de pagina op het pictogram **Instellingen** en ga vervolgens naar **Configuratie**.

    ![Schermopname waarin de configuratielink is opgeroepen.](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Klik aan de linkerkant van het menu op **SAML single sign-on**.

    ![Schermopname toont de configuratieopties met de optie voor eenmalige aanmelding via SAML gemarkeerd.](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Voer op de **configuratiepagina voor eenmalige aanmelding op basis van SAML 2.0** de volgende stappen uit:

    ![Schermopname toont de configuratiepagina voor eenmalige aanmelding voor SAML 2.0, waar u de standaard relay-status en de door service geïnitieerde login-URL kunt bewerken.](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopieer de waarde voor **Standaardrelaystatus** en plak deze in het tekstvak **Relaystatus** in **Standaard SAML-configuratie** in de Azure-portal.

    b. Kopieer de waarde in **Met de service geïnitieerde aanmeldings-URL** en plak deze in het tekstvak **Aanmeldings-URL** in **Standaard SAML-configuratie** in de Azure-portal.

7. Schuif omlaag op de dezelfde pagina en voer de volgende stappen uit onder **Gebruikers identificeren**:

    ![Schermopname toont de identificatie van gebruikers waar u waarden uit deze stap kunt invoeren.](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Plak in het tekstvak **Identity Provider Single Sign-On URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Microsoft Azure-portal en klik op **Save**.

    b. Plak in het tekstvak **URL-verlener voor id-provider** de waarde van de **Microsoft Azure Active Directory-id** die u hebt gekopieerd in de Microsoft Azure-portal, en klik op **Opslaan**.

    c. Open in Kladblok het base-64 gecodeerde certificaat dat u hebt gedownload vanuit de Azure-portal, kopieer de inhoud en plak deze in het vak **X.509-certificaat**, en klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B. Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B. Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Appraisd.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Appraisd** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B. Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-appraisd-test-user"></a>Appraisd testgebruiker maken

Als u wilt dat Microsoft Azure Active Directory-gebruikers zich kunnen aanmelden bij Appraisd, moeten ze worden ingericht in Appraisd. In het geval van Appraisd is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij Appraisd als een beveiligingsbeheerder aan.

2. Klik rechtsboven op de pagina op het pictogram **Instellingen** en ga vervolgens naar **Beheercentrum**.

    ![Schermopname toont de instellingenopties waar u Administratiecentrum kunt selecteren.](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Klik in de werkbalk bovenaan de pagina op **Personen** en ga naar **Een nieuwe gebruiker toevoegen**.

    ![Schermopname toont de getaxeerde pagina met 'Mensen' en 'Een nieuwe gebruiker toevoegen' opgeroepen.](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Voer de volgende stappen uit in het dialoogvenster **Add a new user**:

    ![Schermopname met de knop 'Een nieuwe gebruiker toevoegen'.](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals **Britta**.

    b. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `B. Simon@contoso.com`.

    d. Klik op **Gebruiker toevoegen**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster op de tegel Appraisd klikt, wordt u automatisch aangemeld bij de instantie van Appraisd waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)