---
title: 'Zelfstudie: Azure Active Directory-integratie met Absorb LMS | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 268943463002ddd1dbdbf67df9587758f81f537f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646517"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Zelfstudie: Azure Active Directory-integratie met Absorb LMS

In deze zelf studie leert u hoe u de absorberende LMS integreert met Azure Active Directory (Azure AD). Wanneer u de absorberende LMS integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het absorberen van LMS.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden om LMS te absorberen met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Absorb LMS:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Abonnement voor eenmalige aanmelding voor de LMS is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Absorb LMS ondersteunt door **IDP** geïnitieerde SSO

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-absorb-lms-from-the-gallery"></a>Absorberende LMS toevoegen vanuit de galerie

Om de integratie van Absorb LMS in Azure AD te configureren, moet u Absorb LMS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie in **de galerie toevoegen** de tekst **absorberen** in het zoekvak.
1. Selecteer **LMS** in het paneel resultaten absorberen en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Azure AD-SSO configureren en testen voor het absorberen van de LMS

Azure AD SSO configureren en testen met een geabsorbeerde LMS met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de absorberende LMS.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met de geabsorbeerde LMS:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. De eenmalige aanmelding configureren aan de kant van de toepassing van de sign-on- **[SSO](#configure-absorb-lms-sso)** .
    1. U kunt een gebruiker van de test voor een **[geabsorbeerde LMS maken](#create-absorb-lms-test-user)** , zodat deze een equivalent van B. Simon heeft dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina de integratie van de toepassing **absorberen** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Klik op de pagina **Eenmalige aanmelding met SAML instellen** u de knop **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    Gebruik de volgende configuratie als u met **Absorb 5 - UI** werkt:

    a. Typ in het tekstvak **id** een URL met het volgende patroon: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Typ in het tekstvak **antwoord-URL** een URL met het volgende patroon: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Gebruik de volgende configuratie als u met **Absorb 5 - New Learner Experience** werkt:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Typ in het tekstvak **antwoord-URL** een URL met het volgende patroon: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Absorb LMS](https://support.absorblms.com/hc/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In het gedeelte **Absorb LMS instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de absorberende LMS.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **LMS absorberen**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-absorb-lms-sso"></a>Eenmalige aanmelding voor de microlms configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Absorb LMS.

2. Selecteer rechtsboven de knop **Account**.

    ![De knop Account](./media/absorblms-tutorial/account.png)

3. Selecteer **Portal Settings** in het deelvenster Account.

    ![De koppeling Portal Settings](./media/absorblms-tutorial/portal.png)

4. Selecteer het tabblad **Manage SSO Settings**.

    ![Het tabblad Users](./media/absorblms-tutorial/sso.png)

5. Ga als volgt te werk op de pagina **Manage Single Sign-On Settings**:

    ![De pagina Manage Single Sign-On Settings](./media/absorblms-tutorial/settings.png)

    a. Voer in het tekstvak **Name** een naam in, zoals Azure AD Marketplace SSO.

    b. Selecteer **SAML** bij **Method**.

    c. Open in Kladblok het certificaat dat u hebt gedownload van de Azure-portal. Verwijder de tags **---BEGIN CERTIFICATE---** en **---END CERTIFICATE---**. Plak vervolgens de resterende inhoud in het vak **Key**.

    d. Selecteer **Identity Provider Initiated** in de lijst **Mode**.

    e. Selecteer in de lijst **Id Property** het kenmerk dat u in Azure AD hebt geconfigureerd als de gebruikers-id. Als u bijvoorbeeld *nameidentifier* hebt geselecteerd in Azure AD, selecteert u **Username**.

    f. Selecteer **Sha256** bij **Signature Type**.

    g. Plak in het vak **Login URL** de waarde voor **URL gebruikerstoegang** van de pagina **Eigenschappen** van de toepassing in de Azure-portal.

    h. Plak in het vak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit het venster **Aanmelding configureren** in de Azure-portal.

    i. Zet **Automatically Redirect** op **On**.

6. Selecteer **Save.**

    ![De wisselknop Only Allow SSO Login](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Testgebruiker voor Absorb LMS maken

Azure AD-gebruikers kunnen zich alleen aanmelden bij Absorb LMS als ze zijn ingesteld in Absorb LMS. In het geval van Absorb LMS moet dit handmatig gebeuren.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u als beheerder aan bij de bedrijfssite van Absorb LMS.

2. Selecteer **Users** in het deelvenster **Users**.

    ![De koppeling Users](./media/absorblms-tutorial/users.png)

3. Selecteer het tabblad **User**.

    ![De vervolgkeuzelijst Add New](./media/absorblms-tutorial/add.png)

4. Ga als volgt te werk op de pagina **Add User**:

    ![De pagina Add User](./media/absorblms-tutorial/user.png)

    a. Typ in het vak **First Name** de voornaam, zoals **Britta**.

    b. Typ in het vak **Last Name** de achternaam, zoals **Simon**.

    c. Typ in het vak **Username** de volledige naam, zoals **Britta Simon**.

    d. Typ in het vak **Password** het wachtwoord voor de gebruiker.

    e. Typ het wachtwoord nogmaals in het veld **Confirm Password**.

    f. Stel **Is Active** in op **Active**.

5. Selecteer **Save.**

    ![De wisselknop Only Allow SSO Login](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Standaard is inrichten van gebruikers niet ingeschakeld in SSO. Als de klant deze functie wil inschakelen, moet dit volgens de instructies in [deze](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentatie. Het inrichten van gebruikers is overigens alleen beschikbaar in **Absorb 5 - New Learner Experience** met ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en meld u automatisch aan bij het absorberende LMS waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel LMS opnemen in de mijn apps klikt, moet u automatisch worden aangemeld bij het absorberende LMS waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de absorberende LMS hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).