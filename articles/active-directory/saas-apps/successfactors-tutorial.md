---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met SuccessFactors | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en SuccessFactors configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: a903eb6000cdd5212ad358cae4952e27a4719070
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725258"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met SuccessFactors

In deze zelfstudie leert u hoe u SuccessFactors kunt integreren met Azure Active Directory (Azure AD). Wanneer u SuccessFactors integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot SuccessFactors.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij SuccessFactors.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SuccessFactors waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SuccessFactors biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-successfactors-from-the-gallery"></a>SuccessFactors toevoegen vanuit de galerie

Om de integratie van SuccessFactors te configureren in Azure AD, moet u SuccessFactors vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **SuccessFactors** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **SuccessFactors** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Eenmalige aanmelding van Azure AD voor SuccessFactors configureren en testen

Configureer en test eenmalige aanmelding van Azure AD voor SuccessFactors met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SuccessFactors.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met SuccessFactors te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding voor SuccessFactors configureren](#configure-successfactors-sso)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
    1. **[Een testgebruiker voor SuccessFactors maken](#create-successfactors-test-user)** : als u een tegenhanger van B. Simon in SuccessFactors wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **SuccessFactors** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL in een van de volgende notaties:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. In het tekstvak **Id** typt u een van de URL's in het volgende notaties:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. In het tekstvak **Antwoord-URL** typt u een URL in een van de volgende notaties:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van SuccessFactors](https://www.sap.com/support.html) om deze waarden op te vragen.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **SuccessFactors instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SuccessFactors.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SuccessFactors** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-successfactors-sso"></a>Eenmalige aanmelding voor SuccessFactors configureren

1. Meld u in een ander browservenster als beheerder aan bij de **beheerportal van SuccessFactors**.

2. Ga naar **Application Security** en **Single Sign On Features**.

3. Typ een waarde in het veld **Reset Token** en klik op **Save Token** om eenmalige aanmelding via SAML in te schakelen.

    ![Schermopname met het tabblad Application Security, met Single Sign On Features, waar u een token kunt invoeren.][11]

    > [!NOTE]
    > Deze waarde wordt gebruikt als een schakeloptie. Als er een waarde wordt opgeslagen, is eenmalige aanmelding via SAML ingeschakeld. Als er een lege waarde wordt opgeslagen, is eenmalige aanmelding via SAML uitgeschakeld.

4. Ga naar het onderstaande scherm en voer de volgende acties uit:

    ![Schermopname van het deelvenster For SAML-based SSO, waar u de beschreven waarden kunt invoeren.][12]
  
    a. Schakel het keuzerondje **SAML v2 SSO** in.
  
    b. Geef een waarde op voor **SAML Asserting Party Name**(bijvoorbeeld de SAML-verlener plus de bedrijfsnaam).

    c. Plak in het tekstvak **SAM Issuer** de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    d. Selecteer **Assertion** bij **Require Mandatory Signature**.

    e. Selecteer **Enabled** bij **Enable SAML Flag**.

    f. Selecteer **No** bij **Login Request Signature(SF Generated/SP/RP)** .

    g. Selecteer **Browser/Post Profile** bij **SAML Profile**.

    h. Selecteer **No** bij **Enforce Certificate Valid Period**.

    i. Kopieer de inhoud van het certificaatbestand dat u hebt gedownload uit de Azure-portal en plak deze in het tekstvak **SAML Verifying Certificate**.

    > [!NOTE] 
    > De certificaatinhoud moet begin- en eindcodes bevatten voor het certificaat.

5. Ga naar het gedeelte SAML V2 en voer de volgende stappen uit:

    ![Schermopname van het tabblad SAML v2 SP initiated logout, waar u de beschreven waarden kunt invoeren.][13]

    a. Selecteer **Yes** bij **Support SP-initiated Global Logout**.

    b. Plak in het tekstvak **Global Logout Service URL (LogoutRequest destination)** de waarde voor **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Selecteer **No** bij **Require sp must encrypt all NameID elements**.

    d. Selecteer **unspecified** bij **NameID Format**.

    e. Selecteer **Yes** bij **Enable sp initiated login (AuthnRequest)** .

    f. Plak in het tekstvak **single sign on redirect service location** de waarde voor **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

6. Voer deze stappen uit als u de gebruikersnamen voor aanmelding hoofdlettergevoelig wilt maken.

    ![Eenmalige aanmelding configureren][29]

    a. Ga naar **Company Settings**(onderaan het scherm).

    b. Schakel het selectievakje **Enable Non-Case-Sensitive Username** in.

    c. Klik op **Opslaan**.

    > [!NOTE]
    > Als u deze optie probeert in te schakelen, controleert het systeem of er dan een dubbele SAML-aanmeldingsnaam wordt gemaakt. Dit is bijvoorbeeld het geval als de klant de gebruikersnamen Gebruiker1 en gebruiker1 heeft. Er is sprake van dubbele vermeldingen als hoofdlettergevoeligheid wordt uitgeschakeld. Er verschijnt dan een foutbericht en de functie wordt niet ingeschakeld. De klant moet een van de gebruikersnamen wijzigen, zodat deze verschillend zijn gespeld.

### <a name="create-successfactors-test-user"></a>Een testgebruiker maken voor SuccessFactors

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SuccessFactors, moeten ze worden ingericht bij SuccessFactors. In het geval van SuccessFactors is dat een handmatige taak.

Om gebruikers toe te voegen in SuccessFactors, moet u contact opnemen met het [ondersteuningsteam van SuccessFactors](https://www.sap.com/support.html).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van SuccessFactors, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van SuccessFactors en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel SuccessFactors klikt, wordt u omgeleid naar de aanmeldings-URL van SuccessFactors. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u SuccessFactors hebt geconfigureerd, kunt u sessiebesturingselementen afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png