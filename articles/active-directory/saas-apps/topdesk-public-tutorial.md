---
title: 'Zelfstudie: Azure Active Directory-integratie met TOPdesk - Public | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TOPdesk - Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 5d16fd87b01db69d3f55e22aad573b7847b9048c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518024"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Zelfstudie: Azure Active Directory-integratie met TOPdesk - Public

In deze zelfstudie leert u hoe u TOPdesk - Public integreert met Azure Active Directory (Azure AD). Wanneer u TOPdesk - Public integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot TOPdesk - Public.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij TOPdesk - Public.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op TOPdesk - Public met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TOPdesk - Public ondersteunt door **SP geïnitieerde** eenmalige aanmelding.

## <a name="add-topdesk---public-from-the-gallery"></a>TOPdesk - Public toevoegen vanuit de galerie

Om de integratie van TOPdesk - Public te configureren in Azure AD, moet u TOPdesk - Public uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **TOPdesk - Public** in het zoekvak.
1. Selecteer **TOPdesk - Public in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Eenmalige aanmelding van Azure AD voor TOPdesk - Public configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met TOPdesk - Public met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TOPdesk - Public.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met TOPdesk - Public te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij TOPdesk - Public configureren](#configure-topdesk---public-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor TOPdesk - Public](#create-topdesk---public-test-user)** maken : als u een tegenhanger van B.Simon in TOPdesk - Public wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de **toepassing TOPdesk - Public** de sectie Beheren en selecteer Een  **aanmelding.**
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4.  Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U ontvangt het **metagegevensbestand van de serviceprovider** in de sectie **Eenmalige aanmelding voor TOPdesk - Public configureren**, die verderop in de zelfstudie wordt beschreven.

    a. Klik op **Metagegevensbestand uploaden**.
    
    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie.

    d. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.topdesk.net`

    e. Vul in het tekstvak **Identifier URL** de URL voor de metagegevens van TOPdesk in. Deze kunt u ophalen uit de configuratie van TOPdesk. Gebruik hiervoor het volgende patroon: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Als de waarden voor **Identifier** (id) en **Reply URL** (antwoord-URL) niet automatisch worden ingevuld, moet u deze handmatig invoeren. Voor de id gebruikt u het patroon zoals hierboven wordt vermeld en haalt u de waarde voor de antwoord-URL op uit de sectie **Eenmalige aanmelding voor TOPdesk - Public configureren**, die verderop in de zelfstudie wordt beschreven. De waarde van de **aanmeldings-URL** is niet echt en moet door u worden gewijzigd in de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van TOPdesk - Public](https://help.topdesk.com/saas/enterprise/user/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **TOPdesk - Public instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot TOPdesk - Public.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **TOPdesk - Public** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name=&quot;configure-topdesk---public-sso&quot;></a>Eenmalige aanmelding voor TOPdesk - Public configureren

1. Meld u aan bij de bedrijfssite van **TOPdesk - Public** als een beheerder.

2. Klik in het menu van **TOPdesk** op **Settings**.
   
    ![Instellingen](./media/topdesk-public-tutorial/menu.png &quot;Instellingen")

3. Klik op **Login Settings**.
   
    ![Aanmeldingsinstellingen](./media/topdesk-public-tutorial/login.png "Aanmeldingsinstellingen")

4. Vouw het menu **Login Settings** uit en klik op **General**.
   
    ![Algemene instellingen](./media/topdesk-public-tutorial/general.png "Algemene instellingen")

5. Voer de volgende stappen uit in de sectie **Public** van de configuratiesectie **SAML login**:
   
    ![Technische instellingen](./media/topdesk-public-tutorial/public.png "Technical Settings")
   
    a. Klik op **Downloaden** om het openbare metagegevensbestand te downloaden en sla het lokaal op uw computer op.
   
    b. Open het gedownloade metagegevensbestand en zoek het knooppunt **AssertionConsumerService**.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Kopieer de waarde van **AssertionConsumerService**, plak deze in het testvak **Antwoord-** in de sectie **Standaard SAML-configuratie**.      
   
6. Als u een certificaatbestand wilt maken, moet u de volgende stappen uitvoeren:
    
    ![Certificaat](./media/topdesk-public-tutorial/certificate-file.png "Certificaat")
    
    a. Open het gedownloade metagegevensbestand vanuit de Azure-portal.
    
    b. Vouw het knooppunt **RoleDescriptor** uit waarvan **xsi:type** is ingesteld op **fed:ApplicationServiceType**.
    
    c. Kopieer de waarde van het knooppunt **X509Certificate**.
    
    d. Sla de gekopieerde waarde van **X509Certificate** lokaal op uw computer op in een bestand.

7. Klik in de sectie **Public** op **Add**.
    
    ![SAML Login](./media/topdesk-public-tutorial/add.png "SAML Login")

8. Voer de volgende stappen uit in het dialoogvenster **SAML configuration assistant**:
    
    ![SAML-configuratie-assistent](./media/topdesk-public-tutorial/configuration.png "SAML Configuration Assistant")
    
    a. Klik onder **Federatieve metagegevens** op **Bladeren** om het gedownloade metagegevensbestand te uploaden vanuit de Azure-portal.

    b. Klik onder **Certificaat (RSA)** op **Bladeren** om het certificaatbestand te uploaden.

    c. Klik onder het **Logo-pictogram** op **Bladeren** om het logobestand dat u van het TOPdesk-ondersteuningsteam hebt verkregen, te uploaden.

    d. In het tekstvak voor het **gebruikersnaamkenmerk** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Typ in het tekstvak **Weergavenaam** een naam voor de configuratie.

    f. Klik op **Opslaan**.

### <a name="create-topdesk---public-test-user"></a>Testgebruiker voor TOPdesk - Public maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij TOPdesk - Public, moet ze worden ingericht in TOPdesk - Public. Het inrichten moet handmatig worden uitgevoerd in TOPdesk - Public.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Voer de volgende stappen uit om de inrichting van gebruikers te configureren:

1. Meld u als beheerder aan bij de bedrijfssite van **TOPdesk - Public**.

2. Klik in het menu aan de bovenkant op **TOPdesk \> New \> Support Files \> Person**.
   
    ![Person](./media/topdesk-public-tutorial/files.png "Person")

3. Voer de volgende stappen uit in het dialoogvenster New Person:
   
    ![New Person](./media/topdesk-public-tutorial/new.png "New Person")
   
    a. Klik op het tabblad General.

    b. Typ in het tekstvak **Surname** de achternaam van de gebruiker, bijvoorbeeld Simon
 
    c. Selecteer een **site** voor het account.
 
    d. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook Azure AD-gebruikersaccounts inrichten met alle andere door TOPdesk - Public geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van TOPdesk - Public, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van TOPdesk - Public en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel TOPdesk - Public in de Mijn apps klikt, wordt u omgeleid naar de aanmeldings-URL van TOPdesk - Public. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u TOPdesk - Public hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
