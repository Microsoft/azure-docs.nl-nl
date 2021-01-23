---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Concur Travel and Expense | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Concur Travel and Expense.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 56eab06bd1afd18bfd4e23b9acb0b44d7bd1f80b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737029"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Concur Travel and Expense

In deze zelfstudie leert u hoe u Concur Travel and Expense integreert met Azure Active Directory (Azure AD). Wanneer u Concur Travel and Expense integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Concur Travel and Expense.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Concur Travel and Expense.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Concur Travel and Expense.
* Een ‘Bedrijfsbeheerder’-rol onder uw Concur-gebruikersaccount. U kunt testen of u de juiste toegang hebt door naar de [Concur-selfservicetool voor eenmalige aanmelding](https://www.concursolutions.com/nui/authadmin/ssoadmin) te gaan. Als u niet de juiste toegang hebt, neemt u contact op met Concur-ondersteuning of de implementatieprojectmanager. 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u eenmalige aanmelding van Azure AD.

* Concur Travel and Expense ondersteunt door **IDP** en **SP** geïnitieerde eenmalige aanmelding
* Concur Travel and Expense ondersteunt het testen van eenmalige aanmelding in zowel een productie- als implementatieomgeving 

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde voor elk van de drie regio’s: VS, EMEA en China. Er kan dus slechts één exemplaar voor elke regio worden geconfigureerd in één tenant. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Concur Travel and Expense toevoegen uit de galerie

Om de integratie van Concur Travel and Expense in Azure AD te configureren, moet u Concur Travel and Expense uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Concur Travel and Expense**.
1. Selecteer **Concur Travel and Expense** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-concur-travel-and-expense"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Concur Travel and Expense

Configureer en test eenmalige aanmelding van Azure AD met Concur Travel and Expense met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Concur Travel and Expense.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Concur Travel and Expense te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Concur Travel and Expense configureren](#configure-concur-travel-and-expense-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Concur Travel and Expense maken](#create-concur-travel-and-expense-test-user)** : als u een tegenhanger van B.Simon in Concur Travel and Expense wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Concur Travel and Expense** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

    > [!NOTE]
    > Id (Entiteits-id) en Antwoord-URL (URL voor Assertion Consumer Service) zijn regiospecifiek. Selecteer op basis van het datacenter van uw Concur-entiteit. Neem contact op met Concur-ondersteuning als u niet weet wat het datacenter van uw Concur-entiteit is. 

5. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het bewerkings-/penpictogram voor **Gebruikerskenmerk** om de instellingen te bewerken. De unieke gebruikers-id moet overeenkomen met de aanmeldings-id van de Concur-gebruiker. Meestal moet u **user.userprincipalname** wijzigen in **user.mail**.

    ![Gebruikerskenmerk bewerken](common/edit-attribute.png)

6. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om de metagegevens te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot Concur Travel and Expense.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Concur Travel and Expense** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-concur-travel-and-expense-sso"></a>Eenmalige aanmelding voor Concur Travel and Expense configureren

1. Als u de configuratie in Concur Travel and Expense wilt automatiseren, moet u **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Concur Travel and Expense instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de toepassing Concur Travel and Expense. Geef hier de beheerdersreferenties op om u aan te melden bij Concur Travel and Expense. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Concur Travel and Expense handmatig wilt instellen, moet u in een ander browservenster het gedownloade **XML-bestand met federatieve metagegevens** uploaden naar [Concur-selfservicetool voor eenmalige aanmelding](https://www.concursolutions.com/nui/authadmin/ssoadmin) en u als beheerder aanmelden bij de bedrijfssite van Concur Travel and Expense.

1. Klik op **Add**.
1. Voer een aangepaste naam voor uw IdP in, bijvoorbeeld ‘Azure AD (VS)’. 
1. Klik op **XML-bestand uploaden** en voeg het **XML-bestand met federatieve metagegevens** bij dat u eerder hebt gedownload.
1. Klik op **Metagegevens toevoegen** om de wijziging op te slaan.

    ![Schermopname van de Concur-selfservicetool voor eenmalige aanmelding](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Testgebruiker voor Concur Travel and Expense maken

In deze sectie maakt u in Concur Travel and Expense een gebruiker met de naam B.Simon. Werk samen met het ondersteuningsteam van Concur om de gebruikers toe te voegen voor het Concur Travel and Expense-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

> [!NOTE]
> De aanmeldings-id van B.Simon in Concur moet overeenkomen met de unieke id van B.Simon in Azure AD. Als de unieke id van B.Simon in Azure AD bijvoorbeeld `B.Simon@contoso.com` is, moet de aanmeldings-id van B.Simon in Concur ook `B.Simon@contoso.com` zijn. 

## <a name="configure-concur-mobile-sso"></a>Eenmalige aanmelding voor Concur Mobile configureren
Om eenmalige aanmelding voor Concur Mobile in te schakelen, moet u het ondersteuningsteam van Concur de **URL van gebruikerstoegang** geven. Volg de onderstaande stappen om de **URL van gebruikerstoegang** op te halen in Azure AD:
1. Ga naar **Bedrijfstoepassingen**
1. Klik op **Concur Travel and Expense**
1. Klik op **Eigenschappen**
1. Kopieer de **URL van gebruikerstoegang** en geef deze URL aan Concur-ondersteuning

> [!NOTE]
> De selfservice-optie om eenmalige aanmelding te configureren is niet beschikbaar, dus werk samen met het Concur-ondersteuningsteam om eenmalige aanmelding voor mobiel in te schakelen. 

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Concur Travel and Expense, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van Concur Travel and Expense en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt dan automatisch aangemeld bij de instantie van Concur Travel and Expense waarvoor u eenmalige aanmelding hebt ingesteld

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel Concur Travel and Expense klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Concur Travel and Expense waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Concur Travel and Expense hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).