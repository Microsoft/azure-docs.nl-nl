---
title: 'Zelfstudie: Azure Active Directory-integratie met iLMS | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en iLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 7289fe2ec1f39679dcec95f1f48a6efa9ed0cdfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460332"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Zelfstudie: iLMS integreren met Azure Active Directory

In deze zelfstudie leert u hoe u iLMS kunt integreren met Azure AD (Active Directory). Wanneer u iLMS integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot iLMS.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij iLMS.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een iLMS-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. iLMS biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-ilms-from-the-gallery"></a>iLMS toevoegen vanuit de galerie

Om de integratie van iLMS te configureren in Azure AD moet u iLMS vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **iLMS**.
1. Selecteer **iLMS** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met iLMS met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in iLMS.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met iLMS, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij iLMS configureren](#configure-ilms-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een iLMS-testgebruiker maken](#create-ilms-test-user)** : als u een tegenhanger van Britta Simon in iLMS wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **iLMS** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. Plak in het tekstvak **Id** de **id** die u kopieert in de sectie **Serviceprovider** met SAML-instellingen in de iLMS-beheerportal.

    b. Plak in het tekstvak **Antwoord-URL** de waarde **Eindpunt (URL)** die u kopieert in de sectie **Serviceprovider** met SAML-instellingen in de iLMS-beheerportal, met het volgende patroon: `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Plak in het tekstvak **Aanmeldings-URL** de waarde **Eindpunt (URL)** die u kopieert in de sectie **Serviceprovider** met SAML-instellingen in de iLMS-beheerportal, met het volgende patroon: `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Als u Just-In-Time inrichten wilt inschakelen, worden in de iLMS-toepassing de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    > [!NOTE]
    > U moet **Een niet-herkend gebruikersaccount maken** inschakelen in iLMS om deze kenmerken toe te wijzen. Volg [deze](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) instructies om een idee te krijgen van de kenmerken van de configuratie.

1. Bovendien worden in de iLMS-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgegeven. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam | Bronkenmerk|
    | --------|------------- |
    | division | user.department |
    | regio | user.state |
    | department | user.jobtitle |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in het gedeelte **iLMS instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ilms-sso"></a>Eenmalige aanmelding bij iLMS configureren

1. Meld u in een ander browservenster als een beheerder aan bij de **iLMS beheerportal**.

2. Klik op **SSO:SAML** onder het tabblad **Instellingen** om de SAML-instellingen te openen, en voer de volgende stappen uit:

    ![Schermopname toont het tabblad 'Instellingen van I L M S' waarin u op 'S S O' kunt klikken: SAML.](./media/ilms-tutorial/1.png)

3. Vouw de sectie **Serviceprovider** uit en kopieer de waarden voor **Id** en **Eindpunt (URL)** .

    ![Schermopname toont de SAML-instellingen waar u de waarden kunt ophalen.](./media/ilms-tutorial/2.png) 

4. Klik onder de sectie **Id-provider** op **Metagegevens importeren**.

5. Selecteer het bestand met **Federatieve metagegevens** dat u hebt gedownload in Azure Portal in de sectie **SAML-handtekeningcertificaat**.

    ![Schermopname toont de SAML-instellingen waar u het bestand met metagegevens kunt selecteren.](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Als u Just-In-Time inrichten wilt inschakelen om iLMS-accounts te maken voor niet-herkende gebruikers, volgt u onderstaande stappen:

    a. Controleer **Een niet-herkend gebruikersaccount maken**.

    ![Schermopname toont de optie 'Niet-herkende gebruikersaccount maken'.](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Wijs de kenmerken in Azure AD toe met de kenmerken in iLMS. Geef in de kenmerkkolom de naam van de kenmerken of de standaardwaarde op.

    c. Ga naar het tabblad **Bedrijfsregels** en voer de volgende stappen uit:

    ![Schermopname toont de sectie 'Instellingen voor Business Rules', waar u de informatie van deze stap kunt invoeren.](./media/ilms-tutorial/5.png)

    d. Controleer **Niet-herkende regio's, divisies en afdelingen maken** om regio's, divisies en afdelingen te maken die nog niet bestaan op het moment van eenmalige aanmelding.

    e. Controleer **Gebruikersprofiel bijwerken tijdens aanmelden** om op te geven of het gebruikersprofiel wordt bijgewerkt tijdens elke aanmelding.

    f. Als de optie **Lege velden bijwerken voor niet-verplichte velden in gebruikersprofiel** is ingeschakeld, bevat het iLMS-profiel van de gebruiker blanco waarden voor alle optionele profielvelden die blanco zijn tijdens het aanmelden.

    g. Controleer **Meldings-e-mail met fouten verzenden** en voer het e-mailadres van de gebruiker in waarop u de meldings-e-mail met fouten wilt ontvangen.

7. Klik op **Save** om de instellingen op te slaan.

    ![Schermopname toont de knop 'Opslaan'.](./media/ilms-tutorial/save.png)

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

In deze sectie geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot iLMS.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **iLMS** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ilms-test-user"></a>Een iLMS-testgebruiker maken

Toepassing biedt ondersteuning voor het Just-In-Time inrichten van gebruikers, en na verificatie worden gebruikers automatisch gemaakt in de toepassing. JIT werkt als u op het selectievakje **Een niet-herkend gebruikersaccount maken** hebt geklikt tijdens het instellen van de SAML-configuratie in de iLMS-beheerportal.

Als u handmatig een gebruiker moet maken, volgt u onderstaande stappen:

1. Meld u als beheerder aan bij de iLMS-bedrijfssite.

2. Klik op **Gebruiker registreren** op het tabblad **Gebruikers**, om de pagina **Gebruiker registreren** te openen.

   ![Schermopname toont het tabblad 'Instellingen van I L M S' waarin u op 'Gebruiker registreren' kunt klikken.](./media/ilms-tutorial/3.png)

3. Voer op de pagina **Gebruiker registreren** de volgende stappen uit.

    ![Schermopname toont het tabblad 'Gebruiker registreren' waarin u de opgegeven informatie invoert.](./media/ilms-tutorial/create_testuser_add.png)

    a. Typ in het tekstvak **Voornaam** de voornaam, bijvoorbeeld Britta.

    b. Typ in het tekstvak **Achternaam** de achternaam, bijvoorbeeld Simon.

    c. Typ in het tekstvak **Emailadres** het e-mailadres van de gebruiker, bijvoorbeeld BrittaSimon@contoso.com.

    d. Selecteer in de vervolgkeuzelijst **Regio** de waarde voor regio.

    e. Selecteer in de vervolgkeuzelijst **Divisie** de waarde voor divisie.

    f. Selecteer in de vervolgkeuzelijst **Afdeling** de waarde voor afdeling.

    g. Klik op **Opslaan**.

    > [!NOTE]
    > U kunt een registratie-e-mail naar de gebruiker verzenden door het selectievakje **Registratie-e-mail verzenden** in te schakelen.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel iLMS selecteert, wordt u automatisch aangemeld bij de instantie van iLMS waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)