---
title: 'Zelfstudie: Integratie van Azure Active Directory eenmalige aanmelding (SSO) met SumoLogic | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SumoLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521890"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Zelfstudie: Integratie van Azure Active Directory eenmalige aanmelding (SSO) met SumoLogic

In deze zelfstudie leert u hoe u SumoLogic integreert met Azure Active Directory (Azure AD). Wanneer u SumoLogic integreert met Azure AD, kunt u het volgende doen:

* Controle in Azure AD die toegang heeft tot SumoLogic.
* Uw gebruikers zich automatisch met hun Azure AD-account laten aanmelden bij SumoLogic.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SumoLogic-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SumoLogic ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-sumologic-from-the-gallery"></a>SumoLogic toevoegen uit de galerie

Om de integratie van SumoLogic te configureren in Azure AD, moet u SumoLogic vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **SumoLogic**.
1. Selecteer **SumoLogic** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SumoLogic

Configureer en test eenmalige aanmelding van Azure AD met SumoLogic met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SumoLogic.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met SumoLogic te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij SumoLogic configureren](#configure-sumologic-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een SumoLogic-testgebruiker maken](#create-sumologic-test-user)** : als u een equivalent van B.Simon in SumoLogic wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure Portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **SumoLogic**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [Ondersteuningsteam voor clients van SumoLogic](https://www.sumologic.com/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de SumoLogic-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing SumoLogic nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    |  Naam | Bronkenmerk |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Rollen | user.assignedroles |

    > [!NOTE]
    > Klik [hier](../develop/active-directory-enterprise-app-role-management.md) als u wilt weten hoe u **rollen** in Azure AD moet configureren.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **SumoLogic instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SumoLogic.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SumoLogic** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sumologic-sso"></a>SumoLogic SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij uw SumoLogic-bedrijfssite.

1. Ga naar **Beheren \> Beveiliging**.

    ![Beheren](./media/sumologic-tutorial/ic778556.png "Beheren")

1. Klik op **SAML**.

    ![Globale beveiligingsinstellingen](./media/sumologic-tutorial/ic778557.png "Globale beveiligingsinstellingen")

1. In de lijst **Een configuratie selecteren of een nieuwe maken**, selecteer **Azure AD** en klik vervolgens op **Configureren**.

    ![Schermopname met Configure SAML 2.0, waar u Azure AD kunt selecteren.](./media/sumologic-tutorial/ic778558.png "SAML 2.0 configureren")

1. Voer in het dialoogvenster **SAML 2.0 configureren** de volgende stappen uit:

    ![Schermopname van het dialoogvenster SAML 2.0 configureren, waar u de beschreven waarden kunt invoeren.](./media/sumologic-tutorial/ic778559.png "SAML 2.0 configureren")

    a. Typ in het tekstvak **Configuratienaam** **Azure AD**.

    b. Selecteer **Foutopsporingsmodus**.

    c. Plak in het tekstvak **Verlener** de waarde van **Azure AD-id**, die u hebt gekopieerd uit Azure Portal.

    d. Plak in het tekstvak **Auth-aanvraag URL** de waarde van **Login URL** die u hebt gekopieerd uit de Azure Portal.

    e. Open in Kladblok het met Base 64 gecodeerde certificaat, kopieer de inhoud ervan naar het klembord en plak het hele certificaat in het tekstvak **X.509 Certificate**.

    f. Selecteer **SAML-kenmerk gebruiken** als **E-mailkenmerk**.  

    g. Selecteer **Door SP geïnitieerde aanmeldingsconfiguratie**.

    h. Typ in het tekstvak **Aanmeldingspad**, **Azure** en klik op **Opslaan**.

### <a name="create-sumologic-test-user"></a>SumoLogic-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SumoLogic, moeten ze worden ingericht bij SumoLogic. In het geval van SumoLogic is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **SumoLogic**-tenant.

1. Ga naar **Beheren \> Gebruikers**.

    ![Schermopname met Users geselecteerd in het menu Manage.](./media/sumologic-tutorial/ic778561.png "Gebruikers")

1. Klik op **Add**.

    ![Schermopname van de knop Add voor Users.](./media/sumologic-tutorial/ic778562.png "Gebruikers")

1. Voer de volgende stappen uit in het dialoogvenster **Nieuwe gebruiker**:

    ![Nieuwe gebruiker](./media/sumologic-tutorial/ic778563.png "Nieuwe gebruiker")

    a. Typ de verwante informatie van het Azure AD-account dat u wilt inrichten in de desbetreffende tekstvakken **Voornaam**, **Achternaam** en **E-mail**.
  
    b. Selecteer een rol.
  
    c. Selecteer **Actief** als **Status**.
  
    d. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van SumoLogic-gebruikersaccounts of API's van SumoLogic gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SumoLogic in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van SumoLogic waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [SumoLogic met Azure AD proberen](https://aad.portal.azure.com/)