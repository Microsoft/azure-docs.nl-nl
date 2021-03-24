---
title: 'Zelfstudie: Azure Active Directory-integratie met SolarWinds Service Desk (voorheen Samanage) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SolarWinds Service Desk (voorheen Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956040"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Zelfstudie: Azure Active Directory-integratie met SolarWinds Service Desk (voorheen Samanage)

In deze zelf studie leert u hoe u SolarWinds integreert met Azure Active Directory (Azure AD). Wanneer u SolarWinds integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SolarWinds.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij SolarWinds met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SolarWinds-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SolarWinds ondersteunt door **SP** geïnitieerde SSO.

## <a name="add-solarwinds-from-the-gallery"></a>SolarWinds toevoegen vanuit de galerie

Om de integratie van SolarWinds te configureren in Azure AD, moet u SolarWinds vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SolarWinds** in het zoekvak.
1. Selecteer **SolarWinds** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Azure AD SSO voor SolarWinds configureren en testen

Azure AD SSO met SolarWinds configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SolarWinds.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met SolarWinds:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SOLARWINDS SSO configureren](#configure-solarwinds-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een SolarWinds-test gebruiker](#create-solarwinds-test-user)** -om een equivalent van B. Simon in SolarWinds te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **SolarWinds** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL voor eenmalige aanmelding en de id, zoals later in de zelfstudie wordt uitgelegd. Neem voor meer informatie contact op met het [ondersteuningsteam van Samanage](https://www.samanage.com/support). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **SolarWinds instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SolarWinds.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SolarWinds** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>SolarWinds SSO configureren

1. Meld u in een ander browservenster aan bij uw SolarWinds-bedrijfssite als beheerder.

2. Klik op **Dashboard** en selecteer **Setup** in het linker navigatievenster.
   
    ![Dashboard](./media/samanage-tutorial/tutorial-samanage-1.png "Dashboard")

3. Klik op **Single Sign-On**.
   
    ![Single Sign-On](./media/samanage-tutorial/tutorial-samanage-2.png "Eenmalige aanmelding")

4. Navigeer naar de sectie **Login using SAML** en voer de volgende stappen uit:
   
    ![Aanmelden met SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Aanmelden met SAML")
 
    a. Klik op **Enable Single Sign-On with SAML**.  
 
    b. Plak de waarde van **Azure Ad-id**, die u hebt gekopieerd uit de Azure-portal, in het tekstvak **Identity Provider URL**.    
 
    c. Bevestig dat de **Login URL** overeenkomt met de **Aanmeldings-URL** van de sectie **Standaard SAML-configuratie** in de Azure-portal.
 
    d. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
 
    e. Typ in het tekstvak **SAML Issuer** de app-id-URI die is ingesteld in uw id-provider.
 
    f. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Paste your Identity Provider x.509 Certificate below**.
 
    g. Klik op **Create users if they do not exist in SolarWinds**.
 
    h. Klik op **Update**.

### <a name="create-solarwinds-test-user"></a>Een SolarWinds-testgebruiker maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij SolarWinds, moeten ze worden ingericht voor SolarWinds.  
In het geval van SolarWinds is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij uw SolarWinds-bedrijfssite aan als beheerder.

2. Klik op **Dashboard** en selecteer **Setup** in het linker navigatievenster.
   
    ![Installatie](./media/samanage-tutorial/tutorial-samanage-1.png "Instellen")

3. Klik op het tabblad **Users**
   
    ![Gebruikers](./media/samanage-tutorial/tutorial-samanage-6.png "Gebruikers")

4. Klik op **New User**.
   
    ![Nieuwe gebruiker](./media/samanage-tutorial/tutorial-samanage-7.png "Nieuwe gebruiker")

5. Typ bij **Name** en **Email Address** de naam en het e-mailadres van een Azure Active Directory-account dat u wilt inrichten, en klik op **Create user**.
   
    ![Create User](./media/samanage-tutorial/tutorial-samanage-8.png "Gebruiker maken")
   
   >[!NOTE]
   >De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt. U kunt ook alle andere hulpprogramma's voor het creëren van SolarWinds-gebruikersaccounts of API's van SolarWinds gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de SolarWinds-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor SolarWinds-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel SolarWinds in de mijn apps klikt, wordt dit omgeleid naar de SolarWinds-aanmeldings-URL. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u SolarWinds hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).