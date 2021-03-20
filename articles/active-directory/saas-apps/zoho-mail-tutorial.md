---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zoho | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648370"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Zelfstudie: Integratie van Azure Active Directory met Zoho

In deze zelf studie leert u hoe u Zoho integreert met Azure Active Directory (Azure AD). Wanneer u Zoho integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Zoho.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Zoho met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u integratie van Azure AD wilt configureren voor Zoho, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Abonnement voor eenmalige aanmelding Zoho ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zoho ondersteunt eenmalige aanmelding die wordt gestart vanuit **SP**

## <a name="add-zoho-from-the-gallery"></a>Zoho toevoegen vanuit de galerie

Als u de integratie van Zoho in Azure AD wilt configureren, dient u Zoho vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Zoho** in het zoekvak.
1. Selecteer **Zoho** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Azure AD SSO voor Zoho configureren en testen

Azure AD SSO met Zoho configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zoho.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Zoho:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Zoho SSO configureren](#configure-zoho-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Zoho-test gebruiker](#create-zoho-test-user)** -om een equivalent van B. Simon in Zoho te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Zoho** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company name>.zohomail.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Zoho](https://www.zoho.com/mail/contact.html) (Engelstalig) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In dit gedeelte **Zoho instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zoho.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Zoho**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-zoho-sso"></a>Zoho SSO configureren

1. Meld u in een andere webbrowser als administrator aan bij de bedrijfssite voor Zoho Mail.

2. Ga naar het **Configuratiescherm**.
   
    ![Configuratiescherm](./media/zoho-mail-tutorial/control-panel.png "Configuratiescherm")

3. Klik op het tabblad **SAML-verificatie**.
   
    ![SAML-verificatie](./media/zoho-mail-tutorial/saml-authentication.png "SAML Authentication")

4. Voer in het gedeelte **Details SAML-verificatie** de volgende stappen uit:
   
    ![Details SAML-verificatie](./media/zoho-mail-tutorial/details.png "Details SAML-verificatie")
   
    a. Plak in het tekstvak **Aanmeldings-URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.
   
    b. Plak in het tekstvak **Afmeldings-URL** de **afmeldings-URL** die u in de Azure-portal hebt gekopieerd.
   
    c. Plak in het tekstvak **Wachtwoord-URL wijzigen****Wachtwoord-URL wijzigen** dat u in de Azure-portal hebt gekopieerd.
       
    d. Open uw met Base 64 gecodeerde certificaat dat u hebt gedownload in de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze in het tekstvak **PublicKey**.
   
    e. Selecteer **RSA** als **algoritme**.
   
    f. Klik op **OK**.

### <a name="create-zoho-test-user"></a>Zoho-testgebruiker maken

Als u gebruikers van Azure AD in staat wilt stellen zich aan te melden bij Zoho Mail, moeten ze worden ingericht in Zoho Mail. Bij Zoho Mail is inrichten een handmatige taak.

> [!NOTE]
> U kunt andere hulpmiddelen voor het maken van Zoho Mail-gebruikersaccounts of door Zoho Mail geleverde API's gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u als administrator aan bij de bedrijfssite voor **Zoho Mail**.

1. Ga naar **Configuratiescherm \> Mail & Docs**.

1. Ga naar **Gebruikersdetails \> Gebruiker toevoegen**.
   
    ![Schermopname van de Zoho Mail-site met Gebruikersdetails en Gebruiker toevoegen geselecteerd.](./media/zoho-mail-tutorial/add-user-1.png "Gebruiker toevoegen")

1. Voer in het dialoogvenster **Gebruiker toevoegen** de volgende stappen uit:
   
    ![Schermopname van het dialoogvenster Gebruikers toevoegen waar u de beschreven waarden kunt invoeren.](./media/zoho-mail-tutorial/add-user-2.png "Gebruiker toevoegen")
   
    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker in, bijvoorbeeld **Britta**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker in, bijvoorbeeld **Simon**.

    c. In het tekstvak **e-mail-id** typt u de e-mail-id van de gebruiker, zoals **brittasimon \@ contoso.com**.

    d. Voer in het tekstvak **Wachtwoord** het wachtwoord van de gebruiker in.
   
    e. Klik op **OK**.  
      
    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Zoho-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor Zoho-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Zoho in de mijn apps klikt, moet u automatisch worden aangemeld bij de Zoho waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Zoho hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).