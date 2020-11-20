---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met HRworks Single Sign-On | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en HRworks Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: d6b23213d9d986c62a227b3e182a22896d128222
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442538"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met HRworks Single Sign-On

In deze zelfstudie leert u hoe u HRworks Single Sign-On kunt integreren met Azure Active Directory (Azure AD). Wanneer u HRworks Single Sign-On met Azure AD integreert, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot HRworks Single Sign-On.
* Inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij HRworks Single Sign-On.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op HRworks Single Sign-On waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* HRworks Single Sign-On biedt ondersteuning voor door de **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>HRworks Single Sign-On toevoegen vanuit de galerie

Voor het configureren van de integratie van HRworks Single Sign-On in Azure AD, moet u HRworks Single Sign-On vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **HRworks Single Sign-On** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **HRworks Single Sign-On** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen voor HRworks Single Sign-On

Configureer en test eenmalige aanmelding van Azure AD met HRworks Single Sign-On met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in HRworks Single Sign-On.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met HRworks Single Sign-On, voltooit u de volgende procedures:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor HRworks Single Sign-On configureren](#configure-hrworks-single-sign-on-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    * **[Een testgebruiker voor HRworks Single Sign-On maken](#create-hrworks-single-sign-on-test-user)** : als u een equivalent van B.Simon in HRworks Single Sign-On wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **HRworks Single Sign-On** naar het gedeelte **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van HRworks Single Sign-On](mailto:nadja.sommerfeld@hrworks.de) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In het gedeelte **HRworks Single Sign-On instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot HRworks Single Sign-On.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **HRworks Single Sign-On** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-hrworks-single-sign-on-sso"></a>Eenmalige aanmelding configureren voor HRworks Single Sign-On

1. Als u de configuratie in HRworks Single Sign-On wilt automatiseren, moet u de **browserextensie My Apps Secure Sign-in** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Na de toevoeging van de extensie aan de browser, klikt u op **HRworks Single Sign-On instellen** om naar de toepassing HRworks Single Sign-On te gaan. Geef hier de beheerdersreferenties op om u aan te melden bij HRworks Single Sign-On. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 en 4 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u HRworks Single Sign-On handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van HRworks Single Sign-On. Voer daarna de volgende stappen uit:

1. Klik links op **Administrator** > **Basics** > **Security** > **Single Sign-on** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Schakel het selectievakje **Use Single Sign-on** in.

    b. Selecteer **XML Metadata** bij **Meta data input method**.

    c. Selecteer **Individual NameID identifier** bij **Value for NameID**.

    d. Open in Kladblok het XML-bestand met metagegevens dat u hebt gedownload uit Azure Portal, kopieer de inhoud en plak deze in het vak **Metadata**.

    e. Klik op **Opslaan**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Testgebruiker maken voor HRworks Single Sign-On

Als u Azure AD-gebruikers de gelegenheid wilt geven om zich aan te melden bij HRworks Single Sign-On, moeten ze worden ingericht in HRworks Single Sign-On. In HRworks Single Sign-On is het inrichten van gebruikers een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij HRworks Single Sign-On.

1. Klik links in het menu op **Administrator** > **Persons** > **Persons** > **New person**.

     ![Schermopname met de pagina H R works met Persons en New person geselecteerd.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Klik in het pop-upvenster op **Next**.

    ![Schermopname met een lijst met landen waaruit u kunt kiezen voor de persoon.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Vul in de pop-up **Create new person with country for legal terms** de benodigde gegevens in, zoals **First name** en **Last name**, en klik op **Create**.

    ![Schermopname met tekstvakken waarin u de voor- en achternaam van de persoon kunt invoeren.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel HRworks Single Sign-On klikt, wordt u automatisch aangemeld bij de instantie van HRworks Single Sign-On waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [HRworks Single Sign-On uitproberen met Azure AD](https://aad.portal.azure.com/)