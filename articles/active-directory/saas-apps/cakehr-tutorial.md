---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met CakeHR - SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en CakeHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 08e028ba057ad57f3d600bc59bf7595c0b1d354c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met CakeHR - SSO

In deze zelfstudie leert u hoe u CakeHR integreert met Azure AD (Azure Active Directory). Wanneer u CakeHR integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot CakeHR.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij CakeHR.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een CakeHR-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* CakeHR biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-cakehr-from-the-gallery"></a>CakeHR uit de galerie toevoegen

Voor het configureren van de integratie van CakeHR in Azure Active Directory, moet u CakeHR uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **CakeHR**.
1. Selecteer in het resultatenvenster **CakeHR** en voeg dan de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Eenmalige aanmelding van Azure AD configureren en testen voor CakeHR

Configureer en test eenmalige aanmelding van Azure AD met CakeHR met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in CakeHR.

Voltooi de volgende stappen om eenmalige aanmelding bij CakeHR met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij CakeHR configureren](#configure-cakehr-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[Testgebruiker voor CakeHR maken](#create-cakehr-test-user)** : als u een equivalent van B.Simon in CakeHR wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **CakeHR** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<yourcakedomain>.cake.hr/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [klantondersteuningsteam voor CakeHR](mailto:info@cake.hr) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-handtekeningcertificaat** de **VINGERAFDRUKwaarde** en sla deze in uw Kladblok op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. In de sectie **CakeHR instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot CakeHR.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **CakeHR** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cakehr-sso"></a>Eenmalige aanmelding CakeHR configureren

1. Als u de configuratie in CakeHR wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **CakeHR instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de CakeHR-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij CakeHR. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u CakeHR handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de CakeHR-bedrijfssite. Voer hierna de volgende stappen uit:

1. Klik in de rechterbovenhoek van de pagina op **Profiel** en navigeer vervolgens naar **Instellingen**.

    ![Schermopname met Profiel en Instellingen geselecteerd.](./media/cakehr-tutorial/config01.png)

1. Klik in het linkermenu op **INTEGRATIE** > **SAML SSO** en voer de volgende stappen uit:

    ![Schermopname met het deelvenster Instellingen waarin u deze stappen kunt uitvoeren.](./media/cakehr-tutorial/config02.png)

    a. Typ `cake.hr` in het tekstvak **Entiteits-ID**.

    b. Plak in het tekstvak **Verificatie-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Plak in het tekstvak **Sleutelvingerafdruk (SHA1-indeling)** de waarde van de **THUMBPRINT** van het certificaat die u hebt gekopieerd uit de Azure-portal.

    d. Schakel het selectievakje **Enable Single Sign-On** in.

    e. Klik op **Opslaan**.

### <a name="create-cakehr-test-user"></a>Testgebruiker voor CakeHR maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij CakeHR, moeten ze worden ingericht in CakeHR. Het inrichten is in CakeHR een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij CakeHR aan als een beveiligingsbeheerder.

2. Klik in de linkerkant van de menubalk op **BEDRIJF** > **TOEVOEGEN**.

    ![Schermopname van CakeHR met COMPANY en ADD geselecteerd.](./media/cakehr-tutorial/config03.png)

3. Voer de volgende stappen uit in de pop-up **Nieuwe werknemer toevoegen**:

     ![Schermopname met Add new employee waar u deze stappen uitvoert.](./media/cakehr-tutorial/config04.png)

    a. Voer in het tekstvak **Full name** de naam van de gebruiker in, bijvoorbeeld B.Simon.

    b. Voer in het tekstvak **Werk-e-mailadres** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    c. Klik op **ACCOUNT MAKEN**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel CakeHR in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van CakeHR waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [CakeHR proberen met Azure AD](https://aad.portal.azure.com/)