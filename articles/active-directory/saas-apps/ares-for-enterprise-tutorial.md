---
title: 'Zelfstudie: Azure Active Directory-integratie met ARES for Enterprise | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en ARES for Enterprise configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 7cd8faece0d737e97f1c5809f27840f1d9192548
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673400"
---
# <a name="tutorial-azure-active-directory-integration-with-ares-for-enterprise"></a>Zelfstudie: Azure Active Directory-integratie met ARES for Enterprise

In deze zelfstudie leert u hoe u ARES for Enterprise integreert met Azure Active Directory (Azure AD).
De integratie van ARES for Enterprise met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot ARES for Enterprise.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ARES for Enterprise (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met ARES for Enterprise te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op ARES for Enterprise waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ARES for Enterprise ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* ARES for Enterprise ondersteunt het **Just In Time** inrichten van gebruikers

## <a name="adding-ares-for-enterprise-from-the-gallery"></a>ARES for Enterprise toevoegen vanuit de galerie

Als u de integratie van ARES for Enterprise in Azure AD wilt configureren, moet u ARES for Enterprise vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om ARES for Enterprise toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **ARES for Enterprise** in het zoekvak, selecteer **ARES for Enterprise** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![ARES for Enterprise toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met ARES for Enterprise op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in ARES for Enterprise tot stand is gebracht.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD voor ARES for Enterprise te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor ARES for Enterprise configureren](#configure-ares-for-enterprise-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren. 
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor ARES for Enterprise maken](#create-ares-for-enterprise-test-user)**: als u een equivalent van Britta Simon in ARES for Enterprise wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor ARES for Enterprise:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **ARES for Enterprise** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stap uit:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van ARES for Enterprise](common/sp-intiated.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://login.graebert.com`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-ares-for-enterprise-single-sign-on"></a>Eenmalige aanmelding configureren voor ARES for Enterprise

Als u eenmalige aanmelding aan de **ARES for Enterprise**-zijde wilt configureren, dient u de **URL voor federatieve metagegevens van de app** naar het [ondersteuningsteam van ARES for Enterprise](mailto:support@graebert.com) te verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In dit gedeelte stelt u Britta Simon in staat om gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot ARES for Enterprise.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **ARES for Enterprise**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ARES for Enterprise** in de lijst met toepassingen.

    ![De koppeling naar ARES for Enterprise in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ares-for-enterprise-test-user"></a>Een testgebruiker maken voor ARES for Enterprise

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in ARES for Enterprise. ARES for Enterprise ondersteunt **Just-In-Time-inrichting** van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in ARES for Enterprise bestaat, wordt er een nieuwe gemaakt wanneer u ARES for Enterprise probeert te openen.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel ARES for Enterprise klikt, wordt u automatisch aangemeld bij de instantie van ARES for Enterprise waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)