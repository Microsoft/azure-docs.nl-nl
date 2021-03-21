---
title: 'Zelfstudie: Azure Active Directory-integratie met Carbonite Endpoint Backup | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Carbonite Endpoint Backup configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: ff19275270e5b6572fb7d637b88c4736a3aa6ea0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456480"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Zelfstudie: Carbonite Endpoint Backup integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Carbonite Endpoint Backup integreert met Azure Active Directory (Azure AD). Als u Carbonite Endpoint Backup integreert met Azure Active Directory, kunt u:

* Bepaal in Azure AD wie er toegang heeft tot Carbonite Endpoint Backup.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Carbonite Endpoint Backup.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Carbonite Endpoint Backup met eenmalige aanmelding ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Carbonite Endpoint Backup ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Carbonite Endpoint Backup toevoegen vanuit de galerie

Om de integratie van Carbonite Endpoint Backup in Azure AD te configureren, moet u Carbonite Endpoint Backup vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **Toevoegen uit de galerie** **Carbonite Endpoint Backup** in het zoekvak.
1. Selecteer **Carbonite Endpoint Backup** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Carbonite Endpoint Backup met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Carbonite Endpoint Backup.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Carbonite Endpoint Backup te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Carbonite Endpoint Backup configureren](#configure-carbonite-endpoint-backup-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Testgebruiker voor Carbonite Endpoint Backup maken](#create-carbonite-endpoint-backup-test-user)** : een tegenhanger voor B.Simon maken in Carbonite Endpoint Backup die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de toepassingsintegratiepagina van **Carbonite Endpoint Backup** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een van de volgende URL's:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. In het tekstvak **Antwoord-URL** typt u een van de volgende URL's:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een van de volgende URL's:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In het gedeelte **Carbonite Endpoint Backup instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Eenmalige aanmelding Carbonite Endpoint Backup configureren

1. Als u de configuratie in Carbonite Endpoint Backup wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Door op **Carbonite Endpoint Backup instellen** te klikken nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Carbonite Endpoint Backup-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij Carbonite Endpoint Backup. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Carbonite Endpoint Backup handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Carbonite Endpoint Backup-bedrijfssite. Voer daarna de volgende stappen uit:

4. Klik in het linkerdeelvenster op het **Bedrijf**.

    ![Schermopname met Carbonite-eindpunt waarbij Bedrijf is geselecteerd.](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Klik op **Eenmalige aanmelding**.

    ![Schermopname met Bedrijf waarbij Eenmalige aanmelding is geselecteerd.](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Klik op **Inschakelen** en klik vervolgens op **Instellingen bewerken** om te configureren.

    ![Schermopname die het tabblad Eenmalige aanmelding toont en de instellingen voor Inschakelen en Bewerken zijn gemarkeerd.](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Voer op de pagina instellingen voor **Eenmalige aanmelding** de volgende stappen uit:

    ![Schermopname die het tabblad Eenmalige aanmelding toont met de informatie die in deze stap wordt beschreven.](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Plak in het tekstvak **Naam van id-provider** de waarde van de **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het tekstvak **URL van id-provider** de waarde voor **Aanmeldigs-URL** die u uit de Azure-portal hebt gekopieerd.

    1. Klik op **Bestand kiezen** om het gedownloade **certificaat (Base64)** vanuit de Azure-portal te uploaden.

    1. Klik op **Opslaan**.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Carbonite Endpoint Backup.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Carbonite Endpoint Backup**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Testgebruiker voor Carbonite Endpoint Backup maken

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Carbonite Endpoint Backup.

1. Klik op **Gebruikers** in het linkerdeelvenster en vervolgens op **Gebruikers toevoegen**.

    ![Schermopname die de pagina Carbonite-eindpunt toont met Gebruikers en Gebruikers toevoegen geselecteerd.](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Voer op de pagina **Gebruiker toevoegen** de volgende stappen uit:

    ![Schermopname die de pagina Gebruiker toevoegen toont, waar u de stappen kunt uitvoeren die hier worden beschreven.](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Voer **e-mailadres**, **voornaam** en **achternaam** van de gebruiker in en geef de vereiste machtigingen aan de gebruiker op basis van de vereisten van de organisatie.

    1. Klik op **Gebruiker toevoegen**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Carbonite Endpoint Backup klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Carbonite Endpoint Backup in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)