---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met F5 | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1290a13f23f49a99d5b9d962d93d91bc3331356d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093128"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Zelfstudie: Eenmalige aanmelding (SSO) configureren tussen Azure Active Directory en F5

In deze zelfstudie leert u hoe u F5 integreert met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot F5.
* Uw gebruikers zich met hun Azure AD-account automatisch laten aanmelden bij F5.
* Uw accounts op een centrale locatie beheren: Azure Portal.

> [!NOTE]
> F5 BIG-IP APM [Nu kopen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

* Een abonnement op F5 waarvoor eenmalige aanmelding is ingeschakeld.

* Voor de implementatie van de gezamenlijke oplossing is de volgende licentie vereist:

    * F5 BIG-IP® Best-bundel (of) 

    * Zelfstandige licentie voor F5 BIG-IP Access Policy Manager™ (APM) 

    * Invoegtoepassingslicentie voor F5 BIG-IP Access Policy Manager™ (APM) in een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).

    * Naast de bovenstaande licentie kan het F5-systeem ook in licentie worden verkregen met: 

        * Een abonnement op URL-filtering voor het gebruik van de URL-categoriedatabase

        * Een F5 IP Intelligence-abonnement voor het detecteren en blokkeren van bekende aanvallers en schadelijk verkeer
     
        * Een netwerk-HSM (Hardware Security module) voor het beveiligen en beheren van digitale sleutels voor sterke verificatie

* Het F5 BIG-IP-systeem is ingericht met APM-modules (LTM is optioneel)

* Hoewel dit optioneel is, is het raadzaam om de F5-systemen te implementeren in een [synchronisatie/failover-apparaatgroep](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), die het actieve stand-by-paar omvat, met een zwevend IP-adres voor hoge beschikbaarheid. U kunt verdere interfaceredundantie bereiken met behulp van het Link Aggregation Control Protocol (LACP). LACP beheert de verbonden fysieke interfaces als één virtuele interface (statistische groep) en detecteert interfacefouten binnen de groep.

* Voor Kerberos-toepassingen, een on-premises AD-serviceaccount voor beperkte delegatie.  Raadpleeg de [documentatie bij F5](https://support.f5.com/csp/article/K43063049) voor het maken van een AD-delegatieaccount.

## <a name="access-guided-configuration"></a>Begeleide toegangsconfiguratie

* Begeleide toegangsconfiguratie wordt ondersteund in F5 TMOS-versie 13.1.0.8 en hoger. Als op uw BIG-IP-systeem een versie wordt uitgevoerd die lager is dan 13.1.0.8, raadpleegt u de sectie **Geavanceerde configuratie**.

* Begeleide toegangsconfiguratie biedt een volledig nieuwe en gestroomlijnde gebruikerservaring. Deze architectuur op basis van een werkstroom biedt intuïtieve, herhaalde configuratiestappen die zijn afgestemd op de geselecteerde topologie.

* Voordat u doorgaat met de configuratie, moet u de begeleide configuratie bijwerken door het meest recente gebruiksscenariopakket te downloaden van [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Volg de onderstaande procedure om een upgrade uit te voeren.

    >[!NOTE]
    >De onderstaande schermopnamen zijn voor de meest recente versie (BIG-IP 15.0 met AGC versie 5.0). De onderstaande configuratiestappen zijn geldig voor dit gebruiksscenario van 13.1.0.8 tot de nieuwste BIG-IP-versie.

1. Klik in de F5 BIG-IP-webgebruikersinterface op **Access >> Guide Configuration**.

1. Klik op de pagina **Guided Configuration** op **Upgrade Guided Configuration** in de linkerbovenhoek.

    ![Schermopname van de pagina Guided Configuration met de koppeling Update Guided Configuration.](./media/headerf5-tutorial/configure14.png) 

1. Selecteer in het pop-upvenster Upgrade Guide Configuration op de optie **Choose File** om het gedownloade gebruiksscenariopakket te uploaden en klik op de knop **Upload and Install**.

    ![Schermopname van het dialoogvenster Upgrade Guided Configuration met Choose File geselecteerd.](./media/headerf5-tutorial/configure15.png) 

1. Wanneer de upgrade is voltooid, klikt u op de knop **Continue**.

    ![Schermopname van het dialoogvenster Upgrade Guided Configuration met een voltooiingsbericht.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Eenmalige aanmelding bij F5 kan op drie verschillende manieren worden geconfigureerd.

- [Eenmalige aanmelding bij F5 configureren voor op een header gebaseerde toepassing](#configure-f5-single-sign-on-for-header-based-application)

- [Eenmalige aanmelding bij F5 configureren voor een Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding bij F5 configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Belangrijkste verificatiescenario's

* Naast dat het systeemeigen integratie van Azure Active Directory voor moderne verificatieprotocollen zoals Open ID Connect, SAML en WS-Fed ondersteunt, breidt F5 beveiligde toegang uit voor op verouderde verificatietoepassingen voor zowel interne als externe toegang met Azure AD, waardoor moderne scenario's (zoals toegang zonder wachtwoord) mogelijk worden voor deze toepassingen. Dit zijn onder meer:

* Op headers gebaseerde verificatietoepassingen

* Kerberos-verificatietoepassingen

* Anonieme verificatie of apps zonder ingebouwde verificatie

* NTLM-verificatietoepassingen (beveiliging met dubbele prompts voor de gebruiker)

* Op formulieren gebaseerde toepassing (beveiliging met dubbele prompts voor de gebruiker)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 in Azure AD wilt configureren, moet u F5 vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **F5** in het zoekvak.
1. Selecteer **F5** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-f5"></a>Azure AD SSO voor F5 configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met F5 met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in F5.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met F5:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij F5 configureren](#configure-f5-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Een testgebruiker voor F5 maken](#create-f5-test-user)** : als u een tegenhanger van B.Simon in F5 wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina voor de integratie van de **F5** -toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [F5-ondersteuningsteam](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en **Certificaat (base64)** en selecteer **Downloaden** om het certificaat te downloaden. Sla het certificaat vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **F5 instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot F5.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **F5** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-f5-sso"></a>Eenmalige aanmelding bij F5 configureren

- [Eenmalige aanmelding bij F5 configureren voor een Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding bij F5 configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Eenmalige aanmelding bij F5 configureren voor een op een header gebaseerde toepassing

### <a name="guided-configuration"></a>Begeleide configuratie

1. Open een nieuw webbrowservenster en meld u als beheerder bij uw bedrijfssite van F5 (gebaseerd op header) aan en voer de volgende stappen uit:

1. Navigeer naar **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **Import** in de rechterhoek. Geef een **certificaatnaam op** (verderop in de configuratie wordt hiernaar verwezen). Selecteer bij **Certificate Source** de optie Upload File en geef het certificaat op dat u van Azure hebt gedownload tijdens het configureren van eenmalige aanmelding met SAML. Klik op **Import**.

    ![Schermopname van de lijst met S S L-certificaten waar u de certificaatnaam en -bron selecteert.](./media/headerf5-tutorial/configure12.png)
 
1. Daarnaast hebt u het **SSL-certificaat voor de hostnaam van de toepassing nodig. Navigeer naar System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **Import** in de rechterhoek. Het **Import Type** wordt **PKCS 12 (IIS)** . Geef een **Key Name** op (verderop in de configuratie wordt hiernaar verwezen) en geef het PFX-bestand op. Geef het **Password** voor de PFX op. Klik op **Import**.

    >[!NOTE]
    >In het voorbeeld is `Headerapp.superdemo.live` de naam van onze app. We gebruiken een wildcardcertificaat en onze keyname is `WildCard-SuperDemo.live`.

    ![Schermopname van de pagina S S L Certificate/Key Source.](./media/headerf5-tutorial/configure13.png)

1. We gebruiken de begeleide ervaring voor het instellen van de Azure AD-federatie en -toepassingstoegang. Ga naar F5 BIG-IP **Main** en selecteer **Access > Guided Configuration > Federation > SAML Service Provider**. Klik op **Next** klik vervolgens op **Next** om de configuratie te starten.

    ![Schermopname van de pagina Guided Configuration met Federation geselecteerd.](./media/headerf5-tutorial/configure01.png)

    ![Schermopname van de pagina SAML Service Provider.](./media/headerf5-tutorial/configure02.png)
 
1. Geef een **Configuration Name** op. Geef de **Entity ID** op (dezelfde als die u hebt geconfigureerd in de configuratie van de Azure AD-toepassing). Geef de **Host name** op. Voeg een **Description** toe ter referentie. Accepteer de overige standaardgegevens en selecteer en klik op **Save & Next**.

    ![Schermopname van de pagina Service Provider Properties.](./media/headerf5-tutorial/configure03.png) 

1. In dit voorbeeld maken we een nieuwe virtuele server als 192.168.30.20 met poort 443. Geef het IP-adres van de virtuele server op bij **Destination Address**. Selecteer het **SSL Profile** van de client en selecteer Create new. Geef het eerder geüploade toepassingscertificaat (het wildcardcertificaat in dit voorbeeld) en de bijbehorende sleutel op en klik op **Save & Next**.

    >[!NOTE]
    >In dit voorbeeld wordt onze interne webserver uitgevoerd op poort 888 en willen we deze publiceren met 443.

    ![Schermopname van de pagina Virtual Server Properties.](./media/headerf5-tutorial/configure04.png) 

1. Geef bij **Select method to configure your IdP connector** de optie Metadata op, klik op Choose File en upload het XML-bestand met metagegevens dat u eerder hebt gedownload van Azure AD. Geef een unieke **naam** op voor de SAML IDP-connector. Kies het **Metadata Signing Certificate** dat eerder is geüpload. Klik op **Save & Next**.

    ![Schermopname van de pagina External Identity Provider Connector Settings.](./media/headerf5-tutorial/configure05.png)
 
1. Selecteer onder **Select a Pool** de optie **Create New** (u kunt ook een pool selecteren die al bestaat). Laat andere waarden op de standaardwaarde ingesteld. Typ onder Pool Servers het IP-adres onder **IP Address/Node Name**. Geef de **Port** op. Klik op **Save & Next**.

    ![Schermopname van de pagina Pool Properties.](./media/headerf5-tutorial/configure06.png)

1. Selecteer in het scherm Single Sign-On Settings de optie **Enable Single Sign-On**. Kies onder Selected Single Sign-On Type de optie **HTTP header-based**. Vervang **session.saml.last.Identity** door **session.saml.last.attr.name.Identity** onder Username Source (deze variabele is ingesteld met behulp van claimtoewijzing in Azure AD). Onder SSO Headers.

    * **HeaderName: MyAuthorization**

    * **Header Value : %{session.saml.last.attr.name.Identity}**

    * Klik op **Save & Next**

    Raadpleeg de bijlage voor een volledige lijst met variabelen en waarden. U kunt desgewenst meer headers toevoegen.

    >[!NOTE]
    >Account Name is het F5-delegatieaccount dat is gemaakt (raadpleeg de documentatie van F5).

    ![Schermopname van de pagina Single Sign-On Settings.](./media/headerf5-tutorial/configure07.png) 

1. In het kader van deze richtlijnen slaan we eindpuntcontroles over.  Raadpleeg de documentatie van F5 voor meer informatie. Selecteer **Save & Next**.

    ![Schermopname van de pagina Endpoint Checks Properties.](./media/headerf5-tutorial/configure08.png)

1. Accepteer de standaardwaarden voor schijven en klik op **Save & Next**. Raadpleeg de documentatie van F5 voor meer informatie over de instellingen voor het beheer van SAML-sessies.

    ![Schermopname van de pagina Timeout Settings.](./media/headerf5-tutorial/configure09.png)

1. Bekijk het overzichtsscherm en selecteer **Deploy** om de BIG-IP te configureren. Klik op **Finish**.

    ![Schermopname van de pagina Your application is ready to be deployed.](./media/headerf5-tutorial/configure10.png)

    ![Schermopname van de pagina Your application is deployed.](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Geavanceerde configuratie

Gebruik deze sectie als u de begeleide configuratie niet kunt gebruiken of als u extra parameters wilt toevoegen of wijzigen. U hebt een TLS/SSL-certificaat voor de hostnaam van de toepassing nodig.

1. Navigeer naar **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **Import** in de rechterhoek. Het **Import Type** wordt **PKCS 12 (IIS)** . Geef een **Key Name** op (verderop in de configuratie wordt hiernaar verwezen) en geef het PFX-bestand op. Geef het **Password** voor de PFX op. Klik op **Import**.

    >[!NOTE]
    >In het voorbeeld is `Headerapp.superdemo.live` de naam van onze app. We gebruiken een wildcardcertificaat en onze keyname is `WildCard-SuperDemo.live`.
  
    ![Schermopname van de pagina S S L Certificate/Key Source voor geavanceerde configuratie.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Een nieuwe webserver toevoegen aan BigIP-F5

1. Klik op **Main > IApps > Application Services > Application > Create**.

1. Geef de **naam** op en kies onder **Template** de optie **f5.http**.
 
    ![Schermopname van de pagina Application Services met Template Selection.](./media/headerf5-tutorial/configure18.png)

1. We gaan in dit geval onze HeaderApp2 extern publiceren als HTTPS, **hoe moet het BIG-IP-systeem SSL-verkeer afhandelen**? We geven **Terminate SSL from Client, Plaintext to servers (SSL Offload)** op. Geef uw certificaat en sleutel op onder **Which SSL certificate do you want to use?** en **Which SSL private key do you want to use?** . Geef het IP-adres van de virtuele server op onder **What IP Address do you want to use for the Virtual Server?** . 

    * **Andere details opgeven**

        * FQDN  

        * Geef een bestaande app-pool op of maak een nieuwe.

        * Als u een nieuwe app-server maakt, geeft u het **interne IP-adres** en **poortnummer** op.

        ![Schermopname van het deelvenster waar u deze detail kunt opgeven.](./media/headerf5-tutorial/configure19.png) 

1. Klik op **Finished**.

    ![Schermopname van de pagina na voltooiing.](./media/headerf5-tutorial/configure20.png) 

1. Zorg ervoor dat de app-eigenschappen kunnen worden gewijzigd. Klik op **Main > IApps > Application Services: Applications >> HeaderApp2**. Schakel het selectievakje **Strict Updates** uit (er worden enkele instellingen buiten de gebruikersinterface gewijzigd). Klik op de knop **Update**.

    ![Schermopname van de pagina Application Services met het tabblad Properties geselecteerd.](./media/headerf5-tutorial/configure21.png) 

1. Op dit moment moet u kunnen bladeren naar de virtuele server.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>F5 als SP en Azure als IDP configureren

1.  Klik op **Access > Federation> SAML Service Provider > Local SP Service > klik op Create of het +-teken**.

    ![Schermopname van de pagina About this BIG I P. ](./media/headerf5-tutorial/configure22.png)

1. Geef details op voor de serviceproviderservice. Geef de **naam** op die de SP-configuratie van F5 vertegenwoordigt. Geef de **entiteit-ID** op (meestal hetzelfde als de URL van de toepassing).

    ![Schermopname van de pagina SAML Service Provider met het dialoogvenster Create New SAML S P Service.](./media/headerf5-tutorial/configure23.png)

    ![Schermopname van het dialoogvenster Create New SAML S P Service met Endpoint Settings geselecteerd.](./media/headerf5-tutorial/configure24.png)

    ![Schermopname van het dialoogvenster Create New SAML S P Service met Security Settings geselecteerd.](./media/headerf5-tutorial/configure25.png)

    ![Schermopname van het dialoogvenster Create New SAML S P Service met Authentication Context geselecteerd.](./media/headerf5-tutorial/configure26.png)

    ![Schermopname van het dialoogvenster Create New SAML S P Service met Requested Attributes geselecteerd.](./media/headerf5-tutorial/configure27.png)

    ![Schermopname van het dialoogvenster Edit New SAML S P Service met Advanced Settings geselecteerd.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>IdP-connector maken

1. Klik op de knop **Bind/Unbind IdP Connectors**, selecteer **Create New IdP Connector** en maak een keuze bij de optie **Metadata**. Voer vervolgens de volgende stappen uit:
 
    ![Schermopname van het dialoogvenster Edit SAML I d Ps that use this S P met Create New I d P Connector geselecteerd.](./media/headerf5-tutorial/configure29.png)

    a. Blader naar het bestand metadata.xml dat u hebt gedownload van Azure AD en geef een **Identity Provider Name** op.

    b. Klik op **OK**.

    c. De connector wordt gemaakt en het certificaat is automatisch gereed vanuit het bestand metadata.xml.
    
    ![Schermopname van het dialoogvenster Create New SAML I d P Connector.](./media/headerf5-tutorial/configure30.png)

    d. Configureer F5BIG-IP om alle aanvragen naar Azure AD te verzenden.

    e. Klik op **Add New Row**, kies **AzureIDP** (zoals gemaakt in de vorige stappen) 

    f. **Matching Source   =  %{session.server.landinguri}** 

    g. **Matching Value     = /** *

    h. Klik op **Update**

    i. Klik op **OK**

    j. **Het instellen van SAML IDP is voltooid**
    
    ![Schermopname van het dialoogvenster Edit SAML I d Ps that use this S P.](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>F5-beleid configureren om gebruikers om te leiden naar Azure SAML IDP

1. Voer de volgende stappen uit om F5-beleid te configureren om gebruikers om te leiden naar Azure SAML IDP:

    a. Klik op **Main > Access > Profile/Policies > Access Profiles**.

    b. Klik op de knop **Create**.

    ![Schermopname van de pagina Access Profiles.](./media/headerf5-tutorial/configure32.png)
 
    c. Geef de **naam** op (HeaderAppAzureSAMLPolicy in het voorbeeld).

    d. U kunt andere instellingen aanpassen. Raadpleeg de documentatie van F5.

    ![Schermopname van de pagina General Properties.](./media/headerf5-tutorial/configure33.png)

    ![Schermopname van het vervolg van de pagina General Properties.](./media/headerf5-tutorial/configure34.png) 

    e. Klik op **Finished**.

    f. Nadat het beleid is gemaakt, klikt u op het beleid en gaat u naar het tabblad **Access Policy**.

    ![Schermopname van het tabblad Access Policy met General Properties.](./media/headerf5-tutorial/configure35.png)
 
    g. Klik op de **Visual Policy editor** en bewerk de koppeling **Access Policy for Profile**.

    h. Klik op het plusteken in de Visual Policy editor en kies **SAML Auth**.

    ![Schermopname van een toegangsbeleid.](./media/headerf5-tutorial/configure36.png)

    ![Schermopname van een zoekvenster waarin SAML Auth is geselecteerd.](./media/headerf5-tutorial/configure37.png)
 
    i. Klik op **Add Item**.

    j. Geef onder **Properties** een **naam** op en selecteer onder **AAA Server** de eerder geconfigureerde SP. Klik op **SAVE**.
 
    ![Schermopname van de eigenschappen van het item, waaronder de A A A-server.](./media/headerf5-tutorial/configure38.png)

    k. Het basisbeleid is gereed. U kunt het beleid aanpassen om extra bronnen/kenmerkarchieven op te nemen.

    ![Schermopname van het aangepaste beleid.](./media/headerf5-tutorial/configure39.png)
 
    l. Zorg ervoor dat u op de koppeling **Apply Access Policy** bovenaan klikt.

### <a name="apply-access-profile-to-the-virtual-server"></a>Toegangsprofiel toepassen op de virtuele server

1. Wijs het toegangsprofiel toe aan de virtuele server om F5 BIG-IP APM te gebruiken om de profielinstellingen toe te passen op binnenkomend verkeer en het eerder gedefinieerde toegangsbeleid uit te voeren.

    a. Klik op **Main** > **Local Traffic** > **Virtual Servers**.

    ![Schermopname van de pagina Virtual Servers List.](./media/headerf5-tutorial/configure40.png)
 
    b. Klik op de virtuele server, blader naar de sectie **Access Policy** in de vervolgkeuzelijst **Access Profile** en selecteer het SAML-beleid dat is gemaakt (in het voorbeeld HeaderAppAzureSAMLPolicy)

    c. Klik op **Update**
 
    ![Schermopname van het deelvenster Access Policy.](./media/headerf5-tutorial/configure41.png)

    d. Maak een F5-iRule® voor het extraheren van de aangepaste SAML-kenmerken van de binnenkomende bevestiging en geef ze door als HTTP-headers voor de testtoepassing voor de back-end. Klik op **Main > Local Traffic > iRules > iRule List > klik op Create**

    ![Schermopname van de Local Traffic iRule List.](./media/headerf5-tutorial/configure42.png)
 
    e. Plak de tekst van F5 BIG-IP iRule hieronder in het definitievenster.

    ![Schermopname van de pagina New iRule.](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Voorbeelduitvoer hieronder**

    ![Schermopname van de voorbeelduitvoer.](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Een F5-testgebruiker maken

In dit gedeelte maakt u in F5 een gebruiker met de naam B.Simon. Neem contact op met het [ondersteuningsteam van F5 Client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen in het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Hiermee wordt de URL naar F5 sign on doorgestuurd, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van F5 en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de F5 waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de F5-tegel in de mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de F5 waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

> [!NOTE]
> F5 BIG-IP APM [Nu kopen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="next-steps"></a>Volgende stappen

Zodra u F5 hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

