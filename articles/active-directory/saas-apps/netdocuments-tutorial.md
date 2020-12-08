---
title: 'Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met NetDocuments | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en NetDocuments.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: b9a85c7093d0f42ac97c4a548ba6f0feebae8b89
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350718"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met NetDocuments

In deze zelfstudie leert u hoe u NetDocuments integreert met Azure Active Directory (Azure AD). Wanneer u NetDocuments integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD beheren wie toegang heeft tot NetDocuments.
* U kunt instellen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij NetDocuments.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een NetDocuments-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* NetDocuments biedt ondersteuning voor door **SP** geïnitieerde SSO

## <a name="adding-netdocuments-from-the-gallery"></a>NetDocuments toevoegen vanuit de galerie

Als u de integratie van NetDocuments met Azure AD wilt configureren, moet u NetDocuments vanuit de galerie toevoegen aan uw lijst van beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **NetDocuments** in het zoekvak.
1. Selecteer **NetDocuments** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Eenmalige aanmelding van Azure AD configureren en testen voor NetDocuments

Configureer en test eenmalige aanmelding van Azure AD met NetDocuments met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in NetDocuments.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met NetDocuments, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van NetDocuments configureren](#configure-netdocuments-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor NetDocuments maken](#create-netdocuments-test-user)** : als u in NetDocuments een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **NetDocuments** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Opslagplaats-id is een waarde die begint met **CA-** , gevolgd door een code van 8 tekens die aan uw NetDocuments-opslagplaats is gekoppeld. Raadpleeg het [ondersteuningsdocument NetDocuments Federated Identity](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) voor meer informatie. Ook kunt u contact opnemen met het [klantondersteuningsteam van NetDocuments](https://support.netdocuments.com/hc/) om deze waarden te verkrijgen als u de configuratie niet kunt uitvoeren met behulp van de bovenstaande gegevens. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de NetDocuments-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. De NetDocuments-toepassing verwacht dat **nameidentifier** wordt toegewezen met **employeeid** of een andere claim die als **nameidentifier** van toepassing is op uw organisatie, dus u moet de kenmerktoewijzing bewerken door te klikken op het pictogram **Bewerken** en de kenmerktoewijzing wijzigen.

    ![image](common/edit-attribute.png)

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **NetDocuments instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot NetDocuments.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **NetDocuments** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netdocuments-sso"></a>Eenmalige aanmelding van NetDocuments configureren

1. Meld u in een ander browservenster als beheerder aan bij uw NetDocuments-bedrijfssite.

2. Selecteer in de rechterbovenhoek uw naam > **Beheerder**.

3. Selecteer **Beveiligingscentrum**.
   
    ![Schermopname met Security Center geselecteerd in Juridische documenten.](./media/netdocuments-tutorial/security-center.png "Beveiligingscentrum")

4. Selecteer **Geavanceerde verificatie**.
    
    ![Opties voor geavanceerde verificatie configureren](./media/netdocuments-tutorial/advance-authentication.png "Opties voor geavanceerde verificatie configureren")

5.  Voer op het tabblad **Federatieve id** de volgende stappen uit:   
   
    ![Federatieve identiteit](./media/netdocuments-tutorial/federated-id.png "Federatieve identiteit")
   
    a. Selecteer **Active Directory Federation Services** als **Federatief identiteitsservertype**.
    
    b.  Selecteer **Bestand kiezen** om het gedownloade XML-bestand met metagegevens, dat u hebt gedownload vanuit Azure Portal, te uploaden.
    
    c.  Selecteer **SAVE** (Opslaan).

### <a name="create-netdocuments-test-user"></a>Een NetDocuments-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij NetDocuments, moeten ze worden ingericht in NetDocuments. In het geval van NetDocuments is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij uw **NetDocuments**-bedrijfssite.

2. Selecteer in de rechterbovenhoek uw naam > **Beheerder**.
   
    ![Beheerder](./media/netdocuments-tutorial/user-admin.png "Beheerder")

3. Selecteer **Gebruikers en groepen**.
   
    ![Schermopname met Gebruikers en groepen geselecteerd in Juridische documenten.](./media/netdocuments-tutorial/users-groups.png "Opslagplaats")

4. Typ in het tekstvak **E-mailadres** het e-mailadres van een geldig Azure Active Directory-account dat u wilt inrichten en klik vervolgens op **Gebruiker toevoegen**.
   
    ![Email Address (E-mailadres)](./media/netdocuments-tutorial/user-mail.png "E-mailadres")
   
    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt. U kunt ook alle andere hulpprogramma's voor het creëren van NetDocuments-gebruikersaccounts of API's van NetDocuments gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel NetDocuments klikt, wordt u automatisch aangemeld bij het exemplaar van NetDocuments waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [NetDocuments gebruiken met Azure AD](https://aad.portal.azure.com/)