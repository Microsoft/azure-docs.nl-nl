---
title: 'Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met Check Point Identity Awareness | Microsoft Docs'
description: Ontdek hoe u een aanmelding configureert tussen Azure Active Directory en Check Point Identity Awareness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520897"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met Check Point Identity Awareness

In deze zelfstudie leert u hoe u Check Point Identity Awareness integreert met Azure Active Directory (Azure AD). Wanneer u Check Point Identity Awareness integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Check Point Identity Awareness.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen Check Point bij Identity Awareness.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Check Point Identity Awareness-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Check Point Identity Awareness ondersteunt door **SP geïnitieerde** eenmalige aanmelding.

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Check Point Identity Awareness toevoegen vanuit de galerie

Om de integratie van Check Point Identity Awareness te configureren in Azure AD, moet u Check Point Identity Awareness vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie Check Point **Identity Awareness** in het zoekvak.
1. Selecteer **Check Point Identity Awareness in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Check Point Identity Awareness

Configureer en test eenmalige aanmelding van Azure AD met Check Point Identity Awareness met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Check Point Identity Awareness.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD Check Point Identity Awareness te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige Check Point Identity Awareness configureren](#configure-check-point-identity-awareness-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker Check Point Identity Awareness](#create-check-point-identity-awareness-test-user)** maken : als u een tegenhanger van B.Simon in Check Point Identity Awareness wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina **van Check Point Identity Awareness** de  sectie Beheren en selecteer **Een aanmelding.**
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact [Check Point het klantondersteuningsteam van Identity Awareness](mailto:support@checkpoint.com) op om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de **sectie Check Point Identity Awareness** instellen kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot Check Point Identity Awareness.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met **toepassingen Check Point Identity Awareness**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-check-point-identity-awareness-sso"></a>Eenmalige Check Point voor Identity Awareness configureren

1. Meld u als beheerder Check Point aan bij de bedrijfssite van Identity Awareness.

1. Klik in de weergave SmartConsole > **Gateways & Servers** op **New > More > User/Identity > Identity Provider.**

    ![schermopname voor de nieuwe id-provider.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. Voer de volgende stappen uit in **het venster Nieuwe id-provider.**

    ![schermopname voor de sectie Identity Provider.](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. Selecteer in **het veld** Gateway de beveiligingsgateway die de SAML-verificatie moet uitvoeren.

    b. Selecteer in **het** veld Service de **identity awareness** in de vervolgkeuzekeuze.

    c. Kopieer **de waarde voor Identifier (Entity ID)** en plak deze in het tekstvak Id in de sectie Standaard  **SAML-configuratie** in Azure Portal.

    d. Kopieer **de waarde van Antwoord-URL** en plak deze waarde in het tekstvak Antwoord-URL in de sectie Standaard **SAML-configuratie** in Azure Portal. 

    e. Selecteer **Import the Metadata File** om het gedownloade Certificaat **(Base64) te uploaden** uit de Azure Portal.

    > [!NOTE]
    > U kunt ook  Handmatig invoegen selecteren om  de waarden voor Entiteits-id en Aanmeldings-URL handmatig in de bijbehorende velden te plakken en het certificaatbestand te uploaden vanuit de Azure Portal.  

    f. Klik op **OK**.

### <a name="create-check-point-identity-awareness-test-user"></a>Testgebruiker Check Point Identity Awareness maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Check Point Identity Awareness. Werk samen [met Check Point Identity Awareness-ondersteuningsteam](mailto:support@checkpoint.com) om de gebruikers toe te voegen aan het Check Point Identity Awareness-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid Check Point aanmeldings-URL van Identity Awareness, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks Check Point aanmeldings-URL voor Identity Awareness en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Check Point Identity Awareness-tegel in de Mijn apps klikt, wordt u omgeleid naar de aanmeldings-URL Check Point Identity Awareness. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Check Point Identity Awareness hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


