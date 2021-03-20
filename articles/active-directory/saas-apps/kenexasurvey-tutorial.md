---
title: 'Zelfstudie: Azure Active Directory-integratie met IBM Kenexa Survey Enterprise | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en IBM Kenexa Survey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: faea5fdc7fdab11c630f24dbb8527e5bf198765c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459132"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Zelfstudie: Azure Active Directory-integratie met IBM Kenexa Survey Enterprise

In deze zelfstudie leert u hoe u IBM Kenexa Survey Enterprise kunt integreren met Azure Active Directory (Azure AD).
De integratie van IBM Kenexa Survey Enterprise met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot IBM Kenexa Survey Enterprise.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij IBM Kenexa Survey Enterprise (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om de integratie van Azure AD met Kenexa Survey Enterprise te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op IBM Kenexa Survey Enterprise waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* IBM Kenexa Survey Enterprise biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>IBM Kenexa Survey Enterprise toevoegen vanuit de galerie

Voor het configureren van de integratie van IBM Kenexa Survey Enterprise in Azure AD moet u IBM Kenexa Survey Enterprise vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om IBM Kenexa Survey Enterprise vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **IMB Kenexa Survey Enterprise**, selecteer **IBM Kenexa Survey Enterprise** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![IBM Kenexa Survey Enterprise in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met IBM Kenexa Survey Enterprise op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in IBM Kenexa Survey Enterprise tot stand is gebracht.

U hebt de volgende bouwstenen nodig om eenmalige aanmelding van Azure AD met IBM Kenexa Survey Enterprise te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor IBM Kenexa Survey Enterprise configureren](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor IBM Kenexa Survey Enterprise maken](#create-ibm-kenexa-survey-enterprise-test-user)** : als u een tegenhanger van Britta Simon in IBM Kenexa Survey Enterprise wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding in Azure AD te configureren voor IBM Kenexa Survey Enterprise:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **IBM Kenexa Survey Enterprise** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor IBM Kenexa Survey Enterprise-domein en -URL's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://surveys.kenexa.com/<companycode>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In de IBM Kenexa Survey Enterprise-toepassing worden de SAML-asserties (Security Assertions Markup Language) in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. De waarde van de claim voor de gebruikers-id in het antwoord moet overeenkomen met de id voor eenmalige aanmelding die is geconfigureerd in het Kenexa-systeem. Als u de juiste gebruikers-id in uw organisatie wilt toewijzen als IDP (Internet Data gram Protocol) voor eenmalige aanmelding, neemt u contact op met het [ondersteuningsteam van IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

    In azure AD wordt standaard de gebruikers-id ingesteld als de waarde voor de user principal name (UPN). U kunt deze waarde wijzigen op het tabblad **Gebruikerskenmerken**, zoals wordt weergegeven in de volgende schermopname. De integratie werkt pas nadat u de toewijzing op de juiste wijze hebt voltooid.

    ![image](common/edit-attribute.png)

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In de sectie **IBM Kenexa Survey Enterprise instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Eenmalige aanmelding voor IBM Kenexa Survey Enterprise configureren

Als u eenmalige aanmelding aan de zijde van **IBM Kenexa Survey Enterprise** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding via Azure door haar toegang te geven tot IBM Kenexa Survey Enterprise.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **IBM Kenexa Survey Enterprise**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **IBM Kenexa Survey Enterprise** in de lijst met toepassingen.

    ![De koppeling naar IBM Kenexa Survey Enterprise in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Testgebruiker voor IBM Kenexa Survey Enterprise maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in IBM Kenexa Survey Enterprise.

Als u gebruikers in het IBM Kenexa Survey Enterprise-systeem wilt maken en de id voor eenmalige aanmelding aan hen wilt toewijzen, kunt u contact opnemen met het [ondersteuningsteam van IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Deze id voor eenmalige aanmelding moet ook worden toegewezen aan de gebruikers-id uit Azure AD. U kunt deze standaard instelling wijzigen op het tabblad **Kenmerk**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel IBM Kenexa Survey Enterprise in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van IBM Kenexa Survey Enterprise waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)