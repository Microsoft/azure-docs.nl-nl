---
title: 'Zelfstudie: Eenmalige aanmelding integreren tussen Azure AD en Trend Micro Web Security (TMWS)'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Trend Micro Web Security (TMWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008392"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Zelfstudie: Integratie van eenmalige aanmelding tussen Azure Active Directory en Trend Micro Web Security (TMWS)

In deze zelfstudie leert u hoe u Trend Micro Web Security (TMWS) kunt integreren met Azure Active Directory (Azure AD). Wanneer u TMWS integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie er toegang heeft tot TMWS.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TMWS.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een TMWS-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TMWS ondersteunt door SP geïnitieerde eenmalige aanmelding.
* Zodra u TMWS hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Voor informatie over het afdwingen van sessiebeheer met behulp van Microsoft Cloud App Security, raadpleegt u [App-beheer voor voorwaardelijke toegang onboarden en implementeren voor elke app](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>TMWS toevoegen uit de galerie

Voor het configureren van de integratie van TMWS met Azure Active Directory moet u TMWS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure Portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Ga in het gedeelte **Toevoegen uit de galerie** naar het zoekvak en voer **Trend Micro Web Security (TMWS)** in.
1. Selecteer **Trend Micro Web Security (TMWS)** in de zoekresultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Eenmalige aanmelding via Azure AD voor TMWS configureren en testen

Configureer en test eenmalige aanmelding via Azure AD voor TMWS met behulp van een testgebruiker met de naam B.Simon. Eenmalige aanmelding werkt alleen als u een koppeling tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TMWS.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met TMWS, volgt u deze basisstappen:

1. [Eenmalige aanmelding via Azure AD configureren](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) om eenmalige aanmelding via Azure AD te testen.
    1. [De Azure AD-testgebruiker](#grant-the-azure-ad-test-user-access-to-tmws) toegang verlenen tot TMWS.
    1. [Gebruikers- en groepssynchronisatie-instellingen configureren in Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Eenmalige aanmelding bij TMWS configureren](#configure-tmws-sso) aan de kant van de toepassing.
1. [Eenmalige aanmelding testen](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Trend Micro Web Security (TMWS)** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken:

   ![Eenvoudige SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende vakken:

    a. Typ in het vak **Id (entiteits-id)** een URL met de volgende indeling:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Voer in het vak **Antwoord-URL** deze URL in:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > De id-waarde in de vorige stap is niet de waarde die u moet invoeren. U moet u de werkelijke id gebruiken. U kunt deze waarde ophalen in het gedeelte **Serviceproviderinstellingen voor de Azure-beheerportal** op de pagina **Verificatiemethode** voor Azure AD vanuit **Beheer > Adreslijstservices**.

1. In TMWS worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. Deze schermopname toont de standaardkenmerken:

    ![Standaardkenmerken](common/default-attributes.png)

1. Naast de kenmerken in de bovenstaande schermopname, verwacht TMWS nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze kenmerken worden weergegeven in de volgende tabel. Deze kenmerken worden vooraf ingevuld, maar u kunt deze wijzigen zodat ze voldoen aan uw vereisten.
    
    | Naam | Bronkenmerk|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en zoek in de sectie **SAML-handtekeningcertificaat** de optie **Certificaat (Base64)** . Selecteer de Link **Downloaden** naast deze certificaatnaam om het certificaat te downloaden en op uw computer op te slaan:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in het gedeelte **Trend Micro Web Security (TMWS) instellen** de juiste URL('s) op basis van uw behoeften:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**. Selecteer **Gebruikers** en daarna **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het vak **Naam** `B.Simon`in.  
   1. Voer in het vak **Gebruikersnaam** **_gebruikersnaam_@* bedrijfsdomein *.* extensie*** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>De Azure AD-testgebruiker toegang verlenen tot TMWS

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot TMWS.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.
1. Selecteer **Trend Micro Web Security (TMWS)** in de lijst met toepassingen.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren** en selecteer **Gebruikers en groepen**:

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers** en selecteer vervolgens de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Gebruikers- en groepssynchronisatie-instellingen configureren in Azure AD

1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

1. Selecteer **App-registraties** onder **Beheren** en selecteer vervolgens uw nieuwe ondernemingstoepassing onder **Alle toepassingen**.

1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.

1. Onder **Clientgeheimen** selecteert u **Nieuw clientgeheim**.

1. Voeg op het scherm **Een clientgeheim toevoegen** een beschrijving toe en selecteer een verloopperiode voor het clientgeheim. Selecteer vervolgens **Toevoegen**. Het nieuwe clientgeheim wordt weergegeven in het gebied **Clientgeheimen**.

1. Noteer de waarde van het clientgeheim. Later voert u deze in TMWS in.

1. Selecteer onder **Beheren** de optie **API-machtigingen**. 

1. Selecteer in het venster **API-machtigingen** de optie **Een machtiging toevoegen**.

1. Selecteer op het tabblad **Microsoft-API's** van het venster **API-machtigingen aanvragen** de optie **Microsoft Graph** en vervolgens **Toepassingsmachtigingen**.

1. Zoek en voeg deze machtigingen toe: 

    * Group.Read.All
    * User.Read.All

1. Selecteer **Machtigingen toevoegen**. Er wordt een bericht wee gegeven om te bevestigen dat uw instellingen zijn opgeslagen. De nieuwe machtigingen worden weergegeven in het venster **API-machtigingen**.

1. Selecteer in het gedeelte **Toestemming geven** de optie **Beheerderstoestemming geven voor *uw beheerders account* (standaardmap)** en selecteer vervolgens **Ja**. Er wordt een bericht weergegeven om te bevestigen dat de beheerder toestemming voor de aangevraagde machtigingen heeft gekregen.

1. Selecteer **Overzicht**. 

1. Registreer de **Toepassings-id (client)** en de **Map-id (tenant)** die u in het rechterdeelvenster ziet. Later voert u die informatie in TMWS in. U kunt ook **Aangepaste domeinnamen** selecteren onder **Azure Active Directory > Beheren**. Noteer de domeinnaam die u in het rechterdeelvenster ziet.

## <a name="configure-tmws-sso"></a>Eenmalige aanmelding bij TMWS configureren

Voltooi deze stappen om eenmalige aanmelding bij TMWS te configureren aan de kant van de toepassing.

1. Meld u aan bij de TMWS-beheerconsole en ga naar **Beheer** > **GEBRUIKERS EN VERIFICATIE** > **Mapservices**.

1. Selecteer **hier** in het bovenste gedeelte van het scherm.

1. Selecteer op de pagina **Verificatiemethode** de optie **Azure AD**.

1. Selecteer **Aan** of **Uit** om te configureren of Azure AD-gebruikers in uw organisatie websites mogen bezoeken via TMWS als hun gegevens niet zijn gesynchroniseerd met TMWS.

    > [!NOTE]
    > Gebruikers die niet zijn gesynchroniseerd vanuit Azure AD kunnen alleen worden geverifieerd via bekende TMWS-gateways of de toegewezen poort voor uw organisatie.

1. Voer de volgende stappen uit op de pagina **Id-providerinstellingen**:

    a. Typ in het vak **Service-URL** de waarde voor **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    b. Voer in het vak **Aanmeldingsnaam** de **Gebruikersclaimnaam** in met het bronkenmerk **user.onpremisessamaccountname** van Azure Portal.

    c. Ga naar het vak **Openbaar SSL-certificaat** en gebruik het **Certificaat (Base64)** dat u vanuit Azure Portal hebt gedownload.

1. Voer de volgende stappen uit op de pagina **Synchronisatie-instellingen**:

    a. Voer in het vak **Tenant** de **Map-id (tenant) in** of de waarde voor de **Aangepaste domeinnaam** vanuit Azure Portal.

    b. Voer in het vak **Toepassings-id** de waarde voor de **Toepassings-id (client)** in vanuit Azure Portal.

    c. Voer in het vak **Clientgeheim** het **Clientgeheim** van Azure Portal in.

    d. Selecteer **Synchronisatieplanning** om handmatig of volgens een planning te synchroniseren met Azure AD. Als u **Handmatig** selecteert en er wijzigingen zijn in de gegevens van Active Directory-gebruikers, moet u terugkeren naar de pagina **Mapservices** en een handmatige synchronisatie uitvoeren zodat de gegevens in TMWS actueel blijven.

    e. Selecteer **Verbinding testen** om te controleren of de Azure AD-service kan worden verbonden.
    
    f. Selecteer **Opslaan**.
 
 > [!NOTE]
 > Zie [Azure AD-instellingen configureren op TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) voor meer informatie over het configureren van TMWS met Azure AD.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

Nadat u de Azure AD-service hebt geconfigureerd en Azure AD hebt opgegeven als de verificatiemethode voor gebruikers, kunt u zich aanmelden bij de TMWS-proxyserver om uw installatie te controleren. Nadat Azure AD uw account heeft gecontroleerd, kunt u het internet bezoeken.

> [!NOTE]
> TMWS biedt geen ondersteuning voor het testen van eenmalige aanmelding bij uw nieuwe ondernemingstoepassing vanuit de portal van Azure AD via **Overzicht** > **Eenmalige aanmelding** > **Eenmalige aanmelding met SAML instellen** > **Testen**.

1. Wis alle cookies in uw browser en start de browser opnieuw. 

1. Ga in uw browser naar de TMWS-proxyserver. Zie [Verkeer doorsturen via PAC-bestanden](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B) voor meer informatie.

1. Ga naar een website. TMWS leidt u naar de TMWS-portal.

1. Geef een Active Directory-account (indeling: *domein*\\*sAMAccountName* of *sAMAccountName*@*domein*), e-mailadres of UPN op en selecteer vervolgens **Aanmelden**. TMWS stuurt u naar het aanmeldingsvenster van Azure AD.

1. Voer in het aanmeldingsvenster van Azure AD uw Azure AD-accountreferenties in. U bent nu aangemeld bij TMWS.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Trend Micro Web Security met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Trend Micro Web Security beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)