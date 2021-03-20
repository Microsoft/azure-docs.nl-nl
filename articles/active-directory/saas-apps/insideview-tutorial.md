---
title: 'Zelfstudie: Azure Active Directory-integratie met InsideView | Microsoft Docs'
description: In deze zelfstudie ontdekt u hoe u eenmalige aanmelding configureert tussen Azure Active Directory en InsideView.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: b8f12b6d55cb11c0cfeda5331072127ea4a696bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460101"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Zelfstudie: Azure Active Directory-integratie met InsideView

In deze zelfstudie leert u hoe u InsideView met Azure Active Directory (Azure AD) integreert.
Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot InsideView.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij InsideView (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met InsideView te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op InsideView waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* InsideView ondersteunt door IdP geïnitieerde eenmalige aanmelding.

## <a name="add-insideview-from-the-gallery"></a>InsideView toevoegen vanuit de galerie

Om de integratie van InsideView in Azure AD te configureren, moet u InsideView uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** > **Alle toepassingen**:

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Selecteer Nieuwe toepassing](common/add-new-app.png)

4. Typ **InsideView** in het zoekvak. Selecteer **InsideView** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

    ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met InsideView door een testgebruiker te gebruiken met de naam Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de tegenhanger daarvan in InsideView.

Als u eenmalige aanmelding van Azure AD met InsideView wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Eenmalige aanmelding voor InsideView configureren](#configure-insideview-single-sign-on)** aan de toepassingszijde.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** om eenmalige aanmelding in te schakelen voor de gebruiker.
5. **[Een testgebruiker voor InsideView maken](#create-an-insideview-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD met InsideView te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van InsideView en selecteer **Eenmalige aanmelding**:

    ![Selecteer Eenmalige aanmelding](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen:

    ![Pictogram bewerken](common/edit-urls.png)

4. Voer in het dialoogvenster **Standaard SAML-configuratie** de volgende stappen uit.

    ![Dialoogvenster Standaard SAML-configuratie](common/idp-reply.png)

    Voer in het vak **Antwoord-URL** een URL met dit patroon in:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Deze waarde is een tijdelijke aanduiding. U moet u de werkelijke antwoord-URL gebruiken. Neem contact op met het [klantondersteuningsteam van InsideView](mailto:support@insideview.com) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, selecteert u de koppeling **Downloaden** naast **Certificaat (Raw)** overeenkomstig uw behoeften, en slaat u het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer in het gedeelte **InsideView instellen** de juiste URL's op basis van uw behoeften:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Afmeldings-URL**.

### <a name="configure-insideview-single-sign-on"></a>Eenmalige aanmelding voor InsideView configureren

1. Meld u in een nieuw browservenster als beheerder aan bij de bedrijfssite van InsideView.

1. Selecteer bovenaan het venster de optie **Beheer**, **Instellingen voor eenmalige aanmelding** en selecteer **SAML toevoegen**.
   
   ![Instellingen voor SAML-eenmalige aanmelding](./media/insideview-tutorial/ic794135.png "Instellingen voor SAML-eenmalige aanmelding")

1. Voer de volgende stappen uit in het gedeelte **Een nieuwe SAML toevoegen**.

    ![Gedeelte Een nieuwe SAML toevoegen](./media/insideview-tutorial/ic794136.png "Gedeelte Een nieuwe SAML toevoegen")

    1. Voer in het vak **STS-naam** een naam voor de configuratie in.

    1. Plak in het val **SamlP/WS-Fed Unsolicited EndPoint** de waarde van de **Aanmeldings-URL** in die u vanuit de Azure-portal hebt gekopieerd.

    1. Open het RAW-certificaat dat u hebt gedownload van de Azure-portal. Kopieer de inhoud van het certificaat naar het klembord en plak de inhoud in het vak **STS-certificaat**.

    1. In het vak **Toewijzing Crm-gebruikers-id** voert u **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** in.

    1. In het vak **Toewijzing Crm-e-mail** voert u **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** in.

    1. In het vak **Toewijzing Crm-voornaam** voert u **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** in.

    1. In het vak **Toewijzing Crm-achternaam** voert u **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** in.  

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** bovenaan het venster:

    ![Selecteer Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoogvenster **Gebruiker** de volgende stappen uit.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam** **Britta Simon** in.
  
    1. Voer in het vak **Naam** **Britta Simon@\<yourcompanydomain> in.\<extension>** . (Bijvoorbeeld: BrittaSimon@contoso.com.)

    1. Selecteer **Wachtwoord weergeven** en noteer de waarde die in het vak **Wachtwoord** wordt getoond.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot InsideView.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **InsideView**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **InsideView** in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **Gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **Britta Simon** in de gebruikerslijst van het dialoogvenster **Gebruikers en groepen** en klik vervolgens op de knop **Selecteren** onderaan het venster.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onderaan het venster op de knop **Selecteren**.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-insideview-test-user"></a>Een InsideView-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij InsideView, moet u ze toevoegen aan InsideView. U moet ze handmatig toevoegen.

Als u gebruikers of contactpersonen in InsideView wilt maken, neemt u contact op met het [ondersteuningsteam van InsideView](mailto:support@insideview.com).

> [!NOTE]
> U kunt ook alle andere door InsideView geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie voor eenmalige aanmelding van Azure AD testen met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster de tegel InsideView selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van InsideView waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Apps openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)