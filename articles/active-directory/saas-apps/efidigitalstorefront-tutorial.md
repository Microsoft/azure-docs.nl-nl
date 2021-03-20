---
title: 'Zelfstudie: Azure Active Directory-integratie met EFI Digital StoreFront | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en EFI Digital StoreFront.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: jeedes
ms.openlocfilehash: c4a07d676f1cb0f47d1eff1dd8e0321bbd407092
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454249"
---
# <a name="tutorial-azure-active-directory-integration-with-efi-digital-storefront"></a>Zelfstudie: Azure Active Directory-integratie met EFI Digital StoreFront

In deze zelfstudie leert u hoe u EFI Digital StoreFront kunt integreren met Azure Active Directory (Azure AD).
De integratie van EFI Digital StoreFront met Azure Active Directory heeft de volgende voordelen:

* U kunt in Azure Active Directory beheren wie toegang heeft tot EFI Digital StoreFront.
* U kunt uw gebruikers zich automatisch laten aanmelden bij EFI Digital StoreFront (eenmalige aanmelding) met hun Azure Active Directory-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure Active Directory-integratie met EFI Digital StoreFront hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op EFI Digital StoreFront waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* EFI Digital StoreFront ondersteunt door **SP**-geïnitieerde eenmalige aanmelding

## <a name="adding-efi-digital-storefront-from-the-gallery"></a>EFI Digital StoreFront uit de galerie toevoegen

Voor het configureren van de integratie van EFI Digital StoreFront in Azure Active Directory moet u EFI Digital StoreFront uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om EFI Digital StoreFront toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **EFI Digital StoreFront** in het zoekvak, selecteer **EFI Digital StoreFront** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![EFI Digital StoreFront in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure Active Directory met EFI Digital StoreFront op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in EFI Digital StoreFront tot stand is gebracht.

Om eenmalige aanmelding van Azure Active Directory met EFI Digital StoreFront te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor EFI Digital StoreFront configureren](#configure-efi-digital-storefront-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken in EFI Digital StoreFront](#create-efi-digital-storefront-test-user)**: om in EFI Digital StoreFront een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory te configureren met EFI Digital StoreFront:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **EFI Digital StoreFront**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij EFI Digital StoreFront](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.myprintdesk.net/DSF`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.myprintdesk.net/DSF/asp4/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van EFI Digital StoreFront](https://www.efi.com/products/productivity-software/ecommerce-web-to-print/efi-digital-storefront/support/) om deze waardes te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In het gedeelte **EFI Digital StoreFront instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-efi-digital-storefront-single-sign-on"></a>Eenmalige aanmelding met EFI Digital configureren

Om eenmalige aanmelding te configureren aan de kant van **EFI Digital StoreFront**, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste gekopieerde URL's uit de Azure Portal verzenden naar het [ondersteuningsteam van EFI Digital StoreFront](https://www.efi.com/products/productivity-software/ecommerce-web-to-print/efi-digital-storefront/support/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot EFI Digital StoreFront.

1. Selecteer in de Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **EFI Digital StoreFront**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **EFI Digital StoreFront**.

    ![De EFI Digital StoreFront-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-efi-digital-storefront-test-user"></a>EFI Digital StoreFront-testgebruiker maken

In deze sectie gaat u een gebruiker met de naam Britta Simon maken in EFI Digital StoreFront. Neem contact op met het [ondersteuningsteam van EFI Digital StoreFront](https://www.efi.com/products/productivity-software/ecommerce-web-to-print/efi-digital-storefront/support/) om de gebruikers toe te voegen in het EFI Digital StoreFront-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel EFI Digital StoreFront klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van EFI Digital StoreFront waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)