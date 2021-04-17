---
title: 'Zelfstudie: Azure Active Directory-integratie met Egnyte | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Egnyte configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 2662b686102a1a4f6aa6db0f7a4052de329def60
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519796"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Egnyte

In deze zelfstudie leert u hoe u Egnyte kunt integreren met Azure AD (Active Directory). Wanneer u Egnyte integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Egnyte.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Egnyte.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Egnyte-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Egnyte ondersteunt door **SP geïnitieerde** eenmalige aanmelding.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-egnyte-from-the-gallery"></a>Egnyte toevoegen vanuit de galerie

Om de integratie van Egnyte te configureren in Azure AD moet u Egnyte vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Egnyte**.
1. Selecteer **Egnyte** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Eenmalige aanmelding van Azure AD voor Egnyte configureren en testen

Configureer en test eenmalige aanmelding van Azure AD Form.com met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Form.com.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD Form.com configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Egnyte configureren](#configure-egnyte-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Egnyte-testgebruiker maken](#create-egnyte-test-user)** : als u een tegenhanger van B.Simon in Egnyte wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de **toepassing Egnyte** de sectie Beheren en **selecteer Een aanmelding.** 
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.egnyte.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [ondersteuningsteam van Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

5. Kopieer in het gedeelte **Egnyte instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Egnyte.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Egnyte** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name=&quot;configure-egnyte-sso&quot;></a>Eenmalige aanmelding bij Egnyte configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van Egnyte.

2. Klik op **Instellingen**.
   
    ![Instellingen 1](./media/egnyte-tutorial/settings-tab.png &quot;Instellingen")

3. Klik in het menu op **Instellingen**.

    ![Menu 1](./media/egnyte-tutorial/menu-tab.png "Menu")

4. Klik op het tabblad **Configuratie** en klik vervolgens op **Beveiliging**.

    ![Beveiliging](./media/egnyte-tutorial/configuration.png "Beveiliging")

5. Voer in de sectie **Verificatie voor eenmalige aanmelding** de volgende stappen uit:

    ![Verificatie voor eenmalige aanmelding](./media/egnyte-tutorial/authentication.png "Verificatie voor eenmalige aanmelding")   
    
    a. Selecteer **SAML 2.0** als **Verificatie voor eenmalige aanmelding**.
   
    b. Selecteer **AzureAD** als **Id-provider**.
   
    c. Plak de **Aanmeldings-URL** die u in de Azure-portal hebt gekopieerd, in het tekstvak **Aanmeldings-URL voor id-provider**.
   
    d. Plak de **Azure AD-id** die u hebt gekopieerd in de Azure-portal, in het tekstvak **Entiteits-id van id-provider**.
      
    e. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Id-providercertificaat**.
   
    f. Selecteer **E-mailadres** als **Standaardgebruikerstoewijzing**.
   
    g. Selecteer **Uitgeschakeld** bij **Waarde voor domeinspecifieke verlener gebruiken**.
   
    h. Klik op **Opslaan**.

### <a name="create-egnyte-test-user"></a>Egnyte-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Egnyte, moeten ze worden ingericht in Egnyte. In het geval van Egnyte is het inrichten een handmatige taak.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u als een beheerder aan bij de bedrijfssite van **Egnyte**.

2. Ga naar **Instellingen \> Gebruikers en groepen**.

3. Klik op **Nieuwe gebruiker toevoegen** en selecteer vervolgens het type gebruiker dat u wilt toevoegen.
   
    ![Gebruikers](./media/egnyte-tutorial/add-user.png "Gebruikers")

4. Voer in de sectie **Nieuwe hoofdgebruiker** de volgende stappen uit:
    
    ![Nieuwe standaardgebruiker](./media/egnyte-tutorial/new-user.png "Nieuwe standaardgebruiker")   

    a. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, bijvoorbeeld **Brittasimon\@contoso.com**.

    b. Voer in het tekstvak **Gebruikersnaam** de gebruikersnaam van een gebruiker zoals **Brittasimon**.

    c. Selecteer als **Verificatietype** de optie **Eenmalige aanmelding**.
   
    d. Klik op **Opslaan**.
    
    >[!NOTE]
    >De gebruiker van het Azure Active Directory-account ontvangt een e-mailmelding.
    >

>[!NOTE]
>U kunt ook andere API’s of hulpprogramma’s voor het maken van Egnyte-gebruikersaccounts die worden geleverd met Egnyte, gebruiken om Azure AD-gebruikersaccounts in te richten.
>

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Egnyte, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Egnyte en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Egnyte in de Mijn apps, wordt u omgeleid naar de aanmeldings-URL van Egnyte. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Egnyte hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
