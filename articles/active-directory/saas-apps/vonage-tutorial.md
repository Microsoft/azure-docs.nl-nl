---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Vonage | Microsoft Docs'
description: Informatie over het configureren van een eenmalige aanmelding tussen Azure Active Directory en Vonage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e42dc6036d7ad44f1bbb15fc3af28c487ee67640
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733483"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-vonage"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Vonage

In deze zelfstudie leert u hoe u Vonage integreert met Azure AD (Azure Active Directory). Wanneer u Vonage integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Vonage.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Vonage.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Vonage waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.


* Vonage ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-vonage-from-the-gallery"></a>Vonage toevoegen vanuit de galerie

Voor het configureren van de integratie van Vonage met Azure AD moet u Vonage vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Vonage** in het zoekvak.
1. Selecteer **Vonage** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-vonage"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Vonage

Configureer en test eenmalige aanmelding van Azure AD met Vonage met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Vonage.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij Vonage te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Vonage configureren](#configure-vonage-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Vonage-testgebruiker maken](#create-vonage-test-user)** : als u een equivalent van B.Simon in Vonage wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure Portal, op de integratiepagina voor de **Vonage**-toepassing, naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `wso2is-<ENVIRONMENT>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://login.auth-<ENVIRONMENT>.vonage.com/accountrecoveryendpoint/saml-translator.jsp?id=<ID>&env=<ENVIRONMENT>&client=Web`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://admin.<ENVIRONMENT>.vocal-<ENVIRONMENT>.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [Vonage-ondersteuningsteam](mailto:office@vonage.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Vonage instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Vonage.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Vonage** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-vonage-sso"></a>Eenmalige aanmelding voor Vonage configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van Vonage.

1. Ga naar **Account > Instellingen voor eenmalige aanmelding > Instellingen**.

1. Voer de volgende stappen uit op de volgende pagina.

    ![Pagina met instellingen voor eenmalige aanmelding](./media/vonage-tutorial/sso-settings.png)

    a. Selecteer **Eenmalige aanmelding inschakelen voor dit account**.

    b. Plak in het tekstvak **Entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **Aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal. 

    d. Upload het bestand **Certificate(Base64)** dat u hebt gedownload van de Azure Portal naar **Certificaat uploaden**.

### <a name="create-vonage-test-user"></a>Testgebruiker voor Vonage maken

1. Meld u in een ander browservenster als beheerder aan bij de website van Vonage.

1. Ga naar **Telefoonsysteem > Gebruikers > Nieuwe toevoegen**.

    ![de pagina gebruiker toevoegen](./media/vonage-tutorial/add-user.png)

1. Voeg de vereiste velden toe op de volgende pagina en klik op **Opslaan**.

    ![de pagina gebruikersformulier toevoegen](./media/vonage-tutorial/add-user-2.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Vonage, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Vonage en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in de Azure Portal. U wordt automatisch aangemeld bij de instantie van Vonage waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel Vonage klikt en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Vonage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Vonage hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).