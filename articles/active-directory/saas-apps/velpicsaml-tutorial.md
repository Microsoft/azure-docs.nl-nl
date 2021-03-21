---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Velpic SAML | Microsoft Docs'
description: Informatie over het configureren van een eenmalige aanmelding tussen Azure Active Directory en Velpic SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.openlocfilehash: 81523a1c11b6a3da4e7d1db5d8249921ad5047ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92635861"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Velpic SAML

In deze zelfstudie leert u hoe u Velpic SAML integreert met Azure Active Directory (Azure AD). Wanneer u Velpic SAML integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot Velpic SAML.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Velpic SAML.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Velpic SAML-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Velpic SAML biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-velpic-saml-from-the-gallery"></a>Velpic SAML toevoegen vanuit de galerie

Als u de integratie van Velpic SAML wilt configureren in Azure AD, moet u Velpic SAML vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Velpic SAML** in het zoekvak.
1. Selecteer **Velpic SAML** in het paneel met resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Velpic SAML

Configureer en test eenmalige aanmelding van Azure AD met Velpic SAML met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Velpic SAML.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Velpic SAML te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Velpic SAML configureren](#configure-velpic-saml-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Velpic SAML-testgebruiker maken](#create-velpic-saml-test-user)** : als u een equivalent van B.Simon in Velpic SAML wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure-portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Velpic SAML**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<sub-domain>.velpicsaml.net`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Merk op dat de Aanmeldings-URL wordt voorzien door het Velpic SAML-team, en de Id-waarde beschikbaar wordt wanneer u de invoegtoepassing voor eenmalige aanmelding configureert bij Velpic SAML. U moet die waarde kopiëren vanaf de Velpic SAML-toepassingspagina en deze hier plakken.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Velpic SAML instellen** de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Velpic SAML.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Velpic SAML** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-velpic-saml-sso"></a>Eenmalige aanmelding voor Velpic configureren

1. Als u de configuratie in Velpic SAML wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Velpic SAML instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Velpic SAML-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij Velpic SAML. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 8 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Velpic SAML handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Velpic SAML-bedrijfssite. Voer daarna de volgende stappen uit:

4. Klik op het tabblad **Beheren** en ga naar de sectie **Integratie** waar u op de knop **Invoegtoepassingen** moet klikken om een nieuwe invoegtoepassing voor aanmelding te maken.

    ![Schermopname van de pagina Integration, waar Plugins kunt selecteren.](./media/velpicsaml-tutorial/velpic_1.png)

5. Klik op de knop **'Invoegtoepassing toevoegen'** .
    
    ![Schermopname waarin de knop Add Plugin is geselecteerd.](./media/velpicsaml-tutorial/velpic_2.png)

6. Klik op de tegel **SAML** op de pagina Invoegtoepassing toevoegen.
    
    ![Schermopname waarin SAML is geselecteerd op de pagina Add Plugin.](./media/velpicsaml-tutorial/velpic_3.png)

7. Voer de naam van de nieuwe SAML-invoegtoepassing in en klik op de knop **'Toevoegen'** .

    ![Schermopname met het dialoogvenster Add new SAML plugin, waarin Azure AD is ingevoerd.](./media/velpicsaml-tutorial/velpic_4.png)

8. Voer de details als volgt in:

    ![Schermopname van de pagina Azure AD, waar u de beschreven waarden kunt invoeren.](./media/velpicsaml-tutorial/velpic_5.png)

    a. Voer in het tekstvak **Naam** de naam van de SAML-invoegtoepassing in.

    b. Plak in het tekstvak **URL verlener** de **Azure AD-id** die u gekopieerd heeft uit het venster **Aanmelding configureren** van het Azure-portaal.

    c. Upload bij **Configuratie metagegevens provider** het XML-bestand met metagegevens dat u vanuit het Azure-portaal hebt gedownload.

    d. U kunt er ook voor kiezen om just-in-time SAML-inrichting in te schakelen door het selectievakje **'Automatisch nieuwe gebruikers maken'** aan te vinken. Als een gebruiker niet bestaat in Velpic en deze vlag niet is ingeschakeld, dan mislukt de aanmelding met Azure. Als de vlag is ingeschakeld, dan wordt de gebruiker automatisch ingericht in Velpic bij de eerste aanmelding. 

    e. Kopieer de **URL voor eenmalige aanmelding** uit het tekstvak en plak deze in het Azure-portaal.
    
    f. Klik op **Opslaan**.

### <a name="create-velpic-saml-test-user"></a>Velpic SAML-testgebruiker maken

Deze stap is meestal niet vereist omdat de toepassing just-in-time gebruikersinrichting ondersteunt. Als de automatische inrichting van gebruikers niet is ingeschakeld, dan kunt u gebruikers handmatig inrichten zoals hieronder beschreven.

Meld u als beheerder aan bij uw Velpic SAML-bedrijfssite en voer de volgende stappen uit:
    
1. Klik op het tabblad Beheren, ga naar de sectie Gebruikers en klik vervolgens op de knop Nieuw om gebruikers toe te voegen.

    ![gebruiker toevoegen](./media/velpicsaml-tutorial/velpic_7.png)

2. In het dialoogvenster **'Nieuwe gebruiker maken'** voert u de volgende stappen uit.

    ![gebruiker](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Typ in het tekstvak **Voornaam** de voornaam B.

    b. Typ in het tekstvak **Achternaam** de achternaam Simon.

    c. Typ in het tekstvak **Gebruikersnaam** de gebruikersnaam B.Simon.

    d. Typ in het tekstvak **E-mail** het e-mailadres van het B.Simon@contoso.com-account.

    e. De rest van de informatie is optioneel. u kunt deze invullen indien nodig.
    
    f. Klik op **OPSLAAN**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

1. Wanneer u in het toegangsvenster op de tegel Velpic SAML klikt, wordt de aanmeldingspagina van Velpic SAML weergegeven. U ziet de knop **'Aanmelden met Azure AD'** op de aanmeldingspagina.

    ![Schermopname van Learning Portal met Log In With Azure AD geselecteerd.](./media/velpicsaml-tutorial/velpic_6.png)

1. Klik op de knop **'Aanmelden met Azure AD'** om u aan te melden bij Velpic met uw Azure AD-account.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Velpic SAML met Azure AD uitproberen](https://aad.portal.azure.com/)