---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ContractSafe Saml2 SSO | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ContractSafe Saml2 SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 2d236b9910e2eda8e574d020544d625a68aefb81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455306"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ContractSafe Saml2 SSO

In deze zelfstudie leert u hoe u ContractSafe Saml2 SSO kunt integreren met Azure AD (Active Directory). Wanneer u ContractSafe Saml2 SSO integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot ContractSafe Saml2 SSO.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij ContractSafe Saml2 SSO.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een ContractSafe Saml2 SSO-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. ContractSafe Saml2 SSO biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>ContractSafe Saml2 SSO toevoegen vanuit de galerie

Om de integratie van ContractSafe Saml2 SSO te configureren in Azure AD moet u ContractSafe Saml2 SSO vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **ContractSafe Saml2 SSO**.
1. Selecteer **ContractSafe Saml2 SSO** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen voor ContractSafe Saml2 SSO

Configureer en test eenmalige aanmelding van Azure AD met ContractSafe Saml2 SSO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ContractSafe Saml2 SSO.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met ContractSafe Saml2 SSO, moet u de volgende procedures uitvoeren:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
   * [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met het account **B.Simon**.
   * [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat **B.Simon** eenmalige aanmelding van Azure AD kan gebruiken.

1. [Configureer ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
   * [Maak een ContractSafe Saml2 SSO-testgebruiker](#create-a-contractsafe-saml2-sso-test-user) om een tegenhanger van **B.Simon** in ContractSafe Saml2 SSO te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
2. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ContractSafe Saml2 SSO** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het bewerkingspictogram (de pen) voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Eenmalige aanmelding instellen met SAML** de waarden voor de bijbehorende velden in:

    a. Voer in het tekstvak **Id** een URL in de volgende indeling in: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Voer in het tekstvak **Antwoord-URL** een URL in de volgende indeling in: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van ContractSafe Saml2 SSO](mailto:support@contractsafe.com) om deze waarden op te vragen. U kunt ook de indelingen raadplegen in de sectie **Standaard SAML-configuratie** van de Azure-portal.

1. In ContractSafe Saml2 SSO worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Algemene standaardkenmerken](common/default-attributes.png)

1. Naast de standaardkenmerken worden in de ContractSafe Saml2 SSO-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgegeven. Deze kenmerken worden vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten. De volgende lijst bevat de extra kenmerken.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | e-mail | user.onpremisesuserprincipalname |

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** in het gedeelte **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens**. Selecteer **Downloaden** om het certificaat te downloaden en op de computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **ContractSafe Saml2 SSO instellen** de juiste URL('s) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam **B.Simon** in Azure Portal.

1. Selecteer in het linkerdeelvenster in Azure Portal de optie **Azure Active Directory**. Selecteer **Gebruikers** en daarna **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **Gebruikersnaam** een e-mailadres in de indeling `username@companydomain.extension` in. Een voorbeeld is `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

## <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u **Britta Simon** de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ContractSafe Saml2 SSO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Templafy Saml2** in de lijst met toepassingen.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren**, en selecteer vervolgens **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers**. Selecteer vervolgens onderaan het scherm de knop **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-contractsafe-saml2-sso"></a>Eenmalige aanmelding bij ContractSafe Saml2 configureren

Als u eenmalige aanmelding aan de zijde van **ContractSafe Saml2 SSO** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [ondersteuningsteam van ContractSafe Saml2 SSO](mailto:support@contractsafe.com). Het team is aan beide zijden verantwoordelijk voor het juist instellen van de SAML-verbinding voor eenmalige aanmelding.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Een ContractSafe Saml2 SSO-testgebruiker maken

Maak een gebruiker met de naam B.Simon in ContractSafe Saml2 SSO. Neem contact op met het [ondersteuningsteam van ContractSafe Saml2 SSO](mailto:support@contractsafe.com) om de gebruikers toe te voegen aan het ContractSafe Saml2 SSO-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

Test uw configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster. Wanneer u in het toegangsvenster op de tegel ContractSafe Saml2 SSO klikt, wordt u automatisch aangemeld bij de instantie van ContractSafe Saml2 SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [ContractSafe Saml2 SSO uitproberen met Azure AD](https://aad.portal.azure.com/)