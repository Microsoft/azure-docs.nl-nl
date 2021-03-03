---
title: 'Zelfstudie: Azure Active Directory-integratie met Canvas | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653023"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Zelfstudie: Azure Active Directory-integratie met Canvas

In deze zelf studie leert u hoe u canvas kunt integreren met Azure Active Directory (Azure AD). Wanneer u canvas integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het canvas.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij canvas met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:
 
* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement voor eenmalige aanmelding (SSO) op het canvas.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Canvas ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-canvas-from-the-gallery"></a>Canvas toevoegen vanuit de galerie

Voor het configureren van de integratie van Canvas in Azure AD moet u Canvas vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **canvas** in het zoekvak.
1. Selecteer **canvas** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Azure AD-SSO configureren en testen voor canvas

Configureer en test Azure AD SSO met canvas met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker op het canvas.

Voer de volgende stappen uit om Azure AD SSO met canvas te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Canvas-SSO configureren](#configure-canvas-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Canvas test gebruiker maken](#create-canvas-test-user)** : als u een equivalent van B. Simon wilt hebben in het canvas dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **canvas** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over domein en URL's voor eenmalige aanmelding met Canvas](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.instructure.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam voor Canvas](https://community.canvaslms.com/community/help) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. In de sectie **Canvas instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan het canvas.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Canvas** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-canvas-sso"></a>Canvas-SSO configureren

1. Meld u in een ander browservenster aan bij de bedrijfssite van Canvas als een beheerder.

2. Ga naar **Courses \> Managed Accounts \> Microsoft**.

    ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. Selecteer in het navigatiedeelvenster aan de linkerkant **Authentication** en klik vervolgens op **Add New SAML Config**.

    ![Verificatie](./media/canvas-lms-tutorial/ic775991.png "Verificatie")

4. Voer de volgende stappen uit op de pagina Current Integration:

    ![Huidige integratie](./media/canvas-lms-tutorial/save.png "Huidige integratie")

    a. Plak in het tekstvak **IDP Entity ID** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **Log On URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Log Out URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Change Password Link** de waarde van **Wachtwoord-URL wijzigen** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **Certificate Fingerprint** de waarde van **Vingerafdruk** die u hebt gekopieerd uit de Azure-portal.

    f. Selecteer in de lijst **Login Attribute** het aanmeldingskenmerk **NameID**.

    g. Selecteer in de lijst **Identifier Format** de id-indeling **emailAddress**.

    h. Klik op **Save Authentication Settings** (Verificatie-instellingen opslaan).

### <a name="create-canvas-test-user"></a>Testgebruiker voor Canvas maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Canvas, moeten ze worden ingericht in Canvas. Het inrichten van gebruikers moet handmatig worden uitgevoerd in Canvas.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Canvas**-tenant.

2. Ga naar **Courses \> Managed Accounts \> Microsoft**.

   ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. Klik op **Users**.

   ![Schermopname met het menu Canvas met Users geselecteerd.](./media/canvas-lms-tutorial/ic775995.png "Gebruikers")

4. Klik op **Add New User**.

   ![Schermopname met de knop Add New User.](./media/canvas-lms-tutorial/ic775996.png "Gebruikers")

5. Voer de volgende stappen uit in het dialoogvenster Een nieuwe gebruiker toevoegen:

   ![Gebruiker toevoegen](./media/canvas-lms-tutorial/ic775997.png "Gebruiker toevoegen")

   a. Voer in het tekstvak **Full Name** de volledige naam van de gebruiker in, bijvoorbeeld **Britta Simon**.

   b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

   c. Voer in het tekstvak **Login** het e-mailadres van de Azure AD-gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

   d. Selecteer **Email the user about this account creation**.

   e. Klik op **Add User**.

> [!NOTE]
> U kunt ook Azure AD-gebruikersaccounts inrichten met alle andere door Canvas geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van het canvas, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van het canvas en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel canvas in de mijn apps klikt, wordt u automatisch aangemeld bij het canvas waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u het canvas hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).