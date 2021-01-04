---
title: 'Zelfstudie: Azure Active Directory-integratie met ArcGIS Online | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: b004394d3f0cc1c4d285c9eba9952a43e8cf65f2
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674024"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Zelfstudie: Azure Active Directory-integratie met ArcGIS Online

In deze zelfstudie leert u hoe u ArcGIS Online integreert met Azure Active Directory (Azure AD).
De integratie van ArcGIS Online met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot ArcGIS Online.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ArcGIS Online (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met ArcGIS Online te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op ArcGIS Online waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ArcGIS Online biedt ondersteuning voor eenmalige aanmelding die is gestart vanuit **SP**

## <a name="adding-arcgis-online-from-the-gallery"></a>ArcGIS Online toevoegen vanuit de galerie

Als u de integratie van ArcGIS Online in Azure AD wilt configureren, moet u ArcGIS Online vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om ArcGIS Online toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **ArcGIS Online** in het zoekvak, selecteer **ArcGIS Online** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![ArcGIS Online in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding voor Azure AD met ArcGIS Online op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in ArcGIS Online tot stand is gebracht.

U moet de volgende bouwstenen voltooien om eenmalige aanmelding voor Azure AD met ArcGIS Online te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met ArcGIS Online configureren](#configure-arcgis-online-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[ArcGIS Online-testgebruiker maken](#create-arcgis-online-test-user)** : als u een equivalent van Britta Simon in ArcGISOnline wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure Ad met ArcGIS Online te configureren:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor toepassingsintegratie van **ArcGIS Online** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over ArcGIS Online-domein en URLs voor eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.maps.arcgis.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met [het ArcGIS Online-clientondersteuningsteam](https://support.esri.com/en/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Als u de configuratie met **ArcGIS Online** wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Als u op **ArcGIS Online instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de ArcGIS Online-toepassing. Geef hier de referenties voor de beheerder op om u aan te melden bij ArcGIS Online. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen in het gedeelte **Eenmalige aanmelding met ArcGIS Online configureren** geautomatiseerd.

### <a name="configure-arcgis-online-single-sign-on"></a>Eenmalige aanmelding met ArcGIS Online configureren

1. Als u ArcGIS Online handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de ArcGIS-bedrijfssite. Voer daarna de volgende stappen uit:

2. Klik op **EDIT SETTINGS** (Instellingen bewerken).

    ![Instellingen bewerken](./media/arcgis-tutorial/ic784742.png "Instellingen bewerken")

3. Klik op **Beveiliging**.

    ![Beveiliging](./media/arcgis-tutorial/ic784743.png "Beveiliging")

4. Klik onder **Enterprise Logins** (Enterprise-aanmeldingen) op **SET IDENTITY PROVIDER** (Id-provider instellen).

    ![Enterprise-aanmeldingen](./media/arcgis-tutorial/ic784744.png "Enterprise-aanmeldingen")

5. Voer op de configuratiepagina **Set Identity Provider** (Id-provider instellen)de volgende stappen uit:

    ![Id-provider instellen](./media/arcgis-tutorial/ic784745.png "Id-provider instellen")

    a. Typ de naam van uw organisatie in het tekstvak **Naam**.

    b. Selecteer bij **Metadata for the Enterprise Identity Provider will be supplied using** (Metagegevens voor de Enterprise-id-provider worden verstrekt met behulp van), selecteer **A file** (Een bestand).

    c. Klik op **Choose file** (Bestand kiezen) om het gedownloade metagegevensbestand te uploaden.

    d. Klik op **SET IDENTITY PROVIDER** (Id-provider instellen).

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

In dit gedeelte stelt u Britta Simon in staat om gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot ArcGIS Online.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, **Alle toepassingen** en vervolgens **ArcGIS Online**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **ArcGIS Online** in de lijst met toepassingen.

    ![De koppeling naar ArcGIS Online in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-arcgis-online-test-user"></a>ArcGIS Online-testgebruiker maken

Opdat gebruikers van Azure AD zich kunnen aanmelden bij ArcGIS Online, moeten ze worden ingericht voor ArcGIS Online.  
In het geval van ArcGIS Online is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **ArcGIS**-tenant.

2. Klik op **INVITE MEMBERS** (Leden uitnodigen).

    ![Leden uitnodigen](./media/arcgis-tutorial/ic784747.png "Leden uitnodigen")

3. Selecteer **Add members automatically without sending an email** (Leden automatisch toevoegen zonder een e-mailbericht te verzenden) en klik op **NEXT** (Volgende).

    ![Leden automatisch toevoegen](./media/arcgis-tutorial/ic784748.png "Leden automatisch toevoegen")

4. Voer in het dialoogvenster **Leden** de volgende stappen uit:

    ![Toevoegen en controleren](./media/arcgis-tutorial/ic784749.png "Toevoegen en controleren")

     a. Voer het **e-mailadres**, de **voornaam** en de **achternaam** in van een geldig Azure AD-account dat u wilt inrichten.

     b. Klik op **ADD AND REVIEW** (Toevoegen en controleren).
5. Controleer de gegevens die u hebt ingevoerd en klik op **ADD MEMBERS** (Leden toevoegen).

    ![Lid toevoegen](./media/arcgis-tutorial/ic784750.png "Lid toevoegen")

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel ArcGIS Online in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van ArcGIS Online waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)