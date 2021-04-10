---
title: 'Zelfstudie: Azure Active Directory-integratie met OneTrust Privacy Management Software | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en OneTrust Privacy Management Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f0a145b0eb9dd9dbed0927ce825a21d8f47c48ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648421"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Zelfstudie: Azure Active Directory-integratie met OneTrust Privacy Management Software

In deze zelf studie leert u hoe u OneTrust privacy management-software integreert met Azure Active Directory (Azure AD). Wanneer u OneTrust-software voor privacybeleid integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de OneTrust-software voor privacybeleid.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij OneTrust-privacybeleid met hun Azure AD-accounts.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met OneTrust Privacy Management Software te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op OneTrust privacybeleid voor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* OneTrust Privacy Management Software ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

* OneTrust Privacy Management Software ondersteunt **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>OneTrust-software voor privacybeleid toevoegen vanuit de galerie

Om de integratie van OneTrust Privacy Management Software in Azure AD te configureren, moet u OneTrust Privacy Management Software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **OneTrust-privacybeleid** in het zoekvak in de sectie **toevoegen vanuit de galerie** .
1. Selecteer **OneTrust-privacybeleid software** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Azure AD SSO configureren en testen voor OneTrust-software voor privacybeleid

Azure AD SSO configureren en testen met OneTrust-software voor privacybeleid met behulp van een test gebruiker met de naam **B. Simon**. Voor een goede werking van SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in OneTrust-software voor privacybeleid.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met OneTrust-privacybeleid:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[OneTrust privacy management software SSO configureren](#configure-onetrust-privacy-management-software-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een OneTrust-privacybeleid voor software test](#create-onetrust-privacy-management-software-test-user)** , zodat het een soort is van B. Simon InOneTrust-privacybeleid voor software die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u eenmalige aanmelding van Azure AD in de Azure Portal inschakelen.
 
1. Ga in het Azure Portal naar de pagina integratie van **OneTrust-software** toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u deze URL: `https://www.onetrust.com/saml2`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

     In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke antwoord-URL en aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van OneTrust Privacy Management Software](mailto:support@onetrust.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **OneTrust Privacy Management Software instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.
1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de OneTrust-software voor privacybeleid.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **OneTrust Privacy Management Software** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>SSO van OneTrust privacy management-software configureren

Als u eenmalige aanmelding aan de zijde van **OneTrust Privacy Management Software** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Azure-portal gekopieerde URL's naar het [ondersteuningsteam van OneTrust Privacy Management Software](mailto:support@onetrust.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Testgebruiker voor OneTrust Privacy Management Software maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in OneTrust Privacy Management Software. OneTrust Privacy Management Software ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in OneTrust Privacy Management Software bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u een gebruiker handmatig wilt maken, neemt u contact op met het [ondersteuningsteam van OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van de OneTrust-privacybeleid, waar u de aanmeldings stroom kunt initiëren.  
 
* Ga naar de aanmeldings-URL van de OneTrust-software voor privacybeleid direct en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de OneTrust-software voor privacybeleid waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel software van de OneTrust-privacyverklaring in de mijn apps klikt, wordt u, indien geconfigureerd in de SP-modus, omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de OneTrust-software voor privacybeleid waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de OneTrust-software voor privacybeleid hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).