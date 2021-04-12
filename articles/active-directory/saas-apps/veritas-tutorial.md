---
title: 'Zelfstudie: Azure Active Directory-integratie met Veritas Enterprise Vault.cloud SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Veritas Enterprise Vault.cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222279"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Zelfstudie: Azure Active Directory-integratie met Veritas Enterprise Vault.cloud SSO

In deze zelf studie leert u hoe u de Vault.cloud-SSO van Veritas Enter prise integreert met Azure Active Directory (Azure AD). Wanneer u de Vault.cloud SSO van Veritas Enter prise integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Vault.cloud SSO van Veritas Enter prise.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij de Vault.cloud SSO van Veritas Enter prise met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u de Azure AD-integratie met Veritas Enterprise Vault.cloud wilt configureren, hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Abonnement voor de eenmalige aanmelding van het Veritas Enter prise Vault.cloud SSO.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Veritas Enter prise Vault.cloud SSO ondersteunt door **SP** geïnitieerde SSO.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Veritas Enter prise Vault.cloud SSO toevoegen vanuit de galerie

Om de integratie van Veritas Enterprise Vault.cloud te configureren in Azure AD moet u Veritas Enterprise Vault.cloud vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u de **Vault.Cloud SSO van Veritas Enter prise** in het zoekvak.
1. Selecteer **Veritas Enter prise Vault.Cloud SSO** van het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Azure AD SSO voor Veritas Enter prise Vault.cloud SSO configureren en testen

Azure AD SSO configureren en testen met Veritas Enter prise Vault.cloud SSO met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Vault.cloud SSO van Veritas Enter prise.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met de Vault.cloud SSO van Veritas Enter prise.

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer de Veritas Enter prise Vault.Cloud SSO SSO](#configure-veritas-enterprise-vaultcloud-sso-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. Maak een Vault.cloud-gebruiker voor het maken van een **[Enter prise-gebruikers](#create-veritas-enterprise-vaultcloud-sso-test-user)** -of-gebruiker-omgeving, zodat deze een soort is van B. Simon in Veritas enter PRISE Vault.Cloud SSO dat is gekoppeld aan de Azure AD-representatie
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Veritas Enter prise Vault.Cloud SSO** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Typ in het vak **id** een van de url's volgens het Data Center:

    | Datacenter| URL |
    |----------|----|
    | Noord-Amerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azië en Stille Oceaan| `https://auth.syd.archivecloud.net`|

    c. Typ in het tekstvak **antwoord-URL** een van de url's volgens het Data Center:

    | Datacenter| URL |
    |----------|----|
    | Noord-Amerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azië en Stille Oceaan| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [Veritas Enterprise Vault.cloud SSO-klantondersteuningsteam](https://www.veritas.com/support/.html) om deze waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Veritas Enterprise Vault.cloud SSO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Vault.cloud SSO van Veritas Enter prise.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Veritas Enterprise Vault.cloud SSO**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>Veritas Enter prise Vault.cloud SSO SSO configureren

Als u eenmalige aanmelding bij **Veritas Enterprise Vault.cloud SSO** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [ondersteuningsteam van Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Veritas Enterprise Vault.cloud SSO-testgebruiker maken

In dit gedeelte maakt u de gebruiker Britta Simon in Veritas Enterprise Vault.cloud SSO. Neem contact op met het [ondersteuningsteam van Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) om de gebruikers toe te voegen in het Veritas Enterprise Vault.cloud SSO-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor de Vault.cloud SSO-aanmeld gegevens van Veritas, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor de Vault.cloud SSO-aanmeld en Initieer de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Veritas Enter prise Vault.cloud SSO-tegel in de mijn apps klikt, wordt dit omgeleid naar de URL van de Veritas Enter prise Vault.cloud SSO-aanmelding. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u de Vault.cloud SSO van Veritas Enter prise hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
