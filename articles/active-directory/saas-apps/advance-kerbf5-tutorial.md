---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met F5 | Microsoft Docs'
description: In dit artikel leert u hoe u F5 kunt integreren met Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: 217872586d09531926b43a13d826efe559c70597
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796759"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory (AD) met F5

In deze zelfstudie leert u hoe u F5 integreert met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot F5.
* Uw gebruikers zich met hun Azure AD-account automatisch laten aanmelden bij F5.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op F5 waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

F5 ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding.

Eenmalige aanmelding bij F5 kan op drie verschillende manieren worden geconfigureerd:

- [Eenmalige aanmelding bij F5 configureren voor geavanceerde Kerberos-toepassing](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Eenmalige aanmelding bij F5 configureren voor op een header gebaseerde toepassing](headerf5-tutorial.md)

- [Eenmalige aanmelding bij F5 configureren voor een Kerberos-toepassing](kerbf5-tutorial.md)

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
1. **[Eenmalige aanmelding bij F5 configureren](#configure-f5-sso)** : hier kunt u de instellingen voor eenmalige aanmelding aan de kant van de toepassing configureren.
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

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

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

## <a name="configure-f5-sso"></a>Eenmalige aanmelding bij F5 configureren

- [Eenmalige aanmelding bij F5 configureren voor op een header gebaseerde toepassing](headerf5-tutorial.md)

- [Eenmalige aanmelding bij F5 configureren voor een Kerberos-toepassing](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Eenmalige aanmelding bij F5 configureren voor geavanceerde Kerberos-toepassing

1. Open een nieuw webbrowservenster en meld u als beheerder bij uw F5 (geavanceerde Kerberos)-bedrijfssite aan en voer de volgende stappen uit:

1. U moet het metagegevenscertificaat importeren in F5 (geavanceerde Kerberos); dit wordt later gebruikt in het installatieproces. Ga naar **Systeem > Certificaatbeheer > Verkeerscertificaatbeheer >> Lijst met SSL-certificaten**. Klik op **Importeren** in de rechterhoek.

    ![Schermopname waarin de knop Import (Importeren) voor het importeren van het metagegevenscertificaat is gemarkeerd.](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Voor het instellen van de SAML IDP gaat u naar **Toegang > Federation > SAML-serviceprovider > Maken > Uit metagegevens**.

    ![Schermopname die laat zien hoe u de SAML IDP maakt uit metagegevens.](./media/advance-kerbf5-tutorial/configure02.png)

    ![Schermopname van het scherm Create New SAML IdP Connector (Nieuwe SAML IdP-connector maken).](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Configuratie van F5 (geavanceerde Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Schermopname van het scherm Single Sign On Service Settings (Instellingen voor eenmalige-aanmeldingsservice). ](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Het certificaat opgeven dat bij taak 3 is geüpload

    ![Schermopname van het scherm Edit SAML IdP Connector (SAML IdP-connector bewerken).](./media/advance-kerbf5-tutorial/configure06.png)

    ![Schermopname van het scherm Single Logout Service Settings (Instellingen voor eenmalige-afmeldingsservice).](./media/advance-kerbf5-tutorial/configure07.png)

 1. Voor het instellen van de SAML SP gaat u naar **Toegang > Federation > SAML Service Federation > Lokale SP-services > Maken**.

    ![Schermopname van het scherm waarin u een lokale SP-service maakt.](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klik op **OK**.

1. Selecteer de SP-configuratie en klik op **IdP-connectors verbinden/verbinding verbreken**.

     ![Schermopname van de SAML-serviceprovider.](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klik op **Nieuwe rij toevoegen** en selecteer de **Externe IdP-connector** die u in de vorige stap hebt gemaakt.

    ![Schermopname met de knop Add New Row (Nieuwe rij toevoegen) gemarkeerd.](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Voor het configureren van Kerberos SSO gaat u naar **Toegang > Eenmalige aanmelding > Kerberos**

    >[!Note]
    >U moet het Kerberos-delegatieaccount maken en opgeven. Verwijzen naar KCD-sectie (verwijzen naar bijlage voor referenties voor variabelen)

    •   Bron voor gebruikersnaam `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    •   Bron voor gebruikersrealm `session.logon.last.domain`

    ![Schermopname met Access (Toegang) > Single Sign On (Eenmalige aanmelding) gemarkeerd.](./media/advance-kerbf5-tutorial/configure11.png)

1. Voor het configureren van een toegangsprofiel gaat u naar **Toegang > Profiel/Beleid > Toegangsprofiel (beleidsregels per sessie)** .

    ![Schermopname met het tabblad Properties (Eigenschappen) onder de menuoptie Profiles/Policies (Profielen/Beleid) geselecteerd.](./media/advance-kerbf5-tutorial/configure12.png)

    ![Schermopname van het tabblad SSO/Auth Domains (SSO/Verficatiedomeinen).](./media/advance-kerbf5-tutorial/configure13.png)

    ![Schermopname van het tabblad Access Policy (Toegangsbeleid).](./media/advance-kerbf5-tutorial/configure14.png)

    ![Scherm opname van het tabblad Properties (Eigenschappen) van het toegangsbeleid.](./media/advance-kerbf5-tutorial/configure15.png)

    ![Scherm opname van de eigenschappen voor Variable Assign (Variabele toewijzen).](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![Schermopname van de eigenschappen van de AD-query.](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Schermopname van het tabblad Branch Rules (Vertakkingsregels) en de regel Check Account (Account controleren).](./media/advance-kerbf5-tutorial/configure18.png)

    ![Schermopname met de tekstvakken Custom Variable (Aangepaste variabele) en Custom Expression (Aangepaste expressie).](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Schermopname van de waarden in de velden SSO Token Name (Naam SSO-token) en SSO Token Password (Wachtwoord SSO-token).](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Voor het toevoegen van een nieuw knooppunt gaat u naar **Lokaal verkeer > Knooppunten > Lijst met knooppunten > +** .

    ![Schermopname met Local Traffic (Lokaal verkeer) > Nodes (Knooppunten) gemarkeerd.](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Voor het maken van een nieuwe pool gaat u naar **Lokaal verkeer > Pools > Lijst met pools > Maken**.

     ![Schermopname met Local Traffic (Lokaal verkeer) > Pools gemarkeerd.](./media/advance-kerbf5-tutorial/configure22.png)

 1. Voor het maken van een nieuwe virtuele server gaat u naar **Lokaal verkeer > Virtuele servers > Lijst met virtuele servers > +** .

    ![Schermopname met Local Traffic (Lokaal verkeer) > Virtual Servers (Virtuele servers) gemarkeerd.](./media/advance-kerbf5-tutorial/configure23.png)

1. Geef het toegangsprofiel op dat in de vorige stap is gemaakt.

    ![Schermopname die laat zien waar u het toegangsprofiel opgeeft dat u hebt gemaakt.](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Kerberos-delegatie instellen 

>[!Note]
>Kijk [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) voor meer details

* **Stap 1: Een delegatieaccount maken**

    * Voorbeeld
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Stap 2: SPN instellen (in het APM-delegatieaccount)**

    *  Voorbeeld
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Stap 3: SPN-delegatie (voor het App Service-account)**

    * Stel de juiste delegatie voor het F5-delegatieaccount in.
    * In het onderstaande voorbeeld wordt een APM-delegatieaccount geconfigureerd voor KCD voor de app FRP-App1.superdemo.live.

        ![Schermopname van APM Delegation Account Properties (Eigenschappen delegatieaccount) > tabblad Delegation (Delegatie).](./media/advance-kerbf5-tutorial/configure25.png)

1. Geef de details op zoals [hier](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) in het bovenstaande referentiedocument wordt vermeld

1. Bijlage - Toewijzingen van SAML – F5 BIG-IP-variabelen wordt hieronder weergegeven:

    ![Schermopname van het tabblad Overview (Overzicht) > Active Sessions (Actieve sessies).](./media/advance-kerbf5-tutorial/configure26.png)

    ![Schermopname van de variabelen en sessiesleutels.](./media/advance-kerbf5-tutorial/configure27.png) 

1. Hieronder ziet u de hele lijst met SAML-standaardkenmerken. GivenName wordt weergegeven met behulp van de volgende tekenreeks.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Sessie | Kenmerk |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

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

- [Eenmalige aanmelding bij F5 configureren voor een Kerberos-toepassing](kerbf5-tutorial.md)

- [F5 BIG-IP APM en Azure AD-integratie voor beveiligde hybride toegang](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Zelf studie voor het implementeren van een VM met een virtuele editie van F5 in azure IaaS voor beveiligde hybride toegang](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-bigip-deployment-guide)

- [Zelf studie voor het Azure Active Directory van eenmalige aanmelding met F5 BIG-IP voor wacht woord-minder VPN](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)
