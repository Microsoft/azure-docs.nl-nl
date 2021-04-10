---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Confluence SAML SSO by Microsoft | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Confluence SAML SSO by Microsoft configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2020
ms.author: jeedes
ms.openlocfilehash: 34365a8bd7a15f502aa89a966adb14807e802cc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736996"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Confluence SAML SSO by Microsoft

In deze zelfstudie leert u hoe u Confluence SAML SSO by Microsoft kunt integreren met Azure Active Directory (Azure AD). Wanneer u Confluence SAML SSO by Microsoft integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Confluence SAML SSO by Microsoft.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Confluence SAML SSO by Microsoft.
* Uw accounts op één centrale locatie beheren: Azure Portal.


## <a name="description"></a>Beschrijving:

Gebruik uw Microsoft Azure Active Directory-account met Confluence Server van Atlassian om eenmalige aanmelding mogelijk te maken. Op deze manier kunnen alle gebruikers in uw organisatie zich met hun Azure AD-referenties aanmelden bij de Confluence-toepassing. Deze invoegtoepassing gebruikt SAML 2.0 voor federatie.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Confluence SAML SSO by Microsoft hebt u de volgende items nodig:

- Een Azure AD-abonnement
- De toepassing Confluence Server geïnstalleerd op een server met 64-bits Windows (on-premises of in de IaaS-infrastructuur in de cloud)
- Confluence Server is ingeschakeld voor HTTPS
- In de volgende sectie kunt u zien wat de ondersteunde versies zijn van de Confluence-invoegtoepassing
- Confluence Server is bereikbaar op internet vanaf de aanmeldingspagina van Azure AD voor verificatie en moet token uit Azure AD kunnen ontvangen
- Beheerderreferenties zijn ingesteld in Confluence
- WebSudo is uitgeschakeld in Confluence
- Testgebruiker gemaakt in Confluence Server

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam niet een productieomgeving van Confluence te gebruiken. Test de integratie eerst in een ontwikkel- of faseringsomgeving van de toepassing en gebruik dan pas de productieomgeving.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

U hebt het volgende nodig om aan de slag te gaan:

* Gebruik niet de productieomgeving, tenzij dit echt nodig is.
* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een[gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Confluence SAML SSO by Microsoft waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="supported-versions-of-confluence"></a>Ondersteunde versies van Confluence

Vanaf dit moment worden de volgende versies van Confluence ondersteund:

- Confluence: 5.0 t/m 5.10
- Confluence: 6.0.1 t/m 6.15.9
- Confluence: 7.0.1 tot en met 7.9.3

> [!NOTE]
> Houd er rekening mee dat onze Confluence-invoegtoepassing ook werkt op Ubuntu versie 16.04

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Confluence SAML SSO by Microsoft ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Confluence SAML SSO by Microsoft toevoegen vanuit de galerie

Om de integratie van Confluence SAML SSO by Microsoft te configureren in Azure AD, moet u Confluence SAML SSO by Microsoft vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Confluence SAML SSO by Microsoft** in het zoekvak.
1. Selecteer **Confluence SAML SSO by Microsoft** vanuit het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Eenmalige aanmelding van Azure AD voor Confluence SAML SSO by Microsoft configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Confluence SAML SSO by Microsoft met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Confluence SAML SSO by Microsoft.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Confluence SAML SSO by Microsoft te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Confluence SAML SSO by Microsoft configureren](#configure-confluence-saml-sso-by-microsoft-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Testgebruiker voor Confluence SAML SSO by Microsoft maken](#create-confluence-saml-sso-by-microsoft-test-user)** : als u een tegenhanger van B.Simon in Confluence SAML SSO by Microsoft wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Confluence SAML SSO by Microsoft Azure** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<domain:port>/plugins/servlet/saml/auth`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<domain:port>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. 'Port' is optioneel als het een benoemde URL betreft. Deze waarden krijgt u tijdens de configuratie van Confluence, wat later in de zelfstudie wordt uitgelegd.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

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

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Confluence SAML SSO by Microsoft.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Confluence SAML SSO by Microsoft** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Eenmalige aanmelding configureren voor Confluence SAML SSO by Microsoft

1. Meld u in een ander browservenster als beheerder aan bij de instantie van Confluence.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Schermopname toont het geselecteerde tandwielpictogram. In het vervolgkeuzemenu is 'Invoegtoepassingen' gemarkeerd.](./media/confluencemicrosoft-tutorial/addon1.png)

1. Download de invoegtoepassing uit het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56503). Upload de invoegtoepassing van Microsoft handmatig via het menu **Upload add-on**. Het downloaden van invoegtoepassingen valt onder de [Microsoft-serviceovereenkomst](https://www.microsoft.com/servicesagreement/).

    ![Schermopname toont de pagina 'Invoegtoepassingen beheren' waarin de actie 'invoegtoepassing uploaden' is geselecteerd.](./media/confluencemicrosoft-tutorial/addon12.png)

1. Voer de volgende stappen uit voor het uitvoeren van het omgekeerde-proxyscenario of load balancer-scenario van Confluence:

    > [!NOTE]
    > U moet de server eerst configureren met de onderstaande instructies en vervolgens de invoegtoepassing installeren.

    a. Voeg het onderstaande kenmerk toe in de **connector**-poort in het bestand **server.xml** van de JIRA-servertoepassing.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Schermopname toont het bestand 'server.xml' met het kenmerk dat wordt toegevoegd aan de poort 'connector'.](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Wijzig **Base URL** in **System Settings** overeenkomstig de proxy/load balancer.

    ![Schermopname toont de pagina 'Beheer - Instellingen' waarin 'Basis-URL' is gemarkeerd.](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Zodra de invoegtoepassing is geïnstalleerd, wordt deze weergegeven bij **User Installed** in de sectie **Manage add-ons**. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Schermopname toont de sectie 'Gebruiker geïnstalleerd' waarin de knop 'Configureren' is gemarkeerd.](./media/confluencemicrosoft-tutorial/addon15.png)

1. Voer de volgende stappen uit op de configuratiepagina:

    ![Schermopname toont de configuratiepagina voor eenmalige aanmelding.](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Zorg ervoor dat er maar één certificaat is toegewezen aan de app, zodat er geen fout optreedt bij het omzetten van de metagegevens. Als er meerdere certificaten zijn, krijgt de beheerder een foutmelding bij het omzetten van de metagegevens.

    1. Plak in het tekstvak **Metadata URL** de waarde voor **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit de Azure-portal en klik op de knop **Resolve**. De URL met de IdP-metagegevens wordt gelezen en de overeenkomende velden worden ingevuld.

    1. Kopieer de waarden voor **Identifier, Reply URL, Sign on URL** en plak deze in respectievelijk de vakken **Id, Antwoord-URL en Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    1. Typ bij **Login Button Name** de naam van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.

    1. Typ bij **Beschrijving knop voor Aanmelden** de beschrijving van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.

    1. Selecteer bij **SAML User ID Location** het keuzerondje **User ID is in the NameIdentifier element of the Subject statement** of **User ID is in an Attribute element**.  Deze id moet de gebruikers-id van Confluence zijn. Als de gebruikers-id niet overeenkomt, kunnen gebruikers zich niet aanmelden. 

       > [!Note]
       > De gebruikers-id in de standaard SAML-configuratie is de NameIdentifier. U kunt dit wijzigen in een kenmerkoptie en de juiste kenmerknaam invoeren.

    1. Als u de optie **User ID is in an Attribute element** selecteert, typt u in het tekstvak **Attribute Name** de naam van het kenmerk waaruit de gebruikers-id moet worden opgehaald. 

    1. Als u het federatieve domein (zoals ADFS, enzovoort) gebruikt met Azure AD, selecteert u de optie **Enable Home Realm Discovery** en geeft u een naam op voor **Domain Name**.

    1. Typ bij **Domain Name** de domeinnaam als het aanmelding op basis van ADFS betreft.

    1. Selecteer **Eenmalige afmelding inschakelen** als een gebruiker moet worden afgemeld bij Azure AD op het moment dat hij of zij zich afmeldt bij Confluence. 

    1. Schakel het selectievakje **Aanmelding bij Azure afdwingen** in als u zich alleen wilt aanmelden via Azure AD-referenties.

       > [!Note]
       > Als u het standaardaanmeldingsformulier wilt inschakelen voor de aanmelding van beheerders op de aanmeldingspagina wanneer het selectievakje Aanmelding bij Azure afdwingen is ingeschakeld, voegt u de queryparameter toe aan de browser-URL.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Klik op **Save** om de instellingen op te slaan.

       > [!NOTE]
       > Raadpleeg de [beheerdershandleiding voor MS Confluence SSO Connector](./ms-confluence-jira-plugin-adminguide.md) voor meer informatie over de installatie en probleemoplossing. U kunt ook [Veelgestelde vragen](./ms-confluence-jira-plugin-adminguide.md) bezoeken voor hulp.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij de on-premises server van Confluence, moeten ze worden ingericht bij Confluence SAML SSO by Microsoft. In het geval van Confluence SAML SSO by Microsoft is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de on-premises server van Confluence.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Werknemer toevoegen](./media/confluencemicrosoft-tutorial/user1.png)

1. Klik onder de sectie Users op het tabblad **Add Users**. Voer de volgende stappen uit in het dialoogvenster **Add a User**:

    ![Schermopname toont het 'Confluence-beheer', waarin het tabblad 'Gebruikers toevoegen' is geselecteerd en de gegevens van 'Een gebruiker toevoegen' zijn toegevoegd.](./media/confluencemicrosoft-tutorial/user2.png)

    a. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld B.Simon.

    b. Typ in het tekstvak **Volledige naam** de volledige naam van de gebruiker, zoals B.Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    d. Typ in het tekstvak **Wachtwoord** het wachtwoord van B.Simon.

    e. Typ het wachtwoord ter bevestiging in het vak **Confirm Password**.

    f. Klik op de knop **Add**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Confluence SAML SSO by Microsoft, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Confluence SAML SSO by Microsoft en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Confluence SAML SSO klikt, wordt u omgeleid naar de aanmeldings-URL van Confluence SAML SSO. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Confluence SAML SSO by Microsoft hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)