---
title: 'Zelfstudie: Azure Active Directory-integratie met xMatters OnDemand | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: cbadf2e072cdd9bfdf64cb2b799355aada8ec4b0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621161"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Zelfstudie: Azure Active Directory-integratie met xMatters OnDemand

In deze zelfstudie leert u hoe u xMatters OnDemand integreert met Azure Active Directory (Azure AD).
xMatters OnDemand integreren met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot xMatters OnDemand.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij xMatters OnDemand (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met xMatters OnDemand wilt integreren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een xMatters OnDemand-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* xMatters OnDemand biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>xMatters OnDemand toevoegen vanuit de galerie

Voor het configureren van de integratie van xMatters OnDemand in Azure AD moet u xMatters OnDemand vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **xMatters OnDemand**.
1. Selecteer  **OnDemand** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Eenmalige aanmelding van Azure AD voor XMatters OnDemand configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met XMatters OnDemand met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in xMatters OnDemand.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij xMatters OnDemand te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    2. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding voor xMatters OnDemand configureren](#configure-xmatters-ondemand-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een xMatters OnDemand-testgebruiker maken](#create-xmatters-ondemand-test-user)** : als u een equivalent van Britta Simon in xMatters OnDemand wilt dat is gekoppeld aan de Azure AD-versie van die gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **xMatters OnDemand** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met een van de volgende notaties:

    | Id |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met één van de volgende patronen:

    | Antwoord-URL |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [xMatters OnDemand Client-ondersteuningsteam](https://www.xmatters.com/company/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

    > [!IMPORTANT]
    > U moet het certificaat doorsturen naar het [xMatters OnDemand-ondersteuningsteam](https://www.xmatters.com/company/contact-us/). Het certificaat moet worden geüpload door het xMatters-ondersteuningsteam voordat u de configuratie voor eenmalige aanmelding kunt voltooien.

6. In de sectie **xMatters OnDemand instellen** kopieert u de juiste URL('s) voor uw behoeften.

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

In deze sectie gaat u het gebruik van eenmalige aanmelding van Azure mogelijk maken voor B.Simon door haar toegang te geven tot xMatters OnDemand.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen **xMatters OnDemand**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.


## <a name="configure-xmatters-ondemand-sso"></a>Eenmalige aanmelding voor xMatters OnDemand configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in xMatters OnDemand.

2. Klik op **Beheerder** en klik vervolgens op **Bedrijfsgegevens**.

    ![Pagina Beheerder](./media/xmatters-ondemand-tutorial/admin.png "Beheerder")

3. Voer de volgende stappen uit op de pagina **SAML Configuration**:

    ![Sectie SAML-configuratie ](./media/xmatters-ondemand-tutorial/saml-configuration.png "SAML-configuratie")

    a. Selecteer **SAML inschakelen**.

    b. Plak in het tekstvak **Id van id-provider** de waarde van de **Azure AD-id** die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak **Eenmalige aanmeldings-URL** de waarde van de **aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    d. Plak in het tekstvak **Omleiding afmeldings-URL** de waarde van **Afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    e. Klik op **Kies bestand** om het **certificaat (base64)** te uploaden dat u uit Azure Portal hebt gedownload. 

    f. Klik op de pagina Bedrijfsgegevens bovenaan op **Wijzigingen opslaan**.

    ![Bedrijfsgegevens](./media/xmatters-ondemand-tutorial/save-button.png "Bedrijfsgegevens")

### <a name="create-xmatters-ondemand-test-user"></a>xMatters OnDemand testgebruiker maken

1. Meld u aan bij uw **xMatters OnDemand** tenant.

2. Ga naar het pictogram **Gebruikers** > **Gebruikers** en klik op **Gebruikers toevoegen**.

    ![Gebruikers](./media/xmatters-ondemand-tutorial/add-user.png "Gebruikers")

3. Vul in de sectie **Gebruikers toevoegen** de vereiste velden in en klik op de knop **Gebruiker toevoegen** .

    ![Een gebruiker toevoegen](./media/xmatters-ondemand-tutorial/add-user-2.png "Een gebruiker toevoegen")



### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik in Azure Portal op Deze toepassing testen. U wordt automatisch aangemeld bij het exemplaar van xMatters OnDemand waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel xMatters OnDemand in Mijn apps klikt, wordt u automatisch aangemeld bij de instantie van xMatters OnDemand waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u xMatters Ondemand hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor uw organisatie in real time wordt beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
