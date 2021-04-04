---
title: 'Zelfstudie: Azure Active Directory-integratie met N2F - Onkostendeclaratie | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en N2F - Onkostendeclaratie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: b260e51321e14a6ea1d1ee75f88ca7564b83d492
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516746"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Zelfstudie: Azure Active Directory-integratie met N2F - Onkostendeclaratie

In deze zelfstudie leert u hoe u N2F - Onkostendeclaratie integreert met Azure Active Directory (Azure AD).
De integratie van N2F - Onkostendeclaratie met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot N2F - Onkostendeclaratie.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij N2F - Onkostendeclaratie (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met N2F - Onkostendeclaratie te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op N2F - Onkostendeclaratie waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* N2F - Onkostendeclaratie ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>N2F - Onkostendeclaratie toevoegen uit de galerie

Om de integratie van N2F - Onkostendeclaratie in Azure AD te configureren, moet u N2F - Onkostendeclaratie uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om N2F - Onkostendeclaratie toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **N2F - Onkostendeclaratie**, selecteer **N2F - Onkostendeclaratie** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![N2F - Onkostendeclaratie in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met N2F - Onkostendeclaratie op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in N2F - Onkostendeclaratie tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met N2F - Onkostendeclaratie te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor N2F - Onkostendeclaratie configureren](#configure-n2f---expense-reports-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor N2F - Onkostendeclaratie maken](#create-n2f---expense-reports-test-user)** : als u een tegenhanger van Britta Simon in N2F - Onkostendeclaratie wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met N2F - Onkostendeclaratie te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **N2F - Onkostendeclaratie** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren, hoeft u in de sectie **Standaard SAML-configuratie** geen stappen uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Schermopname van de op SAML gebaseerde aanmeldingspagina met de Basic SAML-configuratie.](common/preintegrated.png)

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname van de pagina Integraties waar u een Eenmalige aanmelding van Azure kunt toevoegen.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://www.n2f.com/app/`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

7. Kopieer in de sectie **myPolicies instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Eenmalige aanmelding voor N2F - Onkostendeclaratie configureren

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van N2F - Onkostendeclaratie.

2. Klik op **Instellingen** en selecteer **Geavanceerde instellingen** in het vervolgkeuzemenu.

    ![Schermopname met Geavanceerde instellingen geselecteerd.](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecteer het tabblad **Accountinstellingen**.

    ![Schermopname met Accountinstellingen geselecteerd.](./media/n2f-expensereports-tutorial/configure2.png)

4. Selecteer **Verificatie** en selecteer vervolgens het tabblad **+ Een verificatiemethode toevoegen**.

    ![Schermopname van de accountinstelling Verificatie waar u een verificatiemethode kunt instellen.](./media/n2f-expensereports-tutorial/configure3.png)

5. Selecteer **SAML Microsoft Office 365** als verificatiemethode.

    ![Schermopname van Verificatiemethode met SAML Microsoft Office 365 geselecteerd.](./media/n2f-expensereports-tutorial/configure4.png)

6. Voer in de sectie **Verificatiemethode** de volgende stappen uit:

    ![Schermopname van Verificatiemethode, waarin u de beschreven waarden kunt invoeren.](./media/n2f-expensereports-tutorial/configure5.png)

    a. Plak in het tekstvak **Entiteits-id** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    b. Plak in het tekstvak **Metagegevens-URL** de waarde van **App-URL voor federatieve metagegevens** die u uit de Azure-portal hebt gekopieerd.

    c. Klik op **Opslaan**.

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot N2F - Onkostendeclaratie.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **N2F - Onkostendeclaratie**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **N2F - Onkostendeclaratie** in de lijst met toepassingen.

    ![De koppeling naar N2F - Onkostendeclaratie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-n2f---expense-reports-test-user"></a>Testgebruiker voor N2F - Onkostendeclaratie maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij N2F - Onkostendeclaratie, moeten ze worden ingericht in N2F - Onkostendeclaratie. In het geval van N2F - Onkostendeclaratie is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van N2F - Onkostendeclaratie.

2. Klik op **Instellingen** en selecteer **Geavanceerde instellingen** in het vervolgkeuzemenu.

    ![Schermopname met Geavanceerde instellingen geselecteerd.](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecteer het tabblad **Gebruikers** in het linkernavigatievenster.

    ![Schermopname met Gebruikers geselecteerd.](./media/n2f-expensereports-tutorial/user1.png)

4. Selecteer het tabblad **+ Nieuwe gebruiker**.

    ![Schermopname van de optie Nieuwe gebruiker.](./media/n2f-expensereports-tutorial/user2.png)

5. Voer in de sectie **Gebruiker** de volgende stappen uit:

    ![Schermopname van de sectie waarin u de beschreven waarden kunt invoeren.](./media/n2f-expensereports-tutorial/user3.png)

    a. Voer in het tekstvak **E-mailadres** het e-mailadres in van de gebruiker, bijvoorbeeld **brittasimon\@contoso.com**.

    b. Voer in het tekstvak **First name** de voornaam van de gebruiker in, zoals **Britta**.

    c. Voer in het tekstvak **Volledige naam** de volledige naam van de gebruiker in, bijvoorbeeld **Britta Simon**.

    d. Kies **Rol, Directe manager (N+1)** en **Divisie** op basis van de behoeften van uw organisatie.

    e. Klik op **Valideren en uitnodiging verzenden**.

    > [!NOTE]
    > Als u problemen ondervindt tijdens het toevoegen van de gebruiker, neemt u contact op met het [ondersteuningsteam van N2F - Onkostendeclaratie](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel N2F - Onkostendeclaratie klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van N2F - Onkostendeclaratie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)