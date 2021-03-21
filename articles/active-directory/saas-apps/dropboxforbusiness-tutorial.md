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
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 41f6db8cf2454c224addac525e9d039954a95712
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601496"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Zelfstudie: Dropbox Business integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Dropbox Business kunt integreren met Azure Active Directory (Azure AD). Wanneer u Dropbox Business integreert met Azure Active Directory, kunt u:

* In Azure AD beheren wie toegang heeft tot Dropbox Business.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Dropbox Business.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement dat geschikt is voor eenmalige aanmelding (SSO) voor Dropbox Business.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

* In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Dropbox Business ondersteunt door **SP** geïnitieerde SSO.

* Dropbox Business ondersteunt [automatische gebruikers inrichting en](dropboxforbusiness-tutorial.md)het ongedaan maken van de inrichting.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-dropbox-business-from-the-gallery"></a>Dropbox-bedrijven toevoegen vanuit de galerie

Voor het configureren van de integratie van Dropbox Business in Azure AD moet u Dropbox Business uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Dropbox Business** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Dropbox Business** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>Azure AD SSO configureren en testen voor Dropbox-bedrijven

Configureer en test Azure AD-eenmalige aanmelding met Dropbox Business met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Dropbox Business.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Dropbox Business:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.    
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Eenmalige aanmelding bij Dropbox Business configureren](#configure-dropbox-business-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Een testgebruiker maken in Dropbox Business](#create-dropbox-business-test-user)** : om in Dropbox Business een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **Dropbox Business** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.dropbox.com/sso/<id>`
    
     b. Typ in het vak **Id (Entiteits-id)** de waarde: `Dropbox`
    
    > [!NOTE]
    > U kunt de **SSO-id van de Dropbox-ondertekening** vinden op de Dropbox-site in Dropbox > de >-instellingen van de beheer console > eenmalige aanmelding > AANMELD-URL voor SSO.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer onder **Dropbox Business instellen** de URL('s) die u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)


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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Dropbox Business.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Dropbox Business** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-dropbox-business-sso"></a>Eenmalige aanmelding voor Dropbox Business configureren

1. Als u de configuratie met Dropbox Business wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Dropbox Business instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Dropbox Business-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Dropbox Business. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 8 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Dropbox Business handmatig wilt instellen, open dan een nieuw browservenster en ga naar uw Dropbox Business-tenant en meld u aan bij uw Dropbox Business-tenant. en voer de volgende stappen uit:

    ![Schermopname met de pagina voor het aanmelden bij Dropbox Business.](./media/dropboxforbusiness-tutorial/account.png "Eenmalige aanmelding configureren")

4. Klik op het **Gebruikerspictogram** en selecteer het tabblad **Instellingen**.

    ![Schermopname met de actie "USER ICON" en "Settings" geselecteerd.](./media/dropboxforbusiness-tutorial/configure-1.png "Eenmalige aanmelding configureren")

5. Klik in het navigatievenster aan de linkerkant op **Beheerconsole**.

    ![Schermopname met "Admin console" geselecteerd.](./media/dropboxforbusiness-tutorial/configure-2.png "Eenmalige aanmelding configureren")

6. Klik in de **Beheerconsole** op **Instellingen** in het navigatievenster links.

    ![Schermopname met "Settings" geselecteerd.](./media/dropboxforbusiness-tutorial/configure-3.png "Eenmalige aanmelding configureren")

7. Selecteer de optie **Eenmalige aanmelding** in het gedeelte **Verificatie**.

    ![Schermopname met de sectie "Authentication" met "Single sign-on" geselecteerd.](./media/dropboxforbusiness-tutorial/configure-4.png "Eenmalige aanmelding configureren")

8. Voer in het gedeelte **Eenmalige aanmelding** de volgende stappen uit:  

    ![Schermopname met de configuratie-instellingen voor eenmalige aanmelding.](./media/dropboxforbusiness-tutorial/configure-5.png "Eenmalige aanmelding configureren")

    a. Selecteer de optie **Vereist** in de vervolgkeuzelijst voor de **Eenmalige aanmelding**.

    b. Klik op **Aanmeldings-URL toevoegen**, plak in het tekstvak **Aanmeldings-URL identiteitsprovider** de **Aanmeldings-URL** die u hebt gekopieerd van de Azure-portal en selecteer **Gereed**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/sso.png "Eenmalige aanmelding configureren")

    c. Klik op **Certificaat uploaden** en blader naar het **Base64-gecodeerde certificaatbestand** dat u hebt gedownload uit de Azure-portal.

    d. Klik op **Koppeling kopiëren** en plak de gekopieerde waarde in het tekstvak **Aanmeldings-URL** van het gedeelte **Domein en URL’s voor Dropbox Business** in Azure Portal.

    e. Klik op **Opslaan**.

### <a name="create-dropbox-business-test-user"></a>Een testgebruiker maken in Dropbox Business

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Dropbox Business. Dropbox Business ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Dropbox Business bestaat, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u handmatig een gebruiker moet maken, neem dan contact op met [het ondersteuningsteam van Dropbox Business](https://www.dropbox.com/business/contact)

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van Dropbox Business, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de Dropbox-URL voor Business Sign-on en start de aanmeldings stroom vanaf die locatie.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Dropbox-bedrijfs tegel in de mijn apps klikt, wordt dit omgeleid naar de Dropbox Business Sign-on-URL. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u een Dropbox-bedrijf hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).