---
title: 'Zelfstudie: Azure Active Directory-integratie met JIRA SAML SSO by Microsoft (V5.2) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JIRA SAML SSO by Microsoft (V5.2).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 8afbf80fb6fa57db9de57122d7a4bfdb64e456bc
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185509"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Zelfstudie: Azure Active Directory-integratie met JIRA SAML SSO by Microsoft (V5.2)

In deze zelfstudie leert u hoe u JIRA SAML SSO van Microsoft (V5.2) kunt integreren met Azure Active Directory (Azure AD). Wanneer u JIRA SAML SSO van Microsoft (V5.2) integreert met Azure Active Directory, kunt u het volgende doen:

* Beheer in Azure AD wie toegang heeft tot JIRA SAML SSO van Microsoft (V5.2).
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij JIRA SAML SSO van Microsoft (V5.2).
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="description"></a>Beschrijving

Gebruik uw Microsoft Azure Active Directory-account met JIRA Server van Atlassian om eenmalige aanmelding mogelijk te maken. Op deze manier kunnen alle gebruikers in uw organisatie zich met hun Azure AD-referenties aanmelden bij de JIRA-toepassing. Deze invoegtoepassing gebruikt SAML 2.0 voor federatie.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met JIRA SAML SSO by Microsoft (V5.2) hebt u het volgende nodig:

- Een Azure AD-abonnement
- JIRA Core en Software 5.2 moeten zijn geïnstalleerd en geconfigureerd op een 64-bits versie van Windows
- JIRA-server is ingeschakeld voor HTTPS
- In de volgende sectie kunt u zien wat de ondersteunde versies zijn van de JIRA-invoegtoepassing.
- De JIRA-server is bereikbaar op internet vanaf de aanmeldingspagina van Azure AD voor verificatie en moet token uit Azure AD kunnen ontvangen
- Beheerderreferenties zijn ingesteld in JIRA
- WebSudo is uitgeschakeld in JIRA
- Testgebruiker gemaakt in de JIRA-servertoepassing

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, raden wij u aan niet een productieomgeving van JIRA te gebruiken. Test de integratie eerst in een ontwikkel- of faseringsomgeving van de toepassing en gebruik dan pas de productieomgeving.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u hier een gratis proefversie van één maand krijgen: [Proefversie](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Ondersteunde versies van JIRA

* JIRA Core en Software: 5.2
* JIRA ondersteunt ook 6.0 t/m 7.12. Klik voor meer informatie op [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Houd er rekening mee dat onze JIRA-invoegtoepassing ook werkt op Ubuntu versie 16.04.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* JIRA SAML SSO by Microsoft (V5.2) ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>JIRA SAML SSO by Microsoft (V5.2) toevoegen vanuit de galerie

Voor het configureren van de integratie van JIRA SAML SSO by Microsoft (V5.2) in Azure AD, moet u JIRA SAML SSO by Microsoft (V5.2) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het zoekvak van de sectie **Toevoegen uit de galerie** **JIRA SAML SSO van Microsoft (V5.2)** .
1. Selecteer **JIRA SAML SSO van Microsoft (V5.2)** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Eenmalige aanmelding van Azure AD voor JIRA SAML SSO van Microsoft (V5.2) configureren en testen

In deze sectie gaat u eenmalige aanmelding van Azure AD met JIRA SAML SSO van Microsoft (V5.2) configureren en testen. Dit doet u met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in JIRA SAML SSO by Microsoft (V5.2) tot stand is gebracht.

Voor het configureren van eenmalige aanmelding van Azure AD met JIRA SAML SSO van Microsoft (V5.2) moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding van JIRA SAML SSO van Microsoft (V5.2) configureren](#configure-jira-saml-sso-by-microsoft-v52-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Een testgebruiker maken in JIRA SAML SSO by Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)**: om in JIRA SAML SSO by Microsoft (V5.2) een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

1. Ga in de Azure Portal naar de integratiepagina van de toepassing **JIRA SAML SSO van Microsoft (V5.2)** . Selecteer vervolgens **Eenmalige aanmelding** in de sectie **Beheren**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<domain:port>/plugins/servlet/saml/auth`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<domain:port>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. 'Port' is optioneel als het een benoemde URL betreft. Deze waarden krijgt u tijdens de configuratie van de JIRA-invoegtoepassing, wat later in de zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure. Dit doet u door haar toegang te geven tot JIRA SAML SSO van Microsoft (V5.2).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **JIRA SAML SSO by Microsoft (V5.2)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>Eenmalige aanmelding configureren voor JIRA SAML SSO van Microsoft (V5.2)

1. Meld u in een ander browservenster als beheerder aan bij uw JIRA-instantie.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Schermopname met Add-ons (Invoegtoepassingen) geselecteerd in het menu Settings (Instellingen).](./media/jira52microsoft-tutorial/addon1.png)

3. Klik onder de tabbladsectie Invoegtoepassingen op **Manage add-ons**.

    ![Schermopname van Manage add-ons geselecteerd op het tabblad Add-ons.](./media/jira52microsoft-tutorial/addon7.png)

4. Download de invoegtoepassing uit het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56521). Upload de invoegtoepassing van Microsoft handmatig via het menu **Upload add-on**. Het downloaden van invoegtoepassingen valt onder de [Microsoft-serviceovereenkomst](https://www.microsoft.com/servicesagreement/).

    ![Schermopname van Manage add-ons (Invoegtoepassingen beheren) met de koppeling Upload add-on (Invoegtoepassing uploaden) uitgelicht.](./media/jira52microsoft-tutorial/addon12.png)

5. Zodra de invoegtoepassing is geïnstalleerd, wordt deze weergegeven in de sectie **User Installed** met door de gebruiker geïnstalleerde invoegtoepassingen. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Schermopname van de sectie SAML Single Sign-on for Jira van Azure AD met Configure geselecteerd.](./media/jira52microsoft-tutorial/addon13.png)

6. Voer de volgende stappen uit op de configuratiepagina:

    ![Schermopname van de configuratiepagina van Microsoft Jira SSO Connector.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Zorg ervoor dat er maar één certificaat is toegewezen aan de app, zodat er geen fout optreedt bij het omzetten van de metagegevens. Als er meerdere certificaten zijn, krijgt de beheerder een foutmelding bij het omzetten van de metagegevens.

    a. Plak in het tekstvak **Metadata URL** de waarde voor **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit de Azure-portal en klik op de knop **Resolve**. De URL met de IdP-metagegevens wordt gelezen en de overeenkomende velden worden ingevuld.

    b. Kopieer de waarden voor **Identifier, Reply URL, Sign on URL** en plak deze in respectievelijk de vakken **Id, Antwoord-URL en Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    c. Typ bij **Login Button Name** de naam van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.

    d. Selecteer bij **SAML User ID Locations** het keuzerondje **User ID is in the NameIdentifier element of the Subject statement** of **User ID is in an Attribute element**.  Deze id moet de gebruikers-id van JIRA zijn. Als de gebruikers-id niet overeenkomt, kunnen gebruikers zich niet aanmelden.

    > [!Note]
    > De gebruikers-id in de standaard SAML-configuratie is de NameIdentifier. U kunt dit wijzigen in een kenmerkoptie en de juiste kenmerknaam invoeren.

    e. Als u de optie **User ID is in an Attribute element** selecteert, typt u in het tekstvak **Attribute Name** de naam van het kenmerk waaruit de gebruikers-id moet worden opgehaald. 

    f. Als u het federatieve domein (zoals ADFS, enzovoort) gebruikt met Azure AD, selecteert u de optie **Enable Home Realm Discovery** en geeft u een naam op voor **Domain Name**.

    g. Typ bij **Domain Name** de domeinnaam als het aanmelding op basis van ADFS betreft.

    h. Selecteer **Eenmalige afmelding inschakelen** als een gebruiker moet worden afgemeld bij Azure AD op het moment dat deze zich afmeldt bij JIRA. 

    i. Klik op **Save** om de instellingen op te slaan.

    > [!NOTE]
    > Raadpleeg de [Beheerdershandleiding voor MS JIRA SSO Connector](./ms-confluence-jira-plugin-adminguide.md) en de [Veelgestelde vragen](./ms-confluence-jira-plugin-adminguide.md) voor meer informatie over de installatie en probleemoplossing.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Een testgebruiker maken in JIRA SAML SSO by Microsoft (V5.2)

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij de on-premises server van JIRA, moeten ze worden ingericht in de on-premises server van JIRA.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de on-premises server van JIRA.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Schermopname met User management (Gebruikersbeheer) geselecteerd in het menu Instellingen.](./media/jira52microsoft-tutorial/user1.png)

3. U wordt omgeleid naar de toegangspagina voor beheerders. Voer uw **wachtwoord** in en klik op de knop **Bevestigen**.

    ![Schermopname van de toegangspagina voor beheerders waar u uw referenties invoert.](./media/jira52microsoft-tutorial/user2.png)

4. Onder de tabbladsectie **Gebruikersbeheer** klikt u op **Gebruiker maken**.

    ![Schermopname van het tabblad Gebruikersbeheer met de optie Gebruiker maken.](./media/jira52microsoft-tutorial/user3.png) 

5. Op de pagina **Nieuwe gebruiker maken** voert u de volgende stappen uit:

    ![Schermopname met het dialoogvenster Nieuwe gebruiker maken waar u de gegevens voor deze stap kunt invoeren.](./media/jira52microsoft-tutorial/user4.png)

    a. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    b. In het tekstvak **Volledige naam** typt u de volledige naam van de gebruiker, bijvoorbeeld Britta Simon.

    c. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    e. Klik op **Gebruiker maken**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van JIRA SAML SSO van Microsoft (V5.2), waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van JIRA SAML SSO van Microsoft (V5.2) en initieer daar de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in 'Mijn apps' op de tegel 'JIRA SAML SSO door Microsoft (V5.2)' klikt, wordt u omgeleid naar de aanmeldings-URL van JIRA SAML SSO door Microsoft (V5.2). Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u JIRA SAML SSO van Microsoft (V5.2) hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor wordt uw organisatie in real time beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).