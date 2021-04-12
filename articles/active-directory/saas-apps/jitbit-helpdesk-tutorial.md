---
title: 'Zelfstudie: Azure Active Directory-integratie met Jitbit Helpdesk | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Jitbit Helpdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581798"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Zelfstudie: Azure Active Directory-integratie met Jitbit Helpdesk

In deze zelf studie leert u hoe u Jitbit-Help Desk integreert met Azure Active Directory (Azure AD). Wanneer u Jitbit-Help Desk integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Jitbit-Help Desk.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor Jitbit-Help Desk met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement voor eenmalige aanmelding (SSO) van Jitbit helpdesk.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Jitbit-Help Desk ondersteunt door **SP** geïnitieerde SSO.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Jitbit Help Desk toevoegen vanuit de galerie

Om de integratie van Jitbit Helpdesk te configureren in Azure AD, moet u Jitbit Helpdesk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Jitbit Help Desk** in het zoekvak.
1. Selecteer **Jitbit Help Desk** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Azure AD SSO voor Jitbit-Help Desk configureren en testen

Azure AD SSO met Jitbit-Help Desk configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jitbit Help Desk.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Jitbit Help Desk:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Jitbit Help Desk SSO configureren](#configure-jitbit-helpdesk-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Jitbit-gebruikers-helpdesk test gebruiker](#create-jitbit-helpdesk-test-user)** -om een equivalent van B. Simon in Jitbit-Help Desk te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Jitbit Help Desk** Application Integration de sectie **Manage (beheren** ) en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ in het tekstvak **URL voor aanmelden** een van de url's met het volgende patroon:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Jitbit Helpdesk](https://www.jitbit.com/support/) om deze waarde op te vragen.

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://www.jitbit.com/web-helpdesk/`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **Jitbit Helpdesk instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Jitbit-Help Desk.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Jitbit Helpdesk** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name=&quot;configure-jitbit-helpdesk-sso&quot;></a>Jitbit Help Desk-SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij uw Jitbit Helpdesk-bedrijfssite.

1. Klik in de werkbalk bovenaan op **Beheer**.

    ![Beheer](./media/jitbit-helpdesk-tutorial/settings.png &quot;Beheer")

1. Klik op **Algemene instellingen**.

    ![Schermopname van de link Algemene instellingen.](./media/jitbit-helpdesk-tutorial/general.png "Gebruikers, bedrijven en machtigingen")

1. Voer in het gedeelte **Verificatie-instellingen** configureren de volgende stappen uit:

    ![Verificatie-instellingen](./media/jitbit-helpdesk-tutorial/authentication.png "Verificatie-instellingen")

    a. Selecteer **SAML 2.0-eenmalige aanmelding inschakelen** om u aan te melden met behulp van eenmalige aanmelding (SSO) met **OneLogin**.

    b. Plak in het tekstvak **Eindpunt-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Open in Kladblok het met **Base 64** gecodeerde certificaat, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509-certificaat**

    d. Klik op **Save changes** (Wijzigingen opslaan).

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit Helpdesk-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Jitbit Helpdesk, moeten ze worden ingericht in Jitbit Helpdesk. In het geval van Jitbit Helpdesk is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Jitbit Helpdesk**-tenant.

1. Klik in het menu bovenaan op **Beheer**.

    ![Beheer](./media/jitbit-helpdesk-tutorial/settings.png "Beheer")

1. Klik op **Gebruikers, bedrijven en machtigingen**.

    ![Gebruikers, bedrijven en machtigingen](./media/jitbit-helpdesk-tutorial/users.png "Gebruikers, bedrijven en machtigingen")

1. Klik op **Gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/jitbit-helpdesk-tutorial/add.png "Gebruiker toevoegen")

1. Typ in het gedeelte Maken de gegevens van het Azure AD-account dat u als volgt wilt inrichten:

    ![Maken](./media/jitbit-helpdesk-tutorial/create-section.png "Maken")

   a. Typ de gebruikersnaam van de gebruiker in het tekstvak **Gebruikersnaam**, bijvoorbeeld **Britta Simon**.

   b. Typ in het tekstvak **E-mail** het e-mailadres van de gebruiker, bijvoorbeeld **BrittaSimon@contoso.com** .

   c. Typ in het tekstvak **First Name** de voornaam van de gebruiker, bijvoorbeeld **Britta**.

   d. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

   e. Klik op **Create**.

> [!NOTE]
> U kunt de Jitbit Helpdesk-gebruikersaccounts ook inrichten met behulp van andere hulpprogramma's of API's van Jitbit Helpdesk voor het maken van Azure AD-gebruikersaccounts.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van de Jitbit-Help Desk waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van de Jitbit-Help Desk en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de helpdesk tegel Jitbit in de mijn apps klikt, wordt dit omgeleid naar de Jitbit-aanmeld-URL van de Help Desk. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Jitbit-Help Desk hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).