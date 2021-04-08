---
title: 'Zelfstudie: Azure Active Directory-integratie met Knowledge Anywhere LMS | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Knowledge Anywhere LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: eab1304408f896f5c9d736488997c1246ae167f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458979"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Zelfstudie: Knowledge Anywhere LMS integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Knowledge Anywhere LMS kunt integreren met Azure AD (Active Directory). Wanneer u Knowledge Anywhere LMS integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Knowledge Anywhere LMS.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Knowledge Anywhere LMS.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Knowledge Anywhere LMS-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Knowledge Anywhere LMS biedt ondersteuning voor met **SP geïnitieerde eenmalige aanmelding** en **Just-In-Time**-inrichting van gebruikers.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Knowledge Anywhere LMS toevoegen vanuit de galerie

Voor het configureren van de integratie van Knowledge Anywhere LMS met Azure AD moet u Knowledge Anywhere LMS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Knowledge Anywhere LMS**.
1. Selecteer **Knowledge Anywhere LMS** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Knowledge Anywhere LMS met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Knowledge Anywhere LMS.

Als u eenmalige aanmelding van Azure AD met Knowledge Anywhere LMS wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Knowledge Anywhere LMS configureren](#configure-knowledge-anywhere-lms)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een testgebruiker voor Knowledge Anywhere LMS maken](#create-knowledge-anywhere-lms-test-user)** : als u een tegenhanger van B. Simon in Knowledge Anywhere LMS wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Knowledge Anywhere LMS** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL, zoals later in de zelfstudie wordt uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Knowledge Anywhere LMS instellen** de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Knowledge Anywhere LMS configureren

1. Als u de configuratie in Knowledge Anywhere LMS wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Knowledge Anywhere LMS instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Knowledge Anywhere LMS-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Knowledge Anywhere LMS. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Knowledge Anywhere LMS handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Knowledge Anywhere LMS. Voer hierna de volgende stappen uit:

4. Selecteer het tabblad **Site**.

    ![Schermopname met het tabblad Site.](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Selecteer het tabblad **SAML Settings** (SAML-instellingen).

    ![Schermopname met de pagina Knowledge anywhere waarvoor SAML-instellingen zijn geselecteerd.](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Klik op **Add New** (Nieuwe toevoegen).

    ![Schermopname met de knop Nieuwe toevoegen in de instellingen van de serviceprovider.](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Voer de volgende stappen uit op de pagina **Add/Update SAML Settings** (SAML-instellingen toevoegen/bijwerken):

    ![Schermopname met de pagina SAML-instellingen toevoegen/bijwerken waar u de hier beschreven wijzigingen kunt aanbrengen.](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Voer de naam van de id-provider voor uw organisatie in. Bijvoorbeeld: `Azure`.

    b. Plak in het tekstvak **IDP-entiteits-id** de waarde van **Azure AD-id** die u hebt gekopieerd in de Azure-portal.

    c. Plak in het tekstvak **IDP-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    d. Open het certificaatbestand dat u vanuit de Azure-portal hebt gedownload in Kladblok, kopieer de inhoud van het certificaat en plak deze in het tekstvak **Certificate** (Certificaat).

    e. Plak in het tekstvak **Afmeldings-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    f. Selecteer **Main Site** in de vervolgkeuzelijst voor **Domain**.

    g. Kopieer de waarde van **SP Entity ID** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    h. Kopieer de waarde van **SP Response (ACS) URL** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    i. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B. Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Knowledge Anywhere LMS.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Knowledge Anywhere LMS** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B. Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Knowledge Anywhere LMS-testgebruiker maken

In deze sectie maakt u in Knowledge Anywhere LMS een gebruiker met de naam B. Simon. Knowledge Anywhere biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Knowledge Anywhere LMS bestaat, wordt er een nieuwe gemaakt na de verificatie.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster op de tegel Knowledge Anywhere LMS klikt, wordt u automatisch aangemeld bij de instantie van Knowledge Anywhere LMS waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)