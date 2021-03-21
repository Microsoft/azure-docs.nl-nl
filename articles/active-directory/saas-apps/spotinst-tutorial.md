---
title: 'Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Spotinst | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Spotinst.
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
ms.openlocfilehash: 402b5a975b3ded8327edcea81b680e9990bac39e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Spotinst

In deze zelfstudie leert u hoe u Spotinst integreert met Azure Active Directory (Azure AD). Wanneer u Spotinst integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Spotinst.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Spotinst.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Spotinst waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Spotinst biedt ondersteuning voor **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-spotinst-from-the-gallery"></a>Spotinst toevoegen vanuit de galerie

Voor het configureren van de integratie van Spotinst in Azure Active Directory, moet u Spotinst uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Spotinst** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Spotinst** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Eenmalige aanmelding van Azure AD voor Spotinst configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Spotinst met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Spotinst.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Spotinst te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Spotinst configureren](#configure-spotinst-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Spotinst-testgebruiker maken](#create-spotinst-test-user)** : als u een equivalent van B.Simon in Spotinst wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Spotinst**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door IDP geïnitieerde modus wilt configureren:

   1. Zorg ervoor dat de **Antwoord-URL** is ingesteld op https://console.spotinst.com/auth/saml.
   1. Voer in de **Relaystatus** uw Spotinst Organisatie-ID in. U kunt die ook in het tabblad **SSO** bevestigen.
   1. **Aanmeldings-URL** moet leeg zijn.

1. Klik op **Opslaan**.

1. In de Spotinst-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Spotinst-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | -----| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Spotinst instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Spotinst.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Spotinst** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-spotinst-sso"></a>Eenmalige aanmelding voor Spotinst configureren

1. Meld u in een ander browservenster als een beveiligingsbeheerder aan bij Spotinst.

2. Klik rechtsboven in het scherm op het **gebruikerspictogram** en klik op **Instellingen**.

    ![Schermopname met Instellingen geselecteerd vanuit het pictogram Gebruiker.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Klik bovenin op het tabblad **BEVEILIGING** en selecteer vervolgens **ID-providers** en voer de volgende stappen uit:

    ![Spotinst-beveiliging](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopieer de waarde van de **Relaystatus** voor uw exemplaar en plak deze in het tekstvak **Relaystatus** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    b. Klik op **BROWSE** om het XML-bestand met metagegevens dat u hebt gedownload van de Azure-portal te uploaden.

    c. Klik op **OPSLAAN**.

### <a name="create-spotinst-test-user"></a>Testgebruiker maken voor Spotinst

Het doel van dit gedeelte is het maken van een gebruiker met de naam van Britta Simon in Spotinst.

1. Als u de toepassing hebt geconfigureerd in de door **SP** geïnitieerde modus, voert u de volgende stappen uit:

   a. Meld u in een ander browservenster als een beveiligingsbeheerder aan bij Spotinst.

   b. Klik rechtsboven in het scherm op het **gebruikerspictogram** en klik op **Instellingen**.

    ![Schermopname met Instellingen geselecteerd vanuit het pictogram Gebruiker.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klik op **Gebruikers** en selecteer **GEBRUIKER TOEVOEGEN**.

    ![Schermopname van GEBRUIKER TOEVOEGEN geselecteerd vanuit Gebruikers.](./media/spotinst-tutorial/adduser1.png)

    d. Voer in de sectie Gebruiker toevoegen de volgende stappen uit:

    ![Schermopname van de sectie Gebruiker toevoegen waarin u de beschreven waarden kunt invoeren.](./media/spotinst-tutorial/adduser2.png)

    * Voer in het tekstvak **volledige naam** de volledige naam van de gebruiker in `BrittaSimon` .

    * Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, bijvoorbeeld `brittasimon@contoso.com`.

    * Selecteer de organisatie-specifieke gegevens voor de **Organisatierol, de Account-rol en Accounts**.

2. Als u de toepassing hebt geconfigureerd in de door **IDP** geïnitieerde modus, is er in deze sectie geen actie van u nodig. Spotinst biedt ondersteuning voor Just-In-Time-inrichting; dit is standaard ingeschakeld. Tijdens een poging Spotinst te openen, wordt er een nieuwe gebruiker gemaakt als deze nog niet bestaat.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Spotinst in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Spotinst waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Spotinst proberen met Azure AD](https://aad.portal.azure.com/)