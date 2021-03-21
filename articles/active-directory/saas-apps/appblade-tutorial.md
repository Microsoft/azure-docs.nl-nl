---
title: 'Zelfstudie: Azure Active Directory-integratie met AppBlade | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AppBlade.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 49fa266319423b4c1a0bc56ad2a32b49c18f168e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672622"
---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Zelfstudie: Azure Active Directory-integratie met AppBlade

In deze zelfstudie leert u hoe u AppBlade integreert met Azure AD (Azure Active Directory).
De integratie van AppBlade met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot AppBlade.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij AppBlade (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met AppBlade, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement waarvoor AppBlade-eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AppBlade biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding
* AppBlade biedt ondersteuning voor de **Just-In-Time**-inrichting van gebruikers

## <a name="adding-appblade-from-the-gallery"></a>AppBlade toevoegen vanuit de galerie

Als u de integratie van AppBlade in Azure AD wilt configureren, moet u AppBlade vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om AppBlade toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **AppBlade**, selecteer **AppBlade** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![AppBlade in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u Azure AD-eenmalige aanmelding met AppBlade op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in AppBlade.

Als u Azure AD-eenmalige aanmelding met AppBlade wilt configureren en testen, voltooit u de volgende bouwstenen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[AppBlade-eenmalige aanmelding configureren](#configure-appblade-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[AppBlade-testgebruiker maken](#create-appblade-test-user)**: als u een tegenhanger van Britta Simon wilt in AppBlade die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u Azure AD-eenmalige aanmelding met AppBlade wilt configureren, voert u de volgende stappen uit:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **AppBlade**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over het domein en URL’s voor AppBlade-eenmalige aanmelding](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.appblade.com/saml/<tenantid>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam voor AppBlade](mailto:support@appblade.com) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **AppBlade instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-appblade-single-sign-on"></a>AppBlade-eenmalige aanmelding configureren

Als u eenmalige aanmelding aan de zijde van **AppBlade** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam voor AppBlade](mailto:support@appblade.com). Vraag het team ook om de **URL voor de SSO-verlener** te configureren als `https://appblade.com/saml`. Deze instelling is vereist om eenmalige aanmelding te laten werken.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.

    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van Azure-eenmalige aanmelding door haar toegang te geven tot AppBlade.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **AppBlade**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **AppBlade** in de lijst met toepassingen.

    ![De AppBlade-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-appblade-test-user"></a>AppBlade-testgebruiker maken

Het doel van deze sectie is om in de AppBlade een gebruiker met de naam Britta Simon te maken. AppBlade biedt ondersteuning voor Just-In-Time-inrichting. Dit is standaard ingeschakeld. **Zorg ervoor dat uw domeinnaam is geconfigureerd met AppBlade voor het inrichten van gebruikers. Hierna werkt de Just-In-Time-inrichting pas.**

Als de gebruiker een e-mailadres heeft dat eindigt op het domein dat is geconfigureerd met AppBlade voor uw account, is deze gebruiker automatisch lid van het account met het door u opgegeven machtigingsniveau. Dit niveau kan zijn: Basic (een gebruiker die alleen toepassingen kan installeren), Teamlid (een gebruiker die nieuwe app-versies kan uploaden en projecten kan beheren), of Beheerder (een gebruiker met volledige beheerdersbevoegdheden voor het account). U wordt geadviseerd om Basic te kiezen en het niveau voor gebruikers vervolgens handmatig te verhogen via een beheerdersaanmelding. (In AppBlade moet vooraf een beheerdersaanmelding worden geconfigureerd, of na de aanmelding moet het niveau van een gebruiker namens de klant worden verhoogd.)

Er is geen actie-item voor u in deze sectie. Er wordt eventueel een nieuwe gebruiker gemaakt bij een poging AppBlade te openen.

> [!NOTE]
> Neem contact op met het [ondersteuningsteam voor AppBlade](mailto:support@appblade.com) als u handmatig een gebruiker moet maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel AppBlade in het toegangsvenster klikt, wordt u automatisch aangemeld bij het AppBlade-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)