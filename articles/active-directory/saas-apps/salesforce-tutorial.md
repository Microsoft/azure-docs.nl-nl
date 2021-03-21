---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Salesforce | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 0f800d2d42d0d8815021f1582b04750d87aa5abc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Salesforce

In deze zelfstudie leert u hoe u Salesforce integreert met Azure Active Directory (Azure AD). Wanneer u Salesforce integreert met Azure AD, kunt u:

* in Azure AD bepalen wie er toegang heeft tot Salesforce.
* ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Salesforce.
* uw accounts op één centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Salesforce waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sales Force ondersteunt door **SP** geïnitieerde SSO.

* Sales Force ondersteunt [ **automatische** gebruikers inrichting en](salesforce-provisioning-tutorial.md) het ongedaan maken van de inrichting (aanbevolen).

* Sales Force ondersteunt **just-in-time** -gebruikers inrichting.

* De mobiele Salesforce-toepassing kan nu worden geconfigureerd met Azure AD voor het inschakelen van eenmalige aanmelding. In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce toevoegen vanuit de galerie

Voor het configureren van de integratie van Salesforce in Azure AD moet u Salesforce vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie**, **Salesforce** in het zoekvak.
1. Selecteer **Salesforce** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Eenmalige aanmelding van Azure AD voor Salesforce configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Salesforce met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Salesforce.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Salesforce te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Salesforce-eenmalige aanmelding configureren](#configure-salesforce-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Salesforce-testgebruiker maken](#create-salesforce-test-user)** : als u een equivalent van B.Simon in Salesforce wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga naar de integratiepagina van de **Salesforce**-app in de Azure Portal en selecteer **Eenmalige aanmelding** in de sectie **Beheer**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:
    
    a. Typ in het tekstvak **Id** een waarde met de volgende indeling:

    Bedrijfsaccount: `https://<subdomain>.my.salesforce.com`

    Ontwikkelaarsaccount: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Typ in het tekstvak **Antwoord-URL** een URL met de volgende indeling:

    Bedrijfsaccount: `https://<subdomain>.my.salesforce.com`

    Ontwikkelaarsaccount: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende indeling:

    Bedrijfsaccount: `https://<subdomain>.my.salesforce.com`

    Ontwikkelaarsaccount: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [Salesforce-klantondersteuningsteam](https://help.salesforce.com/support) om deze waarden te verkrijgen.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Salesforce instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Salesforce.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Salesforce** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-salesforce-sso"></a>Eenmalige aanmelding configureren voor Salesforce

1. Als u de configuratie in Salesforce wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Na de toevoeging van de extensie aan de browser, klikt u op **Salesforce instellen** om naar de toepassing voor eenmalige aanmelding in Salesforce te worden geleid. Geef hier de Administrator-referenties op om u aan te melden bij Eenmalige aanmelding in Salesforce. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-13 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Salesforce handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Salesforce-bedrijfssite. Voer daarna de volgende stappen uit:

1. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Eenmalige aanmelding configureren - Instellingspictogram](./media/salesforce-tutorial/configure1.png)

1. Schuif in het navigatiedeelvenster omlaag naar **INSTELLINGEN** en klik op **Identiteit** om het bijbehorende gedeelte uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren - Instellingen](./media/salesforce-tutorial/sf-admin-sso.png)

1. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren - Bewerken](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Als u eenmalige aanmelding niet kunt inschakelen voor uw Salesforce-account, moet u mogelijk contact opnemen met het [Salesforce-klantondersteuningsteam](https://help.salesforce.com/support).

1. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren - SAML is ingeschakeld](./media/salesforce-tutorial/sf-enable-saml.png)

1. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Eenmalige aanmelding configureren - Nieuw op basis van een metagegevensbestand](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Klik op **Bestand kiezen** om het XML-bestand met metagegevens te uploaden dat u hebt gedownload uit Azure Portal. Klik dan op **Maken**.

    ![Eenmalige aanmelding configureren - Bestand kiezen](./media/salesforce-tutorial/xmlchoose.png)

1. Op de pagina **SAML Single Sign-On Settings** worden velden automatisch gevuld als u SAML JIT wilt gebruiken, selecteert u de optie voor het inrichten van de **gebruiker** en selecteert u **SAML-identiteits type** als **bevestiging bevat de Federatie-id uit het gebruikers object** anders, schakelt u het selectie vakje **Gebruikers inrichten ingeschakeld** in en selecteert u **SAML-identiteits type** als **bevestiging bevat de gebruikers naam van Sales Force**. Klik op **Opslaan**.

    ![Eenmalige aanmelding configureren - Gebruikers inrichten is ingeschakeld](./media/salesforce-tutorial/salesforcexml.png)

1. Klik in het navigatiedeelvenster links in Salesforce op **Bedrijfsinstellingen** om het bijbehorende gedeelte uit te vouwen. Klik dan op **Mijn domein**.

    ![Eenmalige aanmelding configureren - Mijn domein](./media/salesforce-tutorial/sf-my-domain.png)

1. Schuif omlaag naar het gedeelte **Verificatieconfiguratie** en klik op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren - Verificatieconfiguratie](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Controleer in de sectie **verificatie configuratie** de **aanmeldings pagina** en  **AzureSSO** als **verificatie service** van uw SAML SSO-configuratie en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren - Verificatieservice](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > Als er meer dan één verificatieservice wordt geselecteerd, wordt gebruikers gevraagd met welke verificatieservice ze zich graag willen aanmelden om gebruik te maken van eenmalige aanmelding in uw Salesforce-omgeving. Als u niet wilt dat dit gebeurt, moet u **andere verificatieservices uitgeschakeld laten**.

### <a name="create-salesforce-test-user"></a>Een Salesforce-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Salesforce. Salesforce biedt ondersteuning voor just-in-time inrichten, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Salesforce, wordt er een nieuwe gemaakt wanneer u Salesforce opent. Salesforce ondersteunt ook automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](salesforce-provisioning-tutorial.md).

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Salesforce, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Salesforce en initieer daar de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Sales Force-tegel in de mijn apps klikt, moet u automatisch worden aangemeld bij het Sales Force waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="test-sso-for-salesforce-mobile"></a>Test voor eenmalige aanmelding in Salesforce (mobiel)

1. Open de mobiele Salesforce-toepassing. Klik op de aanmeldpagina op **Aangepast domein gebruiken**.

    ![Mobiele app van Salesforce - Aangepast domein gebruiken](media/salesforce-tutorial/mobile-app1.png)

1. Voer in het tekstvak **Aangepast domein** uw geregistreerde aangepaste domeinnaam in en klik op **Doorgaan**.

    ![Mobiele app van Salesforce - Aangepast domein](media/salesforce-tutorial/mobile-app2.png)

1. Voer uw Azure AD-referenties in om u aan te melden bij de Salesforce-toepassing en klik op **Volgende**.

    ![Mobiele app van Salesforce - Azure AD aanmeldingsgegevens](media/salesforce-tutorial/mobile-app3.png)

1. Klik op de pagina **Toegang toestaan** op **Toestaan** om toegang te verlenen tot de Salesforce-toepassing.

    ![Mobiele app van Salesforce - Toegang toestaan](media/salesforce-tutorial/mobile-app4.png)

1. Als het aanmelden is gelukt, wordt de startpagina van de toepassing weergegeven.

    ![Homepage van de mobiele app van Salesforce](media/salesforce-tutorial/mobile-app5.png) ![Mobiele app van Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Volgende stappen

Zodra u Salesforce hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)