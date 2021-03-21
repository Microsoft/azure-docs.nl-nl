---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Pulse Secure PCS | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Pulse Secure PCS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511440"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Pulse Secure PCS

In deze zelfstudie leert u hoe u Pulse Secure PCS integreert met Azure Active Directory (Azure AD). Wanneer u Pulse Secure PCS integreert met Azure AD, kunt u het volgende doen:

* In Azure AD regelen wie toegang tot Pulse Secure PCS heeft.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Pulse Secure PCS.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Pulse Secure PCS waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Pulse Secure PCS ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Pulse Secure PCS toevoegen uit de galerie

Voor het configureren van de integratie van Pulse Secure PCS met Azure AD moet u Pulse Secure PCS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Pulse Secure PCS**.
1. Selecteer **Pulse Secure PCS** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Eenmalige aanmelding van Azure AD voor Pulse Secure PCS configureren en testen

Configureer en test eenmalige aanmelding van Azure AD voor Pulse Secure PCS met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Pulse Secure PCS.

Doorloop de volgende stappen om eenmalige aanmelding van Azure AD met Pulse Secure PCS te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Pulse Secure PCS configureren](#configure-pulse-secure-pcs-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker maken in Pulse Secure PCS](#create-pulse-secure-pcs-test-user)** : om in Pulse Secure PCS een tegenhanger van B.Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de integratiepagina van de toepassing **Pulse Secure PCS** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, antwoord-URL en id. Neem contact op met het ondersteuningsteam van [Pulse Secure PCS](mailto:support@pulsesecure.net) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Pulse Secure PCS instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Pulse Secure PCS.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Pulse Secure PCS** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-pulse-secure-pcs-sso"></a>Eenmalige aanmelding bij Pulse Secure PCS configureren

In deze sectie worden de SAML-configuraties beschreven die vereist zijn voor het configureren van PCS als SAML SP. De andere basisconfiguraties, zoals het maken van realms en rollen, worden niet behandeld.

**Pulse Connect Secure-configuraties omvatten:**

* Azure AD configureren als SAML-metagegevensprovider
* SAML Auth-server configureren
* Toewijzen aan de respectieve realms en rollen

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Azure AD configureren als SAML-metagegevensprovider

Voer deze stappen uit op de volgende pagina:

![Configuratie van Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Meld u aan bij de Pulse Connect Secure-beheerdersconsole
1. Ga naar **Systeem -> Configuratie -> SAML**
1. Klik op **Nieuwe metagegevensprovider**
1. Geef in het tekstvak **Naam** een geldige naam op
1. Upload het gedownloade XML-bestand met metagegevens van de Azure Portal naar het **Azure AD-metagegevensbestand**.
1. Selecteer **Niet-ondertekende metagegevens accepteren**
1. Selecteer rollen als **Id-provider**
1. Klik op **Wijzigingen opslaan**.

#### <a name="steps-to-create-a-saml-auth-server"></a>Stappen voor het maken van een SAML Auth-server:

1. Navigeer naar **Verificatie -> Auth-servers**
1. Selecteer **Nieuw: SAML-server** en klik op **Nieuwe server**

    ![Pulse Connect Secure Auth-server](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Voer de volgende stappen uit op de instellingenpagina:

    ![Pulse Connect Secure Auth-serverinstellingen](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Geef **Servernaam** op in het tekstvak.

    b. Selecteer **SAML Versie 2.0** en **Configuratiemodus** als **Metagegevens**.

    c. Kopieer de waarde voor **Veilige eintiteits-id verbinden** en plak deze in het dialoogvenster **Standaard SAML-configuratie** in Azure Portal in het vak **Id-URL**.

    d. Selecteer de waarde van Azure AD-entiteits-id uit de **vervolgkeuzelijst Entiteits-id van identiteitsprovider**.

    e. Selecteer de waarde van de aanmeldings-URL van Azure AD uit de **vervolgkeuzelijst Service-URL voor eenmalige aanmelding van identiteitsprovider**.

    f. **Eenmalige afmelding** is een optionele instelling. Als deze optie is geselecteerd, wordt u na de afmelding gevraagd om opnieuw te verifiëren. Als deze optie niet is ingeschakeld en u de browser niet hebt afgesloten, kunt u zonder verificatie opnieuw verbinding maken.

    g. Selecteer de **Aangevraagde Auth-contextklasse** als **Wachtwoord** en de **Vergelijkingsmethode** als **exact**.

    h. Stel een aantal dagen in bij de **Geldigheid van metagegevens**.
    
    i. Klik op **Save Changes**.

### <a name="create-pulse-secure-pcs-test-user"></a>Testgebruiker voor Pulse Secure PCS maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Pulse Secure PCS. Neem contact op met het [ondersteuningsteam van Pulse Secure PCS](mailto:support@pulsesecure.net) om de gebruikers toe te voegen in het Pulse Secure PCS-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt hiermee omgeleid naar de aanmeldings-URL van Pulse Secure PCS, waar u de aanmeldingsstroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van Pulse Secure PCS en initieer de aanmeldingsstroom daar.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u op de tegel 'Pulse Secure PCS' in het toegangsvenster klikt, wordt u omgeleid naar de aanmeldings-URL voor Pulse Secure PCS. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Pulse Secure PCS hebt geconfigureerd kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).