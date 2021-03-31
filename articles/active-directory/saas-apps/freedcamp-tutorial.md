---
title: 'Zelfstudie: Azure Active Directory-integratie met Freedcamp | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Freedcamp configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 19378251408d55868ed844a5505ae48ece55dc3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92451481"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Zelfstudie: Freedcamp integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Freedcamp integreert met Azure Active Directory (Azure AD). Wanneer u Freedcamp integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Freedcamp.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Freedcamp.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* een Freedcamp-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Freedcamp biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding.

## <a name="adding-freedcamp-from-the-gallery"></a>Freedcamp toevoegen vanuit de galerie

Voor het configureren van de integratie van Freedcamp met Microsoft Azure Active Directory moet u Freedcamp vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Freedcamp**.
1. Selecteer **Freedcamp** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Freedcamp met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Freedcamp.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Freedcamp te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Freedcamp](#configure-freedcamp)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. **[Testgebruiker voor Freedcamp maken](#create-freedcamp-test-user)** : als u een tegenhanger van Britta Simon in Freedcamp wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Freedcamp** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Gebruikers kunnen ook de URL-waarden voor hun eigen klantdomein invoeren, die niet noodzakelijkerwijs het patroon `freedcamp.com` hebben. Ze kunnen een specifieke waarde voor het klantdomein invoeren, specifiek voor hun toepassingsinstantie. U kunt ook contact opnemen met [het klantondersteuningsteam van Freedcamp](mailto:devops@freedcamp.com) voor meer informatie over URL-patronen.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Freedcamp instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Freedcamp configureren

1. Als u de configuratie in Freedcamp wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Freedcamp instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Freedcamp-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij Freedcamp. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Freedcamp handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Freedcamp-bedrijfssite. Voer daarna de volgende stappen uit:

4. Klik in de rechterbovenhoek van de pagina op **profiel** en navigeer vervolgens naar **Mijn account**.

    ![Schermopname waarop 'Profiel' en 'Mijn account' zijn geselecteerd.](./media/freedcamp-tutorial/config01.png)

5. Klik in het linkermenu op **SSO** en voer op de pagina **Uw SSO-verbinding** de volgende stappen uit:

    ![Schermopname waarop 'Eenmalige aanmelding' is geselecteerd in de menubalk aan de linkerkant en die de pagina 'Uw SSO-verbinding' toont met ingevoerde waarden en de knop 'Verzenden' geselecteerd.](./media/freedcamp-tutorial/config02.png)

    a. Typ in het tekstvak **Titel** de titel.

    b. Plak in het tekstvak **Entiteits-id** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    c. Plak in het tekstvak **Login URL** de waarde van de **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d. Open het met Base64 gecodeerde certificaat in Kladblok, kopieer de inhoud en plak deze in het tekstvak **Certificaat**.

    e. Klik op **Submit**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Freedcamp.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Freedcamp** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-freedcamp-test-user"></a>Freedcamp-testgebruiker maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij Freedcamp, moeten ze worden ingericht in Freedcamp. In het geval van Freedcamp is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u in een ander browservenster als beveiligingsbeheerder aan bij Freedcamp.

2. Klik in de rechterbovenhoek van de pagina op **profiel** en navigeer vervolgens naar **Systeem beheren**.

    ![Configuratie van Chargebee](./media/freedcamp-tutorial/config03.png)

3. Voer aan de rechterzijde van de pagina Systeem beheren de volgende stappen uit:

    ![Schermopname waarop de knop 'Gebruikers toevoegen of uitnodigen is geselecteerd', het veld 'E-mail' is gemarkeerd en de knop 'Gebruiker toevoegen' is geselecteerd.](./media/freedcamp-tutorial/config04.png)

    a. Klik op **Gebruikers toevoegen of uitnodigen**.

    b. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `Brittasimon@contoso.com`.

    c. Klik op **Add User**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Freedcamp selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van Freedcamp waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)