---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Kemp LoadMaster Azure AD Integration | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Kemp LoadMaster Azure AD Integration.
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
ms.openlocfilehash: 91f6db79b7d18dc8b34ba1712d74a92000d63528
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953524"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Kemp LoadMaster Azure AD Integration

In deze zelfstudie leert u hoe u Kemp LoadMaster Azure AD Integration kunt integreren met Azure Active Directory (Azure AD). Wanneer u Kemp LoadMaster Azure AD Integration met Azure AD integreert, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Kemp LoadMaster Azure AD Integration.
* Ervoor zorgen dat uw gebruikers zich automatisch kunnen aanmelden bij Kemp LoadMaster Azure AD Integration met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Kemp LoadMaster Azure AD Integration waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kemp LoadMaster Azure AD Integration ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="add-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Kemp Kemp Azure AD-integratie toevoegen vanuit de galerie

Als u de integratie van Kemp LoadMaster Azure AD Integration in Azure AD wilt configureren, moet u Kemp LoadMaster Azure AD Integration vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Kemp LoadMaster Azure AD Integration** in het zoekvak.
1. Selecteer **Kemp LoadMaster Azure AD Integration** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Kemp LoadMaster Azure AD Integration

Configureer en test eenmalige aanmelding van Azure AD met Kemp LoadMaster Azure AD Integration met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kemp LoadMaster Azure AD Integration.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Kemp Kemp Azure AD-integratie:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding configureren in Kemp LoadMaster Azure AD Integration](#configure-kemp-loadmaster-azure-ad-integration-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.

1. **[Webserver publiceren](#publishing-web-server)**
    1. **[Een virtuele service maken](#create-a-virtual-service)**
    1. **[Certificaten en beveiliging](#certificates-and-security)**
    1. **[SAML-profiel voor Kemp LoadMaster Azure AD Integration](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Wijzigingen toepassen](#verify-the-changes)**
1. **[Verificatie op basis van Kerberos configureren](#configuring-kerberos-based-authentication)**
    1. **[Een Kerberos-delegatieaccount maken voor Kemp LoadMaster Azure AD Integration](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[KCD (Kerberos-delegatieaccount) voor Kemp LoadMaster Azure AD Integration](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[ESP voor Kemp LoadMaster Azure AD Integration](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Een testgebruiker maken in Kemp LoadMaster Azure AD Integration](#create-kemp-loadmaster-azure-ad-integration-test-user)** : als u in Kemp LoadMaster Azure AD Integration een tegenhanger van B. Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Kemp Kemp Azure AD Integration** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ een URL in het vak **Id (Entiteits-id)** : `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam voor klanten van Kemp LoadMaster Azure AD Integration](mailto:support@kemp.ax) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie Basic SAML-configuratie in Azure Portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, naar **Certificaat (base64)** en **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat en de XML-bestanden met federatieve metagegevens te downloaden. Sla deze vervolgens op de computer op.

    ![De link om het certificaat te downloaden](./media/kemp-tutorial/certificate-base-64.png)

1. In de sectie **Kemp LoadMaster Azure AD Integration instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Kemp LoadMaster Azure AD Integration.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Kemp LoadMaster Azure AD Integration**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Eenmalige aanmelding voor Kemp LoadMaster Azure AD Integration configureren

## <a name="publishing-web-server"></a>Webserver publiceren 

### <a name="create-a-virtual-service"></a>Een virtuele service maken 

1. Ga naar de LoadMaster-webgebruikersinterface van Kemp LoadMaster Azure AD Integration > Virtuele services > Nieuwe toevoegen.

1. Klik op Nieuwe toevoegen.

1. Geef de parameters voor de virtuele service op.

    ![Schermopname van de pagina Geef de parameters voor de virtuele service op, met voorbeeldwaarden in de vakken.](./media/kemp-tutorial/kemp-1.png)

    a. Virtueel adres
    
    b. Poort
    
    c. Servicenaam (optioneel)
    
    d. Protocol 

1. Ga naar de sectie Real Servers.

1. Klik op Nieuwe toevoegen.

1. Geef de parameters voor de echte server op.
    
    ![Schermopname van de pagina Geef de parameters voor de echte server op, met voorbeeldwaarden in de vakken.](./media/kemp-tutorial/kemp-2.png)

    a. Selecteer Allow Remote Addresses
    
    b. Typ het adres van de echte server
    
    c. Poort
    
    d. Doorstuurmethode
    
    e. Gewicht
    
    f. Verbindingslimiet
    
    g. Klik op Add This Real Server

## <a name="certificates-and-security"></a>Certificaten en beveiliging
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Certificaat importeren in Kemp LoadMaster Azure AD Integration 
 
1. Ga naar de webportaal van Kemp LoadMaster Azure AD Integration > Certificates & Security > SSL Certificates.

1. Onder Manage Certificates > Certificate Configuration.

1. Klik op Import Certificate.

1. Geef de naam op van het bestand dat het certificaat bevat. Het bestand kan ook de privésleutel bevatten. Als het bestand de privésleutel niet bevat, moet het bestand dat de privésleutel bevat ook worden opgegeven. Het certificaat kan de indeling .pem of .pfx-indeling (IIS) hebben.

1. Klik in Choose file op Certificate File.

1. Klik op Key File (optioneel).

1. Klik op Save.

### <a name="ssl-acceleration"></a>SSL-versnelling
 
1. Ga naar de webgebruikersinterface van Kemp LoadMaster > Virtual Services > View/Modify Services.

1. Klik onder Operation op Modify.

1. Klik op SSL-eigenschappen (die werkt op laag 7).
    
    ![Schermopname van de sectie SSL-eigenschappen, met SSL-versnelling ingeschakeld en een voorbeeldcertificaat geselecteerd.](./media/kemp-tutorial/kemp-3.png)
    
    a. Klik in SSL Acceleration op Enabled.
    
    b. Selecteer onder Available certificates het geïmporteerde certificaat en klik op het symbool `>`.
    
    c. Zodra het gewenste SSL-certificaat wordt weergegeven in Assigned certificates, klikt u op **Set certificates**.

    > [!NOTE]
    > Vergeet niet op **Set certificates** te klikken.

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>SAML-profiel voor Kemp LoadMaster Azure AD Integration
 
### <a name="import-idp-certificate"></a>IdP-certificaat importeren

Ga naar de webconsole voor Kemp LoadMaster Azure AD Integration 

1. Click onder Certificates and Authority op Intermediate Certificates.

    ![Schermopname van de sectie Momenteel geïnstalleerde tussenliggende certificaten, met een voorbeeldcertificaat geselecteerd.](./media/kemp-tutorial/kemp-6.png)

    a. Click in Add a new Intermediate Certificate op Choose file.
    
    b. Ga naar het certificaatbestand dat eerder is gedownload van de Azure AD-ondernemingstoepassing.
    
    c. Klik op Open.
    
    d. Geef een naam op in Certificate Name.
    
    e. Klik op Add Certificate.

### <a name="create-authentication-policy"></a>Authenticatiebeleid maken 
 
Ga onder Virtual Services naar Manage SSO.

   ![Schermopname van de pagina SSO beheren.](./media/kemp-tutorial/kemp-7.png)
   
   a. Click onder Add new Client Side Configuration op Add nadat u deze een naam hebt gegeven.

   b. Selecteer onder Authentication Protocol de optie SAML.

   c. Select onder IdP Provisioning de optie MetaData File. 

   d. Klik op Choose File.

   e. Ga via Azure Portal naar het eerder gedownloade XML-bestand.

   f. Klik op Open vervolgens op Import IdP MetaData file.

   g. Selecteer onder IdP Certificate het tussenliggende certificaat.

   h. Stel de ID van de SP-entiteit in die moet overeenkomen met de identiteit die is gemaakt in Azure Portal. 

   i. Klik op Set SP Entity ID.

### <a name="set-authentication"></a>Verificatie instellen  
 
Ga als volgt te werk in de webconsole voor Kemp LoadMaster Azure AD Integration

1. Klik op Virtual Services.

1. Klik op View/Modify Services.

1. Klik op Modify en ga naar ESP Options.
    
    ![Schermopname van de pagina Services weergeven/wijzigen, met de secties ESP-opties en Echte servers uitgevouwen.](./media/kemp-tutorial/kemp-8.png)

    a. Klik op Enable ESP.
    
    b. Selecteer SAML in Client Authentication Mode.
    
    c. Selecteer in SSO Domain de eerder gemaakte verificatie aan de clientzijde.
    
    d. Typ onder Allowed Virtual Hosts een hostnaam en klik op Set Allowed Virtual Hosts.
    
    e. Typ /* in Allowed Virtual Directories (op basis van toegangsvereisten) en klik op Set Allowed Directories.

### <a name="verify-the-changes"></a>Wijzigingen controleren 
 
Blader naar de URL van de toepassing 

U ziet de aanmeldingspagina voor tenants in plaats van eerder niet-geverifieerde toegang. 

![Schermopname van de tenantpagina Aanmelden.](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Verificatie op basis van Kerberos configureren 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Een Kerberos-delegatieaccount maken voor Kemp LoadMaster Azure AD Integration 

1. Maak een gebruikersaccount (in dit voorbeeld AppDelegation).
    
    ![Schermopname van het venster KCD-gebruikers, met het tabblad Account geselecteerd.](./media/kemp-tutorial/kemp-10.png)


    a. Selecteer het tabblad Kenmerkeditor.

    b. Ga naar servicePrincipalName. 

    c. Selecteer servicePrincipalName en klik op Bewerken.

    d. Typ http/kcduser in het veld Toe te voegen waarde en klik op Toevoegen. 

    e. Klik op Toepassen en op OK. Het venster moet worden gesloten voordat u het opnieuw opent (om het nieuwe tabblad Delegation te zien). 

1. Open het venster met gebruikerseigenschappen opnieuw. Het tabblad Delegering wordt beschikbaar. 

1. Selecteer het tabblad Delegering.

    ![Schermopname van het venster KCD-gebruikers, met het tabblad Delegering geselecteerd.](./media/kemp-tutorial/kemp-11.png)

    a. Selecteer Deze gebruiker mag alleen aan opgegeven services delegeren.

    b. Selecteer Elk willekeurig protocol voor authenticatie gebruiken.

    c. Voeg de echte servers toe en voeg http toe als de service. 
    
    d. Schakel het selectievakje Uitgebreid in. 

    e. U kunt alle servers met zowel de hostnaam als de FQDN zien.
    
    f. Klik op OK.

> [!NOTE]
> Stel de SPN op de toepassing/website in zoals van toepassing is. Ga als volgt te werk om toegang te krijgen tot de toepassing wanneer de identiteit van de groep van toepassingen is ingesteld. Als u toegang wilt krijgen tot de IIS-toepassing met behulp van de FQDN-naam, gaat u naar de Real Server-opdrachtprompt en typt u SetSpn met de vereiste parameters. Voor Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>KCD (Kerberos-delegatieaccount) voor Kemp LoadMaster Azure AD Integration 

Ga naar de Kemp LoadMaster Azure AD Integration-webconsole > Virtual Services > Manage SSO.

![Schermopname van de pagina SSO beheren - Domein beheren.](./media/kemp-tutorial/kemp-12.png)

a. Navigeer naar configuraties voor eenmalige aanmelding aan de serverzijde.

b. Typ in Add new Server-Side Configuration een naam en klik op Add.

c. Selecteer in Authentication Protocol de optie Kerberos Constrained Delegation.

d. Typ in Kerberos Realm de domeinnaam.

e. Klik op Set Kerberos Realm.

f. Typ in Kerberos Key Distribution Center het IP-adres van de domeincontroller.

g. Klik op Set Kerberos KDC.

h. Typ in Kerberos Trusted User Name de KCD-gebruikersnaam.

i. Klik op Set KDC trusted user name.

j. Typ het wachtwoord in Kerberos Trusted User Password.

k. Klik op Set KCD trusted user password.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>ESP voor Kemp LoadMaster Azure AD Integration 

Ga naar Virtual Services > View/Modify Services.

![Webserver voor Kemp LoadMaster Azure AD Integration](./media/kemp-tutorial/kemp-13.png)

a. Klik voor de bijnaam van de virtuele service op Modify.
    
b. Klik op ESP Options.
    
c. Selecteer onder Server Authentication Mode de optie KCD.
        
d. Selecteer onder Server-Side configuration het eerder gemaakte profiel aan de serverzijde.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Testgebruiker voor Kemp LoadMaster Azure AD Integration maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Kemp LoadMaster Azure AD Integration. Werk met [het klantondersteuningsteam van Kemp LoadMaster Azure AD Integration](mailto:support@kemp.ax) om de gebruikers toe te voegen in het Kemp LoadMaster Azure AD Integration-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de Kemp Kemp Azure AD-integratie waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Kemp Kemp Azure AD-integratie in de mijn apps klikt, moet u automatisch worden aangemeld bij de Kemp Kemp Azure AD-integratie waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Kemp LoadMaster Azure AD Integration hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).