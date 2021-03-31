---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met ServiceNow | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ServiceNow.
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
ms.openlocfilehash: c90234249f3cf7eb6ed4793110d61e1f8190ed60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092598"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met ServiceNow

In deze zelfstudie leert u hoe u ServiceNow kunt integreren met Azure Active Directory (Azure AD). Wanneer u ServiceNow integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot ServiceNow.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij ServiceNow.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Jao6]

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op ServiceNow waarvoor eenmalige aanmelding (SSO) is ingeschakeld.
* Voor ServiceNow ondersteunt een instantie of tenant van ServiceNow Calgary, Kingston, Londen, Madrid, New York, Orlando en Parijs of hoger.
* Voor ServiceNow Express: een instantie van ServiceNow Express, versie Helsinki of later.
* Voor de ServiceNow-tenant moet de [invoegtoepassing Multiple-Provider single sign-on (SSO)](https://old.wiki/index.php/Multiple_Provider_Single_Sign-On#gsc.tab=0) zijn ingeschakeld.
* Schakel voor automatische configuratie de multi-provider-invoegtoepassing voor ServiceNow in.
* Om de toepassing ServiceNow Classic (Mobile) te installeren, gaat u naar de toepasselijke Store en zoekt u naar de ServiceNow Classic-toepassing. Vervolgens downloadt u de toepassing.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 

* ServiceNow ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO).

* ServiceNow ondersteunt het [geautomatiseerd inrichten van gebruikers](servicenow-provisioning-tutorial.md).

* U kunt de toepassing ServiceNow Classic (Mobile) configureren met Azure AD voor het inschakelen van eenmalige aanmelding. Het ondersteunt zowel Android- als iOS-gebruikers. In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

## <a name="add-servicenow-from-the-gallery"></a>ServiceNow toevoegen vanuit de galerie

Voor het configureren van de integratie van ServiceNow in Azure AD moet u ServiceNow uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Ga in de sectie **Toevoegen uit de galerie** naar het zoekvak en voer **ServiceNow** in.
1. Selecteer **ServiceNow** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>Eenmalige aanmelding van Azure AD voor ServiceNow configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met ServiceNow met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ServiceNow.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij ServiceNow te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
    1. [Configureer eenmalige aanmelding van Azure AD voor ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. [Configureer ServiceNow](#configure-servicenow) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. [Maak een testgebruiker in ServiceNow](#create-servicenow-test-user) om in ServiceNow een tegenhanger van B.Simon te hebben, die is gekoppeld aan de Azure AD-weergave van de gebruiker.
    1. [Configureer eenmalige aanmelding voor ServiceNow Express](#configure-servicenow-express-sso) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.  
3. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.
4. [Test eenmalige aanmelding voor ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **ServiceNow** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het penpictogram voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van de pagina Eenmalige aanmelding instellen met SAML, met het penpictogram gemarkeerd](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Voer bij **Aanmeldings-URL** een URL in die het volgende patroon heeft: `https://<instancename>.service-now.com/navpage.do`

    b. Voer bij **Id (Entiteits-id)** een URL in die het volgende patroon heeft: `https://<instance-name>.service-now.com`

    c. Voer bij **Antwoord-URL** een van de volgende URL-patronen in:

    | Antwoord-URL|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. Voer bij **Afmeldings-URL** een URL in die het volgende patroon heeft: `https://<instancename>.service-now.com/navpage.do`

    > [!NOTE]
    > Als '/' wordt toegevoegd in de id-waarde, moet u die handmatig verwijderen.

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de werkelijke aanmeldings-URL, antwoord-URL, afmeldings-URL en id, zoals later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en zoek in de sectie **SAML-handtekeningcertificaat** de optie **Certificaat (Base64)** . 

   ![Schermopname van de sectie SAML-handtekeningcertificaat, met Downloaden gemarkeerd](common/certificatebase64.png)

   a. Selecteer de knop Kopiëren om de **App-URL voor federatieve metagegevens** te kopiëren, en plak deze in Kladblok. Deze URL wordt verderop in de zelfstudie gebruikt.

    b. Selecteer **Downloaden** om het **Certificaat (Base64)** te downloaden, en sla het certificaatbestand vervolgens op uw computer op.

1. Kopieer in de sectie **ServiceNow instellen** de juiste URL's, op basis van uw vereiste.

   ![Schermopname van de sectie ServiceNow instellen, met URL’s gemarkeerd](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker, genaamd B.Simon, maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer `B.Simon` in bij **Naam**.  
   1. Voer bij **Gebruikersnaam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **Wachtwoord weergeven** en noteer de waarde die in het vak **Wachtwoord** wordt getoond.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ServiceNow.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **ServiceNow** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst met gebruikers, en kies vervolgens **Selecteren**.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Eenmalige aanmelding van Azure AD configureren voor ServiceNow Express

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ServiceNow** de optie **Eenmalige aanmelding**.

    ![Schermopname van de integratiepagina van de toepassing ServiceNow, met Eenmalige aanmelding gemarkeerd](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![Schermopname van Selecteer een methode voor eenmalige aanmelding, met SAML gemarkeerd](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het penpictogram om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Schermopname van de pagina Eenmalige aanmelding instellen met SAML, met het penpictogram gemarkeerd](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Voer bij **Aanmeldings-URL** een URL in die het volgende patroon heeft: `https://<instancename>.service-now.com/navpage.do`

    b. Voer bij **Id (Entiteits-id)** een URL in die het volgende patroon heeft: `https://<instance-name>.service-now.com`

    c. Voer bij **Antwoord-URL** een van de volgende URL’s in:

    | Antwoord-URL |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. Voer bij **Afmeldings-URL** een URL in die het volgende patroon heeft: `https://<instancename>.service-now.com/navpage.do`
    
    > [!NOTE]
    > Als '/' wordt toegevoegd in de id-waarde, moet u die handmatig verwijderen.

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de werkelijke aanmeldings-URL, antwoord-URL, afmeldings-URL en id, zoals later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** selecteert u **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties, overeenkomstig uw behoeften. Sla het op uw computer op.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met Downloaden gemarkeerd](common/certificatebase64.png)

6. Azure AD kan ServiceNow automatisch configureren voor verificatie op basis van SAML. Om deze service in te schakelen, gaat u naar de sectie **ServiceNow instellen** en selecteert u **Stapsgewijze instructies weergeven** om het venster **Aanmelding configureren** weer te geven.

    ![Schermopname van de sectie ServiceNow instellen, met Stapsgewijze instructies weergeven gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. Voer in het formulier **Aanmelding configureren** de naam van uw ServiceNow-instantie, de gebruikersnaam van de beheerder en het wachtwoord van de beheerder in. Selecteer **Nu configureren**. De opgegeven gebruikersnaam van de beheerder moet de rol **security_admin** hebben in ServiceNow om dit te laten werken. Anders selecteert u **Eenmalige aanmelding handmatig configureren** om ServiceNow handmatig te configureren om Azure AD als een SAML-identiteitsprovider te gebruiken. Kopieer de **Afmeldings-URL, Azure AD-id en Aanmeldings-URL** in de sectie Snelzoekgids.

    ![Schermopname van het formulier Aanmelding configureren, met Nu configureren gemarkeerd](./media/servicenow-tutorial/configure.png "App-URL configureren")

## <a name="configure-servicenow"></a>ServiceNow configureren

1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

1. Activeer de invoegtoepassing **Integration - Multiple Provider Single Sign-On Installer** door deze stappen te volgen:

    a. Gebruik het zoekvak in het linkerdeelvenster om de sectie **System Definition** op te zoeken, en selecteer vervolgens **Plugins**.

    ![Schermopname van de sectie System Definition, met System Definition en Plugins gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-03.png "Invoegtoepassing activeren")

    b. Zoek naar **Integration - Multiple Provider Single Sign-On Installer**.

     ![Schermopname van de pagina System Plugins, met Integration - Multiple Provider Single Sign-On Installer gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-04.png "Invoegtoepassing activeren")

    c. Selecteer de invoegtoepassing. Klik met de rechtermuisknop en selecteer **Activate/Upgrade**.

     ![Schermopname van het snelmenu van de invoegtoepassing, met Activate/Upgrade gemarkeerd](./media/servicenow-tutorial/tutorial-activate.png "Invoegtoepassing activeren")

    d. Selecteer **Activate**.

     ![Schermopname van het dialoogvenster Activate Plugin, met Activate gemarkeerd](./media/servicenow-tutorial/tutorial-activate-1.png "Invoegtoepassing activeren")

1. Gebruik de zoekbalk in het linkerdeelvenster om de sectie **Multi-Provider SSO** op te zoeken, en selecteer vervolgens **Properties**.

    ![Schermopname van de sectie Multi-Provider SSO, met Multi-Provider SSO en Properties gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-06.png "App-URL configureren")

1. Voer in het dialoogvenster **Multiple Provider SSO Properties** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Multiple Provider SSO Properties](./media/servicenow-tutorial/ic7694981.png "App-URL configureren")

    * Bij **Enable multiple provider SSO** selecteert u **Yes**.
  
    * Bij **Enable Auto Importing of users from all identity providers into the user table** selecteert u **Yes**.

    * Bij **Enable debug logging for the multiple provider SSO integration** selecteert u **Yes**.

    * Bij **The field on the user table that...** voert u **email** in.
  
    * Selecteer **Opslaan**.

1. U kunt ServiceNow automatisch of handmatig configureren. Volg deze stappen om ServiceNow automatisch te configureren:

    1. Ga terug naar de eenmalige aanmeldingspagina van **ServiceNow** in de Azure-portal.

    1. Er wordt een service voor configuratie met één klik geboden voor ServiceNow. Om deze service in te schakelen, gaat u naar de sectie **ServiceNow-configuratie** en selecteert u **ServiceNow configureren** om het venster **Aanmelding configureren** te openen.

        ![Schermopname van ServiceNow instellen, met Stapsgewijze instructies weergeven gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. Voer in het formulier **Aanmelding configureren** de naam van uw ServiceNow-instantie, de gebruikersnaam van de beheerder en het wachtwoord van de beheerder in. Selecteer **Nu configureren**. De opgegeven gebruikersnaam van de beheerder moet de rol **security-admin** hebben in ServiceNow om dit te laten werken. Anders selecteert u **Eenmalige aanmelding handmatig configureren** om ServiceNow handmatig te configureren om Azure AD als een SAML-identiteitsprovider te gebruiken. Kopieer de **Afmeldings-URL, SAML-entiteit-id en SAML-service-URL voor eenmalige aanmelding** in de sectie Snelzoekgids.

        ![Schermopname van het formulier Aanmelding configureren, met Nu configureren gemarkeerd](./media/servicenow-tutorial/configure.png "App-URL configureren")

    1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

       * Bij de automatische configuratie worden alle benodigde instellingen aan de kant van **ServiceNow** geconfigureerd, maar het **X.509-certificaat** wordt standaard niet ingeschakeld en de waarde voor **Script voor eenmalige aanmelding** wordt gegeven als **MultiSSOv2_SAML2_custom**. U moet het handmatig toewijzen aan uw identiteitsprovider in ServiceNow. Volg deze stappen:

         1. Gebruik het zoekvak in het linkerdeelvenster om de sectie **Multi-Provider SSO** op te zoeken, en selecteer **Identity Providers**.

            ![Schermopname van de sectie Multi-Provider SSO, met Identity Providers gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-07.png "Eenmalige aanmelding configureren")

         1. Selecteer de automatisch gegenereerde identiteitsprovider.

            ![Schermopname van identiteitsproviders, met automatisch gegenereerde identiteitsprovider gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-08.png "Eenmalige aanmelding configureren")

         1.  Voer in de sectie **Identity Provider** de volgende stappen uit:

             ![Schermopname van de sectie Identity Provider](./media/servicenow-tutorial/automatic-config.png "Eenmalige aanmelding configureren")

               a. Typ bij **Name** een naam voor uw configuratie (bijvoorbeeld **Microsoft Azure Federated Single Sign-on**).

               b. Kopieer de waarde van **ServiceNow Homepage** en plak deze in **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie voor ServiceNow** van de Azure-portal.

                > [!NOTE]
                > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld:`https://fabrikam.service-now.com/navpage.do`).

              c. Kopieer de waarde van **Entity ID / Issuer** en plak deze in **Id** in de sectie **Standaard SAML-configuratie voor ServiceNow** van de Azure-portal.

              d. Bevestig dat **NameID Policy** op de waarde `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` is ingesteld. 

              e. Klik op **Geavanceerd** en geef de waarde voor **Script voor eenmalige aanmelding** op als **MultiSSOv2_SAML2_custom**.

         1. Schuif omlaag naar de sectie **X.509 Certificate** en selecteer **Edit**.

             ![Schermopname van de sectie X.509 Certificate, met Edit gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-09.png "Eenmalige aanmelding configureren")

         1. Selecteer het certificaat en selecteer vervolgens de pijl naar rechts om het certificaat toe te voegen

            ![Schermopname van Collection, met certificaat en pijl naar rechts gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-11.png "Eenmalige aanmelding configureren")

          1. Selecteer **Opslaan**.

          1. Selecteer **Test Connection** in de rechterbovenhoek van de pagina.

             ![Schermopname van de pagina, met Test Connection gemarkeerd](./media/servicenow-tutorial/tutorial-activate-2.png "Invoegtoepassing activeren")

             > [!NOTE]
             > Als de Test Connection mislukt en u deze verbinding niet kunt activeren, biedt ServiceNow de overschrijvingsschakelaar. U moet **Sys_properties.LIST** invoeren in de **zoeknavigatie**, waarna de nieuwe pagina van System Properties wordt geopend. Hier moet u een nieuwe eigenschap maken waarbij u **glide.authenticate.multisso.test.connection.mandatory** als naam opgeeft, **datatype** op **True/False** instelt en **value** op **False** instelt.

             > ![Schermopname van de pagina Verbinding testen](./media/servicenow-tutorial/test-connection-fail.png "Eenmalige aanmelding configureren")
        
          1. Voer uw referenties in wanneer u daarom wordt gevraagd. U ziet de volgende pagina. De foutmelding **SSO Logout Test Results** is verwacht. Negeer deze fout en selecteer **Activate**.

             ![Schermopname van de pagina met aanmeldingsgegevens](./media/servicenow-tutorial/servicenow-activate.png "Eenmalige aanmelding configureren")
  
1. Volg deze stappen om **ServiceNow** handmatig te configureren:

    1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

    1. Selecteer **Identity Providers** in het linkerdeelvenster.

        ![Schermopname van Multi-Provider SSO, met Identity Providers gemarkeerd](./media/servicenow-tutorial/tutorial-servicenow-07.png "Eenmalige aanmelding configureren")

    1. Selecteer in het dialoogvenster **Identity Providers** de optie **New**.

        ![Schermopname van het dialoogvenster Identity Providers, met New gemarkeerd](./media/servicenow-tutorial/ic7694977.png "Eenmalige aanmelding configureren")

    1. Selecteer in het dialoogvenster **Identity Providers** de optie **SAML**.

        ![Schermopname van het dialoogvenster Identity Providers, met SAML gemarkeerd](./media/servicenow-tutorial/ic7694978.png "Eenmalige aanmelding configureren")

    1. Voer in **Import Identity Provider Metadata** de volgende stappen uit:

        ![Schermopname van Import Identity Provider Metadata, met URL en Import gemarkeerd](./media/servicenow-tutorial/idp.png "Eenmalige aanmelding configureren")

        1. Voer de **App-URL voor federatieve metagegevens** in die u hebt gekopieerd uit de Azure-portal.

        1. Selecteer **Importeren**.

    1. De URL met de IdP-metagegevens wordt gelezen, en de overeenkomende velden worden ingevuld.

        ![Schermopname van Identity Provider](./media/servicenow-tutorial/ic7694982.png "Eenmalige aanmelding configureren")

        a. Typ bij **Name** een naam voor uw configuratie (bijvoorbeeld **Microsoft Azure Federated Single Sign-on**).

        b. Kopieer de waarde van **ServiceNow Homepage**. Plak deze in **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie voor ServiceNow** van de Azure-portal.

        > [!NOTE]
        > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld:`https://fabrikam.service-now.com/navpage.do`).

        c. Kopieer de waarde van **Entity ID / Issuer**. Plak deze in **Id** in de sectie **Standaard SAML-configuratie voor ServiceNow** van de Azure-portal.

        d. Bevestig dat **NameID Policy** op de waarde `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` is ingesteld.

        e. Selecteer **Geavanceerd**. Typ **email** bij **User Field**.

        > [!NOTE]
        > U kunt Azure AD configureren om de Azure AD-gebruikers-id (User Principal Name) of het e-mailadres te verzenden als de unieke id in het SAML-token. U kunt dit doen door naar de sectie **ServiceNow** > **Kenmerken** > **Eenmalige aanmelding** van de Azure-portal te gaan en het gewenste veld toe te wijzen aan het kenmerk **nameidentifier**. De waarde die voor het geselecteerde kenmerk wordt opgeslagen in Azure AD (bijvoorbeeld de User Principal Name) moet overeenkomen met de waarde die is opgeslagen in ServiceNow voor het ingevoerde veld (bijvoorbeeld user_name).

        g. Selecteer **Test Connection** in de rechterbovenhoek van de pagina.

        > [!NOTE]
        > Als de Test Connection mislukt en u deze verbinding niet kunt activeren, biedt ServiceNow de overschrijvingsschakelaar. U moet **Sys_properties.LIST** invoeren in de **zoeknavigatie**, waarna de nieuwe pagina van System Properties wordt geopend. Hier moet u een nieuwe eigenschap maken waarbij u **glide.authenticate.multisso.test.connection.mandatory** als naam opgeeft, **datatype** op **True/False** instelt en **value** op **False** instelt.

          > ![Schermopname van Verbinding testen](./media/servicenow-tutorial/test-connection-fail.png "Eenmalige aanmelding configureren")

        h. Voer uw referenties in wanneer u daarom wordt gevraagd. U ziet de volgende pagina. De foutmelding **SSO Logout Test Results** is verwacht. Negeer deze fout en selecteer **Activate**.

          ![aanmeldingsgegevens](./media/servicenow-tutorial/servicenow-activate.png "Eenmalige aanmelding configureren")

### <a name="create-servicenow-test-user"></a>Een testgebruiker maken in ServiceNow

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in ServiceNow. ServiceNow ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, neemt u contact op met het [ondersteuningsteam van ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="configure-servicenow-express-sso"></a>Eenmalige aanmelding voor ServiceNow Express configureren

1. Meld u aan bij uw ServiceNow Express-toepassing als beheerder.

2. Selecteer **Single Sign-On** in het linkerdeelvenster.

    ![Schermopname van de toepassing ServiceNow Express, met Single Sign-On gemarkeerd](./media/servicenow-tutorial/ic7694980ex.png "App-URL configureren")

3. Selecteer in het dialoogvenster **Single Sign-On** het configuratiepictogram rechtsboven, en stel de volgende eigenschappen in:

    ![Schermopname van het dialoogvenster Single Sign-On](./media/servicenow-tutorial/ic7694981ex.png "App-URL configureren")

    a. Schuif de schakelaar **Enable multiple provider SSO** naar rechts.

    b. Schuif de schakelaar **Enable debug logging for the multiple provider SSO integration** naar rechts.

    c. Bij **The field on the user table that...** voert u **user_name** in.

4. Selecteer in het dialoogvenster **Single Sign-On** de optie **Add New Certificate**.

    ![Schermopname van het dialoogvenster Single Sign-On, met Add New Certificate gemarkeerd](./media/servicenow-tutorial/ic7694973ex.png "Eenmalige aanmelding configureren")

5. Voer in het dialoogvenster **X.509 Certificates** de volgende stappen uit:

    ![Schermopname van het dialoogvenster X.509 Certificates](./media/servicenow-tutorial/ic7694975.png "Eenmalige aanmelding configureren")

    a. Typ bij **Name** een naam voor de configuratie (bijvoorbeeld: **TestSAML2.0**).

    b. Selecteer **Active**.

    c. Bij **Format** selecteert u **PEM**.

    d. Bij **Type** selecteert u **Trust Store Cert**.

    e. Open het Base64-gecodeerde certificaat dat u in de Azure-portal hebt gedownload, in Kladblok. Kopieer de inhoud ervan naar het klembord en plak deze in het tekstvak **PEM Certificate**.

    f. Selecteer **Update**.

6. Selecteer in het dialoogvenster **Single Sign-On** de optie **Add New IdP**.

    ![Schermopname van het dialoogvenster Single Sign-On, met Add New IdP gemarkeerd](./media/servicenow-tutorial/ic7694976ex.png "Eenmalige aanmelding configureren")

7. Voer in het dialoogvenster **Add New Identity Provider** onder **Configure Identity Provider** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Add New Identity Provider](./media/servicenow-tutorial/ic7694982ex.png "Eenmalige aanmelding configureren")

    a. Typ bij **Name** een naam voor de configuratie (bijvoorbeeld: **SAML 2.0**).

    b. Plak bij **Identity Provider URL** de waarde van de identiteitsprovider-id die u hebt gekopieerd uit de Azure-portal.

    c. Plak bij **Identity Provider's AuthnRequest** de waarde van de verificatieaanvraag-URL die u hebt gekopieerd uit de Azure-portal.

    d. Plak bij **Identity Provider's SingleLogoutRequest** de waarde van de afmeldings-URL die u hebt gekopieerd uit de Azure-portal.

    e. Selecteer bij **Identity Provider Certificate** het certificaat dat u in de vorige stap hebt gemaakt.

8. Selecteer **Advanced Settings**. Voer onder **Additional Identity Provider Properties** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Add New Identity Provider, met Advanced Settings gemarkeerd](./media/servicenow-tutorial/ic7694983ex.png "Eenmalige aanmelding configureren")

    a. Bij **Protocol Binding for the IDP's SingleLogoutRequest** typt u **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Bij **NameID Policy** typt u **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. Bij **AuthnContextClassRef Method** typt u `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Zet **Create an AuthnContextClass** uit (niet-geselecteerd).

9. Voer onder **Additional Service Provider Properties** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Add New Identity Provider, met verschillende eigenschappen gemarkeerd](./media/servicenow-tutorial/ic7694984ex.png "Eenmalige aanmelding configureren")

    a. Bij **ServiceNow Homepage** typt u de URL van de startpagina van uw ServiceNow-instantie.

    > [!NOTE]
    > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld: `https://fabrikam.service-now.com/navpage.do`).

    b. Bij **Entity ID / Issuer** typt u de URL van uw ServiceNow-tenant.

    c. Bij **Audience URI** typt u de URL van uw ServiceNow-tenant.

    d. Bij **Clock Skew** typt u **60**.

    e. Bij **User Field** typt u **email**.

    > [!NOTE]
    > U kunt Azure AD configureren om de Azure AD-gebruikers-id (User Principal Name) of het e-mailadres te verzenden als de unieke id in het SAML-token. U kunt dit doen door naar de sectie **ServiceNow** > **Kenmerken** > **Eenmalige aanmelding** van de Azure-portal te gaan en het gewenste veld toe te wijzen aan het kenmerk **nameidentifier**. De waarde die voor het geselecteerde kenmerk wordt opgeslagen in Azure AD (bijvoorbeeld de User Principal Name) moet overeenkomen met de waarde die is opgeslagen in ServiceNow voor het ingevoerde veld (bijvoorbeeld user_name).

    f. Selecteer **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel ServiceNow in het toegangsvenster selecteert, zou u automatisch moeten worden aangemeld bij de ServiceNow-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-servicenow-classic-mobile"></a>Eenmalige aanmelding testen voor ServiceNow Classic (Mobile)

1. Open uw toepassing **ServiceNow Classic (Mobile)** en voer de volgende stappen uit:

    a. Selecteer het plusteken rechtsonder.

    ![Schermopname van de toepassing ServiceNow Classic, met het plusteken gemarkeerd](./media/servicenow-tutorial/test-03.png)

    b. Voer de naam van uw ServiceNow-instantie in en selecteer **Continue**.

    ![Schermopname van de pagina Add Instance, met Continue gemarkeerd](./media/servicenow-tutorial/test-04.png)

    c. Voer op de pagina **Log in** de volgende stappen uit:

    ![Schermopname van de pagina Log in, met Use external login gemarkeerd](./media/servicenow-tutorial/test-01.png)

    *  Voer een **Username** in, zoals B.simon@contoso.com.

    *  Selecteer **USE EXTERNAL LOGIN**. U wordt omgeleid naar de Azure AD-pagina om u aan te melden.

    *  Voer uw referenties in. Als er externe verificatie of een andere beveiligingsfunctie is ingeschakeld, moet de gebruiker dienovereenkomstig reageren. De **startpagina** van de toepassing wordt weergegeven.

        ![Schermopname van de startpagina van de toepassing](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>Volgende stappen

Zodra u ServiceNow hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
