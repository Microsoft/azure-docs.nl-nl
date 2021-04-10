---
title: 'Zelfstudie: Azure Active Directory-integratie met MobileIron | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en MobileIron configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653019"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Zelfstudie: Azure Active Directory-integratie met MobileIron

 In deze zelf studie leert u hoe u Mobile Iron integreert met Azure Active Directory (Azure AD). Wanneer u Mobile Iron integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Mobile Iron.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Mobile Iron met hun Azure AD-accounts.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Mobile Iron-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mobile Iron ondersteunt door **SP en IDP** geïnitieerde SSO.

## <a name="add-mobileiron-from-the-gallery"></a>Mobile Iron toevoegen vanuit de galerie

Om de integratie van MobileIron te configureren in Azure AD, moet u MobileIron vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **toevoegen vanuit de galerie** **Mobile Iron** in het zoekvak.
1. Selecteer **Mobile Iron** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Azure AD SSO voor Mobile Iron configureren en testen

Azure AD SSO met Mobile Iron configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Mobile Iron.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Mobile Iron:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
     1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Mobile Iron SSO configureren](#configure-mobileiron-sso)** : als u de instellingen voor één Sign-On wilt configureren aan de kant van de toepassing.
    1. **[Een testgebruiker voor MobileIron maken](#create-mobileiron-test-user)**: als u een tegenhanger van Britta Simon in MobileIron wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u eenmalige aanmelding van Azure AD in de Azure Portal inschakelen.
 
1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Mobile Iron** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.MobileIron.com/<key>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

     In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. U kunt de waarden voor de sleutel en de host opzoeken in de beheerportal van MobileIron. Dit wordt verderop in de zelfstudie uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Mobile Iron.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Mobile Iron**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-mobileiron-sso"></a>Mobile Iron SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van MobileIron.

2. Ga naar **Admin** > **Identity** en selecteer de optie **AAD** in de vervolgkeuzelijst **Info on Cloud IDP Setup**.

    ![Schermopname toont het beheertabblad van de MobileIron-site, met Identity geselecteerd.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Kopieer de waarden van **Key** en **Host** en plak deze in de sectie **Standaard SAML-configuratie** in de Azure-portal om de URL's te voltooien.

    ![Schermopname toont de optie Setting Up SAML met een sleutel en hostwaarde.](./media/MobileIron-tutorial/key.png)

4. Klik in het veld **Export Metadata file from AAD and Import to MobileIron Cloud** op **Choose File** om de metagegevens die u hebt gedownload uit de Azure-portal te uploaden. Klik op **Done** als het uploaden is voltooid.

    ![Eenmalige aanmelding configureren](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Een testgebruiker maken voor MobileIron

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij MobileIron, moeten ze worden ingericht voor MobileIron.  
In het geval van MobileIron is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van MobileIron.

1. Ga naar **Users** en klik op **Add** > **Single User**.

    ![Eenmalige aanmelding configureren](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Voer in het dialoogvenster **Add Single User** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. Typ in het tekstvak **Email Address** het e-mailadres van de gebruiker, zoals brittasimon@contoso.com.

    b. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals Britta.

    c. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, zoals Simon.

    d. Klik op **Gereed**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor Mobile Iron-aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de URL voor Mobile Iron-aanmelding en start de aanmeldings stroom vanaf daar.

### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Mobile Iron waarvoor u de SSO hebt ingesteld.

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Mobile Iron in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze in de IDP-modus is geconfigureerd, moet u automatisch worden aangemeld bij de Mobile Iron waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u de Mobile Iron hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
