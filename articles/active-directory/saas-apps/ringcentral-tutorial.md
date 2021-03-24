---
title: 'Zelfstudie: Azure Active Directory-integratie met RingCentral | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en RingCentral.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 3c8126c051457d740d1a5414a130b4ee3fffcd8c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954119"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Zelfstudie: RingCentral integreren met Azure Active Directory

In deze zelfstudie leert u hoe u RingCentral integreert met Azure Active Directory (Azure AD). Wanneer u RingCentral integreert met Azure AD, kunt u:

* Beheer in Azure AD wie toegang heeft tot RingCentral.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij RingCentral.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* RingCentral-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RingCentral ondersteunt door **IDP** geïnitieerde SSO.

## <a name="add-ringcentral-from-the-gallery"></a>RingCentral toevoegen vanuit de galerie

Voor het configureren van de integratie van RingCentral met Azure AD moet u RingCentral uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **RingCentral**.
1. Selecteer **RingCentral** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Azure AD SSO voor RingCentral configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met RingCentral met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RingCentral.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met RingCentral:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor RingCentral configureren](#configure-ringcentral-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een RingCentral-testgebruiker maken](#create-ringcentral-test-user)** : als u een equivalent van B.Simon in RingCentral wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **RingCentral** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    1. Klik op **Metagegevensbestand uploaden**.
    1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.
    1. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**.

    > [!Note]
    > U krijgt het **metagegevensbestand van de service provider** op de pagina RingCentral SSO-configuratie die verderop in de zelfstudie wordt beschreven.

1. Als u geen **metagegevensbestand van service provider hebt**, voert u de waarden voor de volgende velden in:

    a. Typ een van de Url's in het tekstvak **id** :
  
    | Id |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Typ een van de Url's in het tekstvak **antwoord-URL** :

    | Antwoord-URL |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan RingCentral.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **RingCentral** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ringcentral-sso"></a>RingCentral met eenmalige aanmelding configureren

1. Als u de configuratie in RingCentral wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **RingCentral instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de RingCentral-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij RingCentral. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u RingCentral handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de RingCentral-bedrijfssite. Voer daarna de volgende stappen uit:

1. Klik bovenaan op **Extra**.

    ![Schermopname met Extra geselecteerd op de bedrijfssite van RingCentral.](./media/ringcentral-tutorial/ringcentral-1.png)

1. Ga naar **eenmalige aanmelding**.

    ![Schermopname met Eenmalige aanmelding geselecteerd in het menu Extra.](./media/ringcentral-tutorial/ringcentral-2.png)

1. Klik op de pagina **eenmalige aanmelding** onder **configuratie van de SSO** in **Stap 1** op **Bewerken** en voer de volgende stappen uit:

    ![Schermopname van de pagina SSO-configuratie waarin u Bewerken kunt selecteren.](./media/ringcentral-tutorial/ringcentral-3.png)

1. Voer op de pagina **Eenmalige aanmelding instellen** de volgende stappen uit:

    ![Schermopname van de pagina Eenmalige aanmelding instellen, waar u I D P-metagegevens kunt uploaden.](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Klik op **Browse** om het bestand met metagegevens dat u hebt gedownload van de Azure-portal te uploaden.

    b. Na het uploaden van metagegevens worden de waarden automatisch ingevuld in de sectie **Algemene informatie over eenmalige aanmelding**.

    c. Selecteer in sectie **Kenmerktoewijzing** **E-mailkenmerk toewijzen** als `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klik op **Opslaan**.

    e. Vanaf **Stap 2** klikt u op **Downloaden** om de **metagegevens van de serviceprovider** te downloaden en deze te uploaden naar de sectie **Basic SAML Configuration** om de waarden **Identifier** en **Reply URL** automatisch in de Azure-portal in te vullen.

    ![Schermopname van de pagina SSO-configuratie waar u Downloaden kunt selecteren.](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Ga op dezelfde pagina naar de sectie **SSO inschakelen** en voer de volgende stappen uit:

    ![Schermopname toont de sectie S S O inschakelen waar u de configuratie kunt voltooien.](./media/ringcentral-tutorial/ringcentral-5.png)

    * Selecteer **SSO-service inschakelen**.

    * Selecteer **Gebruikers toestaan zich aan te melden met een SSO-of RingCentral-referentie**.

    * Klik op **Opslaan**.

### <a name="create-ringcentral-test-user"></a>RingCentral-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in RingCentral. Werk samen met het [ondersteuningsteam van RingCentral](https://success.ringcentral.com/RCContactSupp) om de gebruikers toe te voegen in het RingCentral-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de RingCentral waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel RingCentral in de mijn apps klikt, moet u automatisch worden aangemeld bij de RingCentral waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u RingCentral hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).