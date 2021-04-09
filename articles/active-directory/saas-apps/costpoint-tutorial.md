---
title: 'Zelfstudie: Azure Active Directory-integratie met Costpoint | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 781d1509b69ecb09189772f4a31d4872201d0b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652926"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Zelfstudie: Costpoint integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Costpoint kunt integreren met Azure AD (Active Directory). Wanneer u Costpoint integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Costpoint.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Costpoint.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Costpoint-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u eenmalige aanmelding van Azure AD in een testomgeving. 

* Costpoint biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding.

## <a name="generate-costpoint-metadata"></a>Metagegevens voor Costpoint genereren

Costpoint SAML SSO-configuratie wordt uitgelegd in de handleiding **DeltekCostpoint711Security.pdf**. Download deze handleiding op de ondersteuningssite van Deltek Costpoint en raadpleeg de sectie **Eenmalige aanmelding instellen met SAML** > **Eenmalige aanmelding van SAML instellen tussen Costpoint en Microsoft Azure**. Volg de instructies en genereer een **XML-bestand met federatieve metagegevens voor Costpoint SP**. 

![Schermopname van het 'hulpprogramma voor productconfiguratie'; het tabblad 'WebLogic-beveiliging' is geselecteerd.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Costpoint toevoegen vanuit de galerie

Als u de integratie van Costpoint in azure AD wilt configureren, moet u Costpoint uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Costpoint** in het zoekvak.
1. Selecteer **Costpoint** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Azure AD SSO voor Costpoint configureren en testen

Azure AD SSO met Costpoint configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Costpoint.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Costpoint:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[COSTPOINT SSO configureren](#configure-costpoint-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Costpoint-test gebruiker](#create-costpoint-test-user)** -om een equivalent van B. Simon in Costpoint te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Selecteer op de integratiepagina van de toepassing **Costpoint** de optie **Eenmalige aanmelding**.

1. Voltooi in de sectie **Standaard SAML-configuratie** deze stappen als u het **Service Provider-metagegevensbestand** hebt:

   > [!NOTE]
   > U downloadt het Service Provider-metagegevensbestand in [Metagegevens voor Costpoint genereren](#generate-costpoint-metadata). Later in de zelfstudie wordt uitgelegd hoe u het bestand gebruikt.
 
   1. Selecteer de knop **Metagegevensbestand uploaden**. Selecteer het **XML-bestand met federatieve metagegevens voor Costpoint SP** dat eerder is gegenereerd in Costpoint, en selecteer vervolgens de knop **Toevoegen** om het bestand te uploaden.

      ![Het metagegevensbestand uploaden](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Wanneer het metagegevensbestand is geüpload, worden de waarde voor **Id** en **Antwoord-URL** automatisch ingevuld in de Costpoint-sectie.

      > [!NOTE]
      > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, voert u de waarden handmatig in afhankelijk van uw vereisten. Controleer of **Id (Entiteits-id)** en **Antwoord-URL (URL voor Assertion Consumer Service)** juist zijn ingesteld, en dat **ACS-URL** een geldige Costpoint-URL is die eindigt met **/LoginServlet.cps**.

   1. Selecteer **Extra URL's instellen**. Voer voor **Relaystatus** een waarde in met het volgende patroon:`system=[your system]` (bijvoorbeeld **system=DELTEKCP**).

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** in de sectie **SAML-handtekeningcertificaat** het pictogram **Kopiëren** om de **App-URL voor federatieve metagegevens** te kopiëren, en sla deze op in Kladblok.

   ![SAML-handtekeningcertificaat](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Costpoint.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Costpoint** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-costpoint-sso"></a>Costpoint SSO configureren

1. Ga terug naar het hulpprogramma voor Costpoint-configuratie. Plak in het tekstvak **XML-bestand met federatieve IdP-metagegevens** de inhoud van het bestand *App-URL voor federatieve metagegevens*. 

   ![Hulpprogramma voor Costpoint-configuratie](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Ga verder met de instructies uit de handleiding **DeltekCostpoint711Security.pdf** om het instellen van Costpoint SAML te voltooien.

### <a name="create-costpoint-test-user"></a>Costpoint-test gebruiker maken

In deze sectie maakt u een gebruiker in Costpoint. Stel dat de gebruikers-id **B.SIMON** is, en dat de naam van de gebruiker **B.Simon** is. Neem contact op met het [klantondersteuningsteam van Costpoint](https://www.deltek.com/about/contact-us) om de gebruiker toe te voegen aan het Costpoint-platform. De gebruiker moet worden gemaakt en geactiveerd voordat deze eenmalige aanmelding kan gebruiken.

Nadat de gebruiker is gemaakt, moet de geselecteerde **Verificatiemethode** van de gebruiker **Active Directory** zijn. Ook moet het selectievakje **Eenmalige aanmelding voor SAML** zijn geselecteerd, en moet de gebruikersnaam van Azure Active Directory **Active Directory of Certificaat-id** zijn (zoals weergegeven in de volgende schermopname).

![Costpoint-gebruiker](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor Costpoint-aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de URL voor Costpoint-aanmelding en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Costpoint waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Costpoint in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze in de IDP-modus is geconfigureerd, moet u automatisch worden aangemeld bij de Costpoint waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Costpoint hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).