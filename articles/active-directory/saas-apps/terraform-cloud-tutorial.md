---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Terraform Cloud | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Terraform Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: c26224f388c09ec758373c877004f5485bb420df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97725074"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-terraform-cloud"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Terraform Cloud

In deze zelfstudie leert u hoe u Terraform Cloud integreert met Azure Active Directory (Azure AD). Wanneer u Terraform Cloud integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Terraform Cloud.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Terraform Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Terraform Cloud waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Terraform Cloud ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Terraform Cloud ondersteunt het **Just-In-Time** inrichten van gebruikers


## <a name="adding-terraform-cloud-from-the-gallery"></a>Terraform Cloud toevoegen vanuit de galerie

Om de integratie van Terraform Cloud in Azure AD te configureren, moet u Terraform Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Terraform Cloud**.
1. Selecteer **Terraform Cloud** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-terraform-cloud"></a>Eenmalige aanmelding van Azure AD voor Terraform Cloud configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Terraform Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Terraform Cloud.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Terraform Cloud te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Terraform Cloud configureren](#configure-terraform-cloud-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Terraform Cloud maken](#create-terraform-cloud-test-user)** : als u een tegenhanger van B.Simon in Terraform Cloud wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **Terraform Cloud** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Id** typt u een URL met het volgende patroon: `https://app.terraform.io/sso/saml/samlconf-<ID>/metadata`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://app.terraform.io/session`

    > [!NOTE]
    > De id-waarde is niet echt. Werk de waarde bij met de werkelijke id. Neem contact op met het [klantondersteuningsteam van Terraform Cloud](mailto:tf-cloud@hashicorp.support) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Terraform Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Terraform Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-terraform-cloud-sso"></a>Eenmalige aanmelding voor Terraform Cloud configureren

1. Als u de configuratie in Terraform Cloud wilt automatiseren, moet u de **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Terraform Cloud instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Terraform Cloud-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Terraform Cloud. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Terraform Cloud handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij uw Terraform Cloud-bedrijfssite.

2. Ga naar **Instellingen > Eenmalige aanmelding > Instellingen bewerken**

    !['Instellingen' in Terraform Cloud](./media/terraform-cloud-tutorial/sso-settings.png)

3. Voer op de pagina **Eenmalige aanmelding bewerken** de volgende stappen uit.

    !['Eenmalige aanmelding bewerken' in Terraform Cloud](./media/terraform-cloud-tutorial/edit-sso.png)

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **Entiteits-id of id** de waarde van de **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    c. Open in de Azure-portal het gedownloade **Certificaat** in Kladblok en plak de inhoud in het tekstvak **Openbaar certificaat**.

    d. Klik op **Instellingen opslaan**.

### <a name="create-terraform-cloud-test-user"></a>Testgebruiker voor Terraform Cloud maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Terraform Cloud. Terraform Cloud biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Terraform Cloud bestaat, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Terraform Cloud, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Terraform Cloud en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in de Azure-portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij het exemplaar van Terraform Cloud waarvoor u eenmalige aanmelding hebt ingesteld. 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in het toegangsvenster op de tegel Terraform Cloud klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het exemplaar van Terraform Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u Terraform Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).