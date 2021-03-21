---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Lenses.io | Microsoft Docs'
description: In deze zelfstudie ontdekt u hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458605"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met de Lenses.io DataOps-portal

In deze zelfstudie leert u hoe u de [Lenses.io](https://lenses.io/) DataOps-portal kunt integreren met Azure Active Directory (Azure AD). Nadat u Lenses.io hebt geïntegreerd met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot de Lenses.io-portal.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Lenses.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Wat is toegangsbeheer? en Wat is eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een instantie van een Lenses-portal. U kunt kiezen uit een aantal [implementatieopties](https://lenses.io/product/deployment/).
* Een [Lenses.io-licentie](https://lenses.io/product/pricing/) die ondersteuning biedt voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Lenses.io biedt ondersteuning voor door serviceprovider (SP) geïnitieerde eenmalige aanmelding.

* Nadat u Lenses.io hebt geconfigureerd, kunt u sessiebeheer afdwingen. Sessiebeheer beschermt in realtime tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Lenses.io toevoegen vanuit de galerie

Om de integratie van Lenses.io in Azure AD te configureren, voegt u Lenses.io vanuit de galerie toe aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Lenses.io**.
1. Selecteer **Lenses.io** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Eenmalige aanmelding van Azure AD voor Lenses.io configureren en testen

U gaat een testgebruiker met de naam *B.Simon* maken om eenmalige aanmelding van Azure te configureren en testen. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Lenses.io.

Voer de volgende stappen uit:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker en -groep](#create-an-azure-ad-test-user-and-group) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Configureer eenmalige aanmelding in Lenses.io](#configure-lensesio-sso) om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. [Maak machtigingen voor Lenses.io-testgroepen](#create-lensesio-test-group-permissions) om te bepalen waartoe B.Simon toegang heeft in Lenses.io (autorisatie).
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Zoek in [Azure Portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Lenses.io**, de sectie **Beheren** en selecteer vervolgens **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor bewerken (de pen) voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van het pictogram voor het bewerken van de standaard SAML-configuratie.](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** geeft u waarden op in de volgende tekstinvoervakken:

    a. **Aanmeldings-URL**: voer een URL in met het volgende patroon: `https://<CUSTOMER_LENSES_BASE_URL>`. Een voorbeeld is `https://lenses.my.company.com`.

    b. **Id (Entiteits-id)** : voer een URL in met het volgende patroon: `https://<CUSTOMER_LENSES_BASE_URL>`. Een voorbeeld is `https://lenses.my.company.com`.

    c. **Antwoord-URL**: voer een URL in met het volgende patroon: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`. Een voorbeeld is `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, antwoord-URL en id, op basis van de basis-URL van uw exemplaar van de Lenses-portal. Zie de [Lenses-documentatie voor eenmalige aanmelding](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) voor meer informatie.

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** naar de sectie **SAML-handtekeningcertificaat**. Zoek het **XML-bestand met federatieve metagegevens** en selecteer vervolgens **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Schermopname van de downloadkoppeling voor het certificaat.](common/metadataxml.png)

1. Gebruik in de sectie **Lenses.io instellen** het eerder gedownloade XML-bestand om Lenses te configureren voor eenmalige aanmelding via Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Een Azure AD-testgebruiker en -groep maken

In Azure Portal maakt u een testgebruiker met de naam B.Simon. Vervolgens maakt u een testgroep waarmee de toegangsrechten van B.Simon in Lenses worden beheerd.

U kunt in de [Lenses-documentatie voor eenmalige aanmelding](https://docs.lenses.io/install_setup/configuration/security.html#id3) achterhalen hoe Lenses toewijzing van groepslidmaatschap gebruikt voor autorisatie.

**De testgebruiker maken:**

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer bovenaan het scherm **Nieuwe gebruiker**.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het vak **Naam** de naam **B.Simon** in.  
   1. Voer in het vak **Gebruikersnaam** username@companydomain.extension in. Bijvoorbeeld B.Simon@contoso.com.
   1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer het wachtwoord dat wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

**De groep maken:**

1. Ga naar **Azure Active Directory** en selecteer vervolgens **Groepen**.
1. Selecteer bovenaan het scherm **Nieuwe groep**.
1. Volg in **Eigenschappen van groep** de volgende stappen:
   1. Selecteer **Beveiliging** in het vak **Groepstype**.
   1. Voer in het vak **Groepsnaam** **LensesUsers** in.
   1. Selecteer **Maken**.
1. Selecteer de groep **LensesUsers** en kopieer de **object-id** (bijvoorbeeld f8b5c1ec-45de-4abd-af5c-e874091fb5f7). U gebruikt deze id in Lenses om gebruikers van deze groep toe te wijzen aan de [juiste machtigingen](https://docs.lenses.io/install_setup/configuration/security.html#id3).  

**De groep toewijzen aan de testgebruiker:**

1. Ga naar **Azure Active Directory** en selecteer vervolgens **Gebruikers**.
1. Selecteer de testgebruiker **B.Simon**.
1. Selecteer **Groepen**.
1. Selecteer bovenaan het scherm **Lidmaatschappen toevoegen**.
1. Zoek en selecteer **LensesUsers**.
1. Klik op **Selecteren**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Lenses.io.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Lenses.io** in de lijst met toepassingen.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermopname van de koppeling Gebruikers en groepen.](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**.

   ![Schermopname van de koppeling Gebruiker toevoegen.](common/add-assign-user.png)

1. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. In het dialoogvenster **Gebruikers en groepen** selecteert u **B.Simon** in de lijst Gebruikers. Klik vervolgens onder aan het scherm op de knop **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-assertie, kiest u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik vervolgens onder aan het scherm op de knop **Selecteren**.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-lensesio-sso"></a>Eenmalige aanmelding voor Lenses.io configureren

Als u eenmalige aanmelding wilt configureren in de **Lenses.io**-portal, installeert u het gedownloade **XML-bestand met federatieve metagegevens** op uw instantie van Lenses en [configureert u Lenses om eenmalige aanmelding in te schakelen](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Machtigingen voor een Lenses.io-testgroep maken

1. Als u een groep in Lenses wilt maken, gebruikt u de **object-id** van de groep **LensesUsers**. Dit is de id die u hebt gekopieerd uit de [maaksectie](#create-an-azure-ad-test-user-and-group).
1. Wijs de gewenste machtigingen voor B.Simon toe.

Zie [Azure - Lenses-groepstoewijzing](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups) voor meer informatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster de tegel Lenses.io selecteert, wordt u automatisch aangemeld bij de Lenses.io-portal. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie.

## <a name="additional-resources"></a>Extra resources

- [Eenmalige aanmelding instellen in uw exemplaar van Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure AD](./tutorial-list.md)

- [Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Wat is voorwaardelijke toegang in Azure AD?](../conditional-access/overview.md)

- [Lenses.io met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Lenses.io beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)