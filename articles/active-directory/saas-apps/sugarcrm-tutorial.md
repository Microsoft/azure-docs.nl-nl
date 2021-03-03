---
title: 'Zelfstudie: Integratie van een eenmalige aanmelding (SSO) van Azure Active Directory met Sugar CRM | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Sugar CRM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 8c0bbebf9fdc9e8027b947beb037dde47b26b67b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644841"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Zelfstudie: Integratie van een eenmalige aanmelding (SSO) van Azure Active Directory met Sugar CRM

In deze zelfstudie leert u hoe u Sugar CRM integreert met Azure Active Directory (Azure AD). Wanneer u Sugar CRM integreert met Azure AD, kunt u het volgende doen:

* In Azure AD controleren wie toegang heeft tot Sugar CRM.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-accounts worden aangemeld bij Sugar CRM.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement waarop een eenmalige aanmelding (SSO) voor Sugar CRM mogelijk is gemaakt.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sugar CRM biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="add-sugar-crm-from-the-gallery"></a>Suiker CRM toevoegen vanuit de galerie

Voor het configureren van de integratie van Sugar CRM met Azure AD moet u Sugar CRM vanuit de galerie toevoegen aan uw lijst van beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Sugar CRM** in het zoekvak.
1. Selecteer **Sugar CRM** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sugar-crm"></a>Azure AD-SSO voor suiker CRM configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Sugar CRM met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sugar CRM.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met suiker CRM:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Sugar CRM configureren](#configure-sugar-crm-sso)** : – de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
    1. **[Een Sugar CRM-testgebruiker maken](#create-sugar-crm-test-user)** : – als u een tegenhanger van B.Simon in Sugar CRM wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina voor de integratie van de **suiker-CRM** -toepassing en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [Sugar CRM Client-ondersteuningsteam](https://support.sugarcrm.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Sugar CRM instellen** kopieert u de juiste URL('s) op basis van uw vereiste.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Sugar CRM.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Sugar CRM**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sugar-crm-sso"></a>Sugar CRM-SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij uw Sugar CRM-bedrijfssite.

1. Ga naar **Beheerder**.

    ![Beheerder](./media/sugarcrm-tutorial/ic795888.png "Beheerder")

1. Klik in de sectie **Beheer** op **Wachtwoordbeheer**.

    ![Schermopname met de sectie Administration, waar u Password Management kunt selecteren.](./media/sugarcrm-tutorial/ic795889.png "Beheer")

1. Selecteer **SAML-verificatie inschakelen**.

    ![Schermopname met de optie om SAML Authentication te selecteren.](./media/sugarcrm-tutorial/ic795890.png "Beheer")

1. Voer in het gedeelte **SAML-verificatie** de volgende stappen uit:

    ![SAML-verificatie](./media/sugarcrm-tutorial/save.png "SAML Authentication")  

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.
  
    b. Plak in het tekstvak **SLO URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure Portal.
  
    c. Open in Kladblok het met Base 64 gecodeerde certificaat, kopieer de inhoud ervan naar het klembord en plak het hele certificaat in het tekstvak **X.509 Certificate**.
  
    d. Klik op **Opslaan**.

### <a name="create-sugar-crm-test-user"></a>Een Sugar CRM-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Sugar CRM, moeten ze worden ingericht bij Sugar CRM. In het geval van Sugar CRM is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Sugar CRM**-bedrijfssite als beheerder.

1. Ga naar **Beheerder**.

    ![Beheerder](./media/sugarcrm-tutorial/ic795888.png "Beheerder")

1. Klik in de sectie **Beheer** op **Gebruikersbeheer**.

    ![Schermopname met de sectie Administration, waar u User Management kunt selecteren.](./media/sugarcrm-tutorial/ic795893.png "Beheer")

1. Ga naar **Gebruikers \> Nieuwe gebruiker maken**.

    ![Nieuwe gebruiker maken](./media/sugarcrm-tutorial/ic795894.png "Nieuwe gebruiker maken")

1. In het tabblad **Gebruikersprofiel** voert u de volgende stappen uit:

    ![Schermopname van de sectie User Profile, waar u de beschreven waarden kunt invoeren.](./media/sugarcrm-tutorial/ic795895.png "Nieuwe gebruiker")

    * Typ de **gebruikersnaam**, **achternaam** en **e-mailadres** van een geldig Azure Active Directory-gebruiker die u wilt inrichten in de desbetreffende tekstvakken.
  
1. Selecteer **Actief** als **Status**.

1. Voer op het tabblad Wachtwoord de volgende stappen uit:

    ![Schermopname van het tabblad Password, waar u de beschreven waarden kunt invoeren.](./media/sugarcrm-tutorial/ic795896.png "Nieuwe gebruiker")

    a. Typ het wachtwoord in het bijbehorende tekstvak.

    b. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van Sugar CRM-gebruikersaccounts of API's van Sugar CRM gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van suiker CRM, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor de hand tekening van de suiker CRM en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel suiker-CRM in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van suiker CRM. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u een suiker-CRM hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).