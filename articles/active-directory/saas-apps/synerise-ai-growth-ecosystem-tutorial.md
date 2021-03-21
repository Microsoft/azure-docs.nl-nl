---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Synerise AI Growth Operating System | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Synerise AI Growth Operating System.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: jeedes
ms.openlocfilehash: 0ffc6543a2597e39470c0d4aff86e7d5a1631d69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736625"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-operating-system"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Synerise AI Growth Operating System

In deze zelfstudie leert u hoe u Synerise kunt integreren met Azure Active Directory (Azure AD). Wanneer u Synerise integreert met Azure Active Directory, kunt u het volgende doen:

* Beheer in Azure AD wie toegang heeft tot Synerise.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Synerise.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Synerise-abonnement met eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Synerise biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
* Synerise biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-synerise-ai-growth-operating-system-from-the-gallery"></a>Synerise AI Growth Operating System toevoegen vanuit de galerie

Als u de integratie van Synerise in Azure AD wilt configureren, moet u Synerise vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het zoekvak van de sectie **Toevoegen uit de galerie** **Synerise AI Growth Operating System**.
1. Selecteer **Synerise AI Growth Operating System** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-operating-system"></a>Eenmalige aanmelding van Azure AD voor Synerise AI Growth Operating System configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Synerise met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Synerise.

Voor het configureren van eenmalige aanmelding van Azure AD met Synerise moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van Synerise AI Growth Operating System configureren](#configure-synerise-ai-growth-operating-system-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Testgebruiker voor Synerise AI Growth Operating System maken](#create-synerise-ai-growth-operating-system-test-user)** : om een tegenhanger van B.Simon in Synerise AI Growth Operating System te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure Portal naar de integratiepagina van de toepassing **Synerise AI Growth Operating System**. Selecteer vervolgens **Eenmalige aanmelding** in de sectie **Beheren**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke antwoord-URL en aanmeldings-URL. Neem contact op met het [Klantondersteuningsteam van Synerise](mailto:support@synerise.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Synerise instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure. Dit doet u door haar toegang te geven tot Synerise.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Synerise AI Growth Operating System** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-synerise-ai-growth-operating-system-sso"></a>Eenmalige aanmelding configureren voor Synerise AI Growth Operating System

1. Meld u bij Synerise aan als beheerder.

1. Ga naar de **Instellingen > Access Control**.

    ![Synerise-instellingen](./media/synerise-ai-growth-ecosystem-tutorial/settings.png)

1. Klik op de pagina **Access Control** op de knop **Weergeven** op het tabblad **Eenmalige aanmelding** .

    ![Synerise-toegangsbeheer](./media/synerise-ai-growth-ecosystem-tutorial/single-sign-on.png)

1. Voer de volgende stappen uit op de onderstaande pagina.

    ![Synerise-configuratie](./media/synerise-ai-growth-ecosystem-tutorial/configuration.png)

    a. Plak in het tekstvak **Identity Provider Entity ID** de waarde van de **Azure AD-id** die u hebt gekopieerd uit de Azure Portal.

    b. Plak in het tekstvak voor het **Eindpunt voor eenmalige aanmelding (https)** de waarde van de **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Plak de waarde **toepassings-ID** in het tekstvak **Id van de identiteitsprovider van de toepassing**.

    d. Kopieer de waarde van **Omleidings-URI van de serviceprovider**. Plak deze waarde vervolgens in het tekstvak **Antwoord-URL** in de sectie 'Standaard-SAML-configuratie' in de Azure Portal.

    e. Selecteer **HTTP-omleiding** in de **Aanvraagbinding**.

    f. Schakel over op de **Handtekening aanvragen**.

    g. Upload het gedownloade bestand **Certificaat(Base64)** naar **Certificaat van de identiteitsprovider**.

    i. Klik op **Toepassen**.

### <a name="create-synerise-ai-growth-operating-system-test-user"></a>Synerise AI Growth Operating System-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Synerise. Synerise biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in Synerise, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Synerise, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Synerise en initieer daar de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in de Azure Portal. U wordt automatisch aangemeld bij de instantie van Synerise waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in 'Mijn apps' op de tegel 'Synerise' klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Synerise waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Synerise hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor wordt uw organisatie in real time beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).