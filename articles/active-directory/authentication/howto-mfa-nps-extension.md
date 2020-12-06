---
title: Azure AD-Multi-Factor Authentication gebruiken met NPS-Azure Active Directory
description: Meer informatie over het gebruik van Azure AD Multi-Factor Authentication-mogelijkheden met uw bestaande Network Policy Server (NPS)-verificatie-infra structuur
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8340712e10721374bb2f0a35ac2e2e9a6abf181c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743034"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Uw bestaande Network Policy Server (NPS)-infra structuur integreren met Azure AD Multi-Factor Authentication

De Network Policy Server (NPS)-extensie voor Azure AD Multi-Factor Authentication voegt op de cloud gebaseerde MFA-mogelijkheden toe aan uw verificatie-infra structuur met behulp van uw bestaande servers. Met de NPS-extensie kunt u een telefoon gesprek, SMS-bericht of de verificatie van de mobiele app toevoegen aan uw bestaande verificatie stroom zonder dat u nieuwe servers hoeft te installeren, configureren en onderhouden.

De NPS-uitbrei ding fungeert als een adapter tussen RADIUS-en Cloud-Azure AD-Multi-Factor Authentication om een tweede factor van verificatie te bieden voor federatieve of gesynchroniseerde gebruikers.

## <a name="how-the-nps-extension-works"></a>Hoe de NPS-extensie werkt

Wanneer u de NPS-extensie voor Azure AD Multi-Factor Authentication gebruikt, bevat de verificatie stroom de volgende onderdelen:

1. **NAS/VPN-server** ontvangt aanvragen van VPN-clients en converteert deze naar RADIUS-aanvragen naar NPS-servers.
2. **NPS-server** maakt verbinding met Active Directory Domain Services (AD DS) voor het uitvoeren van de primaire verificatie voor de RADIUS-aanvragen en, wanneer de aanvraag is voltooid, door gegeven aan alle geïnstalleerde uitbrei dingen.  
3. Met de **NPS-extensie** wordt een aanvraag voor de secundaire verificatie door Azure AD multi-factor Authentication geactiveerd. Zodra de uitbrei ding het antwoord ontvangt en de MFA-uitdaging is geslaagd, wordt de verificatie aanvraag voltooid door de NPS-server te voorzien van beveiligings tokens die een MFA-claim bevatten die is uitgegeven door Azure STS.
4. **Azure AD MFA** communiceert met Azure Active Directory (Azure AD) om de details van de gebruiker op te halen en de secundaire verificatie uit te voeren met behulp van een verificatie methode die voor de gebruiker is geconfigureerd.

In het volgende diagram ziet u de verificatie aanvraag stroom op hoog niveau:

![Diagram van de verificatie stroom voor het verifiëren van gebruikers via een VPN-server naar NPS-server en de Azure AD Multi-Factor Authentication NPS-extensie](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Gedrag van RADIUS-protocol en de NPS-extensie

Als RADIUS is een UDP-protocol, neemt de afzender pakket verlies op en wacht op een antwoord. Na een bepaalde tijd kan er een time-out optreden voor de verbinding. Als dit het geval is, wordt het pakket opnieuw verzonden, omdat de afzender ervan uitgaat dat het pakket de bestemming niet heeft bereikt. In het verificatie scenario in dit artikel verzenden VPN-servers de aanvraag en wachten ze op een reactie. Als er een time-out optreedt voor de verbinding, verzendt de VPN-server de aanvraag opnieuw.

![Diagram van RADIUS UDP-pakket stroom en aanvragen na time-out op reactie van NPS-server](./media/howto-mfa-nps-extension/radius-flow.png)

De NPS-server reageert mogelijk niet op de oorspronkelijke aanvraag van de VPN-server voordat er een time-out is opgetreden voor de verbinding, omdat de MFA-aanvraag nog wordt verwerkt. De gebruiker heeft mogelijk niet gereageerd op de MFA-prompt, dus de Azure AD Multi-Factor Authentication NPS-extensie wacht op het volt ooien van die gebeurtenis. In dit geval identificeert de NPS-server extra VPN-server aanvragen als dubbele aanvragen. Deze dubbele VPN-server aanvragen worden door de NPS-server verwijderd.

![Diagram van NPS-server waarbij dubbele aanvragen worden verwijderd van de RADIUS-server](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Als u de logboeken van de NPS-server bekijkt, ziet u mogelijk dat er extra aanvragen worden verwijderd. Dit gedrag is inherent aan het ontwerp om te voor komen dat de eind gebruiker meerdere aanvragen voor één verificatie poging ontvangt. Verwijderde aanvragen in het gebeurtenis logboek van de NPS-server geven niet aan dat er een probleem is met de NPS-server of de Azure AD Multi-Factor Authentication NPS-extensie.

Voor het minimaliseren van verwijderde aanvragen raden wij aan dat VPN-servers ten minste 60 seconden een time-out hebben. Indien nodig, of om verwijderde aanvragen te verminderen in de gebeurtenis logboeken, kunt u de time-outwaarde voor de VPN-server verhogen naar 90 of 120 seconden.

Vanwege dit gedrag van het UDP-protocol kan de NPS-server een dubbele aanvraag ontvangen en een andere MFA-prompt verzenden, zelfs nadat de gebruiker al heeft gereageerd op de eerste aanvraag. Om deze timing voorwaarde te vermijden, blijft de Azure AD Multi-Factor Authentication NPS-extensie dubbele aanvragen gedurende Maxi maal 10 seconden filteren en verwijderen nadat een geslaagde reactie naar de VPN-server is verzonden.

![Diagram van de NPS-server die nog tien seconden na het retour neren van een geslaagde reactie dubbele aanvragen van de VPN-server negeert.](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Het is ook mogelijk dat er genegeerde aanvragen worden weer gegeven in de gebeurtenis logboeken van de NPS-server, zelfs wanneer de Azure AD-Multi-Factor Authentication prompt is voltooid. Dit is normaal gedrag en duidt niet op een probleem met de NPS-server of Azure AD Multi-Factor Authentication NPS-extensie.

## <a name="plan-your-deployment"></a>Uw implementatie plannen

De NPS-extensie verwerkt automatisch redundantie, dus u hebt geen speciale configuratie nodig.

U kunt net zoveel Azure AD Multi-Factor Authentication NPS-servers maken als u wilt. Als u meerdere-servers installeert, moet u een diff-client certificaat voor elke server gebruiken. Als u een certificaat voor elke server maakt, kunt u elk certificaat afzonderlijk bijwerken en hoeft u zich geen zorgen te maken over uitval tijd op al uw servers.

VPN-servers routeren verificatie aanvragen, zodat ze op de hoogte moeten zijn van de nieuwe Azure AD Multi-Factor Authentication NPS-servers.

## <a name="prerequisites"></a>Vereisten

De NPS-extensie is bedoeld om te werken met uw bestaande infra structuur. Zorg ervoor dat u aan de volgende vereisten voldoet voordat u begint.

### <a name="licenses"></a>Licenties

De NPS-extensie voor Azure AD Multi-Factor Authentication is beschikbaar voor klanten met [licenties voor Azure AD-multi-factor Authentication](./concept-mfa-howitworks.md). Licenties op basis van verbruik voor Azure AD Multi-Factor Authentication, zoals per gebruiker of per verificatie licentie, zijn niet compatibel met de NPS-extensie.

### <a name="software"></a>Software

Windows Server 2012 of hoger.

### <a name="libraries"></a>Bibliotheken

U moet de volgende bibliotheek hand matig installeren:

- [Visual C++ Redistributable voor Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

De volgende bibliotheken worden automatisch met de extensie geïnstalleerd.

- [Herdistribueerbare Visual C++-pakketten voor Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory-module voor Windows PowerShell versie 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

De Microsoft Azure Active Directory-module voor Windows PowerShell wordt ook geïnstalleerd via een configuratie script dat u uitvoert als onderdeel van het installatie proces, indien dit nog niet is gebeurd. U hoeft deze module niet vooraf te installeren als deze nog niet is geïnstalleerd.

### <a name="azure-active-directory"></a>Azure Active Directory

Iedereen die de NPS-extensie gebruikt, moet worden gesynchroniseerd met Azure AD met behulp van Azure AD Connect en moet worden geregistreerd voor MFA.

Wanneer u de uitbrei ding installeert, hebt u de *Tenant-id* en de beheerders referenties nodig voor uw Azure AD-Tenant. Voer de volgende stappen uit om de Tenant-ID op te halen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder van de Azure-Tenant.
1. Zoek en selecteer de **Azure Active Directory**.
1. Op de pagina **overzicht** wordt de *informatie* over de Tenant weer gegeven. Selecteer het pictogram **kopiëren** naast de *Tenant-id*, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

   ![De Tenant-ID ophalen uit de Azure Portal](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Netwerkvereisten

De NPS-server moet kunnen communiceren met de volgende Url's via de poorten 80 en 443:

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /credentials.Azure.com*

Daarnaast is connectiviteit met de volgende Url's vereist voor het volt ooien [van de installatie van de adapter met het opgegeven Power shell-script](#run-the-powershell-script):

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.net*

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

Voordat u de NPS-extensie installeert, moet u de omgeving voorbereiden om het verificatie verkeer te verwerken.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>De NPS-functie inschakelen op een server die lid is van een domein

De NPS-server maakt verbinding met Azure AD en verifieert de MFA-aanvragen. Kies één server voor deze rol. We raden u aan een server te kiezen die geen aanvragen van andere services verwerkt, omdat de NPS-extensie fouten genereert voor aanvragen die geen RADIUS zijn. De NPS-server moet worden ingesteld als de primaire en secundaire verificatie server voor uw omgeving. Er kunnen geen RADIUS-aanvragen naar een andere server worden geproxyeerd.

1. Open **Serverbeheer** op uw server. Selecteer de **wizard functies en onderdelen toevoegen** in het menu *Quick* start.
2. Kies voor uw installatie type installatie die op de **functie of het onderdeel is gebaseerd**.
3. Selecteer de server functie **Services voor netwerk beleid en-toegang** . Er wordt mogelijk een venster met de melding dat u aanvullende vereiste functies nodig hebt om deze functie uit te voeren.
4. Ga door met de wizard totdat de pagina *bevestiging wordt bevestigd* . Wanneer u klaar bent, selecteert u **installeren**.

Het kan enkele minuten duren om de NPS-server functie te installeren. Wanneer u klaar bent, gaat u door met de volgende secties om deze server te configureren voor het afhandelen van binnenkomende RADIUS-aanvragen van de VPN-oplossing.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configureer uw VPN-oplossing om te communiceren met de NPS-server

Afhankelijk van de VPN-oplossing die u gebruikt, verschillen de stappen voor het configureren van uw RADIUS-verificatie beleid. Configureer uw VPN-beleid zodat dit verwijst naar uw RADIUS NPS-server.

### <a name="sync-domain-users-to-the-cloud"></a>Domein gebruikers synchroniseren met de Cloud

Deze stap is mogelijk al voltooid voor uw Tenant, maar het is een goed idee om te controleren of Azure AD Connect uw data bases onlangs hebt gesynchroniseerd.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
2. **Azure Active Directory** selecteren  >  **Azure AD CONNECT**
3. Controleer of de synchronisatie status is **ingeschakeld** en of de laatste synchronisatie minder dan een uur geleden is.

Zie [Azure AD Connect Sync: scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)als u een nieuwe ronding van de synchronisatie wilt starten.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Bepalen welke verificatie methoden uw gebruikers kunnen gebruiken

Er zijn twee factoren die invloed hebben op de verificatie methoden die beschikbaar zijn met een implementatie van een NPS-extensie:

* De wachtwoord versleutelings algoritme die wordt gebruikt tussen de RADIUS-client (VPN, NetScaler server of andere) en de NPS-servers.
   - **Pap** ondersteunt alle verificatie methoden van Azure AD-multi-factor Authentication in de Cloud: telefoon oproep, een SMS-bericht, een mobiele app-melding, OATH-hardware-tokens en verificatie code voor mobiele apps.
   - Ondersteuning voor telefonische en **EAP** -telefoon **gesprekken en mobiele** app-meldingen.

    > [!NOTE]
    > Wanneer u de NPS-uitbrei ding implementeert, moet u deze factoren gebruiken om te evalueren welke methoden beschikbaar zijn voor uw gebruikers. Als uw RADIUS-client PAP ondersteunt, maar de client UX geen invoer velden heeft voor een verificatie code, zijn de meldingen voor telefoon gesprekken en mobiele apps de twee ondersteunde opties.
    >
    > Ook is het mogelijk dat de verificatie slaagt, ongeacht het gebruikte verificatie Protocol (PAP, CHAP of EAP), als uw MFA-methode is gebaseerd op tekst (SMS, mobiele app verificatie code of OATH-hardware-token) en als de gebruiker een code of tekst moet invoeren in het invoer veld van de gebruikers interface van de VPN-client. *Maar* alle RADIUS-kenmerken die in het netwerk toegangs beleid zijn geconfigureerd, worden *niet* doorgestuurd naar de RADIUS-Cient (het netwerk toegangs apparaat, zoals de VPN-gateway). Als gevolg hiervan kan de VPN-client meer toegang hebben dan u wilt, of minder toegang of geen toegang.

* De invoer methoden die de client toepassing (VPN, NetScaler server of andere) kan verwerken. Heeft de VPN-client bijvoorbeeld een aantal manieren om de gebruiker toe te staan een verificatie code in te voeren in een tekst-of mobiele app?

U kunt [niet-ondersteunde verificatie methoden uitschakelen](howto-mfa-mfasettings.md#verification-methods) in Azure.

### <a name="register-users-for-mfa"></a>Gebruikers voor MFA registreren

Voordat u de NPS-extensie implementeert en gebruikt, moeten gebruikers die Azure AD-Multi-Factor Authentication moeten uitvoeren, worden geregistreerd voor MFA. Als u de uitbrei ding wilt testen terwijl u deze implementeert, moet u ook ten minste één test account hebben dat volledig is geregistreerd voor Azure AD-Multi-Factor Authentication.

Als u een test account moet maken en configureren, gebruikt u de volgende stappen:

1. Meld u aan [https://aka.ms/mfasetup](https://aka.ms/mfasetup) met een test account.
2. Volg de aanwijzingen voor het instellen van een verificatie methode.
3. Maak in de Azure Portal als gebruiker met beheerders [rechten een beleid voor voorwaardelijke toegang](howto-mfa-getstarted.md#create-conditional-access-policy) om multi-factor Authentication voor het test account te vereisen.

> [!IMPORTANT]
>
> Zorg ervoor dat gebruikers zijn geregistreerd voor Azure AD Multi-Factor Authentication. Als gebruikers eerder alleen zijn geregistreerd voor selfservice voor wachtwoord herstel (SSPR), is *StrongAuthenticationMethods* ingeschakeld voor hun account. Azure AD Multi-Factor Authentication wordt afgedwongen wanneer *StrongAuthenticationMethods* is geconfigureerd, zelfs als de gebruiker alleen is geregistreerd voor SSPR.
>
> Gecombineerde beveiligings registratie kan worden ingeschakeld waarmee SSPR en Azure AD-Multi-Factor Authentication tegelijk worden geconfigureerd. Zie voor meer informatie [registratie van gecombineerde beveiligings gegevens inschakelen in azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> U kunt [gebruikers ook dwingen om verificatie methoden opnieuw te registreren](howto-mfa-userdevicesettings.md#manage-user-authentication-options) als ze eerder alleen SSPR hebben ingeschakeld.

## <a name="install-the-nps-extension"></a>De NPS-extensie installeren

> [!IMPORTANT]
> Installeer de NPS-extensie op een andere server dan het VPN-toegangs punt.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Down load en installeer de NPS-extensie voor Azure AD MFA

Als u de NPS-extensie wilt downloaden en installeren, voert u de volgende stappen uit:

1. [Down load de NPS-extensie](https://aka.ms/npsmfa) vanuit het micro soft Download centrum.
1. Kopieer het binaire bestand naar de Network Policy Server die u wilt configureren.
1. Voer *setup.exe* uit en volg de installatie-instructies. Als er fouten optreden, moet u ervoor zorgen dat de [bibliotheken van de vereiste sectie](#libraries) zijn geïnstalleerd.

#### <a name="upgrade-the-nps-extension"></a>De NPS-extensie upgraden

Als u later een upgrade van een bestaande installatie van een NPS-extensie wilt uitvoeren, moet u de volgende stappen uitvoeren om te voor komen dat de onderliggende server opnieuw wordt opgestart:

1. Verwijder de bestaande versie.
1. Voer het nieuwe installatie programma uit.
1. Start de *Network Policy Server-service (IAS)* opnieuw.

### <a name="run-the-powershell-script"></a>Het PowerShell-script uitvoeren

Het installatie programma maakt een Power shell `C:\Program Files\Microsoft\AzureMfa\Config` -script op (waarbij `C:\` uw installatie station is). Met dit Power shell-script worden de volgende acties uitgevoerd telkens wanneer het wordt uitgevoerd:

* Hiermee maakt u een zelfondertekend certificaat.
* Koppelt de open bare sleutel van het certificaat aan de Service-Principal in azure AD.
* Hiermee slaat u het certificaat op in het certificaat archief van de lokale computer.
* Hiermee wordt toegang verleend tot de persoonlijke sleutel van het certificaat aan de netwerk gebruiker.
* Hiermee wordt de NPS-service opnieuw gestart.

Tenzij u uw eigen certificaten wilt gebruiken (in plaats van de zelfondertekende certificaten die door het Power shell-script worden gegenereerd), voert u het Power shell-script uit om de installatie van de NPS-extensie te volt ooien. Als u de uitbrei ding op meerdere servers installeert, moet elke server een eigen certificaat hebben.

Als u de mogelijkheden voor taak verdeling of redundantie wilt bieden, herhaalt u deze stappen op extra NPS-servers, indien gewenst:

1. Open een Windows Power shell-prompt als beheerder.
1. Wijzig de mappen in de locatie waar het installatie programma het Power shell-script heeft gemaakt:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Voer het Power shell-script uit dat is gemaakt door het installatie programma.

   > [!IMPORTANT]
   > Voor klanten die gebruikmaken van de Clouds van Azure Government of Azure China 21Vianet, bewerkt u eerst de `Connect-MsolService` cmdlets in het *AzureMfaNpsExtnConfigSetup.ps1* script om de *AzureEnvironment* -para meters voor de vereiste Cloud op te neemt. Geef bijvoorbeeld *-AzureEnvironment USGovernment* of *-AzureEnvironment AzureChinaCloud* op.
   >
   > Zie [Connect-MsolService para meter Reference (](/powershell/module/msonline/connect-msolservice#parameters)Engelstalig) voor meer informatie.

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Meld u als beheerder aan bij Azure AD als u hierom wordt gevraagd.
1. Power shell vraagt om uw Tenant-ID. Gebruik de GUID van de *Tenant-id* die u hebt gekopieerd uit de Azure Portal in de sectie vereisten.
1. Er wordt een bericht weer gegeven wanneer het script is voltooid.  

Als uw vorige computer certificaat is verlopen en er een nieuw certificaat is gegenereerd, moet u verlopen certificaten verwijderen. Verlopen certificaten kunnen leiden tot problemen met het starten van de NPS-extensie.

> [!NOTE]
> Als u uw eigen certificaten gebruikt in plaats van certificaten te genereren met het Power shell-script, moet u ervoor zorgen dat ze worden uitgelijnd op de NPS-naamgevings Conventie. De naam van het onderwerp moet **CN = \<TenantID\> , ou = micro soft NPS extension** zijn.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Aanvullende stappen voor Microsoft Azure Government of Azure China 21Vianet

Voor klanten die gebruikmaken van de Clouds van Azure Government of Azure China 21Vianet, zijn de volgende aanvullende configuratie stappen vereist op elke NPS-server.

> [!IMPORTANT]
> Configureer deze register instellingen alleen als u een Azure Government of Azure China 21Vianet-klant bent.

1. Als u een Azure Government of Azure China 21Vianet-klant bent, opent u de **REGI ster-editor** op de NPS-server.
1. Navigeer naar `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Stel voor Azure Government klanten de volgende sleutel waarden in.:

    | Registersleutel       | Waarde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Stel voor Azure China 21Vianet-klanten de volgende sleutel waarden in:

    | Registersleutel       | Waarde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Herhaal de vorige twee stappen om de register sleutel waarden voor elke NPS-server in te stellen.
1. Start de NPS-service voor elke NPS-server opnieuw.

    Voor een minimale impact moet u elke NPS-server uit de NLB-rotatie een voor een tegelijk halen en wachten op alle verbindingen met de afvoer.

### <a name="certificate-rollover"></a>Certificaat overschakeling

Met release- *1.0.1.32* van de NPS-extensie wordt het lezen van meerdere certificaten nu ondersteund. Met deze functie kunt u updates van Rolling certificaten vereenvoudigen voordat ze verlopen. Als in uw organisatie een eerdere versie van de NPS-extensie wordt uitgevoerd, voert u een upgrade uit naar versie *1.0.1.32* of hoger.

Certificaten die zijn gemaakt door het `AzureMfaNpsExtnConfigSetup.ps1` script, zijn twee jaar geldig. Certificaten controleren op verlopen. Certificaten voor de NPS-extensie worden onder *persoonlijk* in het certificaat archief van de *lokale computer* geplaatst en worden *verleend aan* de Tenant-id die is opgegeven voor het installatie script.

Wanneer een certificaat de verval datum nadert, moet er een nieuw certificaat worden gemaakt om het te vervangen.  Dit proces wordt uitgevoerd door de `AzureMfaNpsExtnConfigSetup.ps1` bewerking opnieuw uit te voeren en dezelfde Tenant-id te houden wanneer u hierom wordt gevraagd. Dit proces moet worden herhaald op elke NPS-server in uw omgeving.

## <a name="configure-your-nps-extension"></a>Uw NPS-extensie configureren

Als uw omgeving is voor bereid en de NPS-extensie nu is geïnstalleerd op de vereiste servers, kunt u de extensie configureren.

Deze sectie bevat ontwerp overwegingen en suggesties voor geslaagde implementaties van NPS-extensies.

### <a name="configuration-limitations"></a>Configuratie beperkingen

- De NPS-extensie voor Azure AD Multi-Factor Authentication bevat geen hulp middelen voor het migreren van gebruikers en instellingen van MFA server naar de Cloud. Daarom wordt u aangeraden de uitbrei ding voor nieuwe implementaties te gebruiken in plaats van de bestaande implementatie. Als u de uitbrei ding gebruikt voor een bestaande implementatie, moeten uw gebruikers opnieuw een controle uitvoeren om de MFA-gegevens in de cloud te vullen.  
- De NPS-extensie gebruikt de UPN van de on-premises AD DS omgeving om de gebruiker te identificeren op Azure AD Multi-Factor Authentication voor het uitvoeren van de secundaire auth. De uitbrei ding kan worden geconfigureerd voor het gebruik van een andere id, zoals een alternatieve aanmeldings-ID of een aangepast AD DS veld dan UPN. Zie het artikel [Geavanceerde configuratie-opties voor de NPS-extensie voor multi-factor Authentication](howto-mfa-nps-extension-advanced.md)voor meer informatie.
- Niet alle versleutelings protocollen ondersteunen alle verificatie methoden.
   - **Pap** ondersteunt telefoon gesprekken, tekst berichten in één richting, meldingen over mobiele apps en verificatie code voor mobiele apps
   - Telefonische ondersteuning en melding voor mobiele apps via de service **CHAPV2** en **EAP**

### <a name="control-radius-clients-that-require-mfa"></a>Beheer RADIUS-clients waarvoor MFA is vereist

Zodra u MFA voor een RADIUS-client met de NPS-extensie hebt ingeschakeld, zijn alle verificaties voor deze client vereist om MFA uit te voeren. Als u MFA wilt inschakelen voor sommige RADIUS-clients, maar niet voor anderen, kunt u twee NPS-servers configureren en de uitbrei ding alleen op één van deze-services installeren.

Configureer RADIUS-clients waarvoor u MFA wilt vereisen voor het verzenden van aanvragen naar de NPS-server die is geconfigureerd met de extensie, en andere RADIUS-clients naar de NPS-server die niet met de extensie zijn geconfigureerd.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Voorbereiden voor gebruikers die niet zijn Inge schreven voor MFA

Als u gebruikers hebt die niet zijn Inge schreven voor MFA, kunt u bepalen wat er gebeurt wanneer ze proberen te verifiëren. Als u dit gedrag wilt beheren, gebruikt u de instelling *REQUIRE_USER_MATCH* in het registerpad *HKLM\Software\Microsoft\AzureMFA*. Deze instelling heeft één configuratie optie:

| Sleutel | Waarde | Standaard |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | WAAR/ONWAAR | Niet ingesteld (gelijk aan TRUE) |

Met deze instelling wordt bepaald wat er moet gebeuren wanneer een gebruiker niet is inge schreven voor MFA. Als de sleutel niet bestaat, niet is ingesteld of is ingesteld op *True* en de gebruiker niet is inge schreven, mislukt de extensie de MFA-Challenge.

Als de sleutel is ingesteld op *False* en de gebruiker niet is inge schreven, wordt de verificatie voortgezet zonder MFA uit te voeren. Als een gebruiker is inge schreven bij MFA, moeten ze worden geverifieerd met MFA, zelfs als *REQUIRE_USER_MATCH* is ingesteld op *False*.

U kunt ervoor kiezen om deze sleutel te maken en deze in te stellen op *Onwaar* wanneer uw gebruikers onboarding hebben en mogelijk nog niet allemaal zijn Inge schreven voor Azure AD multi-factor Authentication. Omdat het instellen van de sleutel echter toestaat dat gebruikers die niet zijn Inge schreven voor MFA zich kunnen aanmelden, moet u deze sleutel verwijderen voordat u naar de productie gaat.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="nps-extension-health-check-script"></a>Script voor status controle van NPS-uitbrei ding

Het volgende script is beschikbaar voor het uitvoeren van basis stappen voor de status controle bij het oplossen van problemen met de NPS-extensie.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hoe kan ik controleren of het certificaat van de client is geïnstalleerd zoals verwacht?

Zoek naar het zelfondertekende certificaat dat is gemaakt door het installatie programma in het certificaat archief en controleer of de persoonlijke sleutel machtigingen heeft die zijn verleend aan de *netwerk service* van de gebruiker. Het certificaat heeft de onderwerpnaam **CN \<tenantid\> , OE = micro soft NPS-extensie**

Zelfondertekende certificaten die door het script worden gegenereerd, `AzureMfaNpsExtnConfigSetup.ps1` hebben een geldigheids duur van twee jaar. Wanneer u controleert of het certificaat is geïnstalleerd, moet u ook controleren of het certificaat niet is verlopen.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Hoe kan ik controleren of mijn client certificaat is gekoppeld aan mijn Tenant in azure AD?

Open de Power shell-opdracht prompt en voer de volgende opdrachten uit:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Met deze opdrachten worden alle certificaten afgedrukt die aan uw Tenant zijn gekoppeld met uw exemplaar van de NPS-extensie in uw Power shell-sessie. Zoek uw certificaat door uw client certificaat te exporteren als een met *Base-64 gecodeerd X. 509-bestand (. CER)* zonder de persoonlijke sleutel, en vergelijk dit met de lijst van Power shell.

Met de volgende opdracht maakt u een bestand met de naam *npscertificate* in de hoofdmap van het station *C:* in format *. CER*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Nadat u deze opdracht hebt uitgevoerd, gaat u naar de hoofdmap van station *C:* , zoekt u het bestand en dubbelklikt u erop. Ga naar details en schuif omlaag naar vinger afdruk. Vergelijk de vinger afdruk van het certificaat dat op de server is geïnstalleerd. De certificaat vingerafdrukken moeten overeenkomen.

De tijds tempels *geldig van* en *geldig tot* en met een lees bare vorm, kunnen worden gebruikt voor het filteren van duidelijke, niet-passende, als de opdracht meer dan één certificaat retourneert.

### <a name="why-cannot-i-sign-in"></a>Waarom kan ik me niet aanmelden?

Controleer of uw wacht woord niet is verlopen. De NPS-extensie biedt geen ondersteuning voor het wijzigen van wacht woorden als onderdeel van de aanmeld werk stroom. Neem contact op met de IT-afdeling van uw organisatie voor meer informatie.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Waarom mislukt mijn aanvragen met een ADAL-token fout?

Deze fout kan een van de volgende oorzaken hebben. Gebruik de volgende stappen om problemen op te lossen:

1. Start de NPS-server opnieuw op.
2. Controleer of het client certificaat is geïnstalleerd zoals verwacht.
3. Controleer of het certificaat is gekoppeld aan uw Tenant in azure AD.
4. Controleer of `https://login.microsoftonline.com/` toegankelijk is vanaf de server waarop de extensie wordt uitgevoerd.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Waarom mislukt verificatie met een fout in HTTP-logboeken met de melding dat de gebruiker niet is gevonden?

Controleer of AD Connect actief is en of de gebruiker aanwezig is in zowel de on-premises AD DS omgeving als in azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Waarom zie ik HTTP-verbindings fouten in Logboeken met al mijn authenticaties mislukt?

Controleer of https://adnotifications.windowsazure.com bereikbaar is vanaf de server waarop de NPS-extensie wordt uitgevoerd.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Waarom werkt verificatie niet, ondanks dat er een geldig certificaat aanwezig is?

Als uw vorige computer certificaat is verlopen en er een nieuw certificaat is gegenereerd, verwijdert u eventuele verlopen certificaten. Verlopen certificaten kunnen problemen veroorzaken met het starten van de NPS-extensie.

Als u wilt controleren of u een geldig certificaat hebt, controleert u het *certificaat archief van het lokale computer account* met behulp van MMC en zorgt u ervoor dat het certificaat de verval datum niet heeft door gegeven. Voer de stappen uit om [het Power shell-installatie script](#run-the-powershell-script)uit te voeren om een nieuw geldig certificaat te genereren.

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Waarom worden verwijderde aanvragen in de NPS-server logboeken weer geven?

Een VPN-server kan herhaalde aanvragen verzenden naar de NPS-server als de time-outwaarde te laag is. De NPS-server detecteert deze dubbele aanvragen en negeert deze. Dit gedrag is inherent aan het ontwerp en duidt niet op een probleem met de NPS-server of de Azure AD Multi-Factor Authentication NPS-extensie.

Zie voor meer informatie over het weer geven van verwijderde pakketten in de NPS-server Logboeken het [RADIUS-protocol gedrag en de NPS-extensie](#radius-protocol-behavior-and-the-nps-extension) aan het begin van dit artikel.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Beheer van de TLS/SSL-protocollen en coderingssuites

Het is raadzaam oudere en zwakkere coderings suites uit te scha kelen of te verwijderen, tenzij uw organisatie dit vereist. Informatie over het uitvoeren van deze taak vindt u in het artikel, het [beheren van SSL/TLS-protocollen en coderings suites voor AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Extra probleem oplossing

Aanvullende richt lijnen voor probleem oplossing en mogelijke oplossingen vindt u in het artikel, [los fout berichten op uit de NPS-extensie voor Azure AD multi-factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht en configuratie van Network Policy Server in Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Configureer alternatieve Id's voor aanmelding of stel een uitzonderingen lijst in voor IP-adressen die geen verificatie in twee stappen moeten uitvoeren in [Geavanceerde configuratie opties voor de NPS-extensie voor multi-factor Authentication](howto-mfa-nps-extension-advanced.md)

- Meer informatie over het integreren van [extern bureaublad-gateway](howto-mfa-nps-extension-rdg.md) [-en VPN-servers](howto-mfa-nps-extension-vpn.md) met BEhulp van de NPS-extensie

- [Fout berichten oplossen vanuit de NPS-extensie voor Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)