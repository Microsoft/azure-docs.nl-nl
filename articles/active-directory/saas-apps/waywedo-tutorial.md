---
title: 'Zelfstudie: Azure Active Directory-integratie met Way We Do | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Way We Do.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 0205617198ec8e57cb891d2415d2b5413a945f1b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608932"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Zelfstudie: Way We Do integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Way We Do kunt integreren met Azure AD (Active Directory). Wanneer u Way We Do integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Way We Do.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Way We Do.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een Way We Do-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Way We Do biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding
* Way We Do biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-way-we-do-from-the-gallery"></a>Way We Do toevoegen vanuit de galerie

Om de integratie van Way We Do te configureren in Azure AD moet u Way We Do vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Way We Do**.
1. Selecteer **Way We Do** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Way We Do met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Way We Do.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Way We Do, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Way We Do configureren](#configure-way-we-do-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een Way We Do-testgebruiker maken](#create-way-we-do-test-user)** : als u een tegenhanger van Britta Simon in Way We Do wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Way We Do** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Way We Do](mailto:support@waywedo.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Raw)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Kopieer in de sectie **Way We Do instellen** de juiste URL('s) op basis van uw vereisten.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Eenmalige aanmelding bij Way We Do configureren

1. Als u de configuratie met Way We Do wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **Way We Do instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Way We Do-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Way We Do. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Way We Do handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Way We Do-bedrijfssite. Voer hierna de volgende stappen uit:

1. Klik in de rechterbovenhoek van een willekeurige pagina in Way We Do op het **pictogram Persoon**, en klik vervolgens in de vervolgkeuzelijst op **Account**.

    ![Way We Do-account](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klik op het **menupictogram** om het pushnavigatiemenu te openen, en klik op **Eenmalige aanmelding**.

    ![Eenmalige aanmelding bij Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Voer op de pagina **Eenmalige aanmelding instellen** de volgende stappen uit:

    ![Opslaan in Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. Zet de wisselknop **Eenmalige aanmelding inschakelen** in op **Ja** om eenmalige aanmelding in te schakelen.

    1. Voer in het tekstvak **Naam voor eenmalige aanmelding** uw naam in.

    1. Plak in het tekstvak **Entititeits-id** de waarde voor **Azure AD-id** die u hebt gekopieerd uit Azure Portal.

    1. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    1. Upload het certificaat door op de knop **Selecteren** te klikken naast **Certificaat**.

    1. **Optionele instellingen** -

        * Wachtwoorden inschakelen: wanneer deze optie is uitgeschakeld, functioneren de normale wachtwoorden voor Way We Do zo dat gebruikers alleen eenmalige aanmelding kunnen gebruiken.

        * Automatisch inrichting inschakelen: wanneer deze optie is ingeschakeld, wordt het e-mailadres gebruikt voor aanmelden, automatisch vergeleken met de lijst met gebruikers in Way We Do. Als het e-mailadres niet overeenkomt met een actieve gebruiker in Way We Do, wordt automatisch een nieuw gebruikersaccount toegevoegd voor de gebruiker die zich aanmeldt, waarbij eventuele ontbrekende gegevens worden opgevraagd.

          > [!NOTE]
          > Gebruikers die zijn toegevoegd via eenmalige aanmelding, worden toegevoegd als algemene gebruikers. Er wordt aan hen geen rol toegewezen in het systeem. Iemand met de rol Beheerder heeft toegang en kan beveiligingsrollen wijzigen als editor of als beheerder, en kan ook een of meerdere Organigramrollen toewijzen.

    1. Klik op **Opslaan** om de instellingen te behouden.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Way We Do.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Way We Do** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-way-we-do-test-user"></a>Een Way We Do-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Way We Do. Way We Do biedt ondersteuning voor het Just-In-Time inrichten van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Way We Do bestaat, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u handmatig een gebruiker moet maken, neemt u contact op met het [klantondersteuningsteam van Way We Do](mailto:support@waywedo.com).

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Way We Do selecteert, wordt u automatisch aangemeld bij de instantie van Way We Do waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)