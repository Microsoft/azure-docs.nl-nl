---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FortiGate SSL VPN | Microsoft Docs'
description: U leert hoe u FortiGate SSL VPN kunt integreren met Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: 9852752799fd010ebb069637f55008d9c4f68bf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732039"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FortiGate SSL VPN

In deze zelfstudie leert u hoe u FortiGate SSL VPN integreert met Azure Active Directory (Azure AD). Wanneer u FortiGate SSL VPN integreert met Azure AD, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen wie toegang heeft tot FortiGate SSL VPN.
* Zorg ervoor dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij FortiGate SSL VPN.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op FortiGate SSL VPN waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="tutorial-description"></a>Beschrijving van zelfstudie

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

FortiGate SSL VPN biedt ondersteuning voor met SP geïnitieerde eenmalige aanmelding.


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>FortiGate SSL VPN toevoegen vanuit de galerie

Voor het configureren van de integratie van FortiGate SSL VPN met Azure AD moet u FortiGate SSL VPN uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.
1. Typ in het gedeelte **Toevoegen uit de galerie** **FortiGate SSL VPN** in het zoekvak.
1. Selecteer **FortiGate SSL VPN** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Eenmalige aanmelding van Azure AD voor FortiGate SSL VPN configureren en testen

U configureert en test eenmalige aanmelding van Azure AD met FortiGate SSL VPN met behulp van een testgebruiker met de naam B. Simon. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FortiGate SSL VPN.

Voltooi de volgende stappen van hoog niveau om eenmalige aanmelding van Azure AD met FortiGate SSL VPN te configureren en te testen:

1. **[Eenmalige aanmelding via Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
    1. **[Toegang verlenen aan de testgebruiker](#grant-access-to-the-test-user)** zodat de gebruiker eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor FortiGate SSL VPN configureren](#configure-fortigate-ssl-vpn-sso)** aan de toepassingszijde.
    1. **Een testgebruiker maken voor FortiGate SSL VPN** als een tegenhanger van de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Selecteer in Azure Portal op de integratiepagina van de toepassing **FortiGate SSL VPN**, in de sectie **Beheren**, de optie **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken:

   ![Schermopname van het potloodpictogram voor het bewerken van de standaard-SAML-configuratie.](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende waarden in:

    a. Voer in het vak **Aanmeldings-URL** een URL met het patroon `https://<FQDN>/remote/login` in.

    b. Voer in het vak **Id** een URL met het patroon `https://<FQDN>/remote/saml/metadata` in.

    c. Voer in het vak **Antwoord-URL** een URL met het patroon `https://<FQDN>/remote/saml/login` in.

    d. Voer in het vak **Afmeldings-URL** een URL met het patroon `https://<FQDN>/remote/saml/logout` in.

    > [!NOTE]
    > Deze waarden zijn slechts patronen. U moet de werkelijke **Aanmeldings-id**, **Id**, **Antwoord-URL** en **Afmeldings-URL** gebruiken. Neem contact op met de [Fortinet-ondersteuning](https://support.fortinet.com) bij vragen. U kunt ook verwijzen naar de voorbeeldpatronen die zijn opgenomen in de documentatie van Fortinet en naar het gedeelte **Standaard-SAML-configuratie** in Azure Portal.

1. In de FortiGate SSL VPN-toepassing worden SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen aan de configuratie toevoegen. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname met de standaardkenmerken.](common/default-attributes.png)

1. De twee extra claims die vereist zijn voor FortiGate SSL VPN, worden weergegeven in de volgende tabel. De namen van deze claims moeten overeenkomen met de namen die worden gebruikt in de sectie **Opdrachtregelconfiguratie voor FortiGate uitvoeren** van deze zelfstudie. 

   | Naam |  Bronkenmerk|
   | ------------ | --------- |
   | gebruikersnaam | user.userprincipalname |
   | group | user.groups |
   
   U kunt deze extra claims als volgt maken:

   a. Selecteer **Bewerken** bij **Gebruikerskenmerken en -claims**.

   b. Selecteer **Nieuwe claim toevoegen**.

   c. Voer bij **Naam** de **gebruikersnaam** in.

   d. Bij **Bronkenmerk** selecteert u **user.userprincipalname**.

   e. Selecteer **Opslaan**.

   f. Selecteer **Een groepsclaim toevoegen**.

   g. Selecteer **Alle groepen**.

   h. Schakel het selectievakje **De naam van de groepsclaim aanpassen** in.

   i. Voer **groep** in bij **Naam**.
   
   j. Selecteer **Opslaan**.   

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en selecteer de koppeling **Downloaden** naast **Certificaat (Base64)** om het certificaat te downloaden en op te slaan op de computer:

    ![Schermopname met de downloadkoppeling voor het certificaat.](common/certificatebase64.png)

1. In het gedeelte **FortiGate SSL VPN instellen** kopieert u de juiste URL('s) op basis van uw behoeften:

    ![Schermopname met de configuratie-URL's.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**. Selecteer **Gebruikers** en daarna **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Ga als volgt te werk in het venster dat verschijnt:
   1. Voer in het vak **Naam** de naam **B.Simon** in.  
   1. Voer in het vak **Gebruikersnaam** \<username>@\<companydomain> in.\<extension>. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

#### <a name="grant-access-to-the-test-user"></a>De testgebruiker toegang geven

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door die gebruiker toegang te verlenen tot FortiGate SSL VPN.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **FortiGate SSL VPN**.
1. Selecteer op de overzichtspagina van de app in de sectie **Beheren** de optie **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers** en selecteer vervolgens de knop **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het scherm op de knop **Selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

#### <a name="create-a-security-group-for-the-test-user"></a>Maak een beveiligingsgroep voor de testgebruiker

In dit gedeelte maakt u een beveiligingsgroep in Azure Active Directory voor de testgebruiker. Deze beveiligingsgroep wordt door FortiGate gebruikt om de gebruiker netwerktoegang via de VPN te verlenen.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**. Selecteer vervolgens **Groepen**.
1. Selecteer bovenaan het scherm **Nieuwe groep**.
1. Voltooi in de eigenschappen van **Nieuwe groep** de volgende stappen:
   1. In de lijst **Groepstype** selecteert u **Beveiliging**.
   1. In het vak **Groepsnaam** voert u **FortiGateAccess** in.
   1. In het vak **Beschrijving van groep** voert u **Groep om FortiGate VPN toegang te verlenen**.
   1. Selecteer **Nee** voor de **Azure AD-rollen kunnen worden toegewezen aan de groep (Preview)** -instellingen.
   1. Selecteer in het vak **Lidmaatschapstype** de optie **Toegewezen**.
   1. Selecteer onder **Leden** de optie **Geen leden geselecteerd**.
   1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B. Simon** in de lijst **Gebruikers**. Klik vervolgens op de knop **Selecteren** onder aan het scherm.
   1. Selecteer **Maken**.
1. Nadat u weer in het gedeelte **Groepen** in Azure Active Directory bent, gaat u naar de groep **FortiGate Access** en noteert u de **object-id**. U hebt deze later nodig.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Eenmalige aanmelding voor FortiGate SSL VPN configureren

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Upload het Base64 SAML-certificaat naar het FortiGate-apparaat

Nadat u de SAML-configuratie van de FortiGate-app in uw tenant hebt voltooid, hebt u het met Base64 gecodeerde SAML-certificaat gedownload. U dient dit certificaat naar het FortiGate-apparaat te uploaden:

1. Log in op het beheerportaal van uw FortiGate-apparaat.
1. In het linkerdeelvenster selecteert u **Systeem**.
1. Onder **System** selecteert u **Certificates**.
1. Selecteer **Import** > **Remote Certificate**.
1. Blader naar het certificaat dat u hebt gedownload uit de implementatie van de FortiGate-app in de Azure-tenant, selecteer het certificaat en selecteer vervolgens **OK**.

Nadat het certificaat is geüpload, noteert u de naam onder **System** > **Certificates** > **Remote Certificate**. Het krijgt standaard de naam REMOTE_Cert_ *N*, waarbij *N* een geheel getal is.

#### <a name="complete-fortigate-command-line-configuration"></a>FortiGate-opdrachtregelconfiguratie voltooien

Voor de volgende stappen dient u de afmeldings-URL van Azure te configureren. Deze URL bevat een vraagteken (?). U dient specifieke stappen uit te voeren om dit teken te kunnen verzenden. U kunt deze stappen niet uitvoeren vanuit de FortiGate CLI-console. Breng in plaats daarvan een SSH-sessie tot stand met het FortiGate-apparaat met behulp van een hulpprogramma als PuTTY. Als uw FortiGate-apparaat een virtuele machine van Azure is, kunt u de volgende stappen voltooien vanaf de seriële console voor de virtuele Azure-machine.

Als u deze stappen wilt uitvoeren, hebt u de eerder genoteerde waarden nodig:

- Entiteits-id
- Antwoord-URL
- Afmeldings-URL
- URL voor aanmelden bij Azure
- Azure AD-id
- URL voor afmelden bij Azure
- Base64 SAML-certificaatnaam (REMOTE_Cert_ *N*)

1. Breng een SSH-sessie tot stand met het FortiGate-apparaat en meld u aan met een FortiGate-administratoraccount.
1. Voer deze opdrachten uit:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > De Azure-afmeldings-URL bevat een `?`-teken. U dient een speciale toetsenreeks in te voeren om de URL correct op te geven voor de seriële FortiGate-console. De URL is doorgaans `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Voer `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` in om de Azure-afmeldings-URL in de seriële console in te voeren
   > 
   > Selecteer vervolgens CTRL+V en plak de rest van de URL om de regel te voltooien: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

#### <a name="configure-fortigate-for-group-matching"></a>FortiGate configureren om groepen te laten matchen

In deze sectie configureert u FortiGate om de object-id te herkennen van de beveiligingsgroep waarin de testgebruiker zich bevindt. Dankzij de configuratie kan FortiGate toegangsbeslissingen nemen op basis van het groepslidmaatschap.

Als u deze stappen wilt voltooien, hebt u de object-id nodig van de FortiGateAccess-beveiligingsgroep die u eerder in deze zelfstudie hebt gemaakt.

1. Breng een SSH-sessie tot stand met het FortiGate-apparaat en meld u aan met een FortiGate-administratoraccount.
1. Voer deze opdrachten uit:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>FortiGate VPN-portals en firewallbeleid maken

In deze sectie configureert u FortiGate VPN-portals en een firewallbeleid, zodat toegang wordt verleend tot de FortiGateAccess-beveiligingsgroep die u eerder in deze zelfstudie hebt gemaakt.

Neem contact op met het [ondersteuningsteam van FortiGate](mailto:tac_amer@fortinet.com) om de VPN-portals en het firewallbeleid toe te voegen aan het FortiGate VPN-platform. U moet deze stap voltooien voordat u eenmalige aanmelding gebruikt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van FortiGate VPN, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van FortiGate VPN en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel FortiGate VPN klikt, wordt u omgeleid naar de aanmeldings-URL van FortiGate VPN. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u FortiGate VPN hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)