---
title: 'Zelfstudie: Azure Active Directory-integratie met ThirdLight | Microsoft Docs'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en ThirdLight.
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
ms.openlocfilehash: f15d00f1050177c6255fb5528f03314153c3ed9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514657"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Zelfstudie: Azure Active Directory-integratie met ThirdLight

In deze zelfstudie leert u hoe u ThirdLight kunt integreren met Azure Active Directory (Azure AD). Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot ThirdLight.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ThirdLight (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Als u meer informatie wilt over de integratie van SaaS-apps met Azure AD, raadpleegt u [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de integratie van Azure AD met ThirdLight te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op ThirdLight waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ThirdLight biedt ondersteuning voor door SP geïnitieerde eenmalige aanmelding.

## <a name="add-thirdlight-from-the-gallery"></a>ThirdLight toevoegen vanuit de galerie

Voor het instellen van de integratie van ThirdLight met Microsoft Azure Active Directory moet u ThirdLight vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** > **Alle toepassingen**:

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Selecteer Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **ThirdLight** in. Selecteer **ThirdLight** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met ThirdLight met behulp van een testgebruiker met de naam Britta Simon.
Om eenmalige aanmelding in te schakelen, moet u een relatie maken tussen een Azure AD-gebruiker en de desbetreffende gebruiker in ThirdLight.

Als u eenmalige aanmelding van Azure AD met ThirdLight wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Eenmalige aanmelding voor ThirdLight configureren](#configure-thirdlight-single-sign-on)** aan de toepassingszijde.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** om eenmalige aanmelding in te schakelen voor de gebruiker.
5. **[Maak een ThirdLight-testgebruiker](#create-a-thirdlight-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u eenmalige aanmelding van Azure AD met ThirdLight wilt configureren, moet u de volgende stappen uitvoeren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing ThirdLight de optie **Eenmalige aanmelding**.

    ![Selecteer Eenmalige aanmelding](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen:

    ![Pictogram bewerken](common/edit-urls.png)

4. Voer in het dialoogvenster **Standaard SAML-configuratie** de volgende stappen uit.

    ![Dialoogvenster Standaard SAML-configuratie](common/sp-identifier.png)

    1. Voer in het vak **Aanmeldings-URL** een URL met dit patroon in:
    
          `https://<subdomain>.thirdlight.com/`

    1. Voer in het vak **Identifier (Entity ID)** een URL in met dit patroon:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Deze waarden zijn tijdelijke aanduidingen. Hiervoor moet u de werkelijke aanmeldings-URL en id gebruiken. Neem contact op met het [ondersteuningsteam van ThirdLight](https://www.thirdlight.com/support) om de waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, de koppeling **Downloaden** naast **XML-bestand met federatieve metagegevens** overeenkomstig uw behoeften, en sla het bestand op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **ThirdLight instellen** kopieert u de juiste URL's op basis van uw behoeften:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Afmeldings-URL**.

### <a name="configure-thirdlight-single-sign-on"></a>Eenmalige aanmelding voor ThirdLight configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van ThirdLight.

1. Ga naar **Configuration** > **System Administration** > **SAML2**:

    ![System Administration](./media/thirdlight-tutorial/ic805843.png "System Administration")

1. Voer de volgende stappen uit in de SAML2-configuratiesectie.
  
    ![SAML2-configuratiesectie](./media/thirdlight-tutorial/ic805844.png "SAML2-configuratiesectie")

    1. Schakel het selectievakje **Enable SAML2 Single Sign-On** in.

    1. Selecteer onder **Source for IdP Metadata** de optie **Load IdP Metadata from XML**.

    1. Open het metagegevensbestand dat u in de vorige sectie hebt gedownload vanuit de Azure-portal. Kopieer de inhoud van het bestand en plak deze in het vak **IdP Metadata XML**.

    1. Selecteer **Save SAML2 settings**.

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

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding via Azure door haar toegang te geven tot ThirdLight.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **ThirdLight**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ThirdLight** in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **Gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **Britta Simon** in de gebruikerslijst van het dialoogvenster **Gebruikers en groepen** en klik vervolgens op de knop **Selecteren** onderaan het venster.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onderaan het venster op de knop **Selecteren**.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-thirdlight-test-user"></a>Een testgebruiker voor ThirdLight maken

Als u wilt dat Azure AD-testgebruikers zich kunnen aanmelden bij ThirdLight, moet u ze toevoegen in ThirdLight. U moet ze handmatig toevoegen.

Voer deze stappen uit om een gebruikersaccount te maken:

1. Meld u bij de bedrijfssite van ThirdLight aan als beheerder.

1. Ga naar het tabblad **Users** (Gebruikers).

1. Selecteer **Users and Groups** (Gebruikers en groepen).

1. Selecteer **Add New User** (Nieuwe gebruiker toevoegen).

1. Voer de gebruikersnaam, een naam of beschrijving en het e-mailadres in van een geldig Azure AD-account dat u wilt inrichten. Kies een vooraf ingesteld lid of een groep nieuwe leden.

1. Selecteer **Maken**.

> [!NOTE]
> U kunt ook alle andere door ThirdLight geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie voor eenmalige aanmelding van Azure AD testen met behulp van het toegangsvenster.

Wanneer u de tegel ThirdLight in het toegangsvenster selecteert, zou u automatisch moeten worden aangemeld bij de instantie van ThirdLight waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Apps openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)