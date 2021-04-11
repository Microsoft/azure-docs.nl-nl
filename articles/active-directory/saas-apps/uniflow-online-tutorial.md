---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met uniFLOW Online| Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en uniFLOW Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met uniFLOW Online

In deze zelfstudie leert u hoe u uniFLOW Online integreert met Azure Active Directory (Azure AD). Wanneer u uniFLOW Online integreert met Azure AD, kunt u het volgende doen:

* Beheer in Azure Active Directory wie toegang heeft tot uniFLOW Online.
* Stel uw gebruikers in staat om zich online aan te melden bij uniFLOW met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* uniFLOW Online-tenant.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* uniFLOW Online biedt ondersteuning voor eenmalige aanmelding die is gestart vanuit **SP**

## <a name="add-uniflow-online-from-the-gallery"></a>UniFLOW online toevoegen vanuit de galerie

Als u de integratie van uniFLOW in Azure AD wilt configureren, moet u uniFLOW vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **uniFLOW Online**.
1. Selecteer **uniFLOW Online** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Azure AD SSO voor uniFLOW online configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met uniFLOW Online met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in uniFLOW Online.

Voer de volgende stappen uit om Azure AD SSO met uniFLOW online te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
   1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
   1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij uniFLOW Online configureren](#configure-uniflow-online-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Meld u aan bij uniFLOW Online met behulp van de gemaakte test gebruiker](#sign-in-to-uniflow-online-using-the-created-test-user)** - om gebruikersaanmelding te testen aan de kant van de toepassing.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **UniFLOW online** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ in het tekstvak **URL voor aanmelden** een URL met behulp van een van de volgende patronen:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met één van de volgende patronen:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met [het uniFLOW Online-clientondersteuningsteam](mailto:support@nt-ware.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal of raadpleeg de antwoord-URL die wordt weergegeven in uw uniFLOW Online-tenant.

1. In de uniFLOW Online-toepassing worden de SAML-beweringen in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de uniFLOW Online nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | -----------| --------------- |
    | displayname | user.displayname |
    | bijnaam | user.onpremisessamaccountname |

   > [!NOTE]
   > Het kenmerk `user.onpremisessamaccountname` bevat alleen een waarde als uw Azure AD-gebruikers worden gesynchroniseerd vanuit een lokale Windows Active Directory.

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot uniFLOW Online.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **uniFLOW Online**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

> [!NOTE]
> Als u wilt dat alle gebruikers toegang krijgen tot de toepassing zonder handmatige toewijzing, gaat u naar de sectie **Beheren** en selecteert u **Eigenschappen**. Wijzig vervolgens de parameter **Gebruikerstoewijzing vereist** naar **NEE**.

## <a name="configure-uniflow-online-sso"></a>UniFLOW online-SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van uniFLOW Online.

1. Selecteer het tabblad **Gebruikers** in het linkernavigatievenster.

    ![Schermopname met User geselecteerd op de uniflow online-site.](./media/uniflow-online-tutorial/configure-1.png)

1. Klik op **Id-providers**.

    ![Schermopname met Identity Providers geselecteerd.](./media/uniflow-online-tutorial/configure-2.png)

1. Klik op **Id-provider toevoegen**.

    ![Schermopname met Add identity provider geselecteerd.](./media/uniflow-online-tutorial/configure-3.png)

1. Voer in de sectie **IDENTITY PROVIDER TOEVOEGEN** de volgende stappen uit:

    ![Schermopname met de sectie ADD IDENTITY PROVIDER, waar u de beschreven waarden kunt invoeren.](./media/uniflow-online-tutorial/configure-4.png)

    a. Voer de weergavenaam in, bijv.: *AzureAD SSO*.

    b. Selecteer in de vervolgkeuzelijst **WS-Fed** optie voor **Providertype**.

    c. Selecteer bij **WS-Fed-type** de optie **Azure Active Directory** in de vervolgkeuzelijst.

    d. Klik op **Opslaan**.

1. Voer op het tabblad **Algemeen** de volgende stappen uit:

    ![Schermopname van de pagina General, waar u de beschreven waarden kunt invoeren.](./media/uniflow-online-tutorial/configure-5.png)

    a. Voer de weergavenaam in, bijv.: *AzureAD SSO*.

    b. Selecteer de optie **Van URL** voor de **Metagegevens van ADFS Federation**.

    c. Plak in het tekstvak **URL van metagegevens** de waarde van **Metagegevens-URL van App Federation** die u uit de Azure-portal hebt gekopieerd.

    d. Selecteer **Id-provider** als **Ingeschakeld**.

    e. Selecteer **Automatische registratie van gebruikers** als **Geactiveerd**.

    f. Klik op **Opslaan**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Meld u aan bij uniFLOW Online met de gemaakte testgebruiker

1. In een ander browservenster gaat u naar de uniFLOW Online-URL voor uw tenant.

1. Selecteer de eerder gemaakte id-provider om u aan te melden via uw Azure AD-exemplaar.

1. Aanmelden met behulp van de testgebruiker.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de uniFLOW online-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor uniFLOW Online-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel uniFLOW online in de mijn apps klikt, wordt dit omgeleid naar de URL voor uniFLOW online sign-on. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u uniFLOW online hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).