---
title: 'Zelfstudie: Azure Active Directory-integratie met Asana | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a46396d25519decdd45764acbea485bdd0571782
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428817"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Zelfstudie: Azure Active Directory-integratie met Asana

In deze zelf studie leert u hoe u asana integreert met Azure Active Directory (Azure AD). Wanneer u asana integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot asana.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij asana met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Asana-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Asana ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Asana biedt ondersteuning voor het [**geautomatiseerd** inrichten van gebruikers](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Asana toevoegen vanuit de galerie

Voor het configureren van de integratie van Asana in Azure AD moet u Asana uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **asana** in het zoekvak.
1. Selecteer **asana** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Azure AD SSO voor asana configureren en testen

Azure AD SSO met asana configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in asana.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met asana:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Asana SSO configureren](#configure-asana-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een asana-test gebruiker](#create-asana-test-user)** -om een equivalent van B. Simon in asana te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **asana** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het Asana-domein en Asana-URL's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://app.asana.com/`

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://app.asana.com/`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Asana instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan asana.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Asana** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-asana-sso"></a>Asana SSO configureren

1. Meld u in een ander browservenster aan bij uw Asana-toepassing. Voor het configureren van Asana-eenmalige aanmelding opent u de instellingen van de werkruimte door op de naam van de werkruimte in de rechterbovenhoek van het scherm te klikken. Klik vervolgens op **\<your workspace name\> Instellingen**.

    ![Instellingen voor Asana-eenmalige aanmelding](./media/asana-tutorial/settings.png)

2. Klik in het venster **Organisatie-instellingen** op **Beheer**. Klik vervolgens op **Leden moeten zich aanmelden via SAML** om de configuratie voor eenmalige aanmelding in te schakelen. Voer daarna de volgende stappen uit:

    ![Organisatie-instellingen voor eenmalige aanmelding configureren](./media/asana-tutorial/save.png)  

    a. Plak in het tekstvak **Aanmeldingspagina-URL** de **Aanmeldings-URL**.

    b. Klik met de rechtermuisknop op het certificaat dat is gedownload vanuit Azure Portal en open vervolgens het certificaatbestand in Kladblok of uw favoriete teksteditor. Kopieer de inhoud tussen de begin- en de eindcertificaattitel en plak deze in het tekstvak **X.509-certificaat**.

3. Klik op **Opslaan**. Ga naar [Asana-handleiding voor het instellen van eenmalige aanmelding](https://asana.com/guide/help/premium/authentication#gl-saml) als u meer hulp nodig hebt.

### <a name="create-asana-test-user"></a>Een Asana-testgebruiker maken

Het doel van deze sectie is om in Asana een gebruiker te maken met de naam Britta Simon. Asana ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](asana-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

In deze sectie maakt u in Asana een gebruiker met de naam Britta Simon.

1. Ga in **Asana** naar de sectie **Teams** in het linkerdeelvenster. Klik op de knop met het plusteken (+).

    ![Een Azure AD-testgebruiker maken](./media/asana-tutorial/teams.png)

2. Typ het e-mailadres van de gebruiker, bijvoorbeeld **britta.simon\@contoso.com**, in het tekstvak en selecteer vervolgens **Uitnodigen**.

3. Klik op **Uitnodiging verzenden**. De nieuwe gebruiker ontvangt een e-mailbericht in zijn of haar e-mailaccount. gebruiker moet het account maken en valideren.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de asana-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor asana-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel asana in de mijn apps klikt, wordt dit omgeleid naar de asana-aanmeldings-URL. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u asana hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
