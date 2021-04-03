---
title: 'Zelfstudie: Azure Active Directory-integratie met Soloinsight-CloudGate SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Soloinsight-CloudGate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 193d779a2e9246fe36af4828251a15d593e80ac7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514772"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Zelfstudie: Soloinsight-CloudGate SSO integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Soloinsight-CloudGate SSO kunt integreren met Azure Active Directory (Azure AD). Wanneer u Soloinsight-CloudGate SSO integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Soloinsight-CloudGate SSO.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Soloinsight-CloudGate SSO.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement op Soloinsight-CloudGate SSO waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Soloinsight-CloudGate SSO ondersteunt door **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO toevoegen uit de galerie

Voor de configuratie van de integratie van Soloinsight-CloudGate SSO in Azure Active Directory, moet u Soloinsight-CloudGate SSO vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Soloinsight-CloudGate SSO**.
1. Selecteer **Soloinsight-CloudGate SSO** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Soloinsight-CloudGate SSO met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Soloinsight-CloudGate SSO.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Soloinsight-CloudGate SSO te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Soloinsight-CloudGate SSO configureren](#configure-soloinsight-cloudgate-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. **[Testgebruiker voor Soloinsight-CloudGate SSO maken](#create-soloinsight-cloudgate-sso-test-user)** : als u een tegenhanger van Britta Simon in Soloinsight-CloudGate SSO wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Soloinsight-CloudGate SSO** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.sigateway.com/login`

    1. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL voor eenmalige aanmelding en de id, zoals later in de zelfstudie wordt uitgelegd, in de sectie **Eenmalige aanmelding voor Soloinsight-CloudGate SSO configureren**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Soloinsight-CloudGate SSO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO configureren

1. Als u de configuratie met Soloinsight-CloudGate SSO wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Soloinsight-CloudGate SSO instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Soloinsight-CloudGate SSO-toepassing. Geef hier de referenties voor een beheerder op om u aan te melden bij Soloinsight-CloudGate SSO. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 8 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Soloinsight-CloudGate SSO handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Soloinsight-CloudGate SSO. Voer hierna de volgende stappen uit:

4. Om de waarden te verkrijgen die u tijdens het configureren van standaard SAML in de Azure-portal moet plakken, meldt u zich aan bij de CloudGate Web Portal met behulp van uw referenties. Ga vervolgens naar de SSO-instellingen, die te vinden zijn via **Start>Beheer>Systeeminstellingen>Algemeen**.

    ![CloudGate SSO-instellingen](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL voor SAML-consument**

    * Kopieer de koppelingen die worden weergegeven in de velden **URL voor SAML-consument** en **Omleidings-URL** en plak deze in de Azure-portal in de sectie **Standaard SAML-configuratie** in de velden **Id (Entiteits-id)** en **Antwoord-URL**, respectievelijk.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-handtekeningcertificaat**

    * Ga naar de bron van het bestand Certificaat (Base64) dat is gedownload via de SAML-ondertekeningcertificaatlijsten in de Azure-portal en klik hierop met de rechtermuisknop. Kies in de lijst de optie **Bewerken met Notepad++**. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopieer de inhoud in het (Base64)-certificaat naar het Notepad++-bestand.

        ![Certificaat kopiëren](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Plak de inhoud in de CloudGate Web Portal SSO-instellingen in het veld **Certificaat** en klik op de knop Opslaan.

        ![Certificaatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Standaardgroep**

    * Selecteer in de CloudGate Web Portal in de vervolgkeuzelijst voor de optie **Default Group** (Standaardgroep) **Business Admin** (Bedrijfsbeheerder)

        ![Standaardgroep](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-id en aanmeldings-URL**

    * De **Aanmeldings-URL** die u in de Azure-portal tijdens de configuratiestap bij **Soloinsight-CloudGate SSO instellen** hebt gekopieerd, moet worden ingevoerd in de sectie met instellingen voor CloudGate Web Portal SSO.

    * Plak de link van de **Aanmeldings-URL** vanuit de Azure-portal in het veld **Aanmeldings-URL voor AD** van de CloudGate Web Portal.

    * Plak de link van de **Azure AD-id** vanuit de Azure-portal in het veld **AD-id** van de CloudGate Web Portal.

        ![AD-aanmelding](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Soloinsight-CloudGate SSO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Soloinsight-CloudGate SSO**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight CloudGate SSO-testgebruiker maken

Om een testgebruiker te maken, selecteert u **Employees** (Werknemers) in het hoofdmenu van uw CloudGate Web Portal en vult u het formulier voor het toevoegen van nieuwe werknemers in. Het machtigingsniveau dat aan de testgebruiker moet worden toegewezen is **Business Admin** (Bedrijfsbeheerder). Klik op **Create** (Maken) wanneer u alle vereiste velden hebt ingevuld.

![Werknemertest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Soloinsight-CloudGate SSO selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van Soloinsight-CloudGate SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)