---
title: 'Zelfstudie: Azure Active Directory integratie met Clarizen One | Microsoft Docs'
description: Ontdek hoe u een aanmelding configureert tussen Azure Active Directory en Clarizen One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516826"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Zelfstudie: Azure Active Directory integratie met Clarizen One

In deze zelfstudie leert u hoe u Clarizen One integreert met Azure Active Directory (Azure AD). Wanneer u Clarizen One integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Clarizen One.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Clarizen One.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Clarizen One met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Clarizen One ondersteunt door **IDP geïnitieerde** eenmalige aanmelding.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-clarizen-one-from-the-gallery"></a>Clarizen One toevoegen vanuit de galerie

Voor het configureren van de integratie van Clarizen One in Azure AD moet u Clarizen One vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **Clarizen One** in het zoekvak.
1. Selecteer **Clarizen One in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Eenmalige aanmelding van Azure AD voor Clarizen One configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Clarizen One met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Clarizen One.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Clarizen One te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Clarizen One configureren](#configure-clarizen-one-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Clarizen](#create-clarizen-one-test-user)** maken : als u een tegenhanger van B.Simon in Clarizen One wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de **toepassing Clarizen One** de sectie Beheren en selecteer Een  **aanmelding.**
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    a. Typ in het tekstvak **Id** deze waarde: `Clarizen`

    b. Typ in het tekstvak **Antwoord-URL** de URL: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de **sectie Clarizen One instellen kopieert** u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot Clarizen One.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Clarizen One** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name=&quot;configure-clarizen-one-sso&quot;></a>Eenmalige aanmelding voor Clarizen One configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Clarizen One.

1. Klik op uw gebruikersnaam en klik vervolgens op **Instellingen**.

    ![Op ‘Instellingen’ klikken onder uw gebruikersnaam](./media/clarizen-tutorial/setting.png &quot;Instellingen")

1. Klik op het tabblad **Algemene instellingen**. Klik vervolgens naast **Federatieve aanmelding** op **Bewerken**.

    ![Tabblad ‘Algemene instellingen’](./media/clarizen-tutorial/authentication.png "Globale instellingen")

1. Voer in het dialoogvenster **Federatieve aanmelding** de volgende stappen uit:

    ![Dialoogvenster ‘Federatieve aanmelding’](./media/clarizen-tutorial/federated-authentication.png "Federatieve aanmelding")

    a. Selecteer **Federatieve aanmelding inschakelen**.

    b. Klik op **Uploaden** om uw gedownloade certificaat te uploaden.

    c. Voer in het tekstvak **Aanmeldings-URL** de waarde van de **login-URL** in uit het configuratievenster van de Azure AD-toepassing.

    d. Voer in het tekstvak **Afmeldings-URL** de waarde van de **logout-URL** in uit het configuratievenster van de Azure AD-toepassing.

    e. Selecteer **BERICHT gebruiken**.

    f. Klik op **Opslaan**.

### <a name="create-clarizen-one-test-user"></a>Testgebruiker voor Clarizen One maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in Clarizen One.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Clarizen One, moet u gebruikersaccounts inrichten. In het geval van Clarizen One is inrichten een handmatige taak.

1. Meld u als beheerder aan bij de bedrijfssite van Clarizen One.

2. Klik op **People**.

    ![Op ‘Personen’ klikken](./media/clarizen-tutorial/people.png "People")

3. Klik op **Gebruiker uitnodigen**.

    ![Knop ‘Gebruiker uitnodigen’](./media/clarizen-tutorial/user.png "Invite Users")

1. Voer in het dialoogvenster **Personen uitnodigen** de volgende stappen uit:

    ![Dialoogvenster ‘Personen uitnodigen’](./media/clarizen-tutorial/invite-people.png "Invite People")

    a. Typ in het tekstvak **E-mail** het e-mailadres van het account van Britta Simon.

    b. Klik op **Uitnodigen**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in Azure Portal en u wordt automatisch aangemeld bij de clarizen one waarvoor u eenmalige aanmelding hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in de Mijn apps op de tegel Clarizen One klikt, wordt u automatisch aangemeld bij de clarizen one waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Clarizen One hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).