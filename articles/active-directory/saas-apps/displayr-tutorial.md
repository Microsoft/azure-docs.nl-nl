---
title: 'Zelfstudie: Azure Active Directory-integratie met Displayr | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Displayr.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3cb6ee3162c70d2d07c4868ae90ecc54bd489966
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622488"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Zelfstudie: Displayr integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Displayr integreert met Azure Active Directory (Azure AD). Wanneer u Displayr integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Displayr.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Displayr.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Displayr waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Displayr ondersteunt door **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-displayr-from-the-gallery"></a>Displayr toevoegen vanuit de galerie

Voor het configureren van de integratie van Displayr in Azure Active Directory, moet u Displayr uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Displayr**.
1. Selecteer **Displayr** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Displayr met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Displayr.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Displayr te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Displayr](#configure-displayr)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. **[Een testgebruiker maken voor Displayr](#create-displayr-test-user)** : als u in Displayr een equivalent van Britta Simon wilt dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Displayr**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stap uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YOURDOMAIN>.displayr.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie:`<YOURDOMAIN>.displayr.com`
    
    c. Typ in het tekstvak **antwoord-URL** `https://app.displayr.com/Login/ProcessSamlResponse` .
    
    d. Klik op **Opslaan**.

    >[!NOTE]
    >Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Displayr](mailto:support@displayr.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie Basic SAML-configuratie in Azure Portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In Displayr worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

   ![Schermopname die de sectie 'Gebruikerskenmerken' toont met het pictogram 'Bewerken' geselecteerd.](common/edit-attribute.png)

1. Bovendien verwacht Displayr nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Voer in het gedeelte **Gebruikerskenmerken en -claims** in het dialoogvenster **Groepsclaims (preview)** de volgende stappen uit:

   a. Klik op **een groepclaim toevoegen**.

      ![Schermopname toont het venster 'Groepsclaims (voorbeeld)' waarin 'Instellingen' is geselecteerd.](./media/displayr-tutorial/config05.png)

   b. Selecteer **Alle groepen** in de lijst met keuzerondjes.

   c. Selecteer **Bronkenmerk** bij **Groep-ID**.

   d. Selecteer **De naam van de groepsclaim aanpassen**.

   e. Selecteer **Groepen verzenden als rolclaims**.

   f. Klik op **Opslaan**.

1. In de sectie **Displayr instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Displayr configureren

1. Als u de configuratie in Displayr wilt automatiseren, moet u de **browserextensie My Apps Secure Sign-in** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Displayr instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar Displayr. Geef hier de beheerdersreferenties op om u aan te melden bij Displayr. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Displayr handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Displayr. Voer hierna de volgende stappen uit:

4. Klik op **Settings** en ga naar **Account**.

    ![Schermopname waarin het pictogram 'Instellingen' en 'Account' zijn geselecteerd.](./media/displayr-tutorial/config01.png)

5. Ga naar **Settings** in het bovenste menu en schuif naar beneden op de pagina om te klikken op **Configure Single Sign On (SAML)** .

    ![Schermopname toont het tabblad 'Instellingen' waarin de actie 'Eenmalige aanmelding (SAML) configureren' is geselecteerd.](./media/displayr-tutorial/config02.png)

6. Voer op de pagina **Single Sign On (SAML)** de volgende stappen uit:

    ![Configuratie](./media/displayr-tutorial/config03.png)

    a. Schakel het selectievakje **Enable Single Sign On (SAML)** in.

    b. Kopieer de werkelijke **id** uit de sectie **Standaard SAML-configuratie** van Azure AD en plak deze in het tekstvak **Issuer**.

    c. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    d. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    e. Open het certificaat (Base64) in Kladblok, kopieer de inhoud ervan en plak deze in het tekstvak **Certificate**.

    f. Geef desgewenst waarden op voor **Group mappings**.

    g. Klik op **Opslaan**.  

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

In deze sectie geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Displayr.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Displayr** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-displayr-test-user"></a>Testgebruiker voor Displayr maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij Displayr, moeten ze worden ingericht in Displayr. Het inrichten is in Displayr een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij Displayr.

2. Klik op **Settings** en ga naar **Account**.

    ![Schermopname waarin het pictogram 'Instellingen' en 'Account' zijn geselecteerd.](./media/displayr-tutorial/config01.png)

3. Ga naar **Settings** in het bovenste menu en schuif naar beneden op de pagina tot de se **Users** en klik daar op **New User**.

    ![Schermopname toont het tabblad 'Instellingen' waarin 'Gebruikers' is gemarkeerd en de knop 'Nieuwe gebruiker' is geselecteerd.](./media/displayr-tutorial/config07.png)

4. Voer op de pagina **New User** de volgende stappen uit:

    ![Configuratie van Displayr](./media/displayr-tutorial/config06.png)

    a. Voer in het tekstvak **Name** de naam van de gebruiker in, bijvoorbeeld **Brittasimon**.

    b. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `Brittasimon@contoso.com`.

    c. Selecteer de juiste waarde bij **Group membership**.

    d. Klik op **Opslaan**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel Displayr selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij de instantie van Displayr waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
