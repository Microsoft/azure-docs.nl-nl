---
title: 'Zelfstudie: Azure Active Directory-integratie met TOPdesk - Secure | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TOPdesk - Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 93b4030101ab273182a8f9207bc40aa46dbb11c3
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622340"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Zelfstudie: Azure Active Directory-integratie met TOPdesk - Secure

In deze zelf studie leert u hoe u TOPdesk-Secure integreert met Azure Active Directory (Azure AD). Wanneer u TOPdesk-Secure integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot TOPdesk-Secure.
* Stel uw gebruikers in staat automatisch zich aan te melden bij TOPdesk-Secure (eenmalige aanmelding) met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:
 
 * Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een TOPdesk-beveiligd abonnement voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TOPdesk - Secure ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-topdesk---secure-from-the-gallery"></a>TOPdesk-beveiligd toevoegen vanuit de galerie

Voor het configureren van de integratie van TOPdesk - Secure in Azure AD moet u TOPdesk - Secure vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **TopDesk-Secure** in het zoekvak.
1. Selecteer **TopDesk** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Azure AD SSO configureren en testen voor TOPdesk-Secure

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met TOPdesk - Secure op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in TOPdesk - Secure tot stand is gebracht.

U moet de volgende stappen uitvoeren om eenmalige aanmelding voor Azure AD te configureren en te testen met TOPdesk-Secure:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    2. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Configureer TopDesk-Secure SSO](#configure-topdesk---secure-sso)** -om de instellingen voor één Sign-On te configureren aan de kant van de toepassing.
    1. **[Testgebruiker voor TOPdesk - Secure maken](#create-topdesk---secure-test-user)**: als u een tegenhanger van Britta Simon in TOPdesk - Secure wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding met TOPdesk - Secure te configureren:

1. Selecteer in de Azure Portal op de pagina **TopDesk-beveiligde** toepassings integratie de optie **eenmalige aanmelding**.

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het potloodpictogram om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.topdesk.net`

    b. Vul in het tekstvak **Id-URL** de URL voor de metagegevens van QPrism in. Deze kunt u ophalen uit de configuratie van QPrism. Gebruik hiervoor het volgende patroon: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam voor TOPdesk - Secure](https://www.topdesk.com/us/support/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **TOPdesk - Secure instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan TOPdesk-Secure.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **TopDesk-Secure**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-topdesk---secure-sso"></a>TOPdesk-Secure SSO configureren

1. Meld u aan bij de bedrijfssite van **TOPdesk - Secure** als een beheerder.

2. Klik in het menu van **TOPdesk** op **Settings**.

    ![Instellingen](./media/topdesk-secure-tutorial/ic790598.png "Instellingen")

3. Klik op **Login Settings**.

    ![Aanmeldingsinstellingen](./media/topdesk-secure-tutorial/ic790599.png "Aanmeldingsinstellingen")

4. Vouw het menu **Login Settings** uit en klik op **General**.

    ![Algemeen](./media/topdesk-secure-tutorial/ic790600.png "Algemeen")

5. Voer de volgende stappen uit in de sectie **Secure** van de configuratiesectie **SAML login**:

    ![Technische instellingen](./media/topdesk-secure-tutorial/ic790855.png "Technische instellingen")

    a. Klik op **Downloaden** om het openbare metagegevensbestand te downloaden en sla het lokaal op uw computer op.

    b. Open het metagegevensbestand en zoek het knooppunt **AssertionConsumerService**.

    ![Assertion Consumer Service](./media/topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")

    c. Kopieer de waarde **AssertionConsumerService**, plak deze in het testvak Antwoord-URL in de sectie **Domein en URL's voor TOPdesk - Secure**.

6. Als u een certificaatbestand wilt maken, moet u de volgende stappen uitvoeren:

    ![Certificaat](./media/topdesk-secure-tutorial/ic790606.png "Certificaat")

    a. Open het gedownloade metagegevensbestand vanuit de Azure-portal.

    b. Vouw het knooppunt **RoleDescriptor** uit waarvan **xsi:type** is ingesteld op **fed:ApplicationServiceType**.

    c. Kopieer de waarde van het knooppunt **X509Certificate**.

    d. Sla de gekopieerde waarde van **X509Certificate** lokaal op uw computer op in een bestand.

7. Klik in de sectie **Public** op **Add**.

    ![Add](./media/topdesk-secure-tutorial/ic790607.png "Toevoegen")

8. Voer de volgende stappen uit in het dialoogvenster **SAML configuration assistant**:

    ![SAML-configuratie-assistent](./media/topdesk-secure-tutorial/ic790608.png "SAML-configuratie-assistent")

    a. Klik onder **Federatieve metagegevens** op **Bladeren** om het gedownloade metagegevensbestand te uploaden vanuit de Azure-portal.

    b. Klik onder **Certificaat (RSA)** op **Bladeren** om het certificaatbestand te uploaden.

    c. Bij **Persoonlijke sleutel (RSA, PKCS8, DER)** kunt u uw eigen persoonlijke sleutel uploaden of u kunt contact opnemen met het [klantondersteuningsteam voor TOPdesk - Secure](https://www.topdesk.com/us/support) om de persoonlijke sleutel op te halen.

    d. Klik onder het **Logo-pictogram** op **Bladeren** om het logobestand dat u van het TOPdesk-ondersteuningsteam hebt verkregen, te uploaden.

    e. In het tekstvak voor het **gebruikersnaamkenmerk** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Typ in het tekstvak **Weergavenaam** een naam voor de configuratie.

    g. Klik op **Opslaan**.

### <a name="create-topdesk---secure-test-user"></a>Testgebruiker voor TOPdesk - Secure maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij TOPdesk - Secure, moet ze worden ingericht in TOPdesk - Secure.  
Het inrichten moet handmatig worden uitgevoerd in TOPdesk - Secure.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Voer de volgende stappen uit om de inrichting van gebruikers te configureren:

1. Meld u aan bij de bedrijfssite van **TOPdesk - Secure** als beheerder.

2. Klik in het menu aan de bovenkant op **TOPdesk \> New \> Support Files \> Operator**.

    ![Operator](./media/topdesk-secure-tutorial/ic790610.png "Operator")

3. Voer de volgende stappen uit in het dialoogvenster **New Operator**:

    ![Nieuwe operator](./media/topdesk-secure-tutorial/ic790611.png "Nieuwe operator")

    a. Klik op het tabblad **General**.

    b. Typ in het tekstvak **Surname** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Selecteer een **Site** voor het account in de sectie **Location**.

    d. Typ in het tekstvak **Login Name** van de sectie **TOPdesk Login** een aanmeldingsnaam voor uw gebruiker.

    e. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook Azure AD-gebruikersaccounts inrichten met alle andere door TOPdesk - Secure geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de TOPdesk-beveiligde aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor TOPdesk-beveiligde aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel TOPdesk-Secure in de mijn apps klikt, moet u automatisch worden aangemeld bij de TOPdesk-Secure waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Als u TOPdesk-Secure hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

