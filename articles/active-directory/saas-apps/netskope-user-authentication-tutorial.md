---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Netskope-gebruikersverificatie | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en Netskope-gebruikersverificatie configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 1ae9e39f5a23725b17451d7144c8304194d2a1d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514364"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Netskope-gebruikersverificatie

In deze zelfstudie leert u hoe u Netskope-gebruikersverificatie integreert met Azure Active Directory (Azure AD). Wanneer u Netskope-gebruikersverificatie integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Netskope-gebruikersverificatie.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-accounts kunnen aanmelden bij Netskope-gebruikersverificatie.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement waarvoor eenmalige aanmelding (SSO) van Netskope-gebruikersverificatie is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Netskope-gebruikersverificatie ondersteunt eenmalige aanmelding die door **SP en IDP** wordt geïnitieerd

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Netskope-gebruikersverificatie toevoegen uit de galerie

Voor het configureren van de integratie van Netskope-gebruikersverificatie met Azure AD moet u Netskope-gebruikersverificatie uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Netskope-gebruikersverificatie**  in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Netskope-gebruikersverificatie** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Netskope-gebruikersverificatie

Configureer en test eenmalige aanmelding van Azure AD met Netskope-gebruikersverificatie met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Netskope-gebruikersverificatie.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Netskope-gebruikersverificatie te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van Netskope-gebruikersverificatie configureren](#configure-netskope-user-authentication-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[Testgebruiker voor Netskope-gebruikersverificatie maken](#create-netskope-user-authentication-test-user)** : zodat er een tegenhanger van B.Simon is in Netskope-gebruikersverificatie die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Netskope-gebruikersverificatie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<tenantname>.goskope.com/<customer entered string>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Deze waarden worden verderop in de zelfstudie uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Vervang de waarde door de werkelijke aanmeldings-URL. Neem contact op met het ondersteuningsteam van [Netskope-gebruikersverificatie](mailto:support@netskope.com) om de waarde voor de aanmeldings-URL te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Netskope-gebruikersverificatie instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Netskope-gebruikersverificatie.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Netskope-gebruikersverificatie** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netskope-user-authentication-sso"></a>Eenmalige aanmelding van Netskope-gebruikersverificatie configureren

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfssite van Netskope-gebruikersverificatie.

1. Klik op het tabblad **Active Platform**.

    ![Schermopname met de optie Actief platform geselecteerd in Instellingen.](./media/netskope-user-authentication-tutorial/user1.png)

1. Schuif omlaag naar **FORWARD PROXY** en selecteer **SAML**.

    ![Schermopname met SAML geselecteerd in Actief platform.](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Voer in het dialoogvenster **SAML Settings** de volgende stappen uit:

    ![Schermopname van SAML-instellingen, waarin u de beschreven waarden kunt invoeren.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Kopieer de waarde van **SAML-entiteits-id** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    b. Kopieer de waarde van **SAML ACS-URL** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal.

1. Klik op **ACCOUNT TOEVOEGEN**.

    ![Schermopname van ACCOUNT TOEVOEGEN geselecteerd in het deelvenster SAML.](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Voer op de pagina **SAML-account toevoegen** de volgende stappen uit:

    ![Schermopname van SAML-account toevoegen waar u de beschreven waarden kunt invoeren.](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. Geef in het tekstvak **NAME** de naam op, bijvoorbeeld Azure AD.

    b. Plak in het tekstvak **IDP URL** de waarde van de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **IDP ENTITY ID** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    d. Open het gedownloade bestand met metagegevens in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze in het tekstvak **IDP CERTIFICATE**.

    e. Klik op **OPSLAAN**.

### <a name="create-netskope-user-authentication-test-user"></a>Testgebruiker voor Netskope-gebruikersverificatie maken

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfssite van Netskope-gebruikersverificatie.

1. Klik in het linkernavigatievenster op het tabblad **Settings**.

    ![Schermopname met Instelling geselecteerd.](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Klik op het tabblad **Active Platform**.

    ![Schermopname met de optie Actief platform geselecteerd in Instellingen.](./media/netskope-user-authentication-tutorial/user1.png)

1. Klik op het tabblad **Users**.

    ![Schermopname met Gebruikers geselecteerd in Actief platform.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Klik op **ADD USERS**.

    ![Schermopname met het dialoogvenster Gebruikers waarin u GEBRUIKERS TOEVOEGEN kunt selecteren.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Voer het e-mailadres van de gebruiker in die u wilt toevoegen en klik op **ADD**.

    ![Schermopname van Gebruikers toevoegen waarin u een lijst met gebruikers kunt invoeren.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Netskope-gebruikersverificatie klikt in het toegangsvenster wordt u automatisch aangemeld bij de instantie van Netskope-gebruikersverificatie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Netskope-gebruikersverificatie met Azure AD](https://aad.portal.azure.com/)