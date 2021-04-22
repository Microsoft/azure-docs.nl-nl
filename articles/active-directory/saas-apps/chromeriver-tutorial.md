---
title: 'Zelfstudie: Azure Active Directory-integratie met Chromeriver | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Chromeriver.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: jeedes
ms.openlocfilehash: f88e6b52a96013b0e6f9d664c46dae62ce844288
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892108"
---
# <a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Zelfstudie: Azure Active Directory-integratie met Chromeriver

In deze zelfstudie leert u hoe u Chromeriver integreert met Azure Active Directory (Azure AD). Wanneer u Chromeriver integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Chromeriver.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Chromeriver.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een chromeriver-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Chromeriver ondersteunt door **IDP geïnitieerde** eenmalige aanmelding.

## <a name="add-chromeriver-from-the-gallery"></a>Chromeriver toevoegen vanuit de galerie

Voor het configureren van de integratie van Chromeriver met Azure AD moet u Chromeriver uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **Chromeriver** in het zoekvak.
1. Selecteer **Chromeriver in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-chromeriver"></a>Eenmalige aanmelding van Azure AD voor Chromeriver configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Chromeriver met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Chromeriver.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Chromeriver te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Chromeriver configureren](#configure-chromeriver-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Chromeriver-testgebruiker](#create-chromeriver-test-user)** maken : als u een tegenhanger van B.Simon in Chromeriver wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de **toepassing Chromeriver** de sectie Beheren en **selecteer Een aanmelding.** 
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.chromeriver.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.chromeriver.com/login/sso/saml/consume?customerId=<uniqueid>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Chromeriver](https://www.chromeriver.com/services/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Chromeriver instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot Chromeriver.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Chromeriver** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-chromeriver-sso"></a>Eenmalige aanmelding voor Chromeriver configureren

Als u eenmalige aanmelding aan de zijde van **Chromeriver** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit de Microsoft Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Chromeriver](https://www.chromeriver.com/services/support). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-chromeriver-test-user"></a>Chromeriver-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Chromeriver, moeten ze worden ingericht in Chromeriver. In het geval van Chromeriver moeten de gebruikersaccounts worden gemaakt door uw [ondersteuningsteam van Chromeriver](https://www.chromeriver.com/services/support).

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van Chromeriver-gebruikersaccounts of API's van Chromeriver gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in Azure Portal en u wordt automatisch aangemeld bij de chromeriver waarvoor u eenmalige aanmelding hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in het Mijn apps op de tegel Chromeriver klikt, wordt u automatisch aangemeld bij de chromeriver waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Chromeriver hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
