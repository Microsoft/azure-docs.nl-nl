---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met DocuSign | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en DocuSign configureert.
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
ms.openlocfilehash: 00d4381c7af7fdf82ee1e895072d92d1e641f8c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454691"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met DocuSign

In deze zelfstudie leert u hoe u DocuSign kunt integreren met Microsoft Azure Active Directory (Azure AD). Wanneer u DocuSign integreert met Azure AD, kunt u:

* Azure AD gebruiken om te bepalen wie toegang heeft tot DocuSign.
* Automatisch aanmelden bij DocuSign inschakelen voor uw gebruikers via hun Azure AD-account.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een DocuSign-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen om het volgende te controleren:

* DocuSign ondersteunt eenmalige aanmelding die door de serviceprovider **SP** is gestart.

* DocuSign biedt ondersteuning voor het **just-in-time** inrichten van gebruikers.

* DocuSign biedt ondersteuning voor het [automatisch inrichten van gebruikers](./docusign-provisioning-tutorial.md).

* Zodra u DocuSign hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>DocuSign toevoegen vanuit de galerie

Voor het configureren van de integratie van DocuSign in Azure Active Directory, moet u DocuSign uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **DocuSign** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **DocuSign** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>Eenmalige aanmelding van Azure AD voor DocuSign configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met DocuSign met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in DocuSign.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met DocuSign te configureren en testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Configureer eenmalige aanmelding bij DocuSign](#configure-docusign-sso) om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. [Maak een DocuSign-testgebruiker](#create-docusign-test-user) om in DocuSign een tegenhanger van B.Simon te genereren die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Eenmalige aanmelding testen](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal:

1. Zoek in de Azure Portal op de integratiepagina van de toepassing **DocuSign** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het penpictogram voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer deze stappen uit in het gedeelte **Standaard SAML-configuratie**:

    a. Voer in het tekstvak **Aanmeldings-URL** een URL in met het volgende patroon:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Voer in het tekstvak **Id (Entiteits-id)** een URL in met het volgende patroon:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. Voer in het tekstvak **Antwoord-URL** een URL in met een van de volgende patronen:
    
    | Antwoord-URL |
    |-------------|
    |`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>`|
    |`https://<subdomain>.docusign.net/SAML/`|

    > [!NOTE]
    > Deze waarden tussen punthaken zijn tijdelijke aanduidingen. Vervang ze door de waarden in de werkelijke aanmeldings-URL, id en Antwoord-URL. Deze details worden uitgelegd in de sectie 'SAML 2.0-eindpunten bekijken' verderop in deze zelfstudie.

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en zoek in de sectie **SAML-handtekeningcertificaat** de optie **Certificaat (Base64)** . Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Set up DocuSign** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer bovenaan het scherm **Nieuwe gebruiker**.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Typ **B.Simon** in het veld **Naam**.  
   1. Voer `<username>@<companydomain>.<extension>` in in het veld **Gebruikersnaam**. Bijvoorbeeld: `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u B. Simon toegang tot DocuSign, zodat deze gebruiker de eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **DocuSign** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de naam **B.Simon** in de lijst **Gebruikers** en klik op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-docusign-sso"></a>DocuSign SSO configureren

1. Als u de configuratie in DocuSign wilt automatiseren, moet u de browseruitbreiding My Apps Secure Sign-in installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, selecteert u **Setup DocuSign**. U wordt omgeleid naar de DocuSign-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij DocuSign. In de browserextensie wordt de toepassing automatisch geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u DocuSign handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich bij uw DocuSign-bedrijfssite aan als beheerder.

4. Selecteer in de rechterbovenhoek van de pagina het profiellogo en selecteer vervolgens **Go to Admin** (Ga naar Beheer).
  
    ![Go to Admin onder profiel][51]

5. Selecteer **Domains** (Domeinen) op de pagina met domeinoplossingen.

    ![Domain Solutions/Domains][50]

6. Selecteer in de sectie **Domains** (Domeinen) **CLAIM DOMAIN** (Domein claimen).

    ![De optie Claim Domain][52]

7. Typ in het dialoogvenster **Claim a Domain** (Een domein claimen) in het vak **Domain Name** (Domeinnaam) de domeinnaam van uw bedrijf en selecteer **CLAIM**. Zorg ervoor dat u verifieert of het domein bestaat en of de status actief is.

    ![Dialoogvenster Claim a Domain/Domain Name][53]

8. Selecteer op de pagina met domeinoplossingen **Identity Providers** (Id-providers).
  
    ![De optie Identity Providers][54]

9. Selecteer in de sectie **Identity Providers** **ADD IDENTITY PROVIDER** (Id-provider toevoegen).

    ![Optie voor het toevoegen van een id-provider][55]

10. Voer de volgende stappen uit op de pagina **Identity Provider Settings** (id-providerinstellingen):

    ![Velden van Identity Provider Settings][56]

    a. Typ in het tekstvak **Name** (Naam) een unieke naam voor de configuratie. Gebruik geen spaties.

    b. Plak in het vak **Identity Provider Issuer** (Verlener id-provider) de waarde van de **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het vak **Identity Provider Login URL** (Aanmeldings-URL id-provider) de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het vak **Identity Provider Logout URL** (Aanmeldings-URL id-provider) de waarde van de **URL voor afmelden** die u hebt gekopieerd uit de Azure-portal.

    e. Selecteer **Sign AuthN request** (Verificatieaanvraag ondertekenen).

    f. Selecteer **POST** bij **Send AuthN request by** (Verificatieaanvraag verzenden per).

    g. Selecteer **GET** bij **Send AuthN request by** (Afmeldingsaanvraag verzenden per).

    h. Selecteer **ADD NEW MAPPING** (Nieuwe toewijzing toevoegen) in de sectie **Custom Attribute Mapping** (Aangepaste attribuuttoewijzing).

       ![Gebruikersinterface voor aangepaste attribuuttoewijzing][62]

    i. Kies het veld dat u wilt toewijzen aan de Azure AD-claim. In dit voorbeeld wordt de claim **emailaddress** toegewezen aan de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`. Dit is de standaardclaimnaam van Azure AD voor de e-mail-claim. Selecteer **SAVE** (Opslaan).

       ![Velden voor aangepaste attribuuttoewijzing][57]

       > [!NOTE]
       > Gebruik de juiste **Gebruikers-id** om de gebruiker van Azure AD toe te wijzen aan de DocuSign-gebruikerstoewijzing. Selecteer het juiste veld en voer de juiste waarde in op basis van de instellingen van uw organisatie.

    j. Selecteer in de sectie **Identity Provider Certificates** (id-providercerticificaten) **ADD CERTIFICATE** (Certificaat toevoegen), upload het certificaat dat u hebt gedownload uit de Azure AD-portal en selecteer **SAVE** (Opslaan).

       ![Id-providercertificaten/Certificaat toevoegen][58]

    k. Selecteer in de sectie **Identity Providers** (Id-providers) **ACTIONS** (Acties) en vervolgens **Endpoints** (Eindpunten).

       ![Id-providers/Eindpunten][59]

    l. Voer in de sectie **SAML 2.0-eindpunten bekijken** van de beheerportal van DocuSign de volgende stappen uit:

       ![SAML 2.0-eindpunten bekijken][60]
       
       1. Kopieer de **Service Provider Issuer URL** (URL serviceprovideruitgever) en plak deze in het vak **Id** in de sectie **Basis-SAML-configuratie** in de Azure-portal.
       
       1. Kopieer de **Service Provider Assertion Consumer Service URL** (Serviceprovider-URL voor Assertion Consumer Service) en plak deze in het vak **Antwoord-URL** in de sectie **Basis-SAML-configuratie** in de Azure-portal.
       
       1. Kopieer de **Service Provider Login URL** (Aanmeldings-URL serviceprovideruitgever) en plak deze in het vak **Aanmeldings-URL** in de sectie **Basis-SAML-configuratie** in de Azure-portal. Aan het einde van de **Service Provider Login URL** krijgt u de IDPID-waarde.

       1. Selecteer **Sluiten**.

### <a name="create-docusign-test-user"></a>DocuSign-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in DocuSign. DocuSign biedt ondersteuning voor just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in DocuSign bestaat, wordt er een nieuwe gemaakt na authenticatie.

> [!Note]
> Neem contact op met het [ondersteuningsteam van DocuSign](https://support.docusign.com/) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van DocuSign, waar u de aanmeldingsstroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van DocuSign en initieer de aanmeldingsstroom daar.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in het toegangsvenster op de tegel DocuSign klikt, wordt u automatisch aangemeld bij de instantie van DocuSign waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u DocuSign hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png