---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met New Relic | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en New Relic configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517603"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met New Relic

In deze zelfstudie leert u hoe u New Relic integreert met Azure Active Directory (Azure AD). Wanneer u New Relic met Azure AD integreert, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot New Relic.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij New Relic.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op New Relic waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Nieuwe Relic biedt ondersteuning voor eenmalige aanmelding die is geïnitieerd met de serviceprovider of de id-provider.

## <a name="add-new-relic-from-the-gallery"></a>New Relic toevoegen vanuit de galerie

Voor het configureren van de integratie van New Relic met Azure AD moet u **New Relic (By Organization)** vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de service **Azure Active Directory**.
1. Selecteer **Bedrijfstoepassingen** > **Nieuwe toepassing**.
1. Typ op de pagina **Bladeren in Azure DA-galerie** **New Relic (By Organization)** in het zoekvak.
1. Selecteer **New Relic (By Organization)** in de resultaten, en selecteer vervolgens **Maken**. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Eenmalige aanmelding van Azure AD voor New Relic configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met New Relic met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een gekoppelde relatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in New Relic.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met New Relic:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
   1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
   1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding van New Relic configureren](#configure-new-relic-sso): als u de instellingen voor eenmalige aanmelding aan de zijde van New Relic wilt configureren.
   1. [Een testgebruiker voor New Relic maken](#create-a-new-relic-test-user): als u een tegenhanger van B.Simon in New Relic wilt hebben die is gekoppeld aan de Azure AD-gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op **de New Relic de** integratiepagina van de toepassing New Relic organisatie de **sectie** Beheren. Selecteer vervolgens **Eenmalige aanmelding**.

1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd.](common/edit-urls.png)

1. In de sectie **Eenvoudige SAML-configuratie** vult u waarden in voor **Id** en **Antwoord-URL**.

   * Haal deze waarden op met behulp van de toepassing New Relic **My Organization**. Doe het volgende om deze toepassing te gebruiken:
      1. [Meld u aan](https://login.newrelic.com/) bij New Relic.
      1. Selecteer **Apps**  in het bovenste menu.
      1. Selecteer in de sectie **Uw apps** achtereenvolgens **My Organization** > **Verificatiedomeinen**.
      1. Kies het verificatiedomein waarmee eenmalige aanmelding van Azure AD verbinding moet maken (als u meer dan één verificatiedomein hebt). De meeste bedrijven hebben slechts één verificatiedomein, met de naam **Standaard**. Als er slechts één verificatiedomein is, hoeft u niets te selecteren.
      1. In de sectie **Verificatie** bevat **URL voor Assertion Consumer** de waarde die moet worden gebruikt voor **Antwoord-URL**.
      1. In de sectie **Verificatie** bevat **Onze entiteits-id** de waarde die moet worden gebruikt voor **Id**.

1. Controleer in de sectie **Gebruikerskenmerken en claims** of **Unieke gebruikers-id** is toegewezen aan een veld met het e-mailadres dat wordt gebruikt in New Relic.

   * Het standaardveld **user.userprincipalname** werkt als de waarden ervan gelijk zijn aan die van de New Relic-e-mailadressen.
   * Het veld **user.mail** werkt mogelijk beter als **user.userprincipalname** niet het New Relic-e-mailadres is.

1. Kopieer in de sectie **SAML-handtekeningcertificaat** de **URL voor federatieve metagegevens van de app** en sla de waarde op voor later gebruik.

1. Kopieer in de sectie **New Relic by Organization instellen** de **Aanmeldings-URL**, en sla de waarde op voor later gebruik.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot New Relic.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met **toepassingen New Relic**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-new-relic-sso"></a>Eenmalige aanmelding voor New Relic configureren

Volg deze stappen om eenmalige aanmelding voor New Relic te configureren.

1. [Meld u aan](https://login.newrelic.com/) bij New Relic.

1. Selecteer **Apps**  in het bovenste menu.

1. Selecteer in de sectie **Uw apps** achtereenvolgens **My Organization** > **Verificatiedomeinen**.

1. Kies het verificatiedomein waarmee eenmalige aanmelding van Azure AD verbinding moet maken (als u meer dan één verificatiedomein hebt). De meeste bedrijven hebben slechts één verificatiedomein, met de naam **Standaard**. Als er slechts één verificatiedomein is, hoeft u niets te selecteren.

1. Selecteer in de sectie **Verificatie** de optie **Configureren**.

   1. Voer bij **Bron van SAML-metagegevens** de waarde in die u eerder hebt opgeslagen vanuit het Azure AD-veld **URL voor de federatieve metagegevens van de app**.

   1. Voer bij **Doel-URL voor eenmalige aanmelding** de waarde in die u eerder hebt opgeslagen vanuit het Azure AD-veld **Aanmeldings-URL**.

   1. Selecteer **Opslaan** nadat u hebt geverifieerd dat de instellingen kloppen aan de zijden van Azure AD en New Relic. Als beide zijden niet op de juiste wijze zijn geconfigureerd, kunnen gebruikers zich niet aanmelden bij New Relic.

### <a name="create-a-new-relic-test-user"></a>Een New Relic-testgebruiker maken

In deze sectie maakt u in New Relic een gebruiker met de naam B. Simon.

1. [Meld u aan](https://login.newrelic.com/) bij New Relic.

1. Selecteer **Apps**  in het bovenste menu.

1. In de sectie **Uw apps** selecteert u de optie **Gebruikersbeheer**.

1. Selecteer **Gebruiker toevoegen**.

   1. Voer bij **Naam** in: **B.Simon**.
   
   1. Voer bij **E-mail** de waarde in die wordt verzonden door eenmalige aanmelding van Azure.
   
   1. Kies een **type** en **groep** voor de gebruiker. Voor een testgebruiker en voor type zijn **Basisgebruiker** respectievelijk **Gebruiker** redelijke keuzes.
   
   1. Selecteer **Gebruiker toevoegen** om de gebruiker op te slaan.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid New Relic aanmeldings-URL waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks New Relic aanmeldings-URL en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de New Relic waarvoor u eenmalige aanmelding hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel New Relic in de Mijn apps klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de New Relic waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de New Relic kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
