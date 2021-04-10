---
title: 'Zelfstudie: Azure Active Directory-integratie met Andromeda | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736035"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Zelfstudie: Azure Active Directory-integratie met Andromeda

In deze zelfstudie leert u hoe u Andromeda integreert met Azure Active Directory (Azure AD).
De integratie van Andromeda met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Andromeda.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Andromeda (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Andromeda te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Andromeda waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Andromeda ondersteunt eenmalige aanmelding via **SP en IDP**
* Andromeda biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-andromeda-from-the-gallery"></a>Andromeda toevoegen vanuit de galerie

Als u de integratie van Andromeda met Azure AD wilt configureren, voegt u Andromeda vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Andromeda** in het zoekvak.
1. Selecteer **Andromeda** in het resultatenvenster en voeg daarna de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Andromeda

Eenmalige aanmelding van Azure AD configureren en testen voor Andromeda met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Andromeda.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor Andromeda:


1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding voor Andromeda configureren](#configure-andromeda-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Andromeda-testgebruiker maken](#create-andromeda-test-user)** : als u een tegenhanger van Britta Simon wilt maken in Andromeda die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Andromeda**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<tenantURL>.ngcxpress.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. U gaat deze waarden bijwerken met de daadwerkelijke id, antwoord-URL en aanmeldings-URL. Dit wordt later in de zelfstudie uitgelegd.

6. De Andromeda-toepassing verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname met standaardwaarden zoals givenname user.givenname en emailaddress user.mail.](common/edit-attribute.png)

    > [!Important]
    > Wis de NameSpace-definities wanneer u deze instelt.

7. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit: 

    | Naam | Bronkenmerk|
    | ------ | -----------|
    | role        | App-specifieke rol |
    | type        | App-type |
    | bedrijf       | CompanyName |

    > [!NOTE]
    > Andromeda verwacht rollen voor gebruikers die zijn toegewezen aan de toepassing. Stel deze rollen in Azure AD in zodat gebruikers de juiste rollen toegewezen kunnen krijgen. Zie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)voor meer informatie over het configureren van rollen in Azure AD.

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname met Gebruikersclaims met opties Nieuwe claim toevoegen en Opslaan.](common/new-save-attribute.png)

    ![Schermopname met het dialoogvenster Gebruikersclaims beheren, waarin u de waarden kunt invoeren die worden beschreven in deze stap.](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

9. In de sectie **Andromeda instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Andromeda.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Andromeda** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-andromeda-sso"></a>Eenmalige aanmelding voor Andromeda configureren

1. Meld u bij uw Andromeda-bedrijfssite aan als beheerder.

2. Klik bovenaan de menubalk op **Beheerder** en navigeer naar **Beheer**.

    ![Andromeda-beheerder](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Klik aan links op de werkbalk onder de sectie **Interfaces** op **SAML-configuratie**.

    ![Andromeda-SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Voer de volgende stappen uit op de sectiepagina **SAML-configuratie**:

    ![Andromeda-configuratie](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Schakel **Eenmalige aanmelding met SAML inschakelen** in.

    b. Kopieer onder de sectie **Andromeda-gegevens** waarde van de **SP-identiteit** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie**.

    c. Kopieer de waarde **Consument-URL** en plak deze in het tekstvak **Antwoord-URL** van de sectie **Standaard SAML-configuratie**.

    d. Kopieer de waarde **Aanmeldings-URL** en plak deze in het tekstvak **Aanmeldings-URL** van de sectie **Standaard SAML-configuratie**.

    e. Typ uw IDP-naam onder **SAML-identiteitsprovider**.

    f. Plak in het tekstvak **Eindpunt met eenmalige aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    g. Open het **gecodeerde Base64-certificaat** dat u hebt gedownload uit de Microsoft Azure-portal in Kladblok en plak het in het tekstvak **X 509-certificaat**.
    
    h. Wijs de volgende kenmerken toe met de relevante waarde om eenmalige aanmelding vanuit Azure AD mogelijk te maken. Het kenmerk **Gebruikers-id** is vereist voor het aanmelden. Voor het inrichten zijn **E-mailadres**, **Bedrijf**, **Gebruikerstype** en **Rol** vereist. In deze sectie definiëren we de kenmerktoewijzingen (naam en waarden) die overeenkomen met de gedefinieerde items in de Azure-portal

    ![Kenmerktoewijzing voor Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klik op **Opslaan**.


### <a name="create-andromeda-test-user"></a>Een testgebruiker voor Andromeda maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Andromeda. Andromeda biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Andromeda bestaat, wordt er een nieuwe gemaakt na verificatie. Neem contact op met het [ondersteuningsteam van Andromeda](https://www.ngcsoftware.com/support/) als u handmatig een gebruiker wilt maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Andromeda, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Andromeda en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij de instantie van Andromeda waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in 'Mijn apps' op de tegel 'Andromeda' klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het Andromeda-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Andromeda hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
