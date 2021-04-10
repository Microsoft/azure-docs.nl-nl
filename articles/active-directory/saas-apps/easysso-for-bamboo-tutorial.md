---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor Bamboo | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en EasySSO voor Bamboo.
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
ms.openlocfilehash: 3d784979876857e94c1e09ffc5b7bef11c0a0d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735963"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bamboo"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor Bamboo

In deze zelfstudie leert u hoe u EasySSO voor Bamboo kunt integreren met Azure AD (Active Directory). Wanneer u EasySSO voor Bamboo integreert met Azure AD, kunt u:

* Controleer in Microsoft Azure AD wie toegang heeft tot Bamboo.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Bamboo met hun Microsoft Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op EasySSO voor Bamboo waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* EasySSO voor Bamboo ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* EasySSO voor Bamboo ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-easysso-for-bamboo-from-the-gallery"></a>EasySSO voor Bamboo toevoegen vanuit de galerie

Om de integratie van EasySSO voor Bamboo in Azure AD te configureren, moet u EasySSO voor Bamboo uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **EasySSO voor Bamboo**.
1. Selecteer **EasySSO voor Bamboo** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bamboo"></a>Eenmalige aanmelding van Azure AD configureren en testen voor EasySSO for Bamboo

Configureer en test eenmalige aanmelding van Azure AD met EasySSO voor Bamboo met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in EasySSO voor Bamboo.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met EasySSO for Bambo te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij EasySSO voor Jira configureren](#configure-easysso-for-bamboo-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor EasySSO voor Jira maken](#create-easysso-for-bamboo-test-user)** : als u een tegenhanger van B.Simon in EasySSO voor Bamboo wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **EasySSO for Bamboo** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van EasySSO](mailto:support@techtime.co.nz) om deze waarden te verkrijgen als u twijfelt. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw EasySSO voor Bamboo-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing EasySSO voor Bamboo nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam | Bronkenmerk |
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Als uw Azure AD-gebruikers **sAMAccountName** hebben geconfigureerd, moet u **urn:oid:0.9.2342.19200300.100.1.1** aan het **sAMAccountName**-kenmerk toewijzen.
    
1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de koppeling **Downloaden** voor de optie **Certificaat (Base64)** of **Federation Metadata XML** en slaat u een optie of allebei op uw computer op. U hebt deze later nodig om Bamboo EasySSO te configureren.

    ![De link om het certificaat te downloaden](./media/easysso-for-bamboo-tutorial/certificate.png)
    
    Als u EasySSO voor Bamboo-configuratie handmatig met certificaat wilt uitvoeren, moet u vanuit onderstaande sectie ook de **Login URL** en de **Microsoft Azure AD-id** kopiëren en op uw computer opslaan.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot EasySSO voor Bamboo.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **EasySSO voor Bamboo** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-easysso-for-bamboo-sso"></a>Eenmalige aanmelding bij EasySSO voor Bamboo configureren

1. Als u de configuratie in Zoom wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Zoom instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Zoom-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Zoom. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 tot 10 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Zoom handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij uw Zoom-bedrijfssite.

1. Ga naar de sectie **Apps beheren**. 

    ![Apps beheren](./media/easysso-for-bamboo-tutorial/bamboo-admin-1.png)

2. Klik links in het scherm op **EasySSO**.

    ![EasySSO](./media/easysso-for-bamboo-tutorial/bamboo-admin-2.png)

3. Selecteer de optie **SAML**. U wordt dan naar de sectie SAML-configuratie geleid.

    ![SAML](./media/easysso-for-bamboo-tutorial/bamboo-admin-3.png)

4. Selecteer het tabblad **Certificaten** tabblad bovenaan om het volgende scherm te openen:

    ![Metagegevens-URL](./media/easysso-for-bamboo-tutorial/bamboo-admin-4.png)

5. Zoek nu **Certificaat (base64)** of het **metagegevensbestand** dat u hebt opgeslagen in de eerdere stappen van de configuratie voor **Azure AD SSO**. U hebt de volgende opties om verder te gaan:

    a. Gebruik het **Metagegevensbestand** voor app-federatie dat u hebt gedownload naar het lokale bestand op uw computer. Selecteer het keuzerondje **Uploaden** en volg het specifieke dialoogvenster Bestand uploaden voor uw besturingssysteem

    **OF**

    b. Open het **Metagegevensbestand** voor app-federatie om de inhoud van het bestand (in een eenvoudige teksteditor) te bekijken en kopieer deze naar het klembord. Selecteer de optie **Invoer** en plak de klembordinhoud in het tekstveld.
 
    **OF**

    c.  Volledig handmatige configuratie. Open het **Certificaat (Base64)** voor app-federatie om de inhoud van het bestand (in een eenvoudige teksteditor) te bekijken en kopieer deze naar het klembord. Plak de klembordinhoud in het tekstveld **Certificaten voor IdP-tokenondertekening**. Navigeer vervolgens naar het tabblad **Algemeen** en vul de velden **POST-binding-URL** en **Entiteits-id** met respectieve waarden voor **Aanmeldings-URL** en **Azure AD-id** die u eerder hebt opgeslagen.
 
6. Klik op de knop **Opslaan** onderaan de pagina. U ziet dat de inhoud van het bestand Metagegevens of Certificaat wordt geparseerd in de configuratievelden. De configuratie van EasySSO voor Bamboo is voltooid.

7. Voor de beste testervaring navigeert u naar het tabblad **Weergave** en schakelt u de optie **SAML-aanmelding** in. Hiermee wordt een aparte knop op het Bamboo-aanmeldingsscherm ingeschakeld die specifiek is bedoeld om uw Microsoft Azure AD SAML-integratie end-to-end te testen. U kunt deze knop ingeschakeld laten en ook de positie, kleur en vertaling voor productiemodus configureren.

    ![Weergave](./media/easysso-for-bamboo-tutorial/bamboo-admin-5.png)

    > [!NOTE]
    > Neem contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz) als u problemen ondervindt.

### <a name="create-easysso-for-bamboo-test-user"></a>Testgebruiker voor EasySSO voor Bamboo maken

In dit gedeelte maakt u in Bamboo een gebruiker aan met de naam Britta Simon. EasySSO voor Bamboo ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is **uitgeschakeld**. Als u het inrichten van gebruikers wilt inschakelen, moet u de optie **Gebruiker maken na geslaagde aanmelding** expliciet inschakelen in de sectie Algemeen van de configuratie van de EasySSO-invoegtoepassing. Als er nog geen gebruiker in Bamboo bestaat, wordt er na verificatie een nieuwe aangemaakt.

Als u het automatisch inrichten van gebruikers na hun eerste aanmelding echter niet wilt inschakelen, moeten gebruikers bestaan in back-end gebruikerslijsten waarvan het Bamboo-exemplaar gebruik maakt, zoals LDAP of Atlassian Crowd.

![Inrichten van gebruikers](./media/easysso-for-bamboo-tutorial/bamboo-admin-6.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

### <a name="idp-initiated-workflow"></a>Door IdP geïnitieerde werkstroom

In deze sectie test u uw configuratie van eenmalige aanmelding van Azure AD met behulp van Mijn apps.

Wanneer u op de tegel EasySSO voor Bamboo in het toegangsvenster klikt, wordt u automatisch aangemeld bij het Bamboo-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

### <a name="sp-initiated-workflow"></a>Door SP geïnitieerde werkstroom

In deze sectie test u uw configuratie van de eenmalige aanmelding voor Microsoft Azure AD met behulp van de knop Bamboo **SAML-aanmelding**.

![SAML-aanmelding door gebruiker](./media/easysso-for-bamboo-tutorial/bamboo-admin-7.png)

In dit scenario gaan we ervan uit dat u op de **knop SAML-aanmelding** hebt geklikt op het tabblad **Uiterlijk** op de configuratiepagina van uw Bamboo EasySSO (zie hierboven). Open uw aanmeldings-URL voor Bamboo in de incognitomodus van de browser om interferentie met uw bestaande sessies te voorkomen. Klik op de knop **SAML-aanmelding** om te worden omgeleid naar de Azure AD-gebruikersverificatiestroom. Eenmaal succesvol afgerond, wordt u via SAML teruggestuurd naar uw Bamboo-exemplaar als geverifieerde gebruiker.

Mogelijk ziet u het volgende scherm nadat u bent omgeleid vanuit Azure AD

![EasySSO-foutvenster](./media/easysso-for-bamboo-tutorial/bamboo-admin-8.png)

In dit geval volgt u de [instructies op deze pagina]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) om toegang te krijgen tot het bestand **atlassian-bamboo.log**. Details van de fout zijn beschikbaar via de verwijzings-id op de foutpagina van EasySSO.

Neem contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz) als u geen samenvatting van de logboekberichten kunt weergeven.

## <a name="next-steps"></a>Volgende stappen

Zodra u EasySSO for Bamboo hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).