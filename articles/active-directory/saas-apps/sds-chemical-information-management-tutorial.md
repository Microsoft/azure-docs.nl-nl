---
title: 'Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met SDS & Chemical Information Management | Microsoft Docs'
description: Ontdek hoe u een aanmelding configureert tussen Azure Active Directory en SDS & Chemische informatiebeheer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: e83274c404980549063b9a94a9118540f1ec3bb3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509326"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sds--chemical-information-management"></a>Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met SDS & Chemical Information Management

In deze zelfstudie leert u hoe u SDS & Chemische informatiebeheer integreert met Azure Active Directory (Azure AD). Wanneer u SDS & Chemische informatiebeheer integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot SDS & Chemische informatiebeheer.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen & bij SDS &.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SDS & Chemical Information Management met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SDS & Chemische informatiebeheer ondersteunt **door SP geïnitieerde** eenmalige aanmelding.

* SDS & Chemische informatiebeheer biedt ondersteuning **voor Just-In-Time-inrichting** van gebruikers.


## <a name="adding-sds--chemical-information-management-from-the-gallery"></a>SDS & Chemische informatiebeheer toevoegen vanuit de galerie

Voor het configureren van de integratie van SDS & Chemische informatiebeheer in Azure AD, moet u SDS & Chemische informatie management vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **SDS & Chemische informatiebeheer** in het zoekvak.
1. Selecteer **SDS & Chemische informatie management in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-sds--chemical-information-management"></a>Eenmalige aanmelding van Azure AD voor SDS & Chemische informatiebeheer configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SDS & Chemical Information Management met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SDS & Chemische informatiebeheer.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met SDS & Chemical Information Management te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor SDS & Chemical Information Management configureren](#configure-sds--chemical-information-management-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor SDS & Chemical Information Management](#create-sds--chemical-information-management-test-user)** maken : als u een tegenhanger van B.Simon in SDS & Chemical Information Management wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de sectie Beheren op de integratiepagina van de toepassing  **SDS & Chemical Information Management** en selecteer **Een aanmelding.**
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://cs.cloudsds.com/saml/<ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://cs.cloudsds.com/saml/<ID>/consumeAssertion`
    
    c. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://cs.cloudsds.com/saml/<ID>/Login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact op met het [klantondersteuningsteam & SDS &](mailto:info@cloudsds.com) Het klantondersteuningsteam van SDS om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)
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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot SDS & Chemische informatiebeheer.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SDS & Chemische informatiebeheer in de lijst met toepassingen.**
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sds--chemical-information-management-sso"></a>Eenmalige aanmelding voor SDS & Chemische informatiebeheer configureren

Als u een aanmelding aan de zijde van **SDS & Chemical Information Management** wilt configureren, moet u de **App-URL** voor federatiemetagegevens verzenden naar het [ondersteuningsteam van SDS & Chemical Information Management.](mailto:info@cloudsds.com) Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-sds--chemical-information-management-test-user"></a>Testgebruiker voor SDS & Chemische informatiebeheer maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in SDS & Chemische informatiebeheer. SDS & Chemische informatiebeheer biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in SDS & Chemische informatiebeheer, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar SDS & aanmeldings-URL voor Chemische informatiebeheer, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar SDS & Aanmeldings-URL voor Chemische informatiebeheer en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel SDS & Chemische informatiebeheer in de Mijn apps klikt, wordt u omgeleid naar de aanmeldings-URL van SDS & Chemical Information Management. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u SDS & Chemische informatiebeheer kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


