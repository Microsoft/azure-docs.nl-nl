---
title: 'Zelfstudie: Azure Active Directory-integratie met TalentLMS | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en TalentLMS configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 86ce076df076c990b8bdefa9695805d34674a03f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063484"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Zelfstudie: Azure Active Directory-integratie met TalentLMS

In deze zelf studie leert u hoe u TalentLMS integreert met Azure Active Directory (Azure AD). Wanneer u TalentLMS integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot TalentLMS.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij TalentLMS met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met TalentLMS:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Abonnement voor eenmalige aanmelding TalentLMS ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TalentLMS ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-talentlms-from-the-gallery"></a>TalentLMS toevoegen vanuit de galerie

Als u de integratie van TalentLMS in Azure AD wilt configureren, moet u TalentLMS vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **TalentLMS** in het zoekvak.
1. Selecteer **TalentLMS** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Azure AD SSO voor TalentLMS configureren en testen

Azure AD SSO met TalentLMS configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TalentLMS.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met TalentLMS:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[TALENTLMS SSO configureren](#configure-talentlms-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een TalentLMS-test gebruiker](#create-talentlms-test-user)** -om een equivalent van B. Simon in TalentLMS te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **TalentLMS** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij TalentLMS](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.TalentLMSapp.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [clientondersteuningsteam van TalentLMS](https://www.talentlms.com/contact) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. In het gedeelte **TalentLMS instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan TalentLMS.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **TalentLMS** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-talentlms-sso"></a>TalentLMS SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij uw TalentLMS-bedrijfssite.

1. Klik in de sectie **Account en instellingen** op het tabblad **Gebruikers**.

    ![Account en instellingen](./media/talentlms-tutorial/IC777296.png "Account en instellingen")

1. Klik op **Eenmalige aanmelding**.

1. Voer in de sectie Eenmalige aanmelding de volgende stappen uit:

    ![Single Sign-On](./media/talentlms-tutorial/saml.png "Eenmalige aanmelding")

    a. Selecteer **SAML 2.0** in de lijst **Type SSO-integratie**.

    b. Plak de waarde van **Azure Ad-id**, die u hebt gekopieerd uit de Azure-portal, in het tekstvak **Identity Provider (IDP)** .

    c. Plak de waarde van de **Vingerafdruk** uit de Azure-portal in het tekstvak **Vingerafdrukcertificaat**.

    d.  Plak in het tekstvak **URL voor externe aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **URL voor externe afmelding** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    f. Vul het volgende in:

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **TargetedID**

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in het vak **Voornaam**

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in het vak **Achternaam**

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in het tekstvak **E-mailadres**

1. Klik op **Opslaan**.

### <a name="create-talentlms-test-user"></a>TalentLMS-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij TalentLMS, moeten ze worden ingericht in TalentLMS. In het geval van TalentLMS moet dit handmatig gebeuren.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **TalentLMS**-tenant.

1. Klik op **Gebruikers** en klik vervolgens op **Gebruiker toevoegen**.

1. Voer de volgende stappen uit in het dialoogvenster **Add a User**:

    ![Gebruiker toevoegen](./media/talentlms-tutorial/IC777299.png "Gebruiker toevoegen")  

    a. Voer in het tekstvak **First name** de voornaam van de gebruiker in, zoals **Britta**.

    b. Voer in het tekstvak **Last name** de achternaam van de gebruiker in, zoals **Simon**.
 
    c. Typ in het tekstvak **Email-adres** het e-mailadres van de gebruiker, bijvoorbeeld `brittasimon\@contoso.com`.

    d. Klik op **Add User**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van TalentLMS-gebruikersaccounts of API's van TalentLMS gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de TalentLMS-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor TalentLMS-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel TalentLMS in de mijn apps klikt, wordt dit omgeleid naar de TalentLMS-aanmeldings-URL. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u TalentLMS hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
