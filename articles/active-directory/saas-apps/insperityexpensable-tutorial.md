---
title: 'Zelfstudie: Integratie van Azure Active Directory met Insperity ExpensAble | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Insperity ExpensAble.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 4a1680eab345cd78f13860249d6cc0f97c52e56e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460010"
---
# <a name="tutorial-azure-active-directory-integration-with-insperity-expensable"></a>Zelfstudie: Azure Active Directory-integratie met Insperity ExpensAble

In deze zelfstudie leert u hoe u Insperity ExpensAble kunt integreren met Azure Active Directory (Azure AD).
De integratie van Insperity ExpensAble met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Insperity ExpensAble heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Insperity ExpensAble (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD wilt integreren met Insperity ExpensAble, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Insperity ExpensAble waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Insperity ExpensAble ondersteunt door **SP** geïnitieerde SSO

## <a name="adding-insperity-expensable-from-the-gallery"></a>Insperity ExpensAble toevoegen vanuit de galerie

Als u de integratie van Insperity ExpensAble met Azure AD wilt configureren, dient u Insperity ExpensAble vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Insperity ExpensAble vanuit de galerie wilt toevoegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Insperity ExpensAble**, selecteer **Insperity ExpensAble** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Insperity ExpensAble in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD configureren en testen met Insperity ExpensAble op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Insperity ExpensAble tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Insperity ExpensAble wilt testen en configureren, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Insperity ExpensAble-eenmalige aanmelding configureren](#configure-insperity-expensable-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Insperity ExpensAble-testgebruiker maken](#create-insperity-expensable-test-user)** : als u een tegenhanger van Britta Simon in Insperity ExpensAble wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD met Insperity ExpensAble wilt configureren:

1. In de [Azure-portal](https://portal.azure.com/) op de pagina voor integratie van toepassingen met **Insperity ExpensAble** selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor domeinen en URL's van Insperity ExpensAble](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://server.expensable.com/esapp/Authenticate?companyId=<company ID>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Insperity ExpensAble](https://www.insperity.com/products/expense-management/support/express/) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Insperity ExpensAble instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-insperity-expensable-single-sign-on"></a>Eenmalige aanmelding van Insperity ExpensAble configureren

Als u eenmalige aanmelding aan de **Insperity ExpensAble**-zijde wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de correct van de Microsoft Azure-portal gekopieerde URL's naar het [Insperity ExpensAble-ondersteuningsteam](https://www.insperity.com/products/expense-management/support/express/) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte maakt u het mogelijk voor Britta Simon om eenmalige aanmelding voor Azure te gebruiken door haar toegangsrechten voor Insperity ExpensAble te verlenen.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Insperity ExpensAble**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Insperity ExpensAble**.

    ![De koppeling Insperity ExpensAble in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-insperity-expensable-test-user"></a>Insperity ExpensAble-testgebruiker maken

In dit gedeelte maakt u in Insperity ExpensAble een gebruiker met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Insperity ExpensAble](https://www.insperity.com/products/expense-management/support/express/) om de gebruikers toe te voegen in het Insperity ExpensAble-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Insperity ExpensAble in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Insperity ExpensAble waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)