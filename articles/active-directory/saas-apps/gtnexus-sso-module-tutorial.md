---
title: 'Zelfstudie: Azure Active Directory-integratie met GTNexus SSO System | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en GTNexus SSO System.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: jeedes
ms.openlocfilehash: 11439a8c93c0f1ef682e7104e989a3a2cbbdc28e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92446732"
---
# <a name="tutorial-azure-active-directory-integration-with-gtnexus-sso-system"></a>Zelfstudie: Azure Active Directory-integratie met GTNexus SSO System

In deze zelfstudie leert u hoe u GTNexus SSO System integreert met Azure Active Directory (Azure AD).
De integratie van GTNexus SSO System met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot GTNexus SSO System.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij GTNexus SSO System (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met GTNexus SSO System hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* GTNexus SSO System-abonnement waarbij eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GTNexus SSO System ondersteunt door **IDP** gestarte SSO

## <a name="adding-gtnexus-sso-system-from-the-gallery"></a>GTNexus SSO System toevoegen vanuit de galerie

Voor de configuratie van de integratie van GTNexus SSO System in Azure AD, moet u GTNexus SSO System toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om GTNexus SSO System toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **GTNexus SSO System**, selecteer **GTNexus SSO System** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![GTNexus SSO System in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met GTNexus SSO System configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in GTNexus SSO System tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met GTNexus SSO System wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor GTNexus SSO System configureren](#configure-gtnexus-sso-system-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken in GTNexus SSO System](#create-gtnexus-sso-system-test-user)**: om in GTNexus SSO System een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor de configuratie van eenmalige aanmelding in Azure AD met GTNexus SSO System moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **GTNexus SSO System**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Schermopname van de pagina 'Basic SAML-configuratie' met de geselecteerde actie 'Metagegevensbestand uploaden'.](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Schermopname met het veld 'Een bestand selecteren' met het 'map'-logo en de knop 'Upload' geselecteerd.](common/browse-upload-metadata.png)

    c. Zodra het bestand met metagegevens is geüpload, worden de waarde voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie voor GTNexus SSO System:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-gtnexus-sso-system-single-sign-on"></a>Eenmalige aanmelding in GTNexus SSO System configureren

Om eenmalige aanmelding te configureren in **GTNexus SSO System**, moet u het **XML-bestand met federatieve metagegevens** verzenden naar het [ondersteuningsteam van GTNexus SSO System](mailto:support@gtnexus.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot GTNexus SSO System.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **GTNexus SSO System**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **GTNexus SSO System** in de lijst met toepassingen.

    ![De GTNexus SSO System-koppeling in de toepassingenlijst](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-gtnexus-sso-system-test-user"></a>Een testgebruiker voor GTNexus SSO System maken

In deze sectie gaat u een gebruiker maken met de naam Britta Simon in GTNexus SSO System. Werk samen met het [GTNexus SSO System-ondersteuningsteam](mailto:support@gtnexus.com) om de gebruikers toe te voegen in het GTNexus SSO System-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel GTNexus SSO System in het toegangsvenster klikt, wordt u automatisch aangemeld bij het GTNexus SSO System-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)