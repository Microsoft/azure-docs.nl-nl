---
title: VPN met Azure AD MFA met de NPS-extensie-Azure Active Directory
description: Integreer uw VPN-infra structuur met Azure AD MFA met behulp van de extensie Network Policy Server voor Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73fa82c3f162b546517ce40ef1447c002351d5b4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839536"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-ad-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Uw VPN-infra structuur integreren met Azure AD MFA met behulp van de Network Policy Server-extensie voor Azure

Met de uitbrei ding van de Network Policy Server (NPS) voor Azure kunnen organisaties de verificatie van Remote Authentication Dial-In User Service (RADIUS)-client beveiligen met behulp van [Azure AD multi-factor Authentication (MFA)](howto-mfaserver-nps-rdg.md)in de Cloud, dat verificatie in twee stappen biedt.

Dit artikel bevat instructies voor het integreren van een NPS-infra structuur met MFA door de NPS-extensie voor Azure te gebruiken. Dit proces maakt verificatie in twee stappen mogelijk voor gebruikers die verbinding met uw netwerk proberen te maken met behulp van een VPN.

Services voor netwerk beleid en-toegang biedt organisaties de volgende mogelijkheden:

* Wijs een centrale locatie toe voor het beheer en beheer van de netwerk aanvragen die u wilt opgeven:

  * Wie kan verbinding maken

  * De tijden waarop verbindingen worden toegestaan

  * De duur van verbindingen

  * Het beveiligings niveau dat clients moeten gebruiken om verbinding te maken

    In plaats van beleid op elke VPN-of Extern bureaublad-gateway server op te geven, moet u dit doen nadat ze zich op een centrale locatie bevinden. Het RADIUS-protocol wordt gebruikt om gecentraliseerde verificatie, autorisatie en accounting (AAA) te bieden.

* Netwerk toegangs beveiliging (NAP) client Health Policies instellen en afdwingen om te bepalen of apparaten onbeperkte of beperkte toegang tot netwerk bronnen worden verleend.

* Bieden een manier om verificatie en autorisatie af te dwingen voor toegang tot draadloze 802.1 x-compatibele en Ethernet-switches.
  Zie [Network Policy Server](/windows-server/networking/technologies/nps/nps-top)voor meer informatie.

Om de beveiliging te verbeteren en een hoge mate van naleving te bieden, kunnen organisaties NPS integreren met Azure AD Multi-Factor Authentication om ervoor te zorgen dat gebruikers verificatie in twee stappen gebruiken om verbinding te maken met de virtuele poort op de VPN-server. Gebruikers kunnen alleen toegang krijgen als ze de combi natie van gebruikers naam en wacht woord opgeven en andere informatie die ze beheren. Deze informatie moet worden vertrouwd en kan niet eenvoudig worden gedupliceerd. Het kan een GSM-nummer, een vast nummer of een toepassing op een mobiel apparaat bevatten.

Vóór de beschik baarheid van de NPS-extensie voor Azure moesten klanten die verificatie in twee stappen voor geïntegreerde NPS-en MFA-omgevingen willen implementeren, een afzonderlijke MFA-server in een on-premises omgeving configureren en onderhouden. Dit type verificatie wordt aangeboden door Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS.

Met de NPS-extensie voor Azure kunnen organisaties RADIUS-client verificatie beveiligen door een on-premises op basis van MFA-oplossing of een op de cloud gebaseerde MFA-oplossing te implementeren.

## <a name="authentication-flow"></a>Verificatiestroom

Wanneer gebruikers verbinding maken met een virtuele poort op een VPN-server, moeten ze eerst worden geverifieerd met behulp van verschillende protocollen. De protocollen staan het gebruik toe van een combi natie van gebruikers naam en wacht woord en verificatie methoden op basis van certificaten.

Naast het verifiëren en verifiëren van hun identiteit, moeten gebruikers de juiste inbel machtigingen hebben. In eenvoudige implementaties worden inbel machtigingen waarmee toegang wordt toegestaan, direct ingesteld op de Active Directory gebruikers objecten.

![Tabblad Inbellen in Active Directory gebruikers-en computer gebruikers eigenschappen](./media/howto-mfa-nps-extension-vpn/image1.png)

Bij eenvoudige implementaties verleent of weigert elke VPN-server toegang op basis van beleids regels die zijn gedefinieerd op elke lokale VPN-server.

In grotere en meer schaal bare implementaties worden de beleids regels die VPN-toegang verlenen of weigeren, gecentraliseerd op RADIUS-servers. In dergelijke gevallen fungeert de VPN-server als een toegangs server (RADIUS-client) die verbindings aanvragen en account berichten doorstuurt naar een RADIUS-server. Als u verbinding wilt maken met de virtuele poort op de VPN-server, moeten gebruikers worden geverifieerd en voldoen aan de voor waarden die centraal zijn gedefinieerd op RADIUS-servers.

Wanneer de NPS-extensie voor Azure is geïntegreerd met de NPS, worden er als volgt een geslaagde verificatie stroom resultaten gegeven:

1. De VPN-server ontvangt een verificatie aanvraag van een VPN-gebruiker die de gebruikers naam en het wacht woord bevat om verbinding te maken met een bron, zoals een Extern bureaublad sessie.
2. De VPN-server fungeert als een RADIUS-client en converteert de aanvraag naar een RADIUS- *Access-Request-* bericht en verzendt deze (met een versleuteld wacht woord) naar de RADIUS-server waarop de NPS-extensie is geïnstalleerd.
3. De combi natie van gebruikers naam en wacht woord wordt in Active Directory gecontroleerd. Als de gebruikers naam of het wacht woord onjuist is, verzendt de RADIUS-server een bericht over een *toegangs weigering* .
4. Als aan alle voor waarden, zoals opgegeven in de NPS-verbindings aanvraag en het netwerk beleid, wordt voldaan (bijvoorbeeld het tijdstip van de dag of de beperkingen van groepslid maatschappen), activeert de NPS-extensie een aanvraag voor secundaire verificatie met Azure AD Multi-Factor Authentication.
5. Azure AD Multi-Factor Authentication communiceert met Azure Active Directory, haalt de details van de gebruiker op en voert de secundaire verificatie uit met behulp van de methode die door de gebruiker is geconfigureerd (mobiele telefoon oproep, tekst bericht of mobiele app).
6. Wanneer de MFA-uitdaging is geslaagd, communiceert Azure AD Multi-Factor Authentication het resultaat naar de NPS-extensie.
7. Nadat de verbindings poging is geverifieerd en geautoriseerd, verzendt de NPS waar de uitbrei ding is geïnstalleerd een RADIUS- *toegangs acceptatie* bericht naar de VPN-server (RADIUS-client).
8. De gebruiker krijgt toegang tot de virtuele poort op de VPN-server en brengt een versleutelde VPN-tunnel tot stand.

## <a name="prerequisites"></a>Vereisten

In deze sectie worden de vereisten beschreven die moeten worden voltooid voordat u MFA kunt integreren met de VPN-verbinding. Voordat u begint, moet u beschikken over de volgende vereisten op het punt:

* VPN-infra structuur
* Functie Services voor netwerk beleid en-toegang
* Licentie voor Azure AD Multi-Factor Authentication
* Windows Server-software
* Bibliotheken
* Azure Active Directory (Azure AD) gesynchroniseerd met on-premises Active Directory
* Azure Active Directory GUID-ID

### <a name="vpn-infrastructure"></a>VPN-infra structuur

In dit artikel wordt ervan uitgegaan dat u beschikt over een werkende VPN-infra structuur die gebruikmaakt van micro soft Windows Server 2016 en dat uw VPN-server momenteel niet is geconfigureerd voor het door sturen van verbindings aanvragen naar een RADIUS-server. In het artikel configureert u de VPN-infra structuur voor het gebruik van een centrale RADIUS-server.

Als u niet beschikt over een werkende VPN-infra structuur, kunt u deze snel maken door de richt lijnen in talrijke VPN-configuratie-zelf studies te volgen die u op de sites van micro soft en van derden kunt vinden.

### <a name="the-network-policy-and-access-services-role"></a>De functie Services voor netwerk beleid en-toegang

Services voor netwerk beleid en-toegang biedt de RADIUS-server en-client functionaliteit. In dit artikel wordt ervan uitgegaan dat u de functie Services voor netwerk beleid en-toegang hebt geïnstalleerd op een lidserver of domein controller in uw omgeving. In deze hand leiding configureert u RADIUS voor een VPN-configuratie. Installeer de functie Services voor netwerk beleid en-toegang op een *andere server dan* uw VPN-server.

Zie [een NAP-status beleids server installeren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10))voor meer informatie over het installeren van de functie Service Services voor netwerk beleid en-toegang Windows Server 2012 of hoger. NAP is afgeschaft in Windows Server 2016. Zie [Aanbevolen procedures voor NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10))voor een beschrijving van best practices voor NPS, met inbegrip van de aanbeveling voor het installeren van NPS op een domein controller.

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-licentie

Er is een licentie vereist voor Azure AD Multi-Factor Authentication, en deze is beschikbaar via een Azure AD Premium, Enterprise Mobility + Security of een zelfstandige licentie voor Multi-Factor Authentication. Op verbruik gebaseerde licenties voor Azure AD MFA, zoals per gebruiker of per verificatie-licentie, zijn niet compatibel met de NPS-extensie. Zie [Azure AD-multi-factor Authentication ophalen](concept-mfa-licensing.md)voor meer informatie. Voor test doeleinden kunt u een proef abonnement gebruiken.

### <a name="windows-server-software"></a>Windows Server-software

Voor de NPS-extensie is Windows Server 2008 R2 SP1 of hoger vereist, waarbij de functie Services voor netwerk beleid en-toegang is geïnstalleerd. Alle stappen in deze hand leiding zijn uitgevoerd met Windows Server 2016.

### <a name="libraries"></a>Bibliotheken

De volgende bibliotheken worden automatisch geïnstalleerd met de NPS-extensie:

-    [Herdistribueerbare Visual C++-pakketten voor Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Microsoft Azure Active Directory-module voor Windows PowerShell versie 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Als de Microsoft Azure Active Directory Power shell-module nog niet aanwezig is, wordt deze geïnstalleerd met een configuratie script dat u uitvoert als onderdeel van het installatie proces. Het is niet nodig om de module vooraf te installeren als deze nog niet is geïnstalleerd.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory gesynchroniseerd met on-premises Active Directory

On-premises gebruikers moeten worden gesynchroniseerd met Azure Active Directory en zijn ingeschakeld voor MFA om de NPS-extensie te kunnen gebruiken. In deze hand leiding wordt ervan uitgegaan dat on-premises gebruikers worden gesynchroniseerd met Azure Active Directory via Azure AD Connect. Hieronder vindt u instructies voor het inschakelen van gebruikers voor MFA.

Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor meer informatie over Azure AD Connect.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-ID

Als u de NPS-extensie wilt installeren, moet u de GUID van de Azure Active Directory weten. In de volgende sectie vindt u instructies voor het zoeken van de GUID van de Azure Active Directory.

## <a name="configure-radius-for-vpn-connections"></a>RADIUS configureren voor VPN-verbindingen

Als u de NPS-rol op een lidserver hebt geïnstalleerd, moet u deze configureren voor het verifiëren en autoriseren van de VPN-client die VPN-verbindingen aanvraagt. 

In deze sectie wordt ervan uitgegaan dat u de functie Services voor netwerk beleid en-toegang hebt geïnstalleerd, maar deze niet hebt geconfigureerd voor gebruik in uw infra structuur.

> [!NOTE]
> Als u al een werkende VPN-server hebt die gebruikmaakt van een gecentraliseerde RADIUS-server voor authenticatie, kunt u deze sectie overs Laan.
>

### <a name="register-server-in-active-directory"></a>Server registreren bij Active Directory

Als u in dit scenario goed wilt werken, moet de NPS-server zijn geregistreerd in Active Directory.

1. Serverbeheer openen.

2. Selecteer in Serverbeheer **extra** en selecteer vervolgens **Network Policy Server**.

3. Klik in de Network Policy Server-console met de rechter muisknop op **NPS (lokaal)** en selecteer **Server registreren in Active Directory**. Selecteer **OK** twee keer.

    ![Server registreren in Active Directory menu optie](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Houd de console geopend voor de volgende procedure.

### <a name="use-wizard-to-configure-the-radius-server"></a>Wizard gebruiken om de RADIUS-server te configureren

U kunt een standaard instelling (op basis van een wizard) of een geavanceerde configuratie optie gebruiken om de RADIUS-server te configureren. In deze sectie wordt ervan uitgegaan dat u gebruikmaakt van de standaard configuratie optie gebaseerd op de wizard.

1. Selecteer in de console Network Policy Server **NPS (lokaal)**.

2. Onder **standaard configuratie** selecteert u **RADIUS-server voor inbel-of VPN-verbindingen** en selecteert u vervolgens **VPN configureren of inbellen**.

    ![Configureer de RADIUS-server voor inbel-of VPN-verbindingen](./media/howto-mfa-nps-extension-vpn/image3.png)

3. In het venster **inbel-of VPN-verbindingen type selecteren** selecteert u **virtuele particuliere netwerk verbindingen** en selecteert u vervolgens **volgende**.

    ![VPN-verbindingen (virtueel particulier netwerk) configureren](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Selecteer in het venster **inbel-of VPN-server opgeven** de optie **toevoegen**.

5. Geef in het venster **nieuwe RADIUS-client** een beschrijvende naam op, voer de naam of het IP-adres van de VPN-server in en voer vervolgens een gedeeld geheim wacht woord in. Maak het gedeelde geheime wacht woord lang en complex. Neem deze op, want u hebt deze nodig in de volgende sectie.

    ![Een nieuw RADIUS-client venster maken](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Selecteer **OK**, en selecteer vervolgens **Volgende**.

7. Accepteer in het venster **verificatie methoden configureren** de standaard selectie (**micro soft Encrypted Authentication versie 2 [MS-CHAPv2])** of kies een andere optie en selecteer **volgende**.

    > [!NOTE]
    > Als u EAP (Extensible Authentication Protocol) configureert, moet u micro soft Challenge-Handshake Authentication Protocol (CHAPv2) of Protected Extensible Authentication Protocol (PEAP) gebruiken. Er wordt geen andere EAP ondersteund.

8. Selecteer in het venster **gebruikers groepen opgeven** de optie **toevoegen** en selecteer vervolgens een geschikte groep. Als er geen groep bestaat, laat u de selectie leeg om toegang te verlenen aan alle gebruikers.

    ![Geef het venster gebruikers groepen op om toegang toe te staan of te weigeren](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Selecteer **Volgende**.

10. Selecteer **volgende** in het venster **IP-filters opgeven** .

11. Accepteer de standaard instellingen in het venster **versleutelings instellingen opgeven** en selecteer **volgende**.

    ![Het venster versleutelings instellingen opgeven](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Laat in het venster **een realm-naam opgeven** de realm-naam leeg, accepteer de standaard instelling en selecteer **volgende**.

    ![Het venster een realm-naam opgeven](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Selecteer **volt ooien** in het venster **nieuwe inbel-of VPN-clients maken en RADIUS-client** .

    ![Venster configuratie voltooid](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>De RADIUS-configuratie verifiëren

In deze sectie vindt u meer informatie over de configuratie die u hebt gemaakt met behulp van de wizard.

1. Vouw op het Network Policy Server, in de NPS-console (lokaal), **RADIUS-clients** uit en selecteer **RADIUS-clients**.

2. Klik in het detail venster met de rechter muisknop op de RADIUS-client die u hebt gemaakt en selecteer vervolgens **Eigenschappen**. De eigenschappen van de RADIUS-client (de VPN-server) moeten er als volgt uitzien:

    ![De VPN-eigenschappen en-configuratie controleren](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Selecteer **Annuleren**.

4. Vouw op het Network Policy Server in de NPS-console (lokaal) **beleid** uit en selecteer beleid voor **verbindings aanvragen**. Het beleid voor VPN-verbindingen wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

    ![Beleid voor verbindings aanvragen met het beleid voor VPN-verbindingen](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Selecteer onder **beleid** de optie **netwerk beleid**. Als het goed is, ziet u een beleid voor VPN-verbindingen (virtueel particulier netwerk) dat lijkt op het beleid dat wordt weer gegeven in de volgende afbeelding:

    ![Netwerk beleid met beleid voor verbindingen met virtuele particuliere netwerken](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configureer uw VPN-server voor het gebruik van RADIUS-verificatie

In deze sectie configureert u de VPN-server voor het gebruik van RADIUS-verificatie. In de instructies wordt ervan uitgegaan dat u een werkende configuratie hebt van een VPN-server, maar deze niet hebt geconfigureerd voor het gebruik van RADIUS-verificatie. Nadat u de VPN-server hebt geconfigureerd, controleert u of uw configuratie werkt zoals verwacht.

> [!NOTE]
> Als u al een werkende VPN-server configuratie hebt die gebruikmaakt van RADIUS-authenticatie, kunt u deze sectie overs Laan.
>

### <a name="configure-authentication-provider"></a>Verificatie provider configureren

1. Open Serverbeheer op de VPN-server.

2. Selecteer in Serverbeheer **extra** en selecteer vervolgens **route ring en externe toegang**.

3. Klik in het venster **route ring en RAS** met de rechter muisknop **\<server name> (lokaal)** en selecteer vervolgens **Eigenschappen**.

4. Selecteer in het venster **\<server name> Eigenschappen (lokaal)** het tabblad **beveiliging** .

5. Op het tabblad **beveiliging** , onder **verificatie provider**, selecteert u **RADIUS-verificatie** en selecteert **u configureren**.

    ![RADIUS-verificatie provider configureren](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Selecteer in het venster **RADIUS-verificatie** de optie **toevoegen**.

7. Ga als volgt te werk in het venster **RADIUS-server toevoegen** :

    a. Geef in het vak **Server naam** de naam of het IP-adres van de RADIUS-server op die u in de vorige sectie hebt geconfigureerd.

    b. Voor het **gedeelde geheim** selecteert u **wijzigen** en voert u het gedeelde geheime wacht woord in dat u eerder hebt gemaakt en geregistreerd.

    c. Voer in het vak **time-out (seconden)** de waarde **30** in.  
    De time-outwaarde is nodig om voldoende tijd te bieden om de tweede verificatie factor te volt ooien. Sommige Vpn's of regio's vereisen een time-outinstellingen van meer dan 30 seconden om te voor komen dat gebruikers meerdere telefoon gesprekken ontvangen. Als gebruikers met dit probleem te maken krijgen, verhoogt u de time-outwaarde **(seconden)** in stappen van 30 seconden totdat het probleem niet opnieuw optreedt.

    ![Het venster RADIUS-server toevoegen de time-out configureren](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Selecteer **OK**.

### <a name="test-vpn-connectivity"></a>VPN-connectiviteit testen

In deze sectie bevestigt u dat de VPN-client is geverifieerd en geautoriseerd door de RADIUS-server wanneer u verbinding probeert te maken met de virtuele VPN-poort. In deze instructies wordt ervan uitgegaan dat u Windows 10 als een VPN-client gebruikt.

> [!NOTE]
> Als u al een VPN-client hebt geconfigureerd om verbinding te maken met de VPN-server en de instellingen heeft opgeslagen, kunt u de stappen voor het configureren en opslaan van een VPN-verbindings object overs Laan.
>

1. Selecteer op de VPN-client computer de knop **Start** en selecteer vervolgens de knop **instellingen** .

2. Selecteer in het venster **Windows-instellingen** de optie **netwerk & Internet**.

3. Selecteer **VPN**.

4. Selecteer **een VPN-verbinding toevoegen**.

5. In het venster **een VPN-verbinding toevoegen** , in het vak **VPN-provider** , selecteert u **Windows (ingebouwd)**, vult u de resterende velden in, indien van toepassing, en selecteert u vervolgens **Opslaan**.

    ![Het venster een VPN-verbinding toevoegen](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Ga naar **configuratie scherm** en selecteer vervolgens **netwerk centrum**.

7. Selecteer **adapter instellingen wijzigen**.

    ![Netwerk centrum-adapter instellingen wijzigen](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Klik met de rechter muisknop op de VPN-netwerk verbinding en selecteer **Eigenschappen**.

9. Selecteer in het venster VPN-eigenschappen het tabblad **beveiliging** .

10. Zorg ervoor dat op het tabblad **beveiliging** alleen **Microsoft CHAP versie 2 (MS-CHAP v2)** is geselecteerd en selecteer **OK**.

    ![De optie deze protocollen toestaan](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Klik met de rechter muisknop op de VPN-verbinding en selecteer vervolgens **verbinding maken**.

12. Selecteer in het venster **instellingen** de optie **verbinding maken**.  
    Een geslaagde verbinding wordt weer gegeven in het beveiligings logboek op de RADIUS-server, als gebeurtenis-ID 6272, zoals hier wordt weer gegeven:

    ![Gebeurtenis venster Eigenschappen een geslaagde verbinding weer te geven](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Problemen met RADIUS oplossen

Stel dat uw VPN-configuratie werkte voordat u de VPN-server hebt geconfigureerd voor het gebruik van een gecentraliseerde RADIUS-server voor verificatie en autorisatie. Als de configuratie werkt, is het waarschijnlijk dat het probleem wordt veroorzaakt door een onjuiste configuratie van de RADIUS-server of het gebruik van een ongeldige gebruikers naam of wacht woord. Als u bijvoorbeeld het alternatieve UPN-achtervoegsel in de gebruikers naam gebruikt, kan de aanmeldings poging mislukken. Gebruik dezelfde account naam voor de beste resultaten.

Om deze problemen op te lossen, kunt u het beste de beveiligings logboeken op de RADIUS-server onderzoeken. Als u tijd wilt besparen op gebeurtenissen, kunt u de aangepaste weer gave voor op rollen gebaseerd netwerk beleid en toegangs server gebruiken in Logboeken, zoals hier wordt weer gegeven. "Gebeurtenis-ID 6273" geeft gebeurtenissen aan waarbij de NPS toegang tot een gebruiker heeft geweigerd.

![Logboeken met NPAS-gebeurtenissen](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication configureren

Voor hulp bij het configureren van gebruikers voor Multi-Factor Authentication raadpleegt u de artikelen [een Azure AD multi-factor Authentication-implementatie in de Cloud plannen](howto-mfa-getstarted.md#create-conditional-access-policy) en [Mijn account instellen voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>De NPS-extensie installeren en configureren

In deze sectie vindt u instructies voor het configureren van VPN voor het gebruik van MFA voor client verificatie met de VPN-server.

> [!NOTE]
> De register sleutel REQUIRE_USER_MATCH is hoofdletter gevoelig. Alle waarden moeten worden ingesteld in een hoofd letters.
>

Nadat u de NPS-uitbrei ding hebt geïnstalleerd en geconfigureerd, is alle op RADIUS gebaseerde client verificatie die door deze server wordt verwerkt, vereist voor het gebruik van MFA. Als al uw VPN-gebruikers niet zijn Inge schreven in azure AD Multi-Factor Authentication, kunt u een van de volgende handelingen uitvoeren:

* Stel een andere RADIUS-server in voor het verifiëren van gebruikers die niet zijn geconfigureerd voor het gebruik van MFA.

* Maak een register vermelding waarmee gebruikers die zich hebben voorgedaan, een tweede verificatie factor kunnen opgeven als ze zijn Inge schreven bij Azure AD Multi-Factor Authentication.

Maak een nieuwe teken reeks waarde met de naam _REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa_ en stel de waarde in op *waar* of *Onwaar*.

![De instelling gebruikers overeenkomst vereisen](./media/howto-mfa-nps-extension-vpn/image34.png)

Als de waarde is ingesteld op *True* of leeg is, gelden voor alle verificatie aanvragen een MFA-Challenge. Als de waarde is ingesteld op *Onwaar*, worden MFA-uitdagingen alleen verleend aan gebruikers die zijn Inge schreven bij Azure AD multi-factor Authentication. Gebruik de instelling *Onwaar* alleen in tests of productie omgevingen tijdens een voorbereidings periode.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>De Tenant-ID van Azure Active Directory ophalen

Als onderdeel van de configuratie van de NPS-extensie moet u beheerders referenties en de ID van uw Azure AD-Tenant opgeven. Voer de volgende stappen uit om de Tenant-ID op te halen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder van de Azure-Tenant.
1. Selecteer in het menu Azure Portal **Azure Active Directory** of zoek en selecteer **Azure Active Directory** op een wille keurige pagina.
1. Op de pagina **overzicht** wordt de *informatie* over de Tenant weer gegeven. Selecteer het pictogram **kopiëren** naast de *Tenant-id*, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

   ![De Tenant-ID ophalen uit de Azure Portal](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>De NPS-extensie installeren

De NPS-extensie moet zijn geïnstalleerd op een server waarop de functie Services voor netwerk beleid en-toegang is geïnstalleerd en die fungeert als de RADIUS-server in uw ontwerp. Installeer de NPS-extensie *niet* op de VPN-server.

1. Down load de NPS-extensie vanuit het [micro soft Download centrum](https://aka.ms/npsmfa).

2. Kopieer het uitvoer bare installatie bestand (*NpsExtnForAzureMfaInstaller.exe*) naar de NPS-server.

3. Dubbel klik op de NPS-server op **NpsExtnForAzureMfaInstaller.exe** en selecteer **uitvoeren** als u hierom wordt gevraagd.

4. Bekijk in het venster **NPS-uitbrei ding voor Azure AD MFA Setup** de licentie voorwaarden voor software, schakel het selectie vakje **Ik ga akkoord met de licentie voorwaarden** in en selecteer **installeren**.

    ![Het venster ' NPS-extensie voor Azure AD MFA instellen '](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Selecteer **sluiten** in het venster **NPS-uitbrei ding voor Azure AD MFA Setup** .  

    ![Het bevestigings venster "installatie is voltooid"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Certificaten configureren voor gebruik met de NPS-extensie met behulp van een Power shell-script

Configureer certificaten voor gebruik door de NPS-extensie om beveiligde communicatie en zekerheid te garanderen. De NPS-onderdelen bevatten een Windows Power shell-script waarmee een zelfondertekend certificaat wordt geconfigureerd voor gebruik met NPS.

Met het script worden de volgende acties uitgevoerd:

* Hiermee maakt u een zelfondertekend certificaat.
* Koppelt de open bare sleutel van het certificaat aan de Service-Principal in azure AD.
* Hiermee slaat u het certificaat op in het archief van de lokale computer.
* Hiermee wordt de netwerk gebruiker toegang verleend tot de persoonlijke sleutel van het certificaat.
* Hiermee wordt de NPS-service opnieuw gestart.

Als u uw eigen certificaten wilt gebruiken, moet u de open bare sleutel van uw certificaat koppelen aan de Service-Principal in azure AD, enzovoort.

Als u het script wilt gebruiken, geeft u de extensie op met uw Azure Active Directory beheerders referenties en de Azure Active Directory Tenant-ID die u eerder hebt gekopieerd. Het account moet zich in dezelfde Azure AD-Tenant behoren als u de uitbrei ding wilt inschakelen voor. Voer het script uit op elke NPS-server waarop u de NPS-extensie installeert.

1. Voer Windows Power shell uit als beheerder.

2. Ga naar de Power shell-opdracht prompt en voer **cd "C:\Program Files\Microsoft\AzureMfa\Config"** in en selecteer ENTER.

3. Typ **.\AzureMfaNpsExtnConfigSetup.ps1** bij de volgende opdracht prompt en selecteer vervolgens ENTER. Het script controleert of de Azure AD Power shell-module is geïnstalleerd. Als deze niet is geïnstalleerd, wordt de module door het script geïnstalleerd.

    ![Het AzureMfsNpsExtnConfigSetup.ps1-configuratie script uitvoeren](./media/howto-mfa-nps-extension-vpn/image38.png)

    Als er een beveiligings fout is opgetreden vanwege TLS, schakelt u TLS 1,2 in met behulp `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` van de opdracht uit de Power shell-prompt.
    
    Nadat het script de installatie van de Power shell-module heeft gecontroleerd, wordt het aanmeldings venster van de Azure Active Directory Power shell-module weer gegeven.

4. Voer uw Azure AD-beheerders referenties en-wacht woord in en selecteer **Aanmelden**.

    ![Verifiëren bij Azure AD Power shell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Plak de Tenant-ID die u eerder hebt gekopieerd bij de opdracht prompt en selecteer vervolgens ENTER.

    ![Voer de Azure AD-Tenant-ID in die is gekopieerd vóór](./media/howto-mfa-nps-extension-vpn/image40.png)

    Het script maakt een zelfondertekend certificaat en voert andere configuratie wijzigingen uit. De uitvoer is vergelijkbaar met die in de volgende afbeelding:

    ![Power shell-venster met zelfondertekend certificaat](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Start de server opnieuw op.

### <a name="verify-the-configuration"></a>De configuratie controleren

Als u de configuratie wilt controleren, moet u een nieuwe VPN-verbinding tot stand brengen met de VPN-server. Nadat u uw referenties voor primaire verificatie hebt opgegeven, wacht de VPN-verbinding tot de secundaire authenticatie is geslaagd voordat de verbinding tot stand is gebracht, zoals hieronder wordt weer gegeven.

![Het venster Windows-instellingen VPN](./media/howto-mfa-nps-extension-vpn/image42.png)

Als u met de secundaire verificatie methode die u eerder hebt geconfigureerd in azure AD MFA hebt geverifieerd, bent u verbonden met de resource. Als de secundaire verificatie mislukt, hebt u echter geen toegang tot de bron.

In het volgende voor beeld biedt de Microsoft Authenticator-app op een Windows Phone de secundaire verificatie:

![Voor beeld van MFA-prompt op Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Nadat u hebt geverifieerd met behulp van de secundaire methode, krijgt u toegang tot de virtuele poort op de VPN-server. Omdat u een secundaire verificatie methode moet gebruiken met behulp van een mobiele app op een vertrouwd apparaat, is het aanmeldings proces veiliger dan bij gebruik van een combi natie van gebruikers naam en wacht woord.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Logboeken Logboeken weer geven voor geslaagde aanmeldings gebeurtenissen

Als u geslaagde aanmeldings gebeurtenissen in het Windows Logboeken Logboeken wilt weer geven, voert u een query uit op het Windows-beveiligings logboek op de NPS-server door de volgende Power shell-opdracht in te voeren:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Power shell-beveiligings Logboeken](./media/howto-mfa-nps-extension-vpn/image44.png)

U kunt ook het beveiligings logboek of de aangepaste weer gave voor services voor netwerk beleid en-toegang bekijken, zoals hier wordt weer gegeven:

![Voor beeld Network Policy Server logboek](./media/howto-mfa-nps-extension-vpn/image45.png)

Op de server waarop u de NPS-extensie voor Azure AD Multi-Factor Authentication hebt geïnstalleerd, kunt u Logboeken toepassings logboeken vinden die specifiek zijn voor de uitbrei ding op het *Logs\Microsoft\AzureMfa van toepassingen en services*.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Voor beeld van Logboeken taak venster AuthZ-logboeken](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Als de configuratie niet werkt zoals verwacht, start u de probleem oplossing door te controleren of de gebruiker is geconfigureerd voor het gebruik van MFA. Laat de gebruiker verbinding maken met de [Azure Portal](https://portal.azure.com). Als de gebruiker wordt gevraagd om secundaire authenticatie en verificatie kan worden uitgevoerd, kunt u een onjuiste configuratie van MFA elimineren als een probleem.

Als MFA werkt voor de gebruiker, raadpleegt u de relevante Logboeken-Logboeken. De logboeken bevatten de beveiligings gebeurtenis, het functioneren van de gateway en de Azure AD-Multi-Factor Authentication logboeken die in de vorige sectie worden beschreven.

Hier ziet u een voor beeld van een beveiligings logboek waarin een mislukte aanmeld gebeurtenis wordt weer gegeven (gebeurtenis-ID 6273):

![Beveiligings logboek met een mislukte aanmeld gebeurtenis](./media/howto-mfa-nps-extension-vpn/image47.png)

Hier wordt een gerelateerde gebeurtenis uit het Azure AD Multi-Factor Authentication-logboek weer gegeven:

![Azure AD Multi-Factor Authentication-logboeken](./media/howto-mfa-nps-extension-vpn/image48.png)

Raadpleeg de logboek bestanden van de NPS-data base-indeling waarin de NPS-service is geïnstalleerd voor geavanceerde probleem oplossing. De logboek bestanden worden gemaakt in de map _%systemroot%\System32\Logs_ als tekst bestanden met door komma's gescheiden waarden. Zie [NPS data base Format-logboek bestanden interpreteren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10))voor een beschrijving van de logboek bestanden.

De vermeldingen in deze logboek bestanden zijn lastig te interpreteren, tenzij u deze exporteert naar een spread sheet of een Data Base. U kunt een groot aantal hulpprogram ma's voor Internet Authentication Service (IAS) online parseren om u te helpen bij het interpreteren van de logboek bestanden. De uitvoer van een dergelijke Download bare [shareware-toepassing](https://www.deepsoftware.com/iasviewer) wordt hier weer gegeven:

![Voor beeld van shareware-app IAS-parser](./media/howto-mfa-nps-extension-vpn/image49.png)

Als u extra problemen wilt oplossen, kunt u een protocol analyse gebruiken zoals wireshark of [micro soft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide). De volgende afbeelding uit wireshark toont de RADIUS-berichten tussen de VPN-server en de NPS.

![Micro soft Message Analyzer met gefilterd verkeer](./media/howto-mfa-nps-extension-vpn/image50.png)

Zie [uw bestaande NPS-infra structuur integreren met Azure AD multi-factor Authentication](howto-mfa-nps-extension.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Azure AD-Multi-Factor Authentication ophalen](concept-mfa-licensing.md)

[Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md)

[Uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)