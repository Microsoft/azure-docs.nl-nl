---
title: 'Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Cisco Webex Meetings | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Webex Meetings.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.openlocfilehash: 7a4e3a4a6049b48fa70e5352680bdc7342676c0b
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670254"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Cisco Webex Meetings

In deze zelfstudie leert u hoe u Cisco Webex Meetings kunt integreren met Azure Active Directory (Azure AD). Wanneer u Cisco Webex Meetings integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Cisco Webex Meetings.
* Inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Cisco Webex Meetings.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Cisco Webex Meetings waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cisco Webex Meetings biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)

* Cisco Webex Meetings ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco Webex Meetings toevoegen vanuit de galerie

Voor het configureren van de integratie van Cisco Webex Meetings in Azure AD moet u Cisco Webex Meetings vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Cisco Webex Meetings**.
1. Selecteer **Cisco WebEx Meetings** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Cisco Webex Meetings.

Configureer en test eenmalige aanmelding van Azure AD met Cisco Webex Meetings met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Cisco Webex Meetings.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Cisco Webex Meetings te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding voor Cisco Webex Meetings configureren](#configure-cisco-webex-meetings-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
    1. **[Testgebruiker maken in Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** : als u een tegenhanger van Britta Simon in Cisco Webex Meetings wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Cisco Webex Meetings** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** kunt u de toepassing configureren in door **IDP** geïnitieerde modus door het **metagegevensbestand van de serviceprovider** als volgt te uploaden:

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Als het uploaden van het metagegevensbestand van de serviceprovider is geslaagd, worden de waarden voor de **id** en **Antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**.

    >[!Note]
    >U ontvangt het metagegevensbestand van de serviceprovider in de sectie **Eenmalige aanmelding voor Cisco WebEx Meetings configureren**, die verderop in de zelfstudie wordt beschreven. 

1. Als u de toepassing wilt configureren in door **SP** geïnitieerde modus, voert u de volgende stappen uit:  

    a. Klik in de sectie **Standaard SAML-configuratie** op het pictogram voor bewerken/pen.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

    b. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<customername>.my.webex.com`

5. In de toepassing Cisco Webex Meetings worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

6. Bovendien verwacht de Cisco Webex Meetings-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie Gebruikersclaims in het dialoogvenster Gebruikerskenmerken voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel: 

    | Naam | Bronkenmerk|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   e-mail       | user.mail |
    |   uid    | user.mail |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ of selecteer in de lijst **Bronkenmerk** de kenmerkwaarde voor die rij in de vervolgkeuzelijst.

    f. Klik op **Opslaan**.

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Cisco Webex Meetings instellen** de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Cisco Webex Meetings.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Cisco Webex Meetings** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cisco-webex-meetings-sso"></a>Eenmalige aanmelding voor Cisco Webex Meetings configureren

1. Ga naar de URL `https://<customername>.webex.com/admin` met uw beheerdersreferenties.

2. Ga naar **Common Site Setting** (Algemene site-instellingen) en navigeer naar **SSO Configuration** (SSO-configuratie).

    ![Schermopname met Cisco Webex Administration met Common Site Settings en S S O Configuration geselecteerd.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Voer de volgende stappen uit op de pagina **Webex Administration** (Beheer van Webex):

    ![Schermopname met de pagina Webex Administration met de informatie die in deze stap wordt beschreven.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Selecteer **SAML 2.0** als **Federation-protocol**.

    b. Klik op de koppeling **Import SAML Metadata** (SAML-metagegevens importeren) en upload het metagegevensbestand dat u vanuit Azure Portal hebt gedownload.

    c. Klik op de knop **Exporteren** om het metagegevensbestand van de serviceprovider te downloaden en dit te uploaden in de sectie **Standaard SAML-configuratie** in Azure Portal.

    d. Typ in het tekstvak **AuthContextClassRef** `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` en als u de MFA wilt inschakelen met behulp van Azure AD, typt u de twee waarden als `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecteer **Auto Account Creation** (Automatisch account maken).

    >[!NOTE]
    >Voor het inschakelen van **Just-in-Time**-gebruikersinrichting moet u het selectievakje **Auto Account Creation** inschakelen. Daarnaast moeten de SAML-tokenkenmerken worden doorgegeven in het SAML-antwoord.

    f. Klik op **Opslaan**.

    >[!NOTE]
    >Deze configuratie is alleen voor de klanten die gebruikmaken van een Webex-gebruikers-id in e-mailindeling.

### <a name="create-cisco-webex-meetings-test-user"></a>Testgebruiker maken in Cisco Webex Meetings

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in Cisco Webex Meetings. Cisco Webex Meetings biedt ondersteuning voor **Just-In-Time**-inrichting; dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Cisco Webex Meetings, wordt er een nieuwe gemaakt wanneer u Cisco Webex Meetings opent.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Cisco Webex Meetings in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Cisco Webex Meetings waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [ServiceNow uitproberen met Azure AD](https://aad.portal.azure.com)