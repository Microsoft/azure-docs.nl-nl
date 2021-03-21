---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor BitBucket | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en EasySSO voor BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 6fdc9c70d1c9fc67c38edfd794354f9e03321c73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor BitBucket

In deze zelfstudie leert u hoe u EasySSO voor BitBucket kunt integreren met Azure AD (Active Directory). Wanneer u EasySSO voor BitBucket integreert met Azure AD, kunt u:

* Bepalen in Azure AD wie toegang heeft tot EasySSO voor BitBucket.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij EasySSO voor BitBucket.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op EasySSO voor BitBucket dat voor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* EasySSO voor BitBucket ondersteunt door SP en IDP geïnitieerde eenmalige aanmelding.
* EasySSO voor BitBucket ondersteunt het Just-In-Time inrichten van gebruikers.

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>EasySSO voor BitBucket toevoegen vanuit de galerie

Om de integratie van EasySSO voor BitBucket in Azure AD te configureren, moet u EasySSO voor BitBucket uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **EasySSO voor BitBucket**.
1. Selecteer **EasySSO voor BitBucket** in de resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Eenmalige aanmelding van Azure AD configureren en testen voor EasySSO for BitBucket

Configureer en test eenmalige aanmelding van Azure AD met EasySSO voor BitBucket met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een gekoppelde relatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in EasySSO voor BitBucket.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met EasySSO for BitBucket te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Configureer eenmalige aanmelding bij EasySSO voor BitBucket](#configure-easysso-for-bitbucket-sso) als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. [Maak een testgebruiker voor EasySSO voor BitBucket](#create-an-easysso-for-bitbucket-test-user) als u een tegenhanger van B. Simon in EasySSO voor BitBucket wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **EasySSO for BitBucket** naar de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IdP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    - In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem bij twijfel contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De EasySSO voor BitBucket-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van standaardkenmerken](common/default-attributes.png)

1. De toepassing EasySSO voor BitBucket verwacht ook dat er nog enkele kenmerken moeten worden teruggestuurd via de SAML-respons. Dit kunt u in de volgende tabel zien. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.
    
    | Naam | Bronkenmerk|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Als uw Azure AD-gebruikers **sAMAccountName** hebben geconfigureerd, moet u **urn:oid:0.9.2342.19200300.100.1.1** aan het **sAMAccountName**-kenmerk toewijzen.
    
1. Op de pagina **Eenmalige aanmelding met SAML** instellen in de sectie **SAML-handtekeningcertificaat** selecteert u de downloadkoppelingen voor de optie **Certificaat (Base64)** of **Federation Metadata-XML**. Sla een van beide of beide op uw computer op. U hebt deze later nodig om BitBucket EasySSO te configureren.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met de downloadkoppelingen gemarkeerd](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Als u van plan bent om EasySSO voor BitBucket handmatig met een certificaat te configureren, moet u ook de **aanmeldings-URL** en de **Azure AD-id** kopiëren en op uw computer opslaan.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B. Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer `B.Simon` in bij **Naam**.
   1. Voer bij **Gebruikersnaam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot EasySSO voor BitBucket.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **EasySSO voor BitBucket** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in de lijst met gebruikers in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst **Gebruikers** en kies **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Eenmalige aanmelding bij EasySSO voor BitBucket configureren

1. Als u de configuratie in Zoom wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Zoom instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Zoom-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Zoom. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 tot 10 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Zoom handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij uw Zoom-bedrijfssite.

1. Ga naar de sectie **Beheer**.

    ![Schermopname van het BitBucket-exemplaar, met het tandwielpictogram gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Zoek en selecteer **EasySSO**.

    ![Schermopname van de optie EasySSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Selecteer **SAML**. U wordt naar de sectie SAML-configuratie geleid.

    ![Schermopname van de beheerpagina EasySSO, met SAML gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Selecteer het tabblad **Certificaten**. Het volgende scherm wordt geopend:

    ![Schermopname van het tabblad Certificaten, met diverse opties gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Zoek de optie **Certificaat (Base64)** of **Metagegevensbestand** dat u in de vorige sectie van deze zelfstudie hebt opgeslagen. U kunt op een van de volgende manieren doorgaan:

    - Gebruik het **Metagegevensbestand** van de app-federatie dat u naar een lokaal bestand op de computer hebt gedownload. Selecteer het keuzerondje **Uploaden** en volg het specifieke pad voor het besturingssysteem.

    - Open het **Metagegevensbestand** voor app-federatie om de inhoud van het bestand in een eenvoudige teksteditor te bekijken. Kopieer het naar het klembord. Selecteer **Invoer** en plak de inhoud van het klembord in het tekstveld.
 
    - Voer een volledig handmatige configuratie uit. Open het **certificaat (Base64)** voor app-federatie om de inhoud van het bestand in een eenvoudige teksteditor te bekijken. Kopieer het naar het klembord en plak het in het tekstveld **IdP-certificaat voor token-ondertekening**. Ga vervolgens naar het tabblad **Algemeen** en vul de velden **POST-binding-URL** en **Entiteits-id** met respectieve waarden voor **Aanmeldings-URL** en **Azure AD-id** die u eerder hebt opgeslagen.
 
1. Selecteer **Opslaan** onder aan de pagina. U ziet dat de inhoud van de metagegevens- of certificaatbestanden in de configuratievelden wordt geparseerd. De configuratie van EasySSO voor BitBucket is voltooid.

1. Als u de configuratie wilt testen, gaat u naar het tabblad **Uiterlijk** en selecteert u **SAML-aanmeldingsknop**. Hiermee wordt een aparte knop op het BitBucket-aanmeldingsscherm ingeschakeld die specifiek is bedoeld om uw Azure AD SAML-integratie end-to-end te testen. U kunt deze knop ingeschakeld laten en ook de positie, kleur en vertaling voor productiemodus configureren.

    ![Schermopname van het tabblad Uiterlijk, met SAML-aanmeldingsknop gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Als u problemen ondervindt, neemt u contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Testgebruiker voor EasySSO voor BitBucket maken

In deze sectie maakt u in BitBucket een gebruiker met de naam Britta Simon. EasySSO voor BitBucket ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is uitgeschakeld. Als u het wilt inschakelen, moet u **Gebruiker maken na geslaagde aanmelding** expliciet inschakelen in de sectie **Algemeen** van de configuratie voor de EasySSO-invoegtoepassing. Als er nog geen gebruiker in BitBucket bestaat, wordt er na authenticatie een nieuwe gemaakt.

Als u het automatisch inrichten van gebruikers echter niet wilt inschakelen wanneer de gebruiker zich voor het eerst aanmeldt, moeten gebruikers aanwezig zijn in de gebruikersmappen waarvan het exemplaar van BitBucket gebruikmaakt. Deze directory kan bijvoorbeeld LDAP of Atlassian Crowd zijn.

![Schermopname van de sectie Algemeen van de configuratie voor de EasySSO-invoegtoepassing, met Gebruiker maken na geslaagde aanmelding gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van EasySSO for BitBucket, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van EasySSO for BitBucket en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij het exemplaar van EasySSO for BitBucket waarvoor u eenmalige aanmelding hebt ingesteld.

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel EasySSO for BitBucket klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het EasySSO for BitBucket-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u EasySSO for BitBucket hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).