---
title: 'Zelfstudie: Integratie van Azure Active Directory met RStudio Connect SAML Authentication | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en RStudio Connect SAML Authentication kunt configureren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182388"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Zelfstudie: Integratie van Azure Active Directory met RStudio Connect SAML Authentication

In deze zelfstudie leert u hoe u RStudio Connect SAML Authentication integreert met Azure Active Directory (Azure AD).
Integratie van RStudio Connect SAML Authentication met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot RStudio Connect SAML Authentication.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij RStudio Connect SAML Authentication (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met RStudio Connect SAML Authentication hebt u het volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* RStudio Connect SAML Authentication Er is een [gratis proefperiode van 45 dagen.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RStudio Connect SAML Authentication ondersteunt met **SP en IDP** geïnitieerde eenmalige aanmelding

* RStudio Connect SAML Authentication biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>RStudio Connect SAML Authentication toevoegen vanuit de galerie

Voor het configureren van de integratie van RStudio Connect SAML Authentication met Azure AD moet u RStudio Connect SAML Authentication vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **Toevoegen uit de galerie** **RStudio Connect SAML Authentication** in het zoekvak.
1. Selecteer **RStudio Connect SAML Authentication** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Eenmalige aanmelding van Azure AD voor RStudio Connect SAML Authentication configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met RStudio Connect SAML Authentication met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RStudio Connect SAML Authentication.

Om eenmalige aanmelding van Azure AD te configureren en te testen met RStudio Connect SAML Authentication, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    * **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding voor RStudio Connect SAML Authentication configureren](#configure-rstudio-connect-saml-authentication-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[RStudio Connect SAML Authentication-testgebruiker maken](#create-rstudio-connect-saml-authentication-test-user)** : als tegenhanger van Britta Simon in RStudio Connect SAML Authentication die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **RStudio Connect SAML Authentication**, het gedeelte **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren, waarbij u `<example.com>` vervangt door het adres en de poort van uw RStudio Connect SAML Authentication-server:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij RStudio Connect SAML Authentication](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<example.com>/__login__/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<example.com>/__login__/saml/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![RStudio Connect SAML Authentication-metagegevens uploaden](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<example.com>/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Deze worden bepaald op basis van het RStudio Connect SAML Authentication-serveradres (`https://example.com` in de bovenstaande voorbeelden). Neem contact op met het [ondersteuningsteam van RStudio Connect SAML Authentication](mailto:support@rstudio.com) als u problemen ondervindt. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Uw RStudio Connect SAML Authentication-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de RStudio Connect SAML Authentication-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot RStudio Connect SAML Authentication.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **RStudio Connect SAML Authentication** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Eenmalige aanmelding in RStudio Connect SAML Authentication configureren

Als u eenmalige aanmelding wilt configureren voor **RStudio Connect SAML Authentication**, moet u de **App-URL voor federatieve metagegevens** en het **Serveradres** gebruiken die hierboven zijn gebruikt. Dit doet u in het RStudio Connect SAML Authentication-configuratiebestand in `/etc/rstudio-connect.rstudio-connect.gcfg`.

Dit is een voorbeeld van een configuratiebestand:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Sla uw **Serveradres** op in de waarde `Server.Address` en de **App-URL voor federatieve metagegevens** in de waarde `SAML.IdPMetaData`. Let op: in deze voorbeeldconfiguratie wordt een niet-versleutelde HTTP-verbinding gebruikt, terwijl Azure AD het gebruik van een versleutelde HTTPS-verbinding vereist. U kunt een [omgekeerde proxy](https://docs.rstudio.com/connect/admin/proxy/) gebruiken vóór RStudio Connect SAML Authentication of RStudio Connect SAML Authentication configureren om [HTTPS direct te gebruiken](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Als u problemen ondervindt met de configuratie, kunt u de [beheerdershandleiding voor RStudio Connect SAML Authentication](https://docs.rstudio.com/connect/admin/authentication/saml/) lezen of een e-mail sturen aan het [ondersteuningsteam van RStudio](mailto:support@rstudio.com) voor hulp.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Testgebruiker voor RStudio Connect SAML Authentication maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in RStudio Connect SAML Authentication. RStudio Connect SAML Authentication biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in RStudio Connect SAML Authentication, wordt er een nieuwe gemaakt wanneer u RStudio Connect SAML Authentication opent.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van RStudio Connect SAML Authentication, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van RStudio Connect SAML Authentication om de aanmeldingsstroom daar te initiëren.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in de Azure-portal. U wordt automatisch aangemeld bij de instantie van RStudio Connect SAML Authentication waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel van RStudio Connect SAML Authentication in het toegangsvenster klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van RStudio Connect SAML Authentication waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u RStudio Connect SAML Authentication hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).