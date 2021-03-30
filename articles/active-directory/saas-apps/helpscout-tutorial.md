---
title: 'Zelfstudie: Azure Active Directory-integratie met Help Scout | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Help Scout.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7c5e8210bc8b805d72149fd2ef3335c1d637a58f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445185"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Zelfstudie: Azure Active Directory-integratie met Help Scout

In deze zelfstudie leert u hoe u Help Scout kunt integreren met Azure Active Directory (Azure AD).
De integratie van Help Scout met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Help Scout heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Help Scout (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Help Scout hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Help Scout waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Help Scout ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Help Scout biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-help-scout-from-the-gallery"></a>Help Scout toevoegen vanuit de galerie

Voor het configureren van de integratie van Help Scout met Azure AD moet u Help Scout uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **Help Scout**.
1. Selecteer **Help Scout** uit het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure Active Directory met Help Scout op basis van een testgebruiker met de naam **B.Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Help Scout tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij Help Scout moet u de volgende bouwstenen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Help Scout configureren](#configure-help-scout-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een testgebruiker voor Help Scout maken](#create-help-scout-test-user)** : als u een tegenhanger van B.Simon in Help Scout wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Help Scout moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Help Scout** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

1. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. **Id** is de **doelgroep-URI (identiteits-id van de serviceprovider)** van Help Scout, die begint met `urn:`

    b. **Antwoord-URL** is de **terugpost-URL (URL voor Assertion Consumer Service)** van Help Scout, die begint met `https://` 

    > [!NOTE]
    > De waarden in deze URL's zijn alleen ter demonstratie. U moet deze waarden wijzigen in de werkelijke antwoord-URL en id. U vindt deze waarden op het tabblad **Eenmalige aanmelding** in de sectie Verificatie, die verderop in de zelfstudie wordt beschreven.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    Typ een URL in het tekstvak **Aanmeldings-URL**, bijvoorbeeld: `https://secure.helpscout.net/members/login/`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Help Scout instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in Azure Portal een testgebruiker met de naam B.Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Typ **B.Simon** in het veld **Naam**.
  
    b. In het veld **Gebruikersnaam** typt u **B.Simon\@yourcompanydomain.extension**  
    Bijvoorbeeld: B.Simon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Help Scout.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Help Scout**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Help Scout** in de lijst met toepassingen.

    ![De Help Scout-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-help-scout-sso"></a>Eenmalige aanmelding voor Help Scout configureren

1. Als u de configuratie in Help Scout wilt automatiseren, moet u de **browseruitbreiding Beveiligde aanmelding voor Mijn Apps** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **Help Scout instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Help Scout-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Help Scout. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Help Scout handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Help Scout-bedrijfssite. Voer hierna de volgende stappen uit:

1. Klik op **Beheren** in het bovenste menu en selecteer vervolgens **Bedrijf** in de vervolgkeuzelijst.

    ![Schermopname die het menu Beheren toont met Bedrijf geselecteerd.](./media/helpscout-tutorial/settings1.png)

1. Selecteer **Verificatie** in het navigatievenster links.

    ![Schermopname waarop Verificatie is geselecteerd.](./media/helpscout-tutorial/settings2.png)

1. De sectie SAML-instellingen wordt weergegeven. Voer de volgende stappen uit:

    ![Schermopname met het tabblad Eenmalige aanmelding waarop u de opgegeven informatie invoert.](./media/helpscout-tutorial/settings3.png)

    a. Kopieer de waarde van de **terugpost-URL (URL voor Assertion Consumer Service)** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    b. Kopieer de waarde van de **doelgroep-URI (entiteits-id van de serviceprovider)** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Schakel **SAML inschakelen** in en voer de volgende stappen uit:

    ![Schermopname met het tabblad Eenmalige aanmelding waarop u SAML inschakelt en andere informatie toevoegt.](./media/helpscout-tutorial/settings4.png)

    a. Plak in het tekstvak **Eenmalige aanmelding-URL** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd vanuit Azure Portal.

    b. Klik op **Certificaat uploaden** om het **certificaat (Base64)** te uploaden dat u uit de Azure-portal hebt gedownload.

    c. Geef uw organisatie-e-maildomein(en), bijvoorbeeld `contoso.com`, op in het tekstvak **E-maildomeinen**. U kunt meerdere domeinen opgeven door ze te scheiden met een komma. Telkens wanneer gebruikers of beheerders van Help Scout dat specifieke domein benaderen op de [aanmeldingspagina van Help Scout](https://secure.helpscout.net/members/login/), worden zij omgeleid naar de identiteitsprovider om zich te verifiëren met hun referenties.

    d. Ten slotte kunt u **SAML-aanmelding afdwingen** als u wilt dat gebruikers zich alleen op deze manier bij Help Scout kunnen aanmelden. Als u wilt dat ze zich ook nog met hun referenties voor Help Scout kunnen aanmelden, laat u deze optie uitgeschakeld. Zelfs als deze optie is ingeschakeld, kunnen accounteigenaars zich nog altijd bij Help Scout aanmelden met het wachtwoord van hun account.

    e. Klik op **Opslaan**.

### <a name="create-help-scout-test-user"></a>Help Scout-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Help Scout. Help Scout biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Help Scout bestaat, wordt er een nieuwe gemaakt na de verificatie.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Help Scout klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Help Scout waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Help Scout proberen met Azure AD](https://aad.portal.azure.com/)