---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workteam | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Workteam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.openlocfilehash: 077af1e8add63aac9f3d866c5d665ad4a47b133f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520044"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workteam

In deze zelfstudie leert u hoe u Workteam integreert met Azure Active Directory (Azure AD). Wanneer u Workteam integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Workteam.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Workteam.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement met eenmalige aanmelding ingeschakeld voor Workteam.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workteam ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-workteam-from-the-gallery"></a>Workteam toevoegen vanuit de galerie

Als u de integratie van Workteam met Azure AD wilt configureren, dient u Workteam vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Workteam** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Workteam** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Workteam

Configureer en test eenmalige aanmelding van Azure AD met Workteam met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workteam.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Workteam te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Workteam configureren](#configure-workteam-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Workteam-testgebruiker maken](#create-workteam-test-user)** : als u een equivalent van B.Simon in Workteam wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Workteam** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.workte.am`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Workteam instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Workteam.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. In de lijst met toepassingen selecteert u **Workteam**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-workteam-sso"></a>Eenmalige aanmelding voor Workteam configureren

1. Als u de configuratie in Workteam wilt automatiseren, moet u de **browserextensie voor veilig aanmelden bij mijn apps** installeren door te klikken op **De extensie installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Workteam instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Workteam-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Workteam. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Workteam handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Workteam-bedrijfssite. Voer hierna de volgende stappen uit:

4. Klik in de rechter bovenhoek op **Profiel** en klik vervolgens op **Organisatie-instellingen**. 

    ![Workteam-instellingen](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. Klik onder **Verificatie** op **Instellingen**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Voer in het dialoogvenster **SAML-instellingen** de volgende stappen uit:

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecteer **SAML IdP** als **AD Azure**.

    b. Plak in het tekstvak **Service-URL voor eenmalige aanmelding op basis van SAML** de waarde van de **aanmeldings-URL** die u hebt gekopieerd vanuit de Microsoft Azure-portal.

    c. Plak in het tekstvak **SAML entititeits-id** de waarde voor **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    d. Open in Kladblok het **base-64 gecodeerde certificaat** dat u hebt gedownload vanuit Azure Portal, kopieer de inhoud en plak deze in het vak **SAML Signing Certificate (Base64)** .

    e. Klik op **OK**.

### <a name="create-workteam-test-user"></a>Workteam-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Workteam, moeten ze worden ingericht in Workteam. In het geval van Workteam is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij Workteam aan als een beveiligingsbeheerder.

2. Klik in het midden van de pagina **Organisatie-instellingen** op **USERS** en klik vervolgens op **NEW USER**.

    ![Workteam-gebruiker](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Voer de volgende stappen uit op de pagina **Nieuwe werknemer**:

    ![Nieuwe Workteam-gebruiker](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Typ in het tekstvak **Naam** de voornaam van de gebruiker, zoals **B.Simon**.

    b. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `B.Simon\@contoso.com`.

    c. Klik op **OK**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Workteam in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Workteam waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Workteam met Azure AD](https://aad.portal.azure.com/)