---
title: 'Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Cisco AnyConnect | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Cisco AnyConnect configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: a89ab7f2304fa51d3e8c7a968d445c9b40a457a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456085"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Cisco AnyConnect

In deze zelfstudie leert u hoe u Cisco AnyConnect kunt integreren met Azure Active Directory (Azure AD). Wanneer u Cisco AnyConnect integreert met Azure AD, kunt u het volgende doen:

* Bepalen in Azure AD bepalen wie toegang heeft tot Cisco AnyConnect.
* Inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Cisco AnyConnect.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Cisco AnyConnect waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cisco AnyConnect biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>Cisco AnyConnect toevoegen vanuit de galerie

Om de integratie van Cisco AnyConnect te configureren in Azure AD, moet u Cisco AnyConnect vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Cisco AnyConnect**.
1. Selecteer **Cisco AnyConnect** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Eenmalige aanmelding van Azure AD voor Cisco AnyConnect configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Cisco AnyConnect met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Cisco AnyConnect.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD voor Cisco AnyConnect te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Cisco AnyConnect configureren](#configure-cisco-anyconnect-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Cisco AnyConnect maken](#create-cisco-anyconnect-test-user)** : als u een tegenhanger van Britta Simon in Cisco AnyConnect wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. In de Azure Portal, op de integratiepagina van de app **Cisco AnyConnect**, selecteert u in de sectie **Beheren** **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaatbestand te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Cisco AnyConnect instellen** de juiste URL('s) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

> [!NOTE]
> Als u meerdere TGT's van de server wilt onboarden, moet u meerdere exemplaren van de Cisco AnyConnect-toepassing toevoegen vanuit de galerie. U kunt er ook voor kiezen om uw eigen certificaat in Azure AD te uploaden voor al deze toepassingsexemplaren. Op die manier kunt u hetzelfde certificaat voor de toepassingen hebben, maar kunt u voor elke toepassing een andere id en antwoord-URL configureren.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Cisco AnyConnect.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Cisco AnyConnect** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cisco-anyconnect-sso"></a>Eenmalige aanmelding configureren voor Cisco AnyConnect

1. U gaat dit eerst doen in de CLI, maar u kunt dit op een later tijdstip met ASDM doorlopen.

1. Maak verbinding met uw VPN-apparaat. U gaat een ASA met 9.8-codetrain gebruiken en uw VPN-clients zijn 4.6+.

1. Eerst maakt u een Trustpoint en importeert u het SAML-certificaat.

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. Met de volgende opdrachten wordt uw SAML-IdP ingericht.

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. U kunt nu SAML-verificatie toepassen op een configuratie van een VPN-tunnel.

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > Er is een functie bij de configuratie van de SAML-IdP: als u wijzigingen aanbrengt in de IdP-configuratie, moet u de configuratie van de SAML-identiteitsprovider uit uw tunnelgroep verwijderen en opnieuw toepassen om de wijzigingen van kracht te laten worden.

### <a name="create-cisco-anyconnect-test-user"></a>Cisco AnyConnect-testgebruiker maken

In deze sectie gaat u een gebruiker met de naam Britta Simon maken in Cisco AnyConnect. Voeg in samenwerking met het [ondersteuningsteam van Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) de gebruikers toe aan het Cisco AnyConnect-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op 'Deze toepassing testen' in de Azure Portal en u wordt automatisch aangemeld bij de versie van Cisco AnyConnect waarvoor u eenmalige aanmelding hebt ingesteld
* U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in het toegangsvenster op de tegel 'Cisco AnyConnect' klikt, wordt u automatisch aangemeld bij de versie van Cisco AnyConnect waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Nadat u Cisco AnyConnect hebt geconfigureerd kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).