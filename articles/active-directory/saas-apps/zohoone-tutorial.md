---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zoho One | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zoho One.
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
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650646"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Zelfstudie: Integratie van Azure Active Directory met Zoho One

In deze zelf studie leert u hoe u Zoho kunt integreren met Azure Active Directory (Azure AD). Wanneer u Zoho-ene integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Zoho.
* Stel in dat uw gebruikers automatisch worden aangemeld voor een Zoho met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u integratie van Azure AD wilt configureren voor Zoho, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Zoho één abonnement met eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zoho One biedt ondersteuning voor door **SP** en **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-zoho-one-from-the-gallery"></a>Zoho één toevoegen vanuit de galerie

Als u de integratie van Zoho One in Azure AD wilt configureren, dient u Zoho One vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Zoho één** in het zoekvak.
1. Selecteer **Zoho één** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Azure AD SSO configureren en testen voor Zoho One

Azure AD SSO met Zoho One configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zoho.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Zoho One:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Zoho One SSO configureren](#configure-zoho-one-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Zoho één test gebruiker](#create-zoho-one-test-user)** : als u een equivalent van B. Simon wilt hebben in Zoho een item dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Zoho One** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u deze URL: `one.zoho.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > De bovenstaande waarde voor de **aanmeldings-URL** is niet echt. U krijgt de waarde `<saml-identifier>` uit stap 4 van de sectie **Eenmalige aanmelding van Zoho One configureren**. Dit wordt later in de zelfstudie uitgelegd.

    c. Klik op **Extra URL's instellen**.

    d. Typ in het tekstvak **Relaystatus** de URL: `https://one.zoho.com`

5. Als u de toepassing wilt configureren in door **SP** geïnitieerde modus, voert u de volgende stap uit:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > De bovenstaande waarde voor **Aanmeldings-URL** is niet echt. Werk de waarde bij met de echte aanmeldings-URL uit de sectie **Eenmalige aanmelding van Zoho One configureren**. Dit wordt later in de zelfstudie uitgelegd. 

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In de sectie **Zoho One instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zoho One.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Zoho One** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-zoho-one-sso"></a>Zoho One SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij uw Zoho One-bedrijfssite.

2. Klik op het tabblad **Organisatie** op **Installatie** onder **SAML-verificatie**.

    ![Zoho One org](./media/zoho-one-tutorial/set-up.png)

3. Voer op de pop-uppagina de volgende stappen uit:

    ![Zoho One sig](./media/zoho-one-tutorial/save.png)

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    b. Plak in het tekstvak **Afmeldings-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Klik op **Bladeren** om het **certificaat (Base64)** dat u hebt gedownload uit Azure Portal te uploaden.

    d. Klik op **Opslaan**.

4. Nadat u de installatie van de SAML-verificatie hebt opgeslagen, kopieert u de waarde van **SAML-Identifier** en voegt u hieraan de **Antwoord-URL** toe in plaats van `<saml-identifier>`, net zoals `https://accounts.zoho.com/samlresponse/one.zoho.com`. Plak de gegenereerde waarde vervolgens in het tekstvak **Antwoord-URL** onder de sectie **SAML-basisconfiguratie**.

    ![Zoho One saml](./media/zoho-one-tutorial/saml-identifier.png)

5. Ga naar het tabblad **Domeinen** en klik op **Domein toevoegen**.

    ![Zoho One-domein](./media/zoho-one-tutorial/add-domain.png)

6. Voer op de pagina **Domein toevoegen** de volgende stappen uit:

    ![Zoho One add domain](./media/zoho-one-tutorial/add-domain-name.png)

    a. Typ in het tekstvak **Domeinnaam** een domein zoals contoso.com.

    b. Klik op **Add**.

    >[!Note]
    >Volg [deze](https://www.zoho.com/one/help/admin-guide/domain-verification.html) stappen nadat u het domein hebt toegevoegd om uw domein te verifiëren. Zodra het domein is geverifieerd, gebruikt u de domeinnaam bij **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** in Azure Portal.

### <a name="create-zoho-one-test-user"></a>Een Zoho One-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Zoho One, moeten ze worden ingericht in Zoho One. In het geval van Zoho One is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij Zoho One aan als een beveiligingsbeheerder.

2. Klik op het tabblad **Gebruikers** op **gebruikerslogo**.

    ![Zoho One-gebruiker](./media/zoho-one-tutorial/user.png)

3. Voer op de pagina **Gebruiker toevoegen** de volgende stappen uit:

    ![Zoho One add user](./media/zoho-one-tutorial/add-user.png)
    
    a. Voer in het tekstvak **Naam** de naam van de gebruiker in, bijvoorbeeld **Britta Simon**.
    
    b. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, zoals brittasimon@contoso.com.

    >[!Note]
    >Selecteer uw geverifieerde domein in de lijst met domeinen.

    c. Klik op **Add**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar Zoho één aanmeldings-URL waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de Zoho-URL voor eenmalige aanmelding en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en meld u automatisch aan bij de Zoho waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de Zoho één tegel in de mijn apps klikt, wordt u, indien geconfigureerd in de SP-modus, omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Zoho een waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Zoho hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).