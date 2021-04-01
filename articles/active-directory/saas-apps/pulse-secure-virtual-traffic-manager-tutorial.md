---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Pulse Secure Virtual Traffic Manager | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Pulse Secure Virtual Traffic Manager.
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
ms.openlocfilehash: 1e295075a5c1ae8daa6673757770bbef01208c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92505720"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Pulse Secure Virtual Traffic Manager

In deze zelfstudie leert u hoe u Pulse Secure Virtual Traffic Manager integreert met Azure Active Directory (Azure AD). Wanneer u Pulse Secure Virtual Traffic Manager integreert met Azure Active Directory, kunt u het volgende doen:

* In Azure Active Directory regelen wie toegang heeft tot Pulse Secure Virtual Traffic Manager.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Pulse Secure Virtual Traffic Manager.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Pulse Secure Virtual Traffic Manager waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Pulse Secure Virtual Traffic Manager ondersteunt **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Pulse Secure Virtual Traffic Manager toevoegen vanuit de galerie

Voor het configureren van de integratie van Pulse Secure Virtual Traffic Manager met Azure Active Directory moet u Pulse Secure Virtual Traffic Manager uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Pulse Secure Virtual Traffic Manager**.
1. Selecteer **Pulse Secure Virtual Traffic Manager** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Eenmalige aanmelding van Azure Active Directory voor Pulse Secure Virtual Traffic Manager configureren en testen

Configureer en test eenmalige aanmelding van Azure Active Directory voor Pulse Secure Virtual Traffic Manager met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure Active Directory-gebruiker en de bijbehorende gebruiker in Pulse Secure Virtual Traffic Manager.

Doorloop de volgende stappen om eenmalige aanmelding van Azure Active Directory met Pulse Secure Virtual Traffic Manager te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Pulse Secure Virtual Traffic Manager configureren](#configure-pulse-secure-virtual-traffic-manager-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker maken in Pulse Secure Virtual Traffic Manager](#create-pulse-secure-virtual-traffic-manager-test-user)** : om in Pulse Secure Virtual Traffic Manager een tegenhanger van B.Simon te hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de integratiepagina van de toepassing **Pulse Secure Virtual Traffic Manager** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<published virtual server FQDN>/saml/consume`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<published virtual server FQDN>/saml/metadata`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Pulse Secure Virtual Traffic Manager instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Pulse Secure Virtual Traffic Manager.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Pulse Secure Virtual Traffic Manager** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Eenmalige aanmelding voor Pulse Secure Virtual Traffic Manager configureren

In deze sectie wordt de configuratie beschreven die nodig is om SAML-verificatie van Azure Active Directory in te schakelen op de Pulse Virtual Traffic Manager. Alle configuratiewijzigingen worden aangebracht op de Pulse Virtual Traffic Manager met behulp van de webgebruikersinterface voor beheerders. 

#### <a name="create-a-saml-trusted-identity-provider"></a>Een vertrouwde id-provider voor SAML maken

a. Ga naar de pagina **Gebruikersinterface apparaatbeheerder Pulse Virtual Traffic Manager > Catalogus > SAML > Catalogus vertrouwde id-providers** en klik op **Bewerken**.

![pagina met saml-catalogussen](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Voeg de gegevens voor de nieuwe vertrouwde id-provider voor SAML toe, kopieer de informatie uit de ondernemingstoepassing van Azure Active Directory op de pagina instellingen voor eenmalige aanmelding en klik vervolgens op **Nieuwe vertrouwde id-provider maken**.

![Een vertrouwde id-provider maken](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* Voer in het tekstvak **Naam** een naam in voor de vertrouwde id-provider. 

* Plak in het tekstvak **Entiteits_id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.  

* Plak in het tekstvak **Url** de waarde van de **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd. 

* Open in de Azure Portal het gedownloade **Certificaat** in Kladblok en plak de inhoud in het tekstvak **Certificaat**.

c. Controleer of de nieuwe SAML-id-provider is gemaakt. 

![Vertrouwde id-providers verifiëren](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>De virtuele server configureren om Azure Active Directory-verificatie te gebruiken

a. Ga naar de pagina **Gebruikersinterface apparaatbeheerder Pulse Virtual Traffic Manager > Services > Virtuele servers** en klik op **Bewerken** naast de eerder gemaakte virtuele server.

![Virtuele servers bewerken](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. Klik in de sectie **Verificatie** op **Bewerken**. 

![Sectie verificatie](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Configureer de volgende verificatie-instellingen voor de virtuele server: 

1. Verificatie -

    ![verificatie-instellingen voor de virtuele server](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. Selecteer in het **Auth!type** **SAML-serviceprovider** 

    b. Stel **Auth!verbose** in op 'Ja' om problemen met de verificatie op te lossen, laat anders de standaardwaarde 'Nee' staan 

2. Beheer verificatiesessie -

    ![Beheer verificatiesessie](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Laat voor **Auth!session!cookie_name** de standaardwaarde 'VS_SamlSP_Auth' staan 

    b. Laat voor **auth!session!timeout**, de standaardwaarde '7200' staan 

    c. Stel **auth!session!log_external_state** in op 'Ja' om problemen met de verificatie op te lossen, laat anders de standaardwaarde 'Nee' staan 

    d. Wijzig in **auth!session!cookie_attributes** naar 'HTTPOnly' 

3. SAML-serviceprovider -

    ![SAML-serviceprovider](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. Stel in het tekstvak **auth!saml!sp_entity_id** in op dezelfde URL als gebruikt als de configuratie-id (entiteits-id) voor eenmalige aanmelding voor Azure Active Directory. Als `https://pulseweb.labb.info/saml/metadata`. 

    b. Stel in **auth!saml!sp_acs_url** in op dezelfde URL die wordt gebruikt als de replay-URL voor de configuratie van eenmalige aanmelding van Azure Active Directory (URL voor Assertion Consumer Service). Als `https://pulseweb.labb.info/saml/consume`. 

    c. Selecteer in de **auth!saml!idp** de **Vertrouwde id-provider**  die u in de vorige stap hebt gemaakt. 

    d. Laat in de auth!saml!time_tolerance de standaardwaarde '5' seconden staan. 

    e. Selecteer **niet-opgegeven** in de auth!saml!nameid_format.

    f. Als u wijzigingen wilt toepassen, klikt u op **Update** onderaan de pagina.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Een testgebruiker voor Pulse Secure Virtual Traffic Manager maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Pulse Secure Virtual Traffic Manager. Werk met het [Ondersteuningsteam van Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net) om de gebruikers toe te voegen in het Pulse Secure Virtual Traffic Manager-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt hiermee omgeleid naar de aanmeldings-URL van Pulse Secure Virtual Traffic Manager, waar u de aanmeldingsstroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van Pulse Secure Virtual Traffic Manager en initieer de aanmeldingsstroom daar.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u op de tegel Pulse Secure Virtual Traffic Manager in het Toegangsvenster klikt, wordt u omgeleid naar de aanmeldings-URL voor Pulse Secure Virtual Traffic Manager. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Pulse Secure Virtual Traffic Manager hebt geconfigureerd kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).