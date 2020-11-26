---
title: 'Zelfstudie: Azure Active Directory-integratie met Dropbox Business | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Dropbox Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: bf86656ff670df19162867d597a869d762a012e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019341"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Zelfstudie: Dropbox Business integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Dropbox Business kunt integreren met Azure Active Directory (Azure AD). Wanneer u Dropbox Business integreert met Azure Active Directory, kunt u:

* In Azure AD beheren wie toegang heeft tot Dropbox Business.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Dropbox Business.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement dat geschikt is voor eenmalige aanmelding (SSO) voor Dropbox Business.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

* In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Dropbox Business ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Dropbox Business biedt ondersteuning voor het [automatisch inrichten van gebruikers](dropboxforbusiness-tutorial.md)
* Zodra u Dropbox hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>Dropbox Business toevoegen vanuit de galerie

Voor het configureren van de integratie van Dropbox Business in Azure AD moet u Dropbox Business uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Dropbox Business** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Dropbox Business** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met Dropbox Business met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Dropbox Business.

Gebruik de volgende bouwstenen om Azure AD-eenmalige aanmelding met Dropbox Business te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.    
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Eenmalige aanmelding bij Dropbox Business configureren](#configure-dropbox-business-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Een testgebruiker maken in Dropbox Business](#create-dropbox-business-test-user)** : om in Dropbox Business een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Dropbox Business** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.dropbox.com/sso/<id>`

    b. Typ een waarde in het vak **Id (Entiteits-id)** : `Dropbox`

    > [!NOTE]
    > De bovenstaande waarde voor de aanmeldings-URL is geen echte waarde. U werkt de waarde bij met de werkelijke aanmeldings-URL, zoals later in de zelfstudie wordt uitgelegd.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer onder **Dropbox Business instellen** de URL('s) die u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL


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

In dit gedeelte hebt u Britta Simon in staat gesteld gebruik te maken van Azure-eenmalige aanmelding door haar toegang te geven tot Dropbox Business.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Dropbox Business** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-dropbox-business-sso"></a>Eenmalige aanmelding voor Dropbox Business configureren

1. Als u de configuratie met Dropbox Business wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Dropbox Business instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Dropbox Business-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Dropbox Business. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 8 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Dropbox Business handmatig wilt instellen, open dan een nieuw browservenster en ga naar uw Dropbox Business-tenant en meld u aan bij uw Dropbox Business-tenant. en voer de volgende stappen uit:

    ![Schermopname met de pagina voor het aanmelden bij Dropbox Business.](./media/dropboxforbusiness-tutorial/ic769509.png "Eenmalige aanmelding configureren")

4. Klik op het **Gebruikerspictogram** en selecteer het tabblad **Instellingen**.

    ![Schermopname met de actie "USER ICON" en "Settings" geselecteerd.](./media/dropboxforbusiness-tutorial/configure1.png "Eenmalige aanmelding configureren")

5. Klik in het navigatievenster aan de linkerkant op **Beheerconsole**.

    ![Schermopname met "Admin console" geselecteerd.](./media/dropboxforbusiness-tutorial/configure2.png "Eenmalige aanmelding configureren")

6. Klik in de **Beheerconsole** op **Instellingen** in het navigatievenster links.

    ![Schermopname met "Settings" geselecteerd.](./media/dropboxforbusiness-tutorial/configure3.png "Eenmalige aanmelding configureren")

7. Selecteer de optie **Eenmalige aanmelding** in het gedeelte **Verificatie**.

    ![Schermopname met de sectie "Authentication" met "Single sign-on" geselecteerd.](./media/dropboxforbusiness-tutorial/configure4.png "Eenmalige aanmelding configureren")

8. Voer in het gedeelte **Eenmalige aanmelding** de volgende stappen uit:  

    ![Schermopname met de configuratie-instellingen voor eenmalige aanmelding.](./media/dropboxforbusiness-tutorial/configure5.png "Eenmalige aanmelding configureren")

    a. Selecteer de optie **Vereist** in de vervolgkeuzelijst voor de **Eenmalige aanmelding**.

    b. Klik op **Aanmeldings-URL toevoegen**, plak in het tekstvak **Aanmeldings-URL identiteitsprovider** de **Aanmeldings-URL** die u hebt gekopieerd van de Azure-portal en selecteer **Gereed**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure6.png "Eenmalige aanmelding configureren")

    c. Klik op **Certificaat uploaden** en blader naar het **Base64-gecodeerde certificaatbestand** dat u hebt gedownload uit de Azure-portal.

    d. Klik op **Koppeling kopiëren** en plak de gekopieerde waarde in het tekstvak **Aanmeldings-URL** van het gedeelte **Domein en URL’s voor Dropbox Business** in Azure Portal.

    e. Klik op **Opslaan**.

### <a name="create-dropbox-business-test-user"></a>Een testgebruiker maken in Dropbox Business

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Dropbox Business. Dropbox Business ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Dropbox Business bestaat, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u handmatig een gebruiker moet maken, neem dan contact op met [het ondersteuningsteam van Dropbox Business](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel Dropbox Business in het toegangsvenster selecteert, zou u automatisch moeten worden aangemeld bij de instantie van Dropbox Business waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](/cloud-app-security/proxy-intro-aad)

- [Dropbox Business met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)