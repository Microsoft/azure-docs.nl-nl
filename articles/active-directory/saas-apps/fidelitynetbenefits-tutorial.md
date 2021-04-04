---
title: 'Zelfstudie: Azure Active Directory-integratie met Fidelity NetBenefits | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Fidelity NetBenefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: ac41bf89a3c2997fe0c179ef1a64787b5fb8d617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453583"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Zelfstudie: Azure Active Directory-integratie met Fidelity NetBenefits

In deze zelfstudie leert u hoe u Fidelity NetBenefits kunt integreren met Azure Active Directory (Azure AD).
De integratie van Fidelity NetBenefits met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot Fidelity NetBenefits.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Fidelity NetBenefits (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure Active Directory-integratie te configureren met Fidelity NetBenefits hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Fidelity NetBenefits-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Fidelity NetBenefits ondersteunt door **IDP** geïnitieerde SSO

* Fidelity NetBenefits ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Fidelity NetBenefits uit de galerie toevoegen

Voor het configureren van de integratie van Fidelity NetBenefits in Azure Active Directory, moet u Fidelity NetBenefits uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Fidelity NetBenefits wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Fidelity NetBenefits**, selecteer **Fidelity NetBenefits** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Fidelity NetBenefits in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure Active Directory bij Fidelity NetBenefits configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Fidelity NetBenefits tot stand is gebracht.

Om eenmalige aanmelding met Azure Active Directory bij Fidelity NetBenefits te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Fidelity NetBenefits configureren](#configure-fidelity-netbenefits-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Fidelity NetBenefits maken](#create-fidelity-netbenefits-test-user)**: als u een tegenhanger van Britta Simon in Fidelity NetBenefits wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding met Azure Active Directory wilt configureren bij Fidelity NetBenefits:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Fidelity NetBenefits** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Fidelity NetBenefits](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    Voor de testomgeving:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Voor de productieomgeving: `urn:sp:fidelity:geninbndnbparts20`

    b. In het tekstvak **Antwoord-URL** typt u een URL die wordt geleverd door Fidelity op het moment van implementatie, of neem contact op met uw toegewezen Fidelity Client Service Manager.

5. In de Fidelity NetBenefits-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. De Fidelity NetBenefits-toepassing verwacht dat **nameidentifier** wordt toegewezen met **employeeid** of een andere claim die van toepassing is op uw organisatie als **nameidentifier**, dus u moet de kenmerktoewijzing bewerken door te klikken op het pictogram **Bewerken** en de kenmerktoewijzing wijzigen.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits-ondersteuning voor statische en dynamische federatie. Statisch betekent dat SAML geen Just-in-Time-gebruikersinrichting gebruikt en dynamisc betekent dat SAML ondersteuning biedt voor Just-in-Time gebruikersinrichting. Voor het gebruik van inrichten op basis van JIT moeten klanten meer claims toevoegen in Azure Active Directory, zoals de geboortedatum van de gebruiker, enzovoort. Deze gegevens worden geleverd door uw toegewezen **Fidelity Client-Service Manager** en ze moeten deze dynamische federatie voor uw exemplaar inschakelen.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In het gedeelte **Fidelity NetBenefits instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Eenmalige aanmelding bij Fidelity NetBenefits configureren

Als u eenmalige aanmelding aan de zijde van **Fidelity NetBenefits** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Azure-portal gekopieerde URL's naar het [ondersteuningsteam van Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Fidelity NetBenefits.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Fidelity NetBenefits**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Fidelity NetBenefits** in de lijst met toepassingen.

    ![De Fidelity NetBenefits-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-fidelity-netbenefits-test-user"></a>Fidelity NetBenefits-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Fidelity NetBenefits. Als u statische federatie maakt, neemt u contact op met uw toegewezen **Fidelity Client-Service Manager** om gebruikers aan te maken in het Fidelity NetBenefits-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

Gebruikers worden gemaakt met behulp van Just-In-Time-inrichten voor dynamische Federatie. Voor het gebruik van inrichten op basis van JIT moeten klanten meer claims toevoegen in Azure Active Directory, zoals de geboortedatum van de gebruiker, enzovoort. Deze gegevens worden geleverd door uw toegewezen **Fidelity Client-Service Manager** en ze moeten deze dynamische federatie voor uw exemplaar inschakelen.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Fidelity NetBenefits in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Fidelity NetBenefits waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)