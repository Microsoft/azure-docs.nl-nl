---
title: 'Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks - Admin UI | Microsoft Docs'
description: Hier vindt u informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks Admin UI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 57b1d47fa40c0af4bced1e4169fe60cd759ee2f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963635"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks - Admin UI

In deze zelfstudie leert u hoe u Palo Alto Networks - Admin UI integreert met Azure Active Directory (Azure AD).
De integratie van Palo Alto Networks - Admin UI met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Palo Alto Networks - Admin UI heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Palo Alto Networks - Admin UI (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Palo Alto Networks - Admin UI te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor Palo Alto Networks - Admin UI waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Palo Alto Networks - Admin UI ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Palo Alto Networks - Admin UI ondersteunt **just-in-time**-gebruikersinrichting

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Palo Alto Networks - Admin UI toevoegen vanuit de galerie

Als u de integratie van Palo Alto Networks - Admin UI met Azure AD wilt configureren, moet u Palo Alto Networks - Admin UI vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Palo Alto Networks - Admin UI** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Palo Alto Networks - Admin UI** uit het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Palo Alto Networks - Admin UI op basis van een testgebruiker met de naam **B.Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Palo Alto Networks - Admin UI tot stand is gebracht.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Palo Alto Networks – Admin UI te configureren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Palo Alto Networks - Admin UI configureren](#configure-palo-alto-networks---admin-ui-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor Palo Alto Networks - Admin UI maken](#create-palo-alto-networks---admin-ui-test-user)** : als u een tegenhanger van B.Simon in Palo Alto Networks - Admin UI wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal naar de toepassingsintegratiepagina van **Palo Alto Networks – Admin UI**, zoek de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<Customer Firewall FQDN>/php/login.php`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. In he tekstvak **Antwoord-URL** typt u de URL van ACS (Assertion Consumer Service) in de volgende indeling: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.
    >
    > Poort 443 is vereist voor de **Id** en de **Antwoord-URL**, aangezien deze waarden vastliggen in de Palo Alto Firewall. Wanneer het poortnummer wordt verwijderd, treedt er een fout op tijdens het aanmelden.

    > Poort 443 is vereist voor de **Id** en de **Antwoord-URL**, aangezien deze waarden vastliggen in de Palo Alto Firewall. Wanneer het poortnummer wordt verwijderd, treedt er een fout op tijdens het aanmelden.

1. De toepassing Palo Alto Networks - Admin UI verwacht de SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Omdat de kenmerkwaarden slechts voorbeelden zijn, moet u de juiste waarden opgeven voor *username* en *adminrole*. Er is een ander optioneel kenmerk, *accessdomain*, dat wordt gebruikt om beheerderstoegang tot specifieke virtuele systemen in de firewall te beperken.

1. Bovendien verwacht de toepassing Palo Alto Networks - Admin UI dat er nog een paar kenmerken als SAML-antwoord worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | --- | --- |
    | gebruikersnaam | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > De waarde _adminrole_ moet gelijk zijn aan de rolnaam die is geconfigureerd in **Palo Alto Networks**, zoals vermeld in stap 9. 

    > [!NOTE]
    > Zie de volgende artikelen voor meer informatie over de kenmerken:
    > * [Configure an Admin Role Profile (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Device > Access Domain (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In het gedeelte **Palo Alto Networks - Admin UI instellen** kopieert u de URL('s) die u nodig hebt.

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

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Palo Alto Networks - Admin UI.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Palo Alto Networks - Admin UI** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>Eenmalige aanmelding voor Palo Alto Networks - Admin UI configureren

1. Open de gebruikersinterface voor firewallbeheer van Palo Alto Networks als beheerder in een nieuw browservenster.

2. Selecteer het tabblad **Device**.

    ![Het tabblad Device](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecteer **SAML Identity Provider** in het linkerdeelvenster en selecteer vervolgens **Import** om het metagegevensbestand te importeren.

    ![De knop voor het importeren van het bestand met metagegevens](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Ga als volgt te werk in het venster **SAML Identify Provider Server Profile Import**:

    ![Het venster SAML Identify Provider Server Profile Import](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Typ een naam in het vak **Profile Name** (bijvoorbeeld **AzureAD Admin UI**).

    b. Selecteer **Browse** naast **Identity Provider Metadata** en selecteer het bestand metadata.xml file dat u eerder uit de Azure-portal hebt gedownload.

    c. Schakel het selectievakje **Validate Identity Provider Certificate** uit.

    d. Selecteer **OK**.

    e. Selecteer **Commit** om de configuraties door te voeren op de firewall.

5. Selecteer in het linkerdeelvenster **SAML Identity Provider** en selecteer vervolgens het profiel van de SAML-identiteitsprovider (bijvoorbeeld **AzureAD Admin UI**) dat u in de vorige stap hebt gemaakt.

    ![Het profiel van de SAML-identiteitsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Ga als volgt te werk in het venster **SAML Identify Provider Server**:

    ![Het venster SAML Identify Provider Server](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Vervang in het vak **Identity Provider SLO URL** de eerder geïmporteerde SLO-URL door de volgende URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecteer **OK**.

7. Selecteer in de gebruikersinterface voor firewallbeheer van Palo Alto Networks **Device** en selecteer vervolgens **Admin Roles**.

8. Selecteer de knop **Add**.

9. Geef in het venster **Admin Role Profile**, in het vak **Name**, een naam op voor de beheerdersrol (bijvoorbeeld **fwadmin**). De naam van de beheerdersrol moet overeenkomen met de naam van het kenmerk SAML Admin Role dat is verzonden door de id-provider. De naam en waarde van de beheerdersrol zijn gemaakt in de sectie **Gebruikerskenmerken** in de Azure-portal.

    ![Beheerdersrol van Palo Alto Networks configureren](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Selecteer in de gebruikersinterface voor firewallbeheer **Device** en vervolgens **Authentication Profile**.

11. Selecteer de knop **Add**.

12. Ga als volgt te werk in het venster **Authentication Profile**: 

    ![Het venster Authentication Profile](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Typ een naam in het vak **Name** (bijvoorbeeld **AzureSAML_Admin_AuthProfile**).

    b. Selecteer **SAML** in de vervolgkeuzelijst **Type**. 

    c. Selecteer in de vervolgkeuzelijst **IdP Server Profile** het juiste serverprofiel van de SAML-identiteitsprovider (bijvoorbeeld **AzureAD Admin UI**).

    c. Schakel het selectievakje **Enable Single Logout** in.

    d. Voer in het vak **Admin Role Attribute** de naam van het kenmerk in (bijvoorbeeld **adminrole**).

    e. Selecteer het tabblad **Advanced** en selecteer vervolgens **Add** onder **Allow List**.

    ![De knop Add op het tabblad Advanced](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Schakel het selectievakje **All** in of selecteer de gebruikers en groepen die kunnen worden geverifieerd met dit profiel.  
    Wanneer een gebruiker zich wil verifiëren, vergelijkt de firewall de gekoppelde gebruikersnaam of groep met de vermeldingen in deze lijst. Als u geen vermeldingen toevoegt, kunnen er geen gebruikers worden geverifieerd.

    g. Selecteer **OK**.

13. Om beheerders in staat te stellen eenmalige aanmelding van SAML te gebruiken via Azure, selecteert u **Device** > **Setup**. Selecteer in het deelvenster **Setup** het tabblad **Management** en selecteer vervolgens onder **Authentication Settings** de knop **Settings** (tandwielpictogram).

    ![De knop Settings](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selecteer het profiel voor SAML-verificatie dat u in het venster Authentication Profile hebt gemaakt (bijvoorbeeld **AzureSAML_Admin_AuthProfile**).

    ![Het veld Authentication Profile](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selecteer **OK**.

16. Selecteer **Commit** om de configuratie vast te leggen.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Testgebruiker voor Palo Alto Networks - Admin UI maken

Palo Alto Networks - Admin UI ondersteunt just-in-time-gebruikersinrichting. Als een gebruiker nog niet bestaat, wordt deze na geslaagde verificatie automatisch gemaakt in het systeem. Er is geen actie van u vereist om de gebruiker te maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Palo Alto Networks – Admin UI, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Palo Alto Networks – Admin UI en initieer daar de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Palo Alto Networks - Admin UI klikt in Mijn apps, wordt u automatisch aangemeld bij het exemplaar van Palo Alto Networks - Admin UI waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Palo Alto Networks hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).