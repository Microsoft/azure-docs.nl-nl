---
title: 'Zelfstudie: Azure Active Directory-integratie met MyWorkDrive | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en MyWorkDrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: b639ee45bfa6356054d93a1b6fcc8a7959173acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516083"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Zelfstudie: MyWorkDrive integreren met Azure Active Directory

In deze zelfstudie leert u hoe u MyWorkDrive integreert met Azure AD (Azure Active Directory). Wanneer u MyWorkDrive integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot MyWorkDrive.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij MyWorkDrive.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een MyWorkDrive-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. MyWorkDrive ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-myworkdrive-from-the-gallery"></a>MyWorkDrive toevoegen vanuit de galerie

Als u de integratie van MyWorkDrive in Azure AD wilt configureren, moet u MyWorkDrive vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **MyWorkDrive** in het zoekvak van de sectie **Toevoegen uit de galerie**.
1. Selecteer **MyWorkDrive** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met MyWorkDrive met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MyWorkDrive.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met MyWorkDrive te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij MyWorkDrive configureren](#configure-myworkdrive-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor MyWorkDrive maken](#create-myworkdrive-test-user)** : als u een tegenhanger van Britta Simon in MyWorkDrive wilt hebben die is gekoppeld aan de Microsoft Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure Portal](https://portal.azure.com/), op de integratiepagina voor de **MyWorkDrive**-toepassing, naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Voer de hostnaam van de MyWorkDrive-server van uw bedrijf in, bijv.
    > 
    > Antwoord-URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Aanmeldings-URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Neem contact op met het [ondersteuningsteam van MyWorkDrive](mailto:support@myworkdrive.com) als u niet zeker weet hoe u uw eigen hostnaam en TLS/SSL-certificaat voor deze waarden kunt instellen.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren naar uw klembord.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Eenmalige aanmelding bij MyWorkDrive configureren

1. Als u de configuratie in MyWorkDrive wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **MyWorkDrive instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de MyWorkDrive-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij MyWorkDrive. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 en 4 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u MyWorkDrive handmatig wilt instellen in een ander browservenster, meldt u zich aan bij MyWorkDrive als een beveiligingsbeheerder.

1. Klik in de MyWorkDrive-server in het beheerdeelvenster op **ENTERPRISE** en voer de volgende stappen uit:

    ![De Beheerder](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Schakel **Eenmalige aanmelding via SAML/ADFS** in.

    b. Selecteer **SAML - Azure AD**

    c. Plak in het tekstvak **URL van metagegevens uit Azure-app** de waarde van **URL van metagegevens van App Federation** die u uit Azure Portal hebt gekopieerd.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > Lees voor meer informatie het [ondersteuningsartikel over MyWorkDrive en Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

In dit gedeelte hebt u Britta Simon in staat gesteld gebruik te maken van Azure-eenmalige aanmelding door haar toegang te geven tot MyWorkDrive.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **MyWorkDrive** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-myworkdrive-test-user"></a>Een testgebruiker maken in MyWorkDrive

In deze sectie gaat u in MyWorkDrive een gebruiker maken met de naam Britta Simon. Werk samen met het [ondersteuningsteam van MyWorkDrive](mailto:support@myworkdrive.com) om de gebruikers toe te voegen in het MyWorkDrive-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel MyWorkDrive selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)