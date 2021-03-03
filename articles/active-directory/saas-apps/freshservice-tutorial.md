---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Freshservice | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Freshservice configureert.
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
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Freshservice

In deze zelfstudie leert u hoe u Freshservice kunt integreren met Azure Active Directory (Azure AD). Wanneer u Freshservice integreert met Azure AD, kunt u het volgende doen:

* Bepalen in Azure AD wie er toegang heeft tot Freshservice.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Freshservice.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Freshservice waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Freshservice biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-freshservice-from-the-gallery"></a>Freshservice toevoegen vanuit de galerie

Om de integratie van Freshservice te configureren in Azure AD, moet u Freshservice vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **Freshservice**.
1. Selecteer **Freshservice** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Eenmalige aanmelding van Azure AD voor Freshservice configureren en testen

Configureer en test eenmalige aanmelding bij Azure AD met Freshservice met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Freshservice.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Freshservice te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding configureren un Freshservice](#configure-freshservice-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Freshservice maken](#create-freshservice-test-user)** : als u een tegenhanger van B.Simon in Freshservice wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Freshservice** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<company-name>.freshservice.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<company-name>.freshservice.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van Freshservice](https://support.freshservice.com/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Freshservice instellen** in de **Azure-portal** de juiste URL(s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Freshservice.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Freshservice** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-freshservice-sso"></a>Eenmalige aanmelding configureren voor Freshservice

1. Als u de configuratie in Freshservice wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **Freshservice instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Freshservice-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Freshservice. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Freshservice handmatig wilt instellen, meldt u zich als beheerder aan bij de Freshservice-bedrijfssite.

1. Klik in het menu aan de linkerkant op **Admin** en selecteer **Helpdesk Security** bij **General Settings**.

    ![Beheerder](./media/freshservice-tutorial/configure-1.png "Beheerder")

1. Klik in de **beveiliging** op **ga naar Freshservice 360-beveiliging**.

    ![Beveiliging](./media/freshservice-tutorial/configure-2.png "Beveiliging")

1. Voer in de sectie **Security** de volgende stappen uit:

    ![Eenmalige aanmelding](./media/freshservice-tutorial/configure-3.png "Eenmalige aanmelding")
  
    a. Selecteer **On** voor **Single Sign On**.

    b. Selecteer **SAML SSO** bij **Login Method**.

    c. Plak in het tekstvak **Entity ID provided by the IdP** de waarde van **Entiteit-id** die u uit de Azure-portal hebt gekopieerd.

    d. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Selecteer **Only Signed Assertions** in de vervolgkeuzelijst  **Signing Options**.

    f. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    g. Plak in het tekstvak **Security Certificate** de waarde van **Certificaat (Base64)** die u eerder hebt vastgesteld.
  
    h. Klik op **Opslaan**.


## <a name="create-freshservice-test-user"></a>Een testgebruiker maken voor Freshservice

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij FreshService, moeten ze worden ingericht voor FreshService. In het geval van FreshService is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van **Freshservice**.

2. Klik in het menu aan de linkerkant op **Admin**.

3. Klik in de sectie **User Management** op **Requesters**.

    ![Requesters](./media/freshservice-tutorial/create-user-1.png "Requesters")

4. Klik op **New Requester**.

    ![New Requester](./media/freshservice-tutorial/create-user-2.png "New Requesters")

5. Voer in de sectie **New Requester** de vereiste velden in en klik op **Save**.
    ![New Requester](./media/freshservice-tutorial/create-user-3.png "New Requester")  

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.
    >  

    > [!NOTE]
    > U kunt andere hulpprogramma's of API's van FreshService gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Freshservice-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor Freshservice-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Freshservice in de mijn apps klikt, moet u automatisch worden aangemeld bij de Freshservice waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

 Zodra u Freshservice hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).