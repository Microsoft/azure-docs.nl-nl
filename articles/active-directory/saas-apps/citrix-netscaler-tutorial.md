---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Citrix ADC (verificatie op basis van Kerberos) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Citrix ADC met behulp van verificatie op basis van Kerberos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: 75d46edb332fb28132592e414e78bad64e75fef5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-kerberos-based-authentication"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Citrix ADC (verificatie op basis van Kerberos)

In deze zelfstudie leert u hoe u Citrix ADC integreert met Azure AD (Azure Active Directory). Wanneer u Citrix ADC integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Citrix ADC.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Citrix ADC.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Citrix ADC waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. De zelfstudie omvat deze scenario’s:

* Met **SP geïnitieerde** eenmalige aanmelding voor Citrix ADC

* **Just-In-Time**-inrichting van gebruikers voor Citrix ADC

* [Verificatie op basis van Kerberos voor Citrix ADC](#publish-the-web-server)

* [Verificatie op basis van headers voor Citrix ADC](header-citrix-netscaler-tutorial.md#publish-the-web-server)


## <a name="add-citrix-adc-from-the-gallery"></a>Citrix ADC toevoegen vanuit de galerie

Als u Citrix ADC wilt integreren met Azure AD, voegt u Citrix ADC eerst vanuit de galerie toe aan de lijst met beheerde SaaS-apps:

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer **Azure Active Directory** in het linkermenu.

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Citrix ADC**.

1. Selecteer **Citrix ADC** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Citrix ADC

Configureer en test eenmalige aanmelding van Azure AD met Citrix ADC met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Citrix ADC.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Citrix ADC te configureren en te testen:

1. [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso) : zodat uw gebruikers deze functie kunnen gebruiken.

    1. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user): om eenmalige aanmelding van Azure AD te testen met B.Simon.

    1. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user): zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.

1. [Eenmalige aanmelding voor Citrix ADC configureren](#configure-citrix-adc-sso): als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.

    * [Testgebruiker voor Citrix ADC maken](#create-a-citrix-adc-test-user): als u een tegenhanger van B.Simon in Citrix ADC wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.

1. [Eenmalige aanmelding testen](#test-sso) : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen via de Azure-portal:

1. Selecteer in de Azure-portal op het integratiedeelvenster van de toepassing **Citrix ADC**, onder **Beheren**, de optie **Eenmalige aanmelding**.

1. Selecteer in het deelvenster **Selecteer een methode voor eenmalige aanmelding** de optie **SAML**.

1. Selecteer in het deelvenster **Eenmalige aanmelding met SAML instellen** het penpictogram **Bewerken** voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** doet u het volgende om de toepassing in de **door IDP geïnitieerde** modus te configureren:

    1. Typ in het tekstvak **Id** een URL met het volgende patroon: `https://<Your FQDN>`

    1. Typ in het tekstvak **Antwoord-URL** een URL met het volgende patroon: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Als u de toepassing in de **door SP geïnitieerde** modus wilt configureren, selecteert u **Extra URL's instellen** en voert u de volgende stap uit:

    * Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * De URL’s die in deze sectie worden gebruikt, zijn geen echte waarden. Werk deze waarden bij met de werkelijke waarden voor Id, Antwoord-URL en Aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Citrix ADC](https://www.citrix.com/contact/technical-support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.
    > * Om eenmalige aanmelding in te stellen, moeten de URL’s toegankelijk zijn vanaf openbare websites. U moet de firewall of andere beveiligingsinstellingen aan de zijde van Citrix ADC inschakelen, zodat Azure AD het token kan posten op de geconfigureerde URL.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** kopieert u de URL bij **App-URL voor federatieve metagegevens** en slaat u deze op in Kladblok.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Citrix ADC instellen** kopieert u de juiste URL's op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkermenu van de Azure-portal achtereenvolgens **Azure Active Directory**, **Gebruikers** en **Alle gebruikers**.

1. Selecteer **Nieuwe gebruiker** boven aan het scherm.

1. Voltooi deze stappen in **Gebruikerseigenschappen**:

   1. Voer `B.Simon` in bij **Naam**.  

   1. Bij **Gebruikersnaam** voert u _username@companydomain.extension_ in. Bijvoorbeeld `B.Simon@contoso.com`.

   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer of kopieer de waarde die in **Wachtwoord** wordt weergegeven.

   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door de gebruiker toegang te verlenen tot Citrix ADC.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

1. Selecteer **Citrix ADC** in de lijst met toepassingen.

1. In het app-overzicht onder **Beheren** selecteert u **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. In het dialoogvenster **Gebruikers en groepen** selecteert u **B.Simon** in de lijst **Gebruikers**. Kies **Selecteren**.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-citrix-adc-sso"></a>Eenmalige aanmelding voor Citrix ADC configureren

Selecteer een koppeling voor stappen voor het soort verificatie dat u wilt configureren:

- [Eenmalige aanmelding voor Citrix ADC configureren voor Kerberos-gebaseerde verificatie](#publish-the-web-server)

- [Eenmalige aanmelding voor Citrix ADC configureren voor verificatie op basis van headers](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>De webserver publiceren 

Ga als volgt te werk om een virtuele server te maken:

1. Selecteer **Verkeersbeheer** > **Taakverdeling** > **Services**.
    
1. Selecteer **Toevoegen**.

    ![Citrix ADC-configuratie - Deelvenster Services](./media/citrix-netscaler-tutorial/web01.png)

1. Stel de volgende waarden in voor de webserver die de toepassingen uitvoert:

   * **Servicenaam**
   * **IP-adres van de server/Bestaande server**
   * **Protocol**
   * **Poort**

### <a name="configure-the-load-balancer"></a>De load balancer configureren

Ga als volgt te werk om de load balancer te configureren:

1. Ga naar **Verkeersbeheer** > **Taakverdeling** > **Virtuele servers**.

1. Selecteer **Toevoegen**.

1. Stel de volgende waarden in zoals beschreven in de volgende schermopname:

    * **Naam**
    * **Protocol**
    * **IP-adres**
    * **Poort**

1. Selecteer **OK**.

    ![Citrix ADC-configuratie - Deelvenster Basisinstellingen](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>De virtuele server binden

Ga als volgt te werk om de load balancer aan de virtuele server te binden:

1. Selecteer in het deelvenster **Services en servicegroepen** de optie **Geen servicebinding van taakverdelende virtuele server**.

   ![Citrix ADC-configuratie - Deelvenster Servicebinding van taakverdelende virtuele server](./media/citrix-netscaler-tutorial/bind01.png)

1. Verifieer de instellingen zoals weergegeven in de volgende schermopname, en selecteer vervolgens **Sluiten**.

   ![Citrix ADC-configuratie - De servicebinding voor de virtuele server verifiëren](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Het certificaat binden

Om deze service als TLS te publiceren, bindt u het servercertificaat en test u uw toepassing:

1. Onder **Certificaat** selecteert u **Geen servercertificaat**.

   ![Citrix ADC-configuratie - Deelvenster Servercertificaat](./media/citrix-netscaler-tutorial/bind03.png)

1. Verifieer de instellingen zoals weergegeven in de volgende schermopname, en selecteer vervolgens **Sluiten**.

   ![Citrix ADC-configuratie - Het certificaat verifiëren](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profiel

Voltooi de volgende secties om het Citrix ADC SAML-profiel te configureren.

### <a name="create-an-authentication-policy"></a>Een verificatiebeleid maken

Ga als volgt te werk om een verificatiebeleid te maken:

1. Ga naar **Beveiliging** > **AAA – Toepassingsverkeer** > **Beleidsregels** > **Verificatie** > **Verificatiebeleid**.

1. Selecteer **Toevoegen**.

1. Typ of selecteer in het deelvenster **Verificatiebeleid maken** de volgende waarden:

    * **Naam**: Voer een naam in voor uw verificatiebeleid.
    * **Actie**: Voer **SAML** in en selecteer **Toevoegen**.
    * **Expressie**:  Voer **true** in.     
    
    ![Citrix ADC-configuratie - Deelvenster Verificatiebeleid maken](./media/citrix-netscaler-tutorial/policy01.png)

1. Selecteer **Maken**.

### <a name="create-an-authentication-saml-server"></a>Een verificatie-SAML-server maken

Om een verificatie-SAML-server te maken, gaat u naar het deelvenster **Verificatie-SAML-server maken** en voert u de volgende stappen uit:

1. Bij **Naam** voert u een naam in voor de verificatie-SAML-server.

1. Doe het volgende onder **SAML-metagegevens exporteren**:

   1. Schakel het selectievakje **Metagegevens importeren** in.

   1. Voer de URL voor federatieve metagegevens in die u eerder hebt gekopieerd uit de Azure SAML-gebruikersinterface.
    
1. Bij **Naam van uitgever** voert u de relevante URL in.

1. Selecteer **Maken**.

![Citrix ADC-configuratie - Deelvenster Verificatie-SAML-server maken](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Een virtuele verificatieserver maken

Ga als volgt te werk om een virtuele verificatieserver te maken:

1.  Ga naar **Beveiliging** > **AAA – Toepassingsverkeer** > **Beleidsregels** > **Verificatie** > **Virtuele verificatieservers**.

1.  Selecteer **Toevoegen** en voltooi de volgende stappen:

    1. Bij **Naam** voert u een naam in voor de virtuele verificatieserver.

    1. Schakel het selectievakje **Niet-adresseerbaar** in.

    1. Bij **Protocol** selecteert u **SSL**.

    1. Selecteer **OK**.
    
1. Selecteer **Doorgaan**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>De virtuele verificatieserver configureren om Azure AD te gebruiken

Wijzig de twee secties voor de virtuele verificatieserver:

1.  In het deelvenster **Geavanceerd verificatiebeleid** selecteert u **Geen verificatiebeleid**.

    ![Citrix ADC-configuratie - Deelvenster Geavanceerd verificatiebeleid](./media/citrix-netscaler-tutorial/virtual01.png)

1. In het deelvenster **Beleidsbinding** selecteert u het verificatiebeleid en selecteert u vervolgens **Binden**.

    ![Citrix ADC-configuratie - Deelvenster Beleidsbinding](./media/citrix-netscaler-tutorial/virtual02.png)

1. In het deelvenster **Formuliergebaseerde virtuele servers** selecteert u **Geen taakverdelende virtuele server**.

    ![Citrix ADC-configuratie - Deelvenster Formuliergebaseerde virtuele servers](./media/citrix-netscaler-tutorial/virtual03.png)

1. Bij **Verificatie-FQDN** voert u een Fully Qualified Domain Name (FQDN) in (vereist).

1. Selecteer de taakverdelende virtuele server die u wilt beveiligen met Azure AD-verificatie.

1. Selecteer **Binden**.

    ![Citrix ADC-configuratie - Deelvenster Binding van taakverdelende virtuele server](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Zorg ervoor dat u **Gereed** selecteert in het deelvenster **Configuratie van virtuele verificatieserver**.

1. Om uw wijzigingen te verifiëren, gaat u in een browser naar de toepassings-URL. U zou de aanmeldingspagina van uw tenant moeten zien in plaats van de niet-geverifieerde toegang die u eerder zou hebben gezien.

    ![Citrix ADC-configuratie - Een aanmeldingspagina in een webbrowser](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-kerberos-based-authentication"></a>Eenmalige aanmelding voor Citrix ADC configureren voor Kerberos-gebaseerde verificatie

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Een Kerberos-delegatieaccount maken voor Citrix ADC

1. Maak een gebruikersaccount (in dit voorbeeld gebruiken we _AppDelegation_).

    ![Citrix ADC-configuratie - Deelvenster Eigenschappen](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Stel de HOST SPN voor dit account in. 

    Voorbeeld: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    In dit voorbeeld geldt het volgende:

    * `IDENTT.WORK` is de FQDN van het domein.
    * `identt` is de NetBIOS-naam van het domein.
    * `appdelegation` is de naam van het gebruikersaccount voor delegatie.

1. Configureer de delegatie voor de webserver, zoals wordt weergegeven in de volgende schermafbeelding:
 
    ![Citrix ADC-configuratie - Deelvenster Delegatie onder Eigenschappen](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > In het de schermafbeeldingsvoorbeeld is _CWEB2_ de naam van de interne webserver waarop de WIA-site (Windows Integrated Authentication) wordt uitgevoerd.

### <a name="citrix-adc-aaa-kcd-kerberos-delegation-accounts"></a>Citrix ADC AAA KCD (Kerberos-delegatieaccounts)

Het Citrix ADC AAA KCD-account configureren:

1.  Ga naar **Citrix-gateway** > **AAA KCD-accounts (beperkte Kerberos-delegatie)** .

1.  Selecteer **Toevoegen** en voer de volgende waarden in of selecteer deze:

    * **Naam**: Voer een naam in voor het KCD-account.

    * **Realm**: Voer het domein en de uitbreiding in hoofdletters in.

    * **SPN van de service**: `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM` is verplicht en moet in hoofdletters zijn. Bijvoorbeeld: `http/cweb2@IDENTT.WORK`.

    * **Gedelegeerde gebruiker**: Voer de naam van de gedelegeerde gebruiker in.

    * Schakel het selectievakje **Wachtwoord voor gedelegeerde gebruiker** in, voer een wachtwoord in en bevestig dit.

1. Selecteer **OK**.
 
    ![Citrix ADC-configuratie - Deelvenster KCD-account configureren](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Beleid voor Citrix-verkeer en -verkeersprofiel

Het Citrix-verkeersbeleid en -verkeersprofiel configureren:

1.  Ga naar **Beveiliging** > **AAA – Toepassingsverkeer** > **Beleidsregels** > **Verkeersbeleid, Profielen en Formulier Verkeersbeleid SSO-profielen**.

1.  Selecteer **Verkeersprofielen**.

1.  Selecteer **Toevoegen**.

1.  Als u een verkeersprofiel wilt configureren, voert u de volgende waarden in of selecteert u deze.

    * **Naam**: Voer een naam in voor het verkeersprofiel.

    * **Eenmalige aanmelding**: Selecteer **AAN**.

    * **KCD-account**: Selecteer het KCD-account dat u in de vorige sectie hebt gemaakt.

1. Selecteer **OK**.

    ![Citrix ADC-configuratie - Deelvenster Verkeersprofiel configureren](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Selecteer **Verkeersbeleid**.

1.  Selecteer **Toevoegen**.

1.  Als u verkeersbeleid wilt configureren, voert u de volgende waarden in of selecteert u deze.

    * **Naam**: Voer een naam voor het verkeersbeleid in.

    * **Profiel**: Selecteer het verkeersprofiel dat u in de vorige sectie hebt gemaakt.

    * **Expressie**: Voer **true** in.

1. Selecteer **OK**.

    ![Citrix ADC-configuratie - Deelvenster Verkeersbeleid configureren](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Een verkeersbeleid binden aan een virtuele server in Citrix

Ga als volgt te werk om een verkeersbeleid te binden aan een virtuele server met behulp van de GUI:

1. Ga naar **Verkeersbeheer** > **Taakverdeling** > **Virtuele servers**.

1. In de lijst met virtuele servers selecteert u de virtuele server waaraan u het herschrijfbeleid wilt binden, en vervolgens selecteert u **Openen**.

1. In het deelvenster **Taakverdelende virtuele server** onder **Geavanceerde instellingen** selecteert u **Beleidsregels**. Alle beleidsregels die voor uw NetScaler-exemplaar zijn geconfigureerd, worden in de lijst weergegeven.
 
    ![Citrix ADC-configuratie - Deelvenster Taakverdelende virtuele server](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Citrix ADC-configuratie - Dialoogvenster Beleidsregels](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Schakel het selectievakje in naast de naam van het beleid dat u aan deze virtuele server wilt binden.
 
    ![Citrix ADC-configuratie - Deelvenster Verkeersbeleidsbinding van taakverdelende virtuele server](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Doe het volgende in het dialoogvenster **Type kiezen**:

    1. Bij **Beleid kiezen** selecteert u **Verkeer**.

    1. Bij **Type kiezen** selecteert u **Aanvraag**.

    ![Citrix ADC-configuratie - Deelvenster Type kiezen](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Wanneer het beleid is gebonden, selecteert u **Gereed**.
 
    ![Citrix ADC-configuratie - Dialoogvenster Beleidsregels](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Test de binding via de WIA-website.

    ![Citrix ADC-configuratie - Een testpagina in een webbrowser](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-adc-test-user"></a>Testgebruiker voor Citrix ADC maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Citrix ADC. Citrix ADC biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. U hoeft geen handelingen uit te voeren in deze sectie. Als een gebruiker nog niet bestaat in Citrix ADC, wordt er na verificatie een nieuwe gemaakt.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, moet u contact opnemen met het [klantondersteuningsteam van Citrix ADC](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Citrix ADC, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Citrix ADC en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Citrix ADC klikt, wordt u omgeleid naar de aanmeldings-URL van Citrix ADC. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Citrix ADC hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).