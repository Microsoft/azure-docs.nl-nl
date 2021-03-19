---
title: 'Zelfstudie: integratie van eenmalige aanmelding van Azure Active Directory met Hoxhunt | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Hoxhunt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes
ms.openlocfilehash: e18cd80c0232c4552c0e3dc57a23477d410c5395
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442555"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hoxhunt"></a>Zelfstudie: eenmalige aanmelding van Azure Active Directory integreren met Hoxhunt

In deze zelfstudie leert u hoe u Hoxhunt integreert met Azure AD (Microsoft Azure Active Directory). Wanneer u Hoxhunt integreert met Microsoft Azure Active Directory, kunt u het volgende doen:

* In Azure Active Directory bepalen wie er toegang heeft tot Hoxhunt.
* Ervoor zorgen dat gebruikers automatisch met hun Microsoft Azure Active Directory-account worden aangemeld bij Hoxhunt.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Hoxhunt-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Hoxhunt biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-hoxhunt-from-the-gallery"></a>Hoxhunt toevoegen vanuit de galerie

Voor het configureren van de integratie van Hoxhunt met Azure Active Directory moet u Hoxhunt uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak de tekst **Hoxhunt**.
1. Selecteer **Hoxhunt** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-hoxhunt"></a>Eenmalige aanmelding van Microsoft Azure Active Directory configureren en testen voor Hoxhunt

Configureer en test eenmalige aanmelding van Azure Active Directory bij Hoxhunt met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure Active Directory-gebruiker en de bijbehorende gebruiker in Hoxhunt.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij Hoxhunt te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Hoxhunt configureren](#configure-hoxhunt-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Hoxhunt maken](#create-hoxhunt-test-user)** : als u in Hoxhunt een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure Active Directory-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal, op de integratiepagina van de **Hoxhunt**-app, naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://app.hoxhunt.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://app.hoxhunt.com/saml/consume/<ID>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.hoxhunt.com/saml/consume/<ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Vervang deze waarden door de werkelijke antwoord-URL en id. Neem contact op met het [klantondersteuningsteam van Hoxhunt](mailto:support@hoxhunt.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Hoxhunt instellen** de betreffende URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot Hoxhunt.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Hoxhunt** in de lijst met apps.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-hoxhunt-sso"></a>Eenmalige aanmelding voor Hoxhunt configureren

Als u eenmalige aanmelding bij **Hoxhunt** wilt configureren, moet u het gedownloade **certificaat (Base64)** en de betreffende URL's die u uit Azure Portal hebt gekopieerd naar het [Hoxhunt-ondersteuningsteam](mailto:support@hoxhunt.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-hoxhunt-test-user"></a>Hoxhunt-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Hoxhunt. Neem contact op met het [ondersteuningsteam van Hoxhunt](mailto:support@hoxhunt.com) om de gebruikers toe te voegen in het Hoxhunt-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Hoxhunt, waar u de aanmeldingsstroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van Hoxhunt en initieer daar de aanmeldingsstroom.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in Toegangsvenster op de Hoxhunt-tegel klikt, wordt u omgeleid naar de aanmeldings-URL voor Hoxhunt. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Hoxhunt hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor uw organisatie in real time wordt beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).