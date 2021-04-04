---
title: 'Zelfstudie: Azure Active Directory-integratie met ITRP | Microsoft Docs'
description: In deze zelfstudie ontdekt u hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ITRP.
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
ms.openlocfilehash: 2dff68f98e2922d5fc7a4fca1e6de8740bc2ae68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459676"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Zelfstudie: Azure Active Directory-integratie met ITRP

In deze zelfstudie leert u hoe u ITRP integreert met Azure Active Directory (Azure AD).
Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot ITRP.
* U kunt ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij ITRP (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met ITRP te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op ITRP waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ITRP ondersteunt door SP geïnitieerde eenmalige aanmelding.

## <a name="add-itrp-from-the-gallery"></a>ITRP toevoegen uit de galerie

Om de integratie van ITRP in Azure AD te configureren, moet u ITRP uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** > **Alle toepassingen**:

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Selecteer Nieuwe toepassing](common/add-new-app.png)

4. Typ **ITRP** in het zoekvak. Selecteer **ITRP** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met ITRP door een testgebruiker te gebruiken met de naam Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de tegenhanger daarvan in ITRP.

Als u eenmalige aanmelding van Azure AD met ITRP wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Eenmalige aanmelding voor ITRP configureren](#configure-itrp-single-sign-on)** aan de toepassingszijde.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** om eenmalige aanmelding in te schakelen voor de gebruiker.
5. **[Een testgebruiker voor ITRP maken](#create-an-itrp-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD met ITRP te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing ITRP en selecteer **Eenmalige aanmelding**:

    ![‘Eenmalige aanmelding’ selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen:

    ![Schermopname van de pagina Eenmalige aanmelding instellen met SAML met het pictogram Bewerken geselecteerd.](common/edit-urls.png)

4. Voer in het dialoogvenster **Standaard SAML-configuratie** de volgende stappen uit.

    ![Dialoogvenster Standaard SAML-configuratie](common/sp-identifier.png)

    1. Voer in het vak **Aanmeldings-URL** een URL met dit patroon in:
    
       `https://<tenant-name>.itrp.com`

    1. Voer in het vak **Identifier (Entity ID)** een URL in met dit patroon:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. Hiervoor moet u de werkelijke aanmeldings-URL en id gebruiken. Neem contact op met het [klantenondersteuningsteam van ITRP](https://www.4me.com/support/) om de waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

5. Selecteer in de sectie **SAML-handtekeningcertificaat** het pictogram **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen:

    ![Schermopname van de pagina SAML-handtekeningcertificaat waarvoor het pictogram Bewerken is geselecteerd.](common/edit-certificate.png)

6. Kopieer in het dialoogvenster **SAML-handtekeningcertificaat** de waarde van **Vingerafdruk** en sla deze op:

    ![De waarde van ‘Vingerafdruk’ kopiëren](common/copy-thumbprint.png)

7. Kopieer in de sectie **ITRP instellen** de juiste URL's op basis van uw behoeften:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Afmeldings-URL**.

### <a name="configure-itrp-single-sign-on"></a>Eenmalige aanmelding voor ITRP configureren

1. Meld u in een nieuw browservenster als beheerder aan bij de bedrijfssite van ITRP.

1. Selecteer bovenaan het venster het pictogram **Instellingen**:

    ![Het pictogram Instellingen](./media/itrp-tutorial/ic775570.png "Het pictogram Instellingen")

1. Selecteer **Eenmalige aanmelding** in het linkerdeelvenster:

    ![‘Eenmalige aanmelding’ selecteren](./media/itrp-tutorial/ic775571.png "‘Eenmalige aanmelding’ selecteren")

1. Voer in de configuratiesectie **Eenmalige aanmelding** de volgende stappen uit.

    ![Schermopname van de sectie Eenmalige aanmelding met Ingeschakeld geselecteerd.](./media/itrp-tutorial/ic775572.png "Sectie ‘Eenmalige aanmelding’")

    ![Schermopname van de sectie Eenmalige aanmelding, waarin u de informatie kunt toevoegen die in deze stap is beschreven.](./media/itrp-tutorial/ic775573.png "Sectie ‘Eenmalige aanmelding’")

    1. Selecteer **Ingeschakeld**.

    1. Plak in het vak **URL voor externe afmelding** de **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het vak **URL voor eenmalige aanmelding met SAML** de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het vak **Certificaatvingerafdruk** de waarde van **Vingerafdruk** die u hebt gekopieerd uit de Azure-portal.

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm:

    ![Selecteer Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoogvenster **Gebruiker** de volgende stappen uit.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam** **Britta Simon** in.
  
    1. Voer in het vak **Naam** **Britta Simon@\<yourcompanydomain> in.\<extension>** . (Bijvoorbeeld: BrittaSimon@contoso.com.)

    1. Selecteer **Wachtwoord weergeven** en noteer de waarde die in het vak **Wachtwoord** wordt getoond.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot ITRP.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **ITRP**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ITRP** in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **Gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **Britta Simon** in de gebruikerslijst van het dialoogvenster **Gebruikers en groepen** en klik vervolgens op de knop **Selecteren** onderaan het venster.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onderaan het venster op de knop **Selecteren**.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-itrp-test-user"></a>Een testgebruiker voor ITRP maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij ITRP, moet u ze toevoegen aan ITRP. U moet ze handmatig toevoegen.

Voer deze stappen uit om een gebruikersaccount te maken:

1. Meld u aan bij uw ITRP-tenant.

1. Selecteer bovenaan het venster het pictogram **Records**:

    ![Pictogram Records](./media/itrp-tutorial/ic775575.png "Pictogram Records")

1. Selecteer **Personen** in het menu:

    ![‘Personen’ selecteren](./media/itrp-tutorial/ic775587.png "‘Personen’ selecteren")

1. Selecteer het plusteken ( **+** ) om een nieuwe persoon toe te voegen:

    ![Het plusteken selecteren](./media/itrp-tutorial/ic775576.png "Het plusteken selecteren")

1. Voer in het dialoogvenster **Nieuwe persoon toevoegen** de volgende stappen uit.

    ![Dialoogvenster ‘Nieuwe persoon toevoegen’](./media/itrp-tutorial/ic775577.png "Dialoogvenster ‘Nieuwe persoon toevoegen’")

    1. Voer de naam en het e-mailadres in van een geldig Azure AD-account dat u wilt toevoegen.

    1. Selecteer **Opslaan**.

> [!NOTE]
> U kunt ook alle andere door ITRP geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie voor eenmalige aanmelding van Azure AD testen met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster de tegel ITRP selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van ITRP waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Apps openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)