---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure AD met F5 | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding tussen Azure Active Directory en F5 configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 161348f7566ff64858d563f34ad8f3f4c7511adf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009157"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory met F5

In deze zelfstudie leert u hoe u F5 integreert met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot F5.
* Uw gebruikers zich met hun Azure AD-account automatisch laten aanmelden bij F5.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

* Een abonnement op F5 waarvoor eenmalige aanmelding is ingeschakeld.

* Voor de implementatie van de gezamenlijke oplossing is de volgende licentie vereist:
    * F5 BIG-IP&reg; Best-bundel (of)

    * Zelfstandige licentie voor F5 BIG-IP Access Policy Manager&trade; (APM)

    * Invoegtoepassingslicentie voor F5 BIG-IP Access Policy Manager&trade; (APM) in een bestaande BIG-IP F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM).

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

2. Klik op de pagina **Guided Configuration** op **Upgrade Guided Configuration** in de linkerbovenhoek.

    ![Schermopname van de pagina Guided Configuration met de actie Upgrade Guided Configuration geselecteerd.](./media/kerbf5-tutorial/configure14.png) 

3. Selecteer in het pop-upvenster Upgrade Guide Configuration op de optie **Choose File** om het gedownloade gebruiksscenariopakket te uploaden en klik op de knop **Upload and Install**.

    ![Schermopname van het pop-upvenster Upgrade Guided Configuration met Choose File en Upload and Install geselecteerd.](./media/kerbf5-tutorial/configure15.png) 

4. Wanneer de upgrade is voltooid, klikt u op de knop **Continue**.

    ![Schermopname van het dialoogvenster Guided Configuration update is complete met de knop Continue geselecteerd.](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* F5 ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Eenmalige aanmelding bij F5 kan op drie verschillende manieren worden geconfigureerd.

- [Eenmalige aanmelding bij F5 configureren voor een Kerberos-toepassing](#configure-f5-single-sign-on-for-kerberos-application)

- [Eenmalige aanmelding bij F5 configureren voor op een header gebaseerde toepassing](headerf5-tutorial.md)

- [Eenmalige aanmelding bij F5 configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Belangrijkste verificatiescenario's

Naast dat het systeemeigen integratie van Azure Active Directory voor moderne verificatieprotocollen zoals Open ID Connect, SAML en WS-Fed ondersteunt, breidt F5 beveiligde toegang uit voor op verouderde verificatietoepassingen voor zowel interne als externe toegang met Azure AD, waardoor moderne scenario's (zoals toegang zonder wachtwoord) mogelijk worden voor deze toepassingen. Dit zijn onder meer:

* Op headers gebaseerde verificatietoepassingen

* Kerberos-verificatietoepassingen

* Anonieme verificatie of apps zonder ingebouwde verificatie

* NTLM-verificatietoepassingen (beveiliging met dubbele prompts voor de gebruiker)

* Op formulieren gebaseerde toepassing (beveiliging met dubbele prompts voor de gebruiker)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 in Azure AD wilt configureren, moet u F5 vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **F5** in het zoekvak.
1. Selecteer **F5** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Eenmalige aanmelding van Azure AD configureren en testen voor F5

Configureer en test eenmalige aanmelding van Azure AD met F5 met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in F5.

Voltooi de volgende stappen om eenmalige aanmelding bij F5 met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij F5 configureren](#configure-f5-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Een testgebruiker voor F5 maken](#create-f5-test-user)** : als u een tegenhanger van B.Simon in F5 wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **F5** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
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

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en **Certificaat (base64)** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

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

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.
1. Klik op **Voorwaardelijke toegang**.
1. Klik op **Nieuw beleid**.
1. U kunt uw F5-app nu zien als een resource voor CA-beleid en voorwaardelijke toegang toepassen, waaronder meervoudige verificatie, toegangsbeheer op basis van apparaten of beleid voor identiteitsbeveiliging.

## <a name="configure-f5-sso"></a>Eenmalige aanmelding bij F5 configureren

- [Eenmalige aanmelding bij F5 configureren voor op een header gebaseerde toepassing](headerf5-tutorial.md)

- [Eenmalige aanmelding bij F5 configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Eenmalige aanmelding bij F5 configureren voor een Kerberos-toepassing

### <a name="guided-configuration"></a>Begeleide configuratie

1. Open een nieuw webbrowservenster en meld u als beheerder bij uw bedrijfssite van F5 (Kerberos) aan en voer de volgende stappen uit:

1. U moet het metagegevenscertificaat importeren in F5. Dit wordt later gebruikt in het installatieproces.

1. Navigeer naar **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **Import** in de rechterhoek. Geef een **certificaatnaam op** (verderop in de configuratie wordt hiernaar verwezen). Selecteer bij **Certificate Source** de optie Upload File en geef het certificaat op dat u van Azure hebt gedownload tijdens het configureren van eenmalige aanmelding met SAML. Klik op **Import**.

    ![Schermopname van de pagina SSL Certificate/Key Source met Certificate Name en Upload File gemarkeerd en de knop Import geselecteerd.](./media/kerbf5-tutorial/configure01.png) 

1. Daarnaast hebt u het **SSL-certificaat voor de hostnaam van de toepassing nodig. Navigeer naar System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **Import** in de rechterhoek. Het **Import Type** wordt **PKCS 12 (IIS)** . Geef een **Key Name** op (verderop in de configuratie wordt hiernaar verwezen) en geef het PFX-bestand op. Geef het **Password** voor de PFX op. Klik op **Import**.

    >[!NOTE]
    >In het voorbeeld is `Kerbapp.superdemo.live` de naam van onze app. We gebruiken een wildcardcertificaat en onze keyname is `WildCard-SuperDemo.live`

    ![Schermopname van de pagina SSL Certificate/Key Source met ingevoerde waarden en de knop Import geselecteerd.](./media/kerbf5-tutorial/configure02.png) 
 
1. We gebruiken de begeleide ervaring voor het instellen van de Azure AD-federatie en -toepassingstoegang. Ga naar F5 BIG-IP **Main** en selecteer **Access > Guided Configuration > Federation > SAML Service Provider**. Klik op **Next** klik vervolgens op **Next** om de configuratie te starten.

    ![Schermopname van de pagina Guided Configuration met het pictogram Federation gemarkeerd en SAML Service Provider geselecteerd.](./media/kerbf5-tutorial/configure03.png) 

    ![Schermopname van de pagina Guided Configuration - SAML Service Provider met de knop Next geselecteerd.](./media/kerbf5-tutorial/configure04.png)

1. Geef een **Configuration Name** op. Geef de **Entity ID** op (dezelfde als die u hebt geconfigureerd in de configuratie van de Azure AD-toepassing). Geef de **Host name** op. Voeg een **Description** toe ter referentie. Accepteer de overige standaardgegevens en selecteer en klik op **Save & Next**.

    ![Schermopname van Service Provider Properties met de tekstvakken Host name en Description gemarkeerd en de knop Save & Next geselecteerd.](./media/kerbf5-tutorial/configure05.png) 

1. In dit voorbeeld maken we een nieuwe virtuele server als 192.168.30.200 met poort 443. Geef het IP-adres van de virtuele server op bij **Destination Address**. Selecteer het **SSL Profile** van de client en selecteer Create new. Geef het eerder geüploade toepassingscertificaat (het wildcardcertificaat in dit voorbeeld) en de bijbehorende sleutel op en klik op **Save & Next**.

    >[!NOTE]
    >In dit voorbeeld wordt onze interne webserver uitgevoerd op poort 80 en willen we deze publiceren met 443.

    ![Schermopname van de pagina Virtual Server Properties met het tekstvak Destination Address gemarkeerd en de knop Save & Next geselecteerd.](./media/kerbf5-tutorial/configure06.png)

1. Geef bij **Select method to configure your IdP connector** de optie Metadata op, klik op Choose File en upload het XML-bestand met metagegevens dat u eerder hebt gedownload van Azure AD. Geef een unieke **naam** op voor de SAML IDP-connector. Kies het **Metadata Signing Certificate** dat eerder is geüpload. Klik op **Save & Next**.

    ![Schermopname van de pagina External Identity Provider Connector Settings met het tekstvak Name gemarkeerd en de knop Save & Next geselecteerd.](./media/kerbf5-tutorial/configure07.png)  

1. Selecteer onder **Select a Pool** de optie **Create New** (u kunt ook een pool selecteren die al bestaat). Laat andere waarden op de standaardwaarde ingesteld.    Typ onder Pool Servers het IP-adres onder **IP Address/Node Name**. Geef de **Port** op. Klik op **Save & Next**.
 
    ![Schermopname van de pagina Pool Properties met de tekstvakken IP Address/Node Name en Port gemarkeerd en de knop Save & Next geselecteerd.](./media/kerbf5-tutorial/configure08.png)

1. Selecteer in het scherm Single Sign-On Settings de optie **Enable Single Sign-On**. Onder **Selected Single Sign-On Type** kiest u **Kerberos**. Vervang **session.saml.last.Identity** door **session.saml.last.attr.name.Identity** onder **Username Source** (deze variabele is ingesteld met behulp van claimtoewijzing in Azure AD). Selecteer **Show Advanced Setting**. Typ onder **Kerberos Realm** de domeinnaam. Geef onder **Account Name/ Account Password** de accountnaam en het wachtwoord voor APM-delegatie op. Geef het IP-adres van de domeincontroller op in het veld **KDC**. Klik op **Save & Next**.

    ![Schermopname van Single Sign-On Settings met de tekstvakken gemarkeerd en de knop Save & Next geselecteerd.](./media/kerbf5-tutorial/configure09.png)   

1. In het kader van deze richtlijnen slaan we eindpuntcontroles over.  Raadpleeg de documentatie van F5 voor meer informatie.  Selecteer **Save & Next** op het scherm.

    ![Schermopname van de pagina Endpoint Checks Properties met de knop Save & Next geselecteerd.](./media/kerbf5-tutorial/configure10.png) 

1. Accepteer de standaardwaarden voor schijven en klik op **Save & Next**. Raadpleeg de documentatie van F5 voor meer informatie over de instellingen voor het beheer van SAML-sessies.


    ![Schermopname van de pagina Timeout Settings met de knop Save & Next geselecteerd.](./media/kerbf5-tutorial/configure11.png) 
 
1. Bekijk het overzichtsscherm en selecteer **Deploy** om de BIG-IP te configureren.
 
    ![Schermopname van de pagina 'Your application is ready to be deployed' (Uw toepassing is klaar voor implementatie) met de sectie 'Summary' (Samenvatting) gemarkeerd en de knop 'Deploy' (Implementeren) geselecteerd.](./media/kerbf5-tutorial/configure12.png)

1. Zodra de toepassing is geconfigureerd, klikt u op **Finish**.

    ![Schermopname van de pagina Your application is deployed met de knop Finish geselecteerd.](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Geavanceerde configuratie

>[!NOTE]
>Klik [hier](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) voor naslagmateriaal

### <a name="configuring-an-active-directory-aaa-server"></a>Een Active Directory AAA-server configureren

U configureert een Active Directory AAA-server in Access Policy Manager (APM) om domeincontrollers en referenties op te geven voor APM, die moeten worden gebruikt voor de verificatie van gebruikers.

1. Klik op het tabblad Main op **Access Policy > AAA Servers > Active Directory**. Het scherm met de lijst Active Directory Servers wordt geopend.

2. Klik op **Create**. Het scherm New Server properties wordt geopend.

3. Typ in het veld **Name** een unieke naam voor de verificatieserver.

4. Typ in het veld **Domain Name** de naam van het Windows-domein.

5. Selecteer een van de volgende opties voor de instelling **Server Connection**:

   * Selecteer **Use Pool** om hoge beschikbaarheid in te stellen voor de AAA-server.

   * Selecteer **Direct** om de AAA-server in te stellen voor zelfstandige functionaliteit.

6. Als u **Direct** hebt geselecteerd, typt u een naam in het veld **Domain Controller**.

7. Als u **Use Pool** gebruiken hebt geselecteerd, configureert u de pool:

   * Typ een naam in het veld **Domain Controller Pool Name**.

   * Geef de **Domain Controllers** in de pool op door het IP-adres en de hostnaam voor elke domeincontroller te typen en op de knop **Add** te klikken.

   * Als u de status van de AAA-server wilt bewaken, kunt u een statusmonitor selecteren: alleen de monitor **gateway_icmp** is in dit geval geschikt. U kunt deze selecteren in de lijst **Server Pool Monitor**.

8. Typ in het veld **Admin Name** een hoofdlettergevoelige naam voor een beheerder die beheerdersmachtigingen voor Active Directory heeft. APM gebruikt de informatie in de velden **Admin Name** en **Admin Password** voor een AD-query. Als Active Directory is geconfigureerd voor anonieme query's, hoeft u geen beheerdersnaam op te geven. Anders heeft APM een account met voldoende bevoegdheden nodig om verbinding te maken met een Active Directory-server, informatie over gebruikersgroepen op te halen en Active Directory-wachtwoordbeleid op te halen ter ondersteuning van wachtwoordfunctionaliteit. (APM moet wachtwoordbeleid ophalen, bijvoorbeeld als u de optie Prompt user to change password before expiration in een AD-query selecteert.) Als u geen beheerdersaccountgegevens in deze configuratie opgeeft, gebruikt APM het gebruikersaccount om gegevens op te halen. Dit werkt als het gebruikersaccount over voldoende bevoegdheden beschikt.

9. Typ in het veld **Admin Password** het beheerderswachtwoord dat aan de domeinnaam is gekoppeld.

10. Typ in het veld **Verify Admin Password** het beheerderswachtwoord dat is gekoppeld aan de instelling voor de **domeinnaam** nog een keer.

11. Typ het aantal dagen in het veld **Group Cache Lifetime**. De standaardlevensduur is 30 dagen.

12. Typ het aantal dagen in het veld **Password Security Object Cache Lifetime**. De standaardlevensduur is 30 dagen.

13. Selecteer een versleutelingstype in de lijst **Kerberos Preauthentication Encryption Type**. De standaardwaarde is **None**. Als u een versleutelingstype opgeeft, bevat het BIG-IP-systeem Kerberos-gegevens voor verificatie vooraf binnen het eerste pakket voor verificatieserviceaanvragen (AS-REQ).

14. Typ in het veld **Timeout** een time-outinterval (in seconden) voor de AAA-server. (Deze instelling is optioneel.)

15. Klik op **Finished**. De nieuwe server wordt weergegeven in de lijst. Hiermee voegt u de nieuwe Active Directory-server toe aan de lijst met Active Directory-servers.

    ![Schermopname van de secties General Properties en Configuration.](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML-configuratie

1. U moet het metagegevenscertificaat importeren in F5. Dit wordt later gebruikt in het installatieproces. Navigeer naar **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **Import** in de rechterhoek.

    ![Schermopname van de pagina Import SSL Certificate/Key Source met de knop Import geselecteerd.](./media/kerbf5-tutorial/configure18.png)

2. Voor het instellen van de SAML-IDP **navigeert u naar Access > Federation > SAML: Service Provider > External Idp Connectors** en klikt u op **Create > From Metadata**.

    ![Schermopname van de pagina SAML Service Provider met From Metadata geselecteerd in de vervolgkeuzelijst Create.](./media/kerbf5-tutorial/configure19.png)

    ![Schermopname van het dialoogvenster Create New SAML IdP Connector.](./media/kerbf5-tutorial/configure20.png)

    ![Schermopname van het venster Edit SAML IdP Connector met General Settings geselecteerd.](./media/kerbf5-tutorial/configure21.png)

    ![Schermopname van het venster Edit SAML IdP Connector met Single Sign On Service Settings geselecteerd.](./media/kerbf5-tutorial/configure22.png)

    ![Schermopname van het venster Edit SAML IdP Connector met Security Settings geselecteerd.](./media/kerbf5-tutorial/configure23.png)

    ![Schermopname van het venster Edit SAML IdP Connector met SLO Service Settings geselecteerd.](./media/kerbf5-tutorial/configure24.png)

1. Voor het instellen van het SAML-SP navigeert u naar **Access > Federation > SAML Service Provider > Local SP Services** en klikt u op **Create**. Voer de volgende informatie in en klik op **OK**.

    * Type Name: KerbApp200SAML
    * Entity ID*: https://kerb-app.com.cutestat.com
    * SP Name Settings
    * Scheme: https
    * Host: kerbapp200.superdemo.live
    * Description: kerbapp200.superdemo.live

     ![Schermopname van het venster Edit SAML SP Service met General Settings geselecteerd.](./media/kerbf5-tutorial/configure25.png)

     b. Selecteer de SP-configuratie, KerbApp200SAML, en klik op **Bind/UnBind IdP Connectors**.

     ![Schermopname van de pagina SAML Service Provider - Local SP Services met KerbAPP200 SAML geselecteerd.](./media/kerbf5-tutorial/configure26.png)

     ![Schermopname met de knop Bind/Unbind IdP Connectors geselecteerd.](./media/kerbf5-tutorial/configure27.png)

     c. Klik op **Add New Row** en selecteer de **External IdP connector** die u in de vorige stap hebt gemaakt, klik op **Update** en klik op **OK**.

     ![Schermopname van het venster 'Edit SAML IdPs that use this SP' (SAML IdP's bewerken die deze SP gebruiken) met de knop 'Add New Row' (Nieuwe rij toevoegen) geselecteerd.](./media/kerbf5-tutorial/configure28.png)

1. Voor het configureren van eenmalige aanmelding met Kerberos navigeert u naar **Access > Single Sign-on > Kerberos**, vult u de gegevens in en klikt u op **Finished**.

    >[!Note]
    > U moet het Kerberos-delegatieaccount maken en opgeven. Verwijzen naar KCD-sectie (verwijzen naar bijlage voor referenties voor variabelen)

    * **Username Source**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **User Realm Source**: session.logon.last.domain

        ![Schermopname van de pagina Single Sign-On - Properties met de tekstvakken Username Source en User Realm Source geselecteerd.](./media/kerbf5-tutorial/configure29.png)

1. Voor het configureren van het toegangsprofiel navigeert u naar **Access > Profile/Policies > Access Profile (per session policies)** , klikt u op **Create**, vult u de volgende informatie in en klikt u op **Finished**.

    * Naam: KerbApp200
    * Profile Type: Alle
    * Profile Scope: Profiel
    * Languages: Engels

        ![Schermopname van de pagina Profiles/Policies - Properties met de tekstvakken Name, Profile Type en Langauges gemarkeerd.](./media/kerbf5-tutorial/configure30.png)

1. Klik op de naam, KerbApp200, voer de volgende informatie in en klik op **Update**.

    * Domain Cookie: superdemo.live
    * SSO Configuration: KerAppSSO_sso

        ![Schermopname van de pagina SSD/Auth Domains met het tekstvak Domain Cookie en de vervolgkeuzelijst SSO Configuration gemarkeerd en de knop Update geselecteerd.](./media/kerbf5-tutorial/configure31.png)

1. Klik op **Access Policy** en klik vervolgens op **Edit Access Policy** voor het profiel 'KerbApp200'.

    ![Schermopnane van de pagina Access Policy met de actie Edit Access Policy for Profile KerbApp200 geselecteerd.](./media/kerbf5-tutorial/configure32.png)

    ![Schermopname van de pagina 'Access Policy' (Toegangsbeleid) en het dialoogvenster 'SAML Authentication SP' (SAML-verificatie SP).](./media/kerbf5-tutorial/configure33.png)

    ![Schermopname van de pagina Access Policy en het dialoogvenster Variable Assign met de Assignment-tekstvakken gemarkeerd.](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain  TEXT superdemo.live**

        ![Schermopname van de pagina Access Policy en het dialoogvenster Active Directory met het tekstvak SearchFilter gemarkeerd.](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![Schermopname van de pagina Access Policy en het dialoogvenster AD Query - Branch Rules.](./media/kerbf5-tutorial/configure36.png)

        ![Schermopname met de tekstvakken Custom Variable en Custom Expression gemarkeerd.](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![Schermopname met de tekst Username from Logon Page gemarkeerd.](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Voor het toevoegen van een nieuw knooppunt navigeert u naar **Local Traffic > Nodes > Node List, klikt u op Create**, voert u de volgende gegevens in en klikt u op **Finished**.

    * Naam: KerbApp200
    * Beschrijving: KerbApp200
    * Address: 192.168.20.200

        ![Schermopname van de pagina New Node met de tekstvakken Name, Description en Address gemarkeerd en de knop Finished geselecteerd.](./media/kerbf5-tutorial/configure39.png)

1. Voor het maken van een nieuwe pool navigeert u naar **Local Traffic > Pools > Pool List, klikt u op Create**, voert u de volgende gegevens in en klikt u op **Finished**.

    * Naam: KerbApp200-Pool
    * Beschrijving: KerbApp200-Pool
    * Health Monitors: http
    * Address: 192.168.20.200
    * Service Port: 81

        ![Schermopname van de pagina New Pool met ingevoerde waarden, en met de knop Finished geselecteerd.](./media/kerbf5-tutorial/configure40.png)

1. Voor het maken van een virtuele server navigeert u naar **Local Traffic > Virtual Servers > Virtual Server List > +** , voert u de volgende gegevens in en klikt u op **Finished**.

    * Naam: KerbApp200
    * Destination Address/Mask: Host 192.168.30.200
    * Service Port: Port 443 HTTPS
    * Access Profile: KerbApp200
    * Geef het toegangsprofiel op dat in de vorige stap is gemaakt

        ![Schermopname van de pagina Virtual Server List met de tekstvakken Name, Destination Address/Mask en Service Port gemarkeerd.](./media/kerbf5-tutorial/configure41.png)

        ![Schermopname van de pagina Virtual Server List met de vervolgkeuzelijst Access Profile gemarkeerd.](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Kerberos-delegatie instellen 

>[!NOTE]
>Klik [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) voor naslagmateriaal

*  **Stap 1:** Een delegatieaccount maken

    **Voorbeeld:**
    * Domeinnaam: **superdemo.live**

    * SAM-accountnaam: **big-ipuser**

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Stap 2:** SPN instellen (in het APM-delegatieaccount)

    **Voorbeeld:**
    * setspn –A **host/big-ipuser.superdemo.live** big-ipuser

* **Stap 3:** Met SPN-delegatie (voor het App Service-account) stelt u de juiste delegatie in voor het F5-delegatieaccount.
    In het onderstaande voorbeeld wordt een APM-delegatieaccount geconfigureerd voor KCD voor de app FRP-App1.superdemo. live.

    ![Configuratie van F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Geef de details op zoals [hier](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) in het bovenstaande referentiedocument wordt vermeld.

### <a name="create-f5-test-user"></a>Een F5-testgebruiker maken

In dit gedeelte maakt u in F5 een gebruiker met de naam B.Simon. Neem contact op met het [ondersteuningsteam van F5 Client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen in het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel F5 in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van F5 waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [F5 proberen met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding bij F5 configureren voor op een header gebaseerde toepassing](headerf5-tutorial.md)

- [Eenmalige aanmelding bij F5 configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)