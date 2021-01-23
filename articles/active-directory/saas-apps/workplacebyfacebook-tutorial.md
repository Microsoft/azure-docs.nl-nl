---
title: 'Zelfstudie: Azure Active Directory-integratie voor eenmalige aanmelding met Workplace by Facebook | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Workplace by Facebook configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 3f66da38d3303b47c2a9b6cefeee19af6bf64ec1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725487"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Zelfstudie: Azure Active Directory-integratie voor eenmalige aanmelding met Workplace by Facebook

In deze zelfstudie leert u hoe u Workplace by Facebook integreert met Azure Active Directory (Azure AD). Wanneer u Workplace by Facebook integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Workplace by Facebook.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Workplace by Facebook.
* Uw accounts op één centrale locatie beheren: de Azure-portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Abonnement op Workplace by Facebook met eenmalige aanmelding ingeschakeld.

> [!NOTE]
> Facebook heeft twee producten, Workplace Standard (gratis) en Workplace Premium (betaald). Elke tenant van Workplace Premium kan gratis en zonder licenties SCIM- en SSO-integratie configureren. SSO en SCIM zijn niet beschikbaar in exemplaren van Workplace Standard.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workplace by Facebook ondersteunt door **SP** geïnitieerde SSO
* Workplace by Facebook ondersteunt **just-in-time inrichten**
* Workplace by Facebook ondersteunt **[automatisch inrichten van gebruikers](workplacebyfacebook-provisioning-tutorial.md)**
* De Workplace by Facebook-toepassing kan nu worden geconfigureerd met Azure AD voor het inschakelen van eenmalige aanmelding. In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Workplace by Facebook toevoegen vanuit de galerie

Om de integratie van Workplace by Facebook in Azure AD te configureren, moet u Workplace by Facebook vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Workplace by Facebook** in het zoekvak.
1. Selecteer **Workplace by Facebook** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Eenmalige aanmelding van Azure AD voor Workplace by Facebook configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Workplace by Facebook met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workplace by Facebook.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en testen met Workplace by Facebook:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding voor Workplace by Facebook configureren](#configure-workplace-by-facebook-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Testgebruiker voor Workplace by Facebook maken](#create-workplace-by-facebook-test-user)** : een tegenhanger voor B.Simon maken in Workplace by Facebook die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **Workplace by Facebook** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** (in WorkPlace aangeduid als Recipient URL) typt u een URL met de volgende notatie: `https://.workplace.com/work/saml.php`

    b. In het tekstvak **ID (Entiteits-id)** (in WorkPlace aangeduid als Audience URL) typt u een URL met de volgende notatie: `https://www.workplace.com/company/`

    c. Typ in het tekstvak **Antwoord-URL (in WorkPlace aangeduid als Assertion Consumer Service)** een URL met de volgende notatie: `https://.workplace.com/work/saml.php`

    > [!NOTE]
    > Dit zijn niet de echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Zie de pagina Verificatie van het dashboard Workplace Company voor de juiste waarden voor uw Workplace-community. Dit wordt verderop in de zelfstudie toegelicht.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In het gedeelte **Workplace by Facebook instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Workplace by Facebook.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Workplace by Facebook** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-workplace-by-facebook-sso"></a>Eenmalige aanmelding voor Workplace by Facebook configureren

1. Als u de configuratie in Workplace by Facebook wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Workplace by Facebook instellen**. U wordt naar de toepassing Workplace by Facebook geleid. Geef hier de Administrator-referenties op om u aan te melden bij Workplace by Facebook. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Workplace by Facebook handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Workplace by Facebook-bedrijfssite. Voer daarna de volgende stappen uit:

    > [!NOTE]
    > Als onderdeel van het SAML-verificatieproces kan Workplace queryreeksen van maximaal 2,5 kB gebruiken om parameters door te geven aan Azure AD.

1. Navigeer in het navigatievenster aan de linkerkant naar het tabblad **Beveiliging** > **Verificatie**.

    ![Deelvenster Beheer](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Schakel de optie **Eenmalige aanmelding** in.
    
    b. Klik op **+Nieuwe provider voor eenmalige aanmelding toevoegen**.
    > [!NOTE]
    > Zorg ervoor dat u het selectievakje Aanmelden met wachtwoord ook inschakelt. Beheerders hebben deze optie mogelijk nodig voor aanmelding tijdens het aanwijzen van het certificaat om te voorkomen dat ze worden afgemeld.

1. Selecteer op het tabblad **Verificatie** de optie **Eenmalige aanmelding (SSO)** en voer de volgende stappen uit:

    ![Tabblad Verificatie](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Voer bij **Naam van provider van eenmalige aanmelding** de naam van het exemplaar voor eenmalige aanmelding in, bijvoorbeeld Azureadsso.

    b. Plak in het tekstvak **SAM-URL** de waarde van **Aanmeldings-URL**, die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **URL SAML-uitgever** de waarde van **Azure AD-id**, die u hebt gekopieerd uit de Azure-portal.

    d. Open in Kladblok het **met Base 64 gecodeerde certificaat** dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **SAML-certificaat**.

    e. Kopieer de **Doelgroep-URL** voor uw exemplaar en plak deze in het tekstvak **Id (Entiteits-id)** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    f. Kopieer de **Ontvangers-URL** voor uw exemplaar en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    g. Kopieer de **Assertion Consumer Service-URL** voor uw exemplaar en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    h. Blader naar de onderkant van het gedeelte en klik op de knop **SSO testen**. Er verschijnt een pop-upvenster met de aanmeldingspagina van Azure AD. Voer uw referenties op de gebruikelijke manier in om te verifiëren.

    **Problemen oplossen:** Controleer of het e-mailadres dat wordt geretourneerd door Azure AD hetzelfde is als het Workplace-account waarbij u bent aangemeld.

    i. Zodra de test is voltooid, bladert u naar de onderkant van de pagina en klikt u op de knop **Opslaan**.

    j. Alle gebruikers van Workplace zien nu voortaan de Azure AD-aanmeldingspagina voor verificatie.

1. **Omleiden SAML-afmelding (optioneel)**  -

    U kunt eventueel een URL voor SAML afmelding configureren, die kan worden gebruikt om te verwijzen naar de afmeldingspagina van Azure AD. Als deze instelling is ingeschakeld en geconfigureerd, wordt de gebruiker niet meer omgeleid naar de afmeldingspagina van Workplace. In plaats daarvan wordt de gebruiker omgeleid naar de URL die is opgegeven in het tekstvak Omleiden SAML-afmelding.

### <a name="configuring-reauthentication-frequency"></a>Frequentie voor hernieuwde verificatie configureren

U kunt instellen dat Workplace elke dag, drie dagen, week, twee weken, maanden of nooit vraagt om een SAML-controle.

> [!NOTE]
> De minimumwaarde voor de SAML-controle voor mobiele toepassingen is ingesteld op één week.

U kunt ook afdwingen dat SAML opnieuw wordt ingesteld voor alle gebruikers. Dit kan met de knop: Nu SAML-verificatie vereisen voor alle gebruikers.

### <a name="create-workplace-by-facebook-test-user"></a>Testgebruiker voor Workplace by Facebook maken

In dit gedeelte wordt er een gebruiker met de naam B.Simon gemaakt in Workplace by Facebook. Workplace by Facebook ondersteunt just-in-time inrichten. Deze functie is standaard ingeschakeld.

U hoeft geen handelingen uit te voeren in dit gedeelte. Als er geen gebruiker bestaat in Workplace by Facebook, wordt er een nieuwe gemaakt wanneer u probeert toegang te krijgen tot Workplace by Facebook.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met [het ondersteuningsteam van Workplace by Facebook](https://www.workplace.com/help/work/).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Workplace by Facebook, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Workplace by Facebook en initieer daar de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Workplace by Facebook in Mijn apps klikt, wordt u doorgestuurd naar de aanmeldings-URL van Facebook. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Eenmalige aanmelding voor Workplace by Facebook testen (mobiel)

1. Open de mobiele toepassing Workplace by Facebook. Klik op de aanmeldpagina op **LOG IN**.

    ![Het aanmelden](./media/workplacebyfacebook-tutorial/test05.png)

2. Voer uw zakelijke e-mailadres in en klik op **CONTINUE**.

    ![Het e-mailadres](./media/workplacebyfacebook-tutorial/test02.png)

3. Klik op **JUST ONCE**.

    ![Eenmalig](./media/workplacebyfacebook-tutorial/test04.png)

4. Klik op **Allow**.

    ![Toestaan](./media/workplacebyfacebook-tutorial/test03.png)

5. Als het aanmelden is gelukt, wordt de startpagina van de toepassing weergegeven.    

    ![De startpagina](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Volgende stappen

Zodra u Workplace by Facebook hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)