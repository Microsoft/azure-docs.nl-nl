---
title: 'Zelfstudie: Azure Active Directory-integratie met de Mimecast-beheerconsole | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en de Mimecast-beheerconsole.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 606665cead215c049e55f22a5f8ff0f668181fb4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647181"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Zelfstudie: Azure Active Directory-integratie met de Mimecast-beheerconsole voor eenmalige aanmelding

In deze zelfstudie leert u hoe u de Mimecast-beheerconsole kunt integreren met Azure Active Directory (Azure AD). Wanneer u de Mimecast-beheerconsole integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot de Mimecast-beheerconsole.
* Uw gebruikers zich automatisch laten aanmelden bij de Mimecast-beheerconsole met hun Azure AD-account.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op de Mimecast-beheerconsole waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* De Mimecast-beheerconsole biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="add-mimecast-admin-console-from-the-gallery"></a>Mimecast-beheer console toevoegen vanuit de galerie

Voor het configureren van de integratie van de Mimecast-beheerconsole in Azure AD moet u de Mimecast-beheerconsole uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Mimecast-beheerconsole** in het zoekvak.
1. Selecteer **Mimecast-beheerconsole** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-admin-console"></a>Azure AD SSO configureren en testen voor Mimecast-beheer console

Configureer en test eenmalige aanmelding van Azure AD bij de Mimecast-beheerconsole met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Mimecast-beheerconsole.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Mimecast-beheer console:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor de Mimecast-beheerconsole configureren](#configure-mimecast-admin-console-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
    1. **[Een testgebruiker maken in de Mimecast-beheerconsole](#create-mimecast-admin-console-test-user)** : voor een equivalent van B.Simon in de Mimecast-beheerconsole dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina Mimecast van de **beheer console** -toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door IDP geïnitieerde modus wilt configureren:

    a. In het tekstvak **id** typt u de URL met het volgende patroon:

    | Regio  |  Waarde | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Verenigde Staten   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Zuid-Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australië       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > U vindt de waarde `accountcode` in de Mimecast-beheerconsole onder **Account** > **Settings** > **Account Code**. Voeg de `accountcode` toe aan de id.

    b. Typ de URL in het tekstvak **Antwoord-URL**: 

    | Regio  |  Waarde | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Verenigde Staten   | `https://us-api.mimecast.com/login/saml`|
    | Zuid-Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Australië       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Als u de toepassing wilt configureren in door **SP** geïnitieerde modus:

    Typ een URL in het tekstvak **Aanmeldings-URL**: 

    | Regio  |  Waarde | 
    | --------------- | --------------- | 
    | Europa          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | Verenigde Staten   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | Zuid-Afrika    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | Australië       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | Offshore        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

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

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot de Mimecast-beheerconsole.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Mimecast-beheerconsole** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-mimecast-admin-console-sso"></a>Eenmalige aanmelding voor de Mimecast-beheerconsole configureren

1. Meld u in een ander browservenster bij de Mimecast-beheerconsole aan.

1. Ga naar **Administration** > **Services** > **Applications**.

    ![Schermopname van het Mimecast-venster met Applications geselecteerd.](./media/mimecast-admin-console-tutorial/services.png)

1. Klik op het tabblad **Authentication Profiles**.
    
    ![Schermopname van het tabblad Application met Authentication Profiles geselecteerd.](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Klik op het tabblad **New Authentication Profile**.

    ![Schermopname met New Authentication Profile geselecteerd.](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Geef een geldige beschrijving op in het tekstvak **Description** en schakel het selectievakje **Enforce SAML Authentication for Administration Console** in.

    ![Schermopname laat zien waar u Enforce SAML Authentication for Administration Console selecteert.](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. Voer de volgende stappen uit op de pagina **SAML Configuration for Administration Console**:

    ![Schermopname van de pagina SAML Configuration for Administration Console waar u de beschreven waarden kunt invoeren.](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. Selecteer bij **Provider** **Azure Active Directory** in de vervolgkeuzelijst.

    b. Plak in het tekstvak **Metadata URL** de waarde van **App Federation Metadata URL** die u uit de Azure-portal hebt gekopieerd.

    c. Klik op **Import**. Na het importeren van de metagegevens-URL, worden de velden automatisch ingevuld. U hoeft geen actie uit te voeren voor deze velden.

    d. Schakel de selectievakjes **Use Password protected Context** en **Use Integrated Authentication Context** uit.

    e. Klik op **Opslaan**.

### <a name="create-mimecast-admin-console-test-user"></a>Testgebruiker voor de Mimecast-beheerconsole maken

1. Meld u in een ander browservenster bij de Mimecast-beheerconsole aan.

1. Ga naar **Administration** > **Directories** > **Internal Directories**.

    ![Schermopname van het Mimecast-venster met Internal Directories geselecteerd.](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Selecteer uw domein als het domein hieronder wordt vermeld, of maak een nieuw domein door op **New Domain** te klikken.

    ![Schermopname van het geselecteerde domein.](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Klik op het tabblad **New Address**.

    ![Schermopname met New Address geselecteerd.](./media/mimecast-admin-console-tutorial/new-address.png)

1. Geef de vereiste gebruikersgegevens op de volgende pagina op:

    ![Schermopname van de pagina waarin u de beschreven waarden kunt invoeren.](./media/mimecast-admin-console-tutorial/user-information.png)

    a. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, bijvoorbeeld `B.Simon@yourdomainname.com`.

    b. Voer in het tekstvak **globale naam** de **volledige naam** van de gebruiker in.

    c. Voer in de tekstvakken **Password** en **Confirm Password** het wachtwoord van de gebruiker in.

    d. Schakel het selectievakje **Force Change at Login** in.

    e. Klik op **Opslaan**.

    f. Als u rollen aan de gebruiker wilt toewijzen, klikt u op **Role Edit** en wijst u de vereiste rol toe aan de gebruiker volgens de vereisten van uw organisatie.

    ![Schermopname van Address Settings waar u Role Edit kunt selecteren.](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Mimecast-URL van de beheer console, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van de Mimecast-beheer console en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Mimecast-beheer console waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Mimecast-beheer console in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Mimecast-beheer console waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de Mimecast-beheer console hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).