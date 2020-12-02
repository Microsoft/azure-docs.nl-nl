---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met NetSuite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en NetSuite configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: jeedes
ms.openlocfilehash: 9bebb11c3a1162a0a7b72140f7d3812f86f793ce
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791367"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met NetSuite

In deze zelfstudie leert u hoe u NetSuite integreert met Azure Active Directory (Azure AD). Wanneer u NetSuite integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot NetSuite.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij NetSuite.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op NetSuite waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 

Netsuite biedt ondersteuning voor:

* Door IDP geïnitieerde eenmalige aanmelding.
* Just-In-Time inrichten van gebruikers.
* [Geautomatiseerd inrichten van gebruikers](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Omdat de id van deze toepassing een vaste tekenreekswaarde is, kan maar één exemplaar in één tenant worden geconfigureerd.

## <a name="add-netsuite-from-the-gallery"></a>NetSuite toevoegen vanuit de galerie

Om de integratie van NetSuite in Azure AD te configureren, moet u NetSuite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps. U doet dit als volgt:

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in het gedeelte **Toevoegen uit de galerie** **NetSuite** in het zoekvak.
1. Selecteer **NetSuite** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Eenmalige aanmelding van Azure AD configureren en testen voor NetSuite

Configureer en test eenmalige aanmelding van Azure AD met NetSuite met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in NetSuite.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met NetSuite te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    * [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user): om eenmalige aanmelding van Azure AD te testen met de gebruiker B.Simon.  
    * [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat de gebruiker B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding bij NetSuite configureren](#configure-netsuite-sso) als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * [Testgebruiker voor NetSuite maken](#create-the-netsuite-test-user): een tegenhanger voor de gebruiker B.Simon maken in NetSuite die is gekoppeld aan de weergave van de gebruiker in Azure AD.
1. [Eenmalige aanmelding testen](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Ga als volgt te werk om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Ga in de Azure Portal op de integratiepagina van de toepassing **NetSuite** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** in het deelvenster **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer in het deelvenster **Eenmalige aanmelding met SAML instellen** het pictogram **Bewerken** (potlood) naast **Standaard SAML-configuratie**.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Typ in de sectie **Standaard SAML-configuratie** in het tekstvak **Antwoord-URL** een URL in een van de volgende indelingen:

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * U krijgt de waarde voor **<`Instance ID`>** in de configuratiesectie van Netsuite, die verderop in de zelfstudie wordt beschreven in stap 8 onder Netsuite-configuratie. U krijgt het exacte domein (zoals in dit geval system.na0.netsuite.com).

        ![Schermopname van de pagina SAML-installatie waar u het domein kunt ophalen.](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > De waarden in de voorgaande URL's zijn niet echt. Werk de waarden bij met de werkelijke antwoord-URL. Neem contact op met het [ondersteuningsteam van NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) om de waarde te verkrijgen. U kunt ook de indelingen raadplegen in de sectie **Standaard SAML-configuratie** van de Azure-portal.

1. In de NetSuite-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de NetSuite nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > De waarde voor het accountkenmerk is niet echt. U gaat deze waarde later aanpassen. Dit wordt verderop in de zelfstudie uitgelegd.

1. Ga op de pagina Eenmalige aanmelding met SAML instellen in de sectie SAML-handtekeningcertificaat naar XML-bestand met federatieve metagegevens en selecteer Downloaden om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in het gedeelte **NetSuite instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

1. Selecteer **Nieuwe gebruiker** boven aan het scherm.

1. Volg de volgende stappen in het eigenschappendeelvenster **Gebruiker**:

   a. Voer in het vak **Naam** de naam **B.Simon** in.  
   b. Voer in het vak **Gebruikersnaam** de username@companydomain.extension in (bijvoorbeeld B.Simon@contoso.com).  
   c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.  
   d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u de gebruiker B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door hem of haar toegang te verlenen tot NetSuite.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **NetSuite** in de lijst met toepassingen.
1. Zoek in het overzichtsvenster naar het gedeelte **Beheren** en selecteer vervolgens de koppeling **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst **Gebruikers** en selecteer vervolgens de knop **Selecteren** onder aan het scherm.
1. Ga als volgt te werk als u een willekeurige rol in de SAML-assertie verwacht:

   a. Selecteer in het deelvenster **Rol selecteren** in de vervolgkeuzelijst de juiste rol voor de gebruiker.  
   b. Selecteer onder aan het scherm de knop **Selecteren**.
1. Selecteer in het deelvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-netsuite-sso"></a>Eenmalige aanmelding voor NetSuite configureren

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfssite van NetSuite.

2. Selecteer in de bovenste navigatiebalk de optie **Setup** en selecteer vervolgens **Company** > **Enable Features**.

    ![Schermopname van Functies inschakelen geselecteerd vanuit Bedrijf.](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Selecteer op de werkbalk in het midden van de pagina **SuiteCloud**.

    ![Schermopname met SuiteCloud geselecteerd.](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Selecteer onder **Manage Authentication** het selectievakje **SAML Single Sign-on** om de optie voor eenmalige aanmelding met SAML in te schakelen in NetSuite.

    ![Schermopname van Verificatie beheren waar u Eenmalige aanmelding voor SAML kunt selecteren.](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Selecteer in de bovenste navigatiebalk de optie **Setup**.

    ![Schermopname van Installatie geselecteerd vanuit de navigatiebalk van NETSUITE.](./media/NetSuite-tutorial/ns-setup.png)

6. Selecteer in de lijst **Setup Tasks** de optie **Integration**.

    ![Schermopname van Integratie die is geselecteerd vanuit TAKEN INSTALLEREN.](./media/NetSuite-tutorial/ns-integration.png)

7. Selecteer onder **Manage Authentication** de optie **SAML Single Sign-on**.

    ![Schermopname van Eenmalige aanmelding voor SAML geselecteerd vanuit het Integratie-item in TAKEN INSTALLEREN.](./media/NetSuite-tutorial/ns-saml.png)

8. Voer in het deelvenster **SAML Setup**, onder **NetSuite Configuration**, de volgende stappen uit:

    ![Schermopname van de SAML-installatie waarin u de beschreven waarden kunt invoeren.](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Schakel het selectievakje **Primary Authentication Method** in.

    b. Selecteer onder **SAMLV2 Identity Provider Metadata** de optie **Upload IDP Metadata File** en selecteer vervolgens **Browse** om het metagegevensbestand te uploaden dat u hebt gedownload van de Azure-portal.

    c. Selecteer **Indienen**.

9. Selecteer in de bovenste navigatiebalk in NetSuite de optie **Setup** en selecteer vervolgens **Company** > **Company Information**.

    ![Schermopname van Bedrijfsgegevens geselecteerd vanuit Bedrijf.](./media/NetSuite-tutorial/ns-com.png)

    ![Schermopname van het deelvenster waarin u de beschreven waarden kunt invoeren.](./media/NetSuite-tutorial/ns-account-id.png)

    b. Kopieer de waarde van **Account ID** in de rechterkolom in het deelvenster **Company Information**.

    c. Plak de **account-id** die u hebt gekopieerd uit het NetSuite-account in het vak **Waarde kenmerk** in Azure AD.

    ![Schermopname laat zien dat de waarde van de account-id wordt toegevoegd](./media/netsuite-tutorial/attribute-value.png)

10. Voordat gebruikers eenmalige aanmelding kunnen gebruiken met NetSuite moeten ze over de juiste machtigingen beschikken in NetSuite. Ga als volgt te werk om deze machtigingen toe te wijzen:

    a. Selecteer in de bovenste navigatiebalk de optie **Setup**.

    ![Schermopname van Installatie geselecteerd vanuit de navigatiebalk van NETSUITE.](./media/NetSuite-tutorial/ns-setup.png)

    b. Selecteer in het linkerdeelvenster de optie **Users/Roles** en selecteer vervolgens **Manage Roles**.

    ![Schermopname van het deelvenster Rollen beheren waar u een Nieuwe rol kunt selecteren.](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selecteer **New Role**.

    d. Voer een **naam** in voor de nieuwe rol.

    ![Schermopname van de Installatiebeheerder waarin u een naam voor de rol kunt invoeren.](./media/NetSuite-tutorial/ns-new-role.png)

    e. Selecteer **Opslaan**.

    f. Selecteer in de bovenste navigatiebalk de optie **Permissions**. Selecteer vervolgens **Setup**.

    ![Schermopname van het tabblad Installeren waarin u de beschreven waarden kunt invoeren.](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecteer **SAML Single Sign-on** en selecteer vervolgens **Add**.

    h. Selecteer **Opslaan**.

    i. Selecteer in de bovenste navigatiebalk de optie **Setup** en selecteer vervolgens **Setup Manager**.

    ![Schermopname van Installatie geselecteerd vanuit de navigatiebalk van NETSUITE.](./media/NetSuite-tutorial/ns-setup.png)

    j. Selecteer in het linkerdeelvenster de optie **Users/Roles** en selecteer vervolgens **Manage Users**.

    ![Schermopname van het deelvenster Gebruikers beheren waar u Suite Demo Team kunt selecteren.](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecteer een testgebruiker, selecteer **Edit** en selecteer vervolgens het tabblad **Access**.

    ![Schermopname van het deelvenster Gebruikers beheren waar u Bewerken kunt selecteren.](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Wijs in het deelvenster **Roles** de juiste rol toe die u hebt gemaakt.

    ![Schermopname van de Beheerder die is geselecteerd vanuit Werknemer.](./media/NetSuite-tutorial/ns-add-role.png)

    m. Selecteer **Opslaan**.

### <a name="create-the-netsuite-test-user"></a>De testgebruiker voor NetSuite maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in NetSuite. NetSuite biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Deze sectie bevat geen actie-item voor u. Als er nog geen gebruiker bestaat in NetSuite, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

- Klik op Deze toepassing testen in de Azure Portal. U wordt automatisch aangemeld bij het exemplaar van NestSuite waarvoor u eenmalige aanmelding hebt ingesteld

- U kunt Microsoft Mijn apps gebruiken. Wanneer u in de Mijn apps op de tegel NetSuite klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u de eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u NetSuite hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
