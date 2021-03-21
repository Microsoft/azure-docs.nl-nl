---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Oracle PeopleSoft - Protected by F5 BIG-IP APM | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Oracle PeopleSoft - Protected by F5 BIG-IP APM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: 3b7c8e024ac8361c08cc41195531a114bb12fcb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522288"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Oracle PeopleSoft - Protected by F5 BIG-IP APM

In deze zelfstudie leert u hoe u Oracle PeopleSoft - Protected by F5 BIG-IP APM kunt integreren met Azure AD (Azure Active Directory). Wanneer u Oracle PeopleSoft - Protected by F5 BIG-IP APM met Azure AD integreert, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Oracle PeopleSoft - Protected by F5 BIG-IP APM.
* Ervoor zorgen dat gebruikers zich met hun Azure-account automatisch kunnen aanmelden bij Oracle PeopleSoft - Protected by F5 BIG-IP APM.
* Uw accounts op een centrale locatie beheren: Azure Portal.
* In deze zelfstudie worden instructies beschreven voor Oracle PeopleSoft ELM.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

1. Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
1. Abonnement op Oracle PeopleSoft - Protected by F5 BIG-IP APM waarvoor eenmalige aanmelding is ingeschakeld.

1. Voor de implementatie van de gezamenlijke oplossing is de volgende licentie vereist:

    1. F5 BIG-IP® Best-bundel (of) 
    2. Zelfstandige licentie voor F5 BIG-IP Access Policy Manager™ (APM) 
    3. Invoegtoepassingslicentie voor F5 BIG-IP Access Policy Manager™ (APM) in een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).
    4. Naast de bovenstaande licentie kan het F5-systeem ook in licentie worden verkregen met: 
        * Een abonnement op URL-filtering voor het gebruik van de URL-categoriedatabase 
        * Een F5 IP Intelligence-abonnement voor het detecteren en blokkeren van bekende aanvallers en schadelijk verkeer 
        * Een netwerk-HSM (Hardware Security module) voor het beveiligen en beheren van digitale sleutels voor sterke verificatie
1. Het F5 BIG-IP-systeem is ingericht met APM-modules (LTM is optioneel) 
1. Hoewel dit optioneel is, is het raadzaam om de F5-systemen te implementeren in een [synchronisatie/failover-apparaatgroep](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), die het actieve stand-by-paar omvat, met een zwevend IP-adres voor hoge beschikbaarheid. U kunt verdere interfaceredundantie bereiken met behulp van het Link Aggregation Control Protocol (LACP). LACP beheert de verbonden fysieke interfaces als één virtuele interface (statistische groep) en detecteert interfacefouten binnen de groep.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Oracle PeopleSoft - Protected by F5 BIG-IP APM ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Oracle PeopleSoft - Protected by F5 BIG-IP APM toevoegen vanuit de galerie

Als u de integratie van Oracle PeopleSoft - Protected by F5 BIG-IP APM in Azure AD wilt configureren, dient u Oracle PeopleSoft - Protected by F5 BIG-IP APM vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Oracle PeopleSoft - Protected by F5 BIG-IP APM** in het zoekvak.
1. Selecteer **Oracle PeopleSoft - Protected by F5 BIG-IP APM** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Oracle PeopleSoft - Protected by F5 BIG-IP APM

Configureer en test eenmalige aanmelding van Azure AD met Oracle PeopleSoft - Protected by F5 BIG-IP APM met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Oracle PeopleSoft - Protected by F5 BIG-IP APM.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD wilt testen en configureren met Oracle PeopleSoft - Protected by F5 BIG-IP APM:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Oracle PeopleSoft - Protected by F5 BIG-IP APM configureren](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Oracle PeopleSoft-Protected by F5 BIG-IP APM maken](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** : als u een tegenhanger van B. Simon in Oracle PeopleSoft-Protected by F5 BIG-IP APM wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal, op de integratiepagina van de toepassing **Oracle PeopleSoft - Protected by F5 BIG-IP APM**, naar de sectie **Beheren**, en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<FQDN>.peoplesoft.f5.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. Typ in het tekstvak **Afmeldings-URL** een URL met het volgende patroon: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id, antwoord-URL en afmeldings-URL. Neem contact op met [het klantondersteuningsteam van Oracle PeopleSoft - Protected by F5 BIG-IP APM](https://support.f5.com) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De toepassing Oracle PeopleSoft - Protected by F5 BIG-IP APM verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing Oracle PeopleSoft - Protected by F5 BIG-IP APM nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. Op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, downloadt u het **XML-bestand met federatieve metagegevens** en het **certificaat (Base64)** en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

In deze sectie stelt u B. Simon in staat gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot Oracle PeopleSoft - Protected by F5 BIG-IP APM.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Oracle PeopleSoft - Protected by F5 BIG-IP APM**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Oracle PeopleSoft - Protected by F5 BIG-IP APM configureren

### <a name="f5-saml-sp-configuration"></a>Configuratie van F5 SAML SP

Importeer het metagegevenscertificaat in de F5 die later in het installatieproces wordt gebruikt. Navigeer naar **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **Import** in de rechterhoek.

![Configuratie van F5 SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>De SAML IDP-connector instellen 

1. Ga naar **Access > Federation > SAML: Service Provider > External Idp Connectors** en klik op **Create > From Metadata**.

    ![F5 SAML IDP-connector](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Klik op de volgende pagina op **Browse** om het XML-bestand te uploaden.

1. Geef in het tekstvak **Identity Provider Name** een geldige naam op en klik op **OK**.

    ![nieuwe SAML IDP-connector](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Voer de vereiste stappen uit op het tabblad **Beveiligingsinstellingen** en klik op **OK**.

    ![SAML IDP-connector bewerken](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>SAML-SP instellen

1. Ga naar **Access > Federation > SAML Service Provider > Local SP Services** en klik op **Create**. Voer de volgende informatie in en klik op **OK**.

    * Naam: `<Name>`
    * Entity ID: `https://<FQDN>.peoplesoft.f5.com`
    * SP Name Settings
        * Scheme: `https`
        * Host: `<FQDN>.peoplesoft.f5.com`
        * Description: `<Description>`

    ![nieuwe SAML SP-services](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Selecteer de SP-configuratie, PeopleSoftAppSSO, en klik op **Bind/UnBind IdP Connectors**.
Klik op **Add New Row** en selecteer de **External IdP connector** die u in de vorige stap hebt gemaakt, klik op **Update** en klik op **OK**.

    ![SAML SP-services maken](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Toepassing configureren

### <a name="create-a-new-pool"></a>Nieuwe pool maken
1. Ga naar **Local Traffic > Pools > Pool List**, klik op **Create**, vul de volgende gegevens in en klik op **Finished**.

    * Naam: `<Name>`
    * Description: `<Description>`
    * Health Monitors: `http`
    * Address: `<Address>`
    * Service Port: `<Service Port>`

    ![nieuwe pool maken](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Nieuw client-SSL-profiel maken

Ga naar **Local Traffic > Profiles > SSL > Client > +** , vul de volgende gegevens in en klik op **Finished**.

* Name: `<Name>`
* Certificaat: `<Certificate>`
* Key: `<Key>`

![nieuw client-SSL-profiel](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Nieuwe virtuele server maken

1. Ga naar **Local Traffic > Virtual Servers > Virtual Server List > +** , vul de volgende gegevens in en klik op **Finished**.
    * Name: `<Name>`
    * Destination Address/Mask: `<Address>`
    * Service Port: Port 443 HTTPS
    * HTTP Profile (Client): http

    ![Nieuwe virtuele server maken](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Vul de volgende waarden in op de onderstaande pagina:

    * SSL Profile (Client): `<SSL Profile>`
    * Source Address Translation: Auto Map
    * Access Profile: `<Access Profile>`
    * Default Pool: `<Pool>`


    ![Een nieuwe peoplesoft-pool voor de virtuele server maken](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>People Soft-toepassing instellen voor ondersteuning van F5 BIG-IP APM als de oplossing voor eenmalige aanmelding

>[!Note]
> Naslaginformatie: https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Meld u met beheerdersreferenties aan bij de PeopleSoft-console `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` (voorbeeld: PS/PS)

    ![Manager selfservices](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. Maak in de toepassing PeopleSoft **OAMPSFT** als een nieuw gebruikersprofiel en koppel een laag beveiligingsniveau, zoals **PeopleSoft User**.
Ga naar **Peopletools > Security > User Profiles > User Profiles** om een nieuw gebruikersprofiel te maken, bijvoorbeeld: **OAMPSFT** en voeg **Peoplesoft User** toe.

    ![PeopleSoft-gebruiker](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Open het webprofiel en voer **OAMPSFT** in als **gebruikers-id** voor openbare toegang.

    ![Gebruikersprofielen](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. Open in **PeopleTools Application Designer** de record **FUNCLIB_LDAP**.

    ![configuratie van webprofielen](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Werk de gebruikersheader bij met **PS_SSO_UID** voor de functie **OAMSSO_AUTHENTICATION**.
Vervang in de functie **getWWWAuthConfig()** de waarde die aan **&defaultUserId** is toegewezen door **OAMPSFT**, wat in het webprofiel is gedefinieerd. Sla de definitie van de record op.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Open de pagina **Signon PeopleCode** (PeopleTools, Security, Security Objects, Signon PeopleCode) en schakel de functie **OAMSSO_AUTHENTICATION** in: de Signon PeopleCode voor eenmalige aanmelding van Oracle Access Manager.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>F5 BIG-IP APM instellen om de HTTP-header PS_SSO_UID in te vullen met de gebruikers-ID van PeopleSoft

### <a name="configuring-per-request-policy"></a>Beleid per aanvraag configureren
1. Ga naar **Access > Profile/Policies > Per-Request Policies**, klik op **Create**, vul de volgende gegevens in en klik op **Finished**.

    * Name: `<Name>`
    * Profile Type: Alle
    * Languages: `<Language>`

    ![Beleid per aanvraag configureren ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Klik op **Edit** Per-Request Policy `<Name>` ![edit Per-Request Policy PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Beleid per aanvraag toewijzen aan de virtuele server

Ga naar **Local Traffic > Virtual Servers > Virtual Server List > PeopleSoftApp** Geef `<Name>` op als Per-Request Policy

![PeopleSoftSSO als beleid per aanvraag ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>F5 BIG-IP APM instellen om eenmalige afmelding van de toepassing PeopleSoft te ondersteunen

Voer de volgende stappen uit om ondersteuning voor eenmalige afmelding toe te voegen voor alle PeopleSoft-gebruikers:

1. De juiste afmeldings-URL voor de PeopleSoft-portal vaststellen
    * Als u het adres wilt vaststellen dat door de PeopleSoft-toepassing wordt gebruikt om een gebruikerssessie te beëindigen, moet u de portal openen in een webbrowser en foutopsporingsprogramma's in de browser inschakelen, zoals in het volgende voorbeeld wordt weergegeven:

        ![afmeldings-URL voor PeopleSoft-portal ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Zoek het element met de `PT_LOGOUT_MENU`-id en sla het URL-pad met de queryparameters op. In dit voorbeeld gaat het om de volgende waarde: `/psp/ps/?cmd=logout`

1. LTM iRule maken waarmee de gebruiker wordt omgeleid naar de APM-afmeldings-URL: `/my.logout.php3`

    * Ga naar **Local Traffic > iRule**, klik op **Create**, vul de volgende gegevens in en klik op **Finished**.

        Name: `<Name>`  
        Definition:  
                    _when HTTP_REQUEST { switch -glob -- [HTTP::uri] { `/psp/ps/?cmd=logout` { HTTP::redirect `/my.logout.php3` } } }_

1. De gemaakte iRule toewijzen aan de virtuele server

    * Ga naar **Local Traffic > Virtual Servers > Virtual Server List > PeopleSoftApp > Resources**. Klik op de knop **Manage…** :   

    * Geef `<Name>` op als ingeschakelde iRule en klik op **Finished**

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Geef de tekstvakwaarde **Name** op als `<Name>` 

        ![_iRule_PeopleSoftApp voltooid](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Testgebruiker voor Oracle PeopleSoft - Protected by F5 BIG-IP APM maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Oracle PeopleSoft-Protected by F5 BIG-IP APM. Werk samen met het [ondersteuningsteam van Oracle PeopleSoft - Protected by F5 BIG-IP APM](https://support.f5.com) om de gebruikers toe te voegen aan het Oracle PeopleSoft - Protected by F5 BIG-IP APM-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Hiermee wordt u omgeleid naar de aanmeldings-URL voor Oracle PeopleSoft - Protected by F5 BIG-IP APM, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar aanmeldings-URL voor Oracle PeopleSoft - Protected by F5 BIG-IP APM en start de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van Oracle PeopleSoft - Protected by F5 BIG-IP APM waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in het toegangsvenster op de tegel Oracle PeopleSoft - Protected by F5 BIG-IP APM klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Oracle PeopleSoft - Protected by F5 BIG-IP APM waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u Oracle PeopleSoft - Protected by F5 BIG-IP APM hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).