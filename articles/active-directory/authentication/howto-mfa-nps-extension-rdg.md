---
title: RDG integreren met Azure AD MFA NPS-extensie - Azure Active Directory
description: Integreer uw Extern bureaublad-gateway-infrastructuur met Azure AD MFA met behulp van de Network Policy Server-extensie voor Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f50792ec45570f7e90893a97150ea26b63ebf9c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829832"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integreer uw Extern bureaublad-gateway infrastructuur met behulp van de extensie Network Policy Server (NPS) en Azure AD

Dit artikel bevat details voor het integreren van uw Extern bureaublad-gateway-infrastructuur met Azure AD Multi-Factor Authentication (MFA) met behulp van de extensie Network Policy Server (NPS) voor Microsoft Azure.

Met de Network Policy Server-extensie (NPS) voor Azure kunnen klanten Remote Authentication Dial-In User Service-clientverificatie (RADIUS) beveiligen met behulp van [multi-factor authentication (MFA)](./concept-mfa-howitworks.md)in de cloud van Azure. Deze oplossing biedt verificatie in twee stappen voor het toevoegen van een tweede beveiligingslaag aan aanmeldingen en transacties van gebruikers.

Dit artikel bevat stapsgewijs instructies voor het integreren van de NPS-infrastructuur met Azure AD MFA met behulp van de NPS-extensie voor Azure. Hierdoor is beveiligde verificatie mogelijk voor gebruikers die zich proberen aan te melden bij een Extern bureaublad-gateway.

> [!NOTE]
> Dit artikel mag niet worden gebruikt met MFA-serverimplementaties en mag alleen worden gebruikt met Azure AD MFA-implementaties (cloudimplementaties).

De Network Policy and Access Services (NPS) biedt organisaties de mogelijkheid om het volgende te doen:

* Definieer centrale locaties voor het beheer en beheer van netwerkaanvragen door op te geven wie verbinding mag maken, op welke tijden van de dag verbindingen zijn toegestaan, de duur van verbindingen en het beveiligingsniveau dat clients moeten gebruiken om verbinding te maken, en meer. In plaats van deze beleidsregels op te geven op elke VPN- of Extern bureaublad-gatewayserver, kunnen deze beleidsregels eenmaal op een centrale locatie worden opgegeven. Het RADIUS-protocol biedt de gecentraliseerde verificatie, autorisatie en accounting (AAA).
* Stel nap-client health-beleid (Network Access Protection) op en dwing dit af om te bepalen of aan apparaten onbeperkte of beperkte toegang tot netwerkresources wordt verleend.
* Een manier bieden om verificatie en autorisatie af te dwingen voor toegang tot draadloze toegangspunten en Ethernet-switches die geschikt zijn voor 802.1x.

Normaal gesproken gebruiken organisaties NPS (RADIUS) om het beheer van VPN-beleid te vereenvoudigen en te centraliseren. Veel organisaties gebruiken echter ook NPS om het beheer van RD Desktop Connection Authorization Policies (RD CAPs) te vereenvoudigen en te centraliseren.

Organisaties kunnen NPS ook integreren met Azure AD MFA om de beveiliging te verbeteren en een hoog nalevingsniveau te bieden. Dit zorgt ervoor dat gebruikers verificatie in twee stappen tot stand kunnen laten komen om zich aan te melden bij Extern bureaublad-gateway. Gebruikers kunnen alleen toegang krijgen als ze hun combinatie van gebruikersnaam en wachtwoord opgeven, samen met de informatie die de gebruiker in hun beheer heeft. Deze informatie moet worden vertrouwd en niet eenvoudig worden gedupliceerd, zoals een mobiel telefoonnummer, een vast nummer, een toepassing op een mobiel apparaat, en meer. RDG ondersteunt momenteel telefoonoproepen en pushmeldingen van Microsoft Authenticator-app-methoden voor 2FA. Zie de sectie Bepalen welke verificatiemethoden uw gebruikers kunnen gebruiken voor meer informatie over [ondersteunde verificatiemethoden.](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)

Vóór de beschikbaarheid van de NPS-extensie voor Azure moesten klanten die verificatie in twee stappen willen implementeren voor geïntegreerde NPS- en Azure AD MFA-omgevingen, een afzonderlijke MFA-server configureren en onderhouden in de on-premises omgeving, zoals beschreven in Extern bureaublad-gateway en [Azure Multi-Factor Authentication-server met RADIUS.](howto-mfaserver-nps-rdg.md)

De beschikbaarheid van de NPS-extensie voor Azure biedt organisaties nu de keuze om een on-premises MFA-oplossing of een cloudgebaseerde MFA-oplossing te implementeren om RADIUS-clientverificatie te beveiligen.

## <a name="authentication-flow"></a>Verificatiestroom

Gebruikers kunnen alleen toegang krijgen tot netwerkresources via een Extern bureaublad-gateway als ze voldoen aan de voorwaarden die zijn opgegeven in één RD Connection Authorization Policy (RD CAP) en één RD Resource Authorization Policy (RD RAP). Extern bureaublad-CAP's geven aan wie is gemachtigd om verbinding te maken met extern bureaublad-gateways. RD RAPs specificeren de netwerkbronnen, zoals externe bureaubladen of externe apps, die de gebruiker verbinding mag maken via de RD-gateway.

Een RD-gateway kan worden geconfigureerd voor het gebruik van een centraal beleidsopslag voor extern bureaublad-CAP's. RD RAPs kan geen centraal beleid gebruiken, omdat ze worden verwerkt op de RD-gateway. Een voorbeeld van een RD-gateway geconfigureerd voor het gebruik van een centraal beleidsopslag voor extern bureaublad-CSP's is een RADIUS-client naar een andere NPS-server die fungeert als het centrale beleidsopslag.

Wanneer de NPS-extensie voor Azure is geïntegreerd met de NPS en Extern bureaublad-gateway, is de geslaagde verificatiestroom als volgt:

1. De Extern bureaublad-gateway server ontvangt een verificatieaanvraag van een extern bureaublad-gebruiker om verbinding te maken met een resource, zoals een Extern bureaublad sessie. De Extern bureaublad-gateway-server die als RADIUS-client optreedt, converteert de aanvraag naar een RADIUS-Access-Request-bericht en verzendt het bericht naar de RADIUS-server (NPS) waarop de NPS-extensie is geïnstalleerd.
1. De combinatie van gebruikersnaam en wachtwoord wordt geverifieerd in Active Directory en de gebruiker wordt geverifieerd.
1. Als aan alle voorwaarden zoals opgegeven in de NPS-verbindingsaanvraag en het netwerkbeleid is voldaan (bijvoorbeeld de tijd van de dag of beperkingen voor groepslidmaatschap), activeert de NPS-extensie een aanvraag voor secundaire verificatie met Azure AD MFA.
1. Azure AD MFA communiceert met Azure AD, haalt de details van de gebruiker op en voert de secundaire verificatie uit met behulp van ondersteunde methoden.
1. Als de MFA-uitdaging is geslaagd, communiceert Azure AD MFA het resultaat met de NPS-extensie.
1. De NPS-server, waar de extensie is geïnstalleerd, verzendt een RADIUS-Access-Accept voor de RD CAP naar de Extern bureaublad-gateway server.
1. De gebruiker krijgt toegang tot de aangevraagde netwerkresource via de RD-gateway.

## <a name="prerequisites"></a>Vereisten

In deze sectie worden de vereisten bewaarde die nodig zijn voordat u Azure AD MFA integreert met de Extern bureaublad-gateway. Voordat u begint, moet u aan de volgende vereisten zijn gehouden.  

* Extern bureaublad-services (RDS)-infrastructuur
* Azure AD MFA-licentie
* Windows Server-software
* NPS-rol (Network Policy and Access Services)
* Azure Active Directory gesynchroniseerd met on-premises Active Directory
* Azure Active Directory GUID-id

### <a name="remote-desktop-services-rds-infrastructure"></a>Extern bureaublad-services (RDS)-infrastructuur

U moet een werkende Extern bureaublad-services (RDS)-infrastructuur hebben. Als u dat niet doet, kunt u deze infrastructuur snel maken in Azure met behulp van de volgende quickstart-sjabloon: [Implementatie van Extern bureaublad sessieverzameling maken.](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)

Als u voor testdoeleinden handmatig snel een on-premises RDS-infrastructuur wilt maken, volgt u de stappen om er een te implementeren.
**Meer informatie:** [RDS implementeren met Azure-quickstart](/windows-server/remote/remote-desktop-services/rds-in-azure) en [basisimplementatie van RDS-infrastructuur.](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-licentie

Vereist is een licentie voor Azure AD MFA, die beschikbaar is via Azure AD Premium of andere bundels die deze omvatten. Licenties op basis van verbruik voor Azure AD MFA, zoals licenties per gebruiker of per verificatie, zijn niet compatibel met de NPS-extensie. Zie Azure [AD Multi-Factor Authentication](concept-mfa-licensing.md)verkrijgen voor meer informatie. Voor testdoeleinden kunt u een proefabonnement gebruiken.

### <a name="windows-server-software"></a>Windows Server-software

De NPS-extensie vereist Windows Server 2008 R2 SP1 of hoger met de functieservice NPS geïnstalleerd. Alle stappen in deze sectie zijn uitgevoerd met Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>NPS-rol (Network Policy and Access Services)

De functieservice NPS biedt de RADIUS-server en clientfunctionaliteit, evenals de statusservice voor het netwerktoegangsbeleid. Deze rol moet worden geïnstalleerd op ten minste twee computers in uw infrastructuur: de Extern bureaublad-gateway en een andere lidserver of domeincontroller. Standaard is de rol al aanwezig op de computer die is geconfigureerd als de Extern bureaublad-gateway.  U moet ook de NPS-functie installeren op ten minste op een andere computer, zoals een domeincontroller of lidserver.

Zie install a NAP Health Policy Server (Een NAP Health Policy Server installeren) voor meer informatie over het installeren van de [NPS-functieservice](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10))Windows Server 2012 of ouder. Zie aanbevolen procedures voor NPS voor een beschrijving van aanbevolen procedures [voor NPS,](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10))met inbegrip van de aanbeveling voor het installeren van NPS op een domeincontroller.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory gesynchroniseerd met on-premises Active Directory

Als u de NPS-extensie wilt gebruiken, moeten on-premises gebruikers worden gesynchroniseerd met Azure AD en zijn ingeschakeld voor MFA. In deze sectie wordt ervan uitgenomen dat on-premises gebruikers zijn gesynchroniseerd met Azure AD met behulp van AD Connect. Zie Integrate [your on-premises directories with Azure Active Directory (Uw on-premises Azure Active Directory)](../hybrid/whatis-hybrid-identity.md)voor meer informatie over Azure AD Connect.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-id

Als u de NPS-extensie wilt installeren, moet u de GUID van Azure AD kennen. Instructies voor het vinden van de GUID van Azure AD vindt u hieronder.

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication configureren

Deze sectie bevat instructies voor het integreren van Azure AD MFA met de Extern bureaublad-gateway. Als beheerder moet u de Azure AD MFA-service configureren voordat gebruikers hun multi-factor devices of toepassingen zelf kunnen registreren.

Volg de stappen in [Aan de slag met Azure AD Multi-Factor Authentication in de cloud](howto-mfa-getstarted.md) om MFA in te schakelen voor uw Azure AD-gebruikers.

### <a name="configure-accounts-for-two-step-verification"></a>Accounts configureren voor verificatie in twee stappen

Zodra een account is ingeschakeld voor MFA, kunt u zich niet aanmelden bij resources die onder het MFA-beleid vallen, totdat u een vertrouwd apparaat hebt geconfigureerd voor gebruik voor de tweede verificatiefactor en u zich hebt geverifieerd met verificatie in twee stappen.

Volg de stappen in [Wat betekent Azure AD Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md) voor mij? om uw apparaten te begrijpen en correct te configureren voor MFA met uw gebruikersaccount.

> [!IMPORTANT]
> Het aanmeldingsgedrag voor Extern bureaublad-gateway biedt niet de optie om een verificatiecode in te voeren met Azure AD Multi-Factor Authentication. Een gebruikersaccount moet worden geconfigureerd voor telefoonverificatie of de Microsoft Authenticator App met pushmeldingen.
>
> Als geen van beide telefoonverificaties of de Microsoft Authenticator-app met pushmeldingen is geconfigureerd voor een gebruiker, kan de gebruiker de Azure AD Multi-Factor Authentication-uitdaging niet voltooien en zich aanmelden bij Extern bureaublad-gateway.
>
> De sms-tekstmethode werkt niet met Extern bureaublad-gateway omdat deze geen optie biedt om een verificatiecode in te voeren.

## <a name="install-and-configure-nps-extension"></a>NPS-extensie installeren en configureren

Deze sectie bevat instructies voor het configureren van de RDS-infrastructuur voor het gebruik van Azure AD MFA voor clientverificatie met de Extern bureaublad-gateway.

### <a name="acquire-azure-active-directory-tenant-id"></a>Een tenant Azure Active Directory-id verkrijgen

Als onderdeel van de configuratie van de NPS-extensie moet u beheerdersreferenties en de Azure AD-id voor uw Azure AD-tenant leveren. Voltooi de volgende stappen om de tenant-id op te halen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder van de Azure-tenant.
1. Selecteer in Azure Portal menu de **optie Azure Active Directory** of zoek en selecteer **Azure Active Directory** op een pagina.
1. Op de **pagina** Overzicht wordt de *tenantgegevens* weergegeven. Selecteer naast de *Tenant-id* het pictogram **Kopiëren,** zoals wordt weergegeven in de volgende voorbeeldschermafbeelding:

   ![De tenant-id van de Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>De NPS-extensie installeren

Installeer de NPS-extensie op een server met de nps-functie (Network Policy and Access Services). Dit fungeert als de RADIUS-server voor uw ontwerp.

> [!IMPORTANT]
> Installeer de NPS-extensie niet op uw Extern bureaublad-gateway (RDG)-server. De RDG-server maakt geen gebruik van het RADIUS-protocol met de client, zodat de extensie de MFA niet kan interpreteren en uitvoeren.
>
> Wanneer de RDG-server en NPS-server met NPS-extensie verschillende servers zijn, gebruikt RDG NPS intern om te communiceren met andere NPS-servers en radius gebruikt als het protocol correct te communiceren.

1. Download de [NPS-extensie](https://aka.ms/npsmfa).
1. Kopieer het uitvoerbare installatiebestand (NpsExtnForAzureMfaInstaller.exe) naar de NPS-server.
1. Dubbelklik op de NPS-server op **NpsExtnForAzureMfaInstaller.exe**. Klik op Uitvoeren als u daarom **wordt gevraagd.**
1. Controleer in het dialoogvenster NPS-extensie voor Azure AD MFA-installatie de licentievoorwaarden voor software, ga akkoord met de licentievoorwaarden en klik op **Installeren.** 
1. Klik in het dialoogvenster NPS-extensie voor Het instellen van Azure AD MFA op **Sluiten.**

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Certificaten configureren voor gebruik met de NPS-extensie met behulp van een PowerShell-script

Vervolgens moet u certificaten configureren voor gebruik door de NPS-extensie om veilige communicatie en zekerheid te garanderen. De NPS-onderdelen bevatten een Windows PowerShell script dat een zelfonder ondertekend certificaat configureert voor gebruik met NPS.

Met het script worden de volgende acties uitgevoerd:

* Hiermee maakt u een zelf-ondertekend certificaat
* Koppelt de openbare sleutel van het certificaat aan de service-principal in Azure AD
* Slaat het certificaat op in het lokale computeropslag
* Verleent toegang tot de persoonlijke sleutel van het certificaat aan de netwerkgebruiker
* Start de Network Policy Server service opnieuw

Als u uw eigen certificaten wilt gebruiken, moet u de openbare sleutel van uw certificaat koppelen aan de service-principal in Azure AD, en meer.

Als u het script wilt gebruiken, geeft u de extensie op met uw Azure AD-beheerdersreferenties en de Azure AD-tenant-id die u eerder hebt gekopieerd. Voer het script uit op elke NPS-server waarop u de NPS-extensie hebt geïnstalleerd. Ga daarna als volgt te werk:

1. Open een beheerprompt Windows PowerShell te vragen.
1. Typ bij de PowerShell-prompt `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` en druk op **ENTER.**
1. Typ `.\AzureMfaNpsExtnConfigSetup.ps1` en druk op **ENTER.** Het script controleert of de PowerShell Azure Active Directory module is geïnstalleerd. Als dit niet is geïnstalleerd, wordt de module door het script voor u geïnstalleerd.

   ![Een AzureMfaNpsExtnConfigSetup.ps1 in Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Nadat het script de installatie van de PowerShell-module heeft geverifieerd, wordt het dialoogvenster Azure Active Directory PowerShell-module weergegeven. Voer in het dialoogvenster uw Azure AD-beheerdersreferenties en -wachtwoord in en klik **op Aanmelden.**

   ![Authenticeren bij Azure AD in PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Wanneer u hier om wordt gevraagd, plakt u de *tenant-id* die u eerder naar het klembord hebt gekopieerd en drukt u op **ENTER.**

   ![De tenant-id invoeren in PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Het script maakt een zelf-ondertekend certificaat en voert andere configuratiewijzigingen uit. De uitvoer moet er zijn zoals in de onderstaande afbeelding.

   ![Uitvoer van PowerShell met zelf-ondertekend certificaat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>NPS-onderdelen configureren op Extern bureaublad-gateway

In deze sectie configureert u het beleid Extern bureaublad-gateway verbindingsautorisatiebeleid en andere RADIUS-instellingen.

De verificatiestroom vereist dat RADIUS-berichten worden uitgewisseld tussen de Extern bureaublad-gateway en de NPS-server waarop de NPS-extensie is geïnstalleerd. Dit betekent dat u radius-clientinstellingen moet configureren op zowel de Extern bureaublad-gateway als de NPS-server waarop de NPS-extensie is geïnstalleerd.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Autorisatiebeleid Extern bureaublad-gateway voor verbindingen configureren voor het gebruik van een centraal winkel

Extern bureaublad beleid voor verbindingsautorisatie (RD CAPs) de vereisten opgeven voor het maken van verbinding met een Extern bureaublad-gateway server. Extern bureaublad-CAP's kunnen lokaal worden opgeslagen (standaard) of ze kunnen worden opgeslagen in een centrale RD CAP-opslag met NPS. Als u de integratie van Azure AD MFA met RDS wilt configureren, moet u het gebruik van een centraal winkel opgeven.

1. Open op RD-gateway server **Serverbeheer**.
1. Klik in het menu op **Extra,** wijs **Extern bureaublad-services** aan en klik vervolgens op **Extern bureaublad-gatewaybeheer**.
1. Klik in RD-gatewaybeheer met de rechtermuisknop **\[ op Servernaam \] (lokaal)** en klik op **Eigenschappen.**
1. Selecteer in het dialoogvenster Eigenschappen het **tabblad RD CAP Store.**
1. Selecteer op RD CAP tabblad Store de optie **Centrale server met NPS.** 
1. Typ in het veld Een naam of IP-adres invoeren voor de server waarop **NPS** wordt uitgevoerd het IP-adres of de servernaam van de server waarop u de NPS-extensie hebt geïnstalleerd.

   ![Voer de naam of het IP-adres van uw NPS-server in](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klik op **Add**.
1. Voer in **het dialoogvenster** Gedeeld geheim een gedeeld geheim in en klik vervolgens op **OK.** Zorg ervoor dat u dit gedeelde geheim op een veilige manier opgeslagen.

   >[!NOTE]
   >Gedeeld geheim wordt gebruikt om een vertrouwensrelatie tot stand te brengen tussen de RADIUS-servers en -clients. Maak een lang en complex geheim.
   >

   ![Een gedeeld geheim maken om een vertrouwensrelatie tot stand te stellen](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Klik op **OK** om het dialoogvenster te sluiten.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Radius-time-outwaarde configureren op Extern bureaublad-gateway NPS

Om ervoor te zorgen dat er tijd is om de referenties van gebruikers te valideren, verificatie in twee stappen uit te voeren, antwoorden te ontvangen en te reageren op RADIUS-berichten, moet u de time-outwaarde voor RADIUS aanpassen.

1. Open RD-gateway de Serverbeheer. Klik in het menu op **Extra** en klik vervolgens op **Network Policy Server.**
1. Vouw in **de NPS-console (lokaal)** **RADIUS-clients en -servers** uit en selecteer **Externe RADIUS-server.**

   ![Network Policy Server-beheerconsole met externe RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Dubbelklik in het detailvenster op **TS GATEWAY SERVER GROUP.**

   >[!NOTE]
   >Deze RADIUS-servergroep is gemaakt toen u de centrale server voor NPS-beleid configureerde. De RD-gateway radius-berichten doorsturen naar deze server of groep servers, indien meer dan één in de groep.
   >

1. Selecteer in het dialoogvenster Eigenschappen van **TS-GATEWAYSERVERGROEP** het IP-adres of de naam van de NPS-server die u hebt geconfigureerd voor het opslaan van RD-CAP's en klik vervolgens op **Bewerken.**

   ![Selecteer het IP-adres of de naam van de NPS-server die u eerder hebt geconfigureerd](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Selecteer in **het dialoogvenster RADIUS-server** bewerken het **tabblad Taakverdeling.**
1. Wijzig op **het tabblad** Taakverdeling in het veld Aantal seconden zonder reactie voordat de aanvraag wordt beschouwd als een uitgevallen aanvraag, de standaardwaarde van 3 in een waarde tussen 30 en 60 seconden. 
1. Wijzig in het veld Aantal seconden tussen aanvragen wanneer **de server** wordt geïdentificeerd als niet-beschikbaar de standaardwaarde van 30 seconden in een waarde die gelijk is aan of groter is dan de waarde die u in de vorige stap hebt opgegeven.

   ![Time-outinstellingen voor Radius Server bewerken op het tabblad Taakverdeling](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klik **twee keer** op OK om de dialoogvensters te sluiten.

### <a name="verify-connection-request-policies"></a>Beleid voor verbindingsaanvraag controleren

Wanneer u de RD-gateway configureert voor het gebruik van een centraal beleidsopslag voor verbindingsautorisatiebeleid, is de RD-gateway standaard geconfigureerd voor het doorsturen van CAP-aanvragen naar de NPS-server. De NPS-server met de Azure AD MFA-extensie geïnstalleerd, verwerkt de RADIUS-toegangsaanvraag. De volgende stappen laten zien hoe u het standaardbeleid voor verbindingsverzoeken kunt controleren.  

1. Vouw op RD-gateway nps-console (lokaal) beleid uit en selecteer **Verbindingsaanvraagbeleid.**
1. Dubbelklik op **AUTORISATIEBELEID VOOR TS-GATEWAY.**
1. Klik in **het dialoogvenster Autorisatiebeleid voor TS GATEWAY** op het **tabblad** Instellingen.
1. Klik **op het** tabblad Instellingen onder Verbindingsaanvraag doorsturen op **Verificatie.** RADIUS-client is geconfigureerd voor het doorsturen van aanvragen voor verificatie.

   ![Verificatie-instellingen configureren die de servergroep opgeven](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klik op **Annuleren**.

>[!NOTE]
> Zie het artikel Documentatie voor verbindingsaanvraagbeleid configureren voor meer informatie over het maken van een beleid voor verbindingsaanvraag. [](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS configureren op de server waarop de NPS-extensie is geïnstalleerd

De NPS-server waarop de NPS-extensie is geïnstalleerd, moet RADIUS-berichten kunnen uitwisselen met de NPS-server op Extern bureaublad-gateway. Als u deze uitwisseling van berichten wilt inschakelen, moet u de NPS-onderdelen configureren op de server waarop de NPS-extensieservice is geïnstalleerd.

### <a name="register-server-in-active-directory"></a>Server registreren in Active Directory

Als u in dit scenario goed wilt werken, moet de NPS-server worden geregistreerd in Active Directory.

1. Open op de NPS-server **Serverbeheer**.
1. Klik Serverbeheer op **Extra** en klik vervolgens op **Network Policy Server**.
1. Klik in Network Policy Server-console met de rechtermuisknop **op NPS (lokaal)** en klik vervolgens op **Server registreren in Active Directory.**
1. Klik **twee keer op OK.**

   ![De NPS-server registreren in Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Laat de console geopend voor de volgende procedure.

### <a name="create-and-configure-radius-client"></a>RADIUS-client maken en configureren

De Extern bureaublad-gateway moet worden geconfigureerd als een RADIUS-client voor de NPS-server.

1. Klik op de NPS-server waarop de NPS-extensie is geïnstalleerd in de **NPS-console (lokaal)** met de rechtermuisknop op **RADIUS-clients** en klik op **Nieuw.**

   ![Een nieuwe RADIUS-client maken in de NPS-console](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Geef in **het dialoogvenster Nieuwe RADIUS-client** een gebruiksvriendelijke naam op, zoals _Gateway_ en het IP-adres of de DNS-naam van de Extern bureaublad-gateway server.
1. Voer in **de velden Gedeeld geheim** en Gedeeld geheim **bevestigen** hetzelfde geheim in dat u eerder hebt gebruikt.

   ![Een gebruiksvriendelijke naam en het IP- of DNS-adres configureren](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klik **op OK** om het dialoogvenster Nieuwe RADIUS-client te sluiten.

### <a name="configure-network-policy"></a>Netwerkbeleid configureren

Zoals u weet, is de NPS-server met de Azure AD MFA-extensie het centrale beleidsopslag voor het verbindingsautorisatiebeleid (CAP). Daarom moet u een CAP implementeren op de NPS-server om geldige verbindingsaanvragen te autoreren.  

1. Open op de NPS-server de NPS-console (lokaal), vouw Beleid **uit** en klik op **Netwerkbeleid.**
1. Klik met de rechtermuisknop **op Verbindingen met andere toegangsservers** en klik op Beleid **dupliceren.**

   ![De verbinding met het beleid voor andere toegangsservers dupliceren](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klik met de **rechtermuisknop op Kopiëren van verbindingen met andere toegangsservers** en klik op **Eigenschappen.**
1. Voer in het dialoogvenster Kopiëren van verbindingen met andere **toegangsservers** **in** Beleidsnaam een geschikte naam in, _zoals RDG_CAP_. Schakel **Beleid ingeschakeld in** en selecteer Toegang **verlenen.** Selecteer desgewenst in **Type netwerktoegangsserver** de **optie Extern bureaublad-gateway** of laat dit op **Niet-gespecificeerd staan.**

   ![Geef het beleid een naam, schakel het in en verleen toegang](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klik op **het tabblad Beperkingen** en selecteer Clients toestaan om verbinding te maken zonder te onderhandelen over een **verificatiemethode.**

   ![Verificatiemethoden wijzigen zodat clients verbinding kunnen maken](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Klik eventueel op het **tabblad** Voorwaarden en voeg voorwaarden toe die moeten worden voldaan om de verbinding te autor maken, bijvoorbeeld lidmaatschap van een specifieke Windows-groep.

   ![Optioneel verbindingsvoorwaarden opgeven](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klik op **OK**. Wanneer u wordt gevraagd om het bijbehorende Help-onderwerp weer te geven, klikt u op **Nee.**
1. Zorg ervoor dat uw nieuwe beleid bovenaan de lijst staat, dat het beleid is ingeschakeld en dat het toegang verleent.

   ![Uw beleid naar de bovenkant van de lijst verplaatsen](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Configuratie controleren

Als u de configuratie wilt controleren, moet u zich aanmelden bij de Extern bureaublad-gateway met een geschikte RDP-client. Zorg ervoor dat u een account gebruikt dat is toegestaan door uw beleid voor verbindingsautorisatie en is ingeschakeld voor Azure AD MFA.

Zoals u in de onderstaande afbeelding kunt zien, kunt u de Extern bureaublad **webtoegang gebruiken.**

![Testen in Extern bureaublad Web Access](./media/howto-mfa-nps-extension-rdg/image25.png)

Wanneer u uw referenties voor primaire verificatie hebt invoeren, wordt in het dialoogvenster Extern bureaublad Connect de status Externe verbinding initiëren weergegeven, zoals hieronder wordt weergegeven. 

Als u bent geverifieerd met de secundaire verificatiemethode die u eerder hebt geconfigureerd in Azure AD MFA, bent u verbonden met de resource. Als de secundaire verificatie echter niet lukt, wordt de toegang tot de resource geweigerd. 

![Verbinding met extern bureaublad externe verbinding tot stand brengen](./media/howto-mfa-nps-extension-rdg/image26.png)

In het onderstaande voorbeeld wordt de Authenticator-app op een Windows-telefoon gebruikt om de secundaire verificatie te bieden.

![Voorbeeld van Windows Phone Authenticator-app met verificatie](./media/howto-mfa-nps-extension-rdg/image27.png)

Zodra u bent geverifieerd met behulp van de secundaire verificatiemethode, wordt u op de gebruikelijke Extern bureaublad-gateway aangemeld. Omdat u echter een secundaire verificatiemethode moet gebruiken met behulp van een mobiele app op een vertrouwd apparaat, is het aanmeldingsproces veiliger dan anders.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Logboeken Logboeken geslaagde aanmeldingsgebeurtenissen weergeven

Als u de geslaagde aanmeldingsgebeurtenissen in de Windows Logboeken-logboeken wilt weergeven, kunt u de volgende Windows PowerShell-opdracht uitvoeren om een query uit te voeren op de Windows Terminal Services en Windows-beveiliging logboeken.

Gebruik de volgende PowerShell-opdrachten om een query uit te voeren voor geslaagde aanmeldingsgebeurtenissen in de operationele logboeken van de gateway _(Logboeken\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Met deze opdracht worden Windows-gebeurtenissen weergegeven die de gebruiker laten zien aan de vereisten voor het autorisatiebeleid voor resources (RD RAP) en toegang is verleend.

![Gebeurtenissen weergeven met Behulp van PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Met deze opdracht worden de gebeurtenissen weergegeven die worden weergegeven wanneer de gebruiker voldoet aan de vereisten voor verbindingsautorisatiebeleid.

![het autorisatiebeleid voor verbindingen weergeven met behulp van PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

U kunt dit logboek ook weergeven en filteren op gebeurtenis-ID's, 300 en 200. Gebruik de volgende opdracht om een query uit te voeren op geslaagde aanmeldingsgebeurtenissen in de Logboeken van beveiligingsgebeurtenissen:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Deze opdracht kan worden uitgevoerd op de centrale NPS of de RD-gateway Server.

![Voorbeeld van geslaagde aanmeldingsgebeurtenissen](./media/howto-mfa-nps-extension-rdg/image30.png)

U kunt ook het beveiligingslogboek of de aangepaste weergave Services voor netwerkbeleid en -toegang weergeven, zoals hieronder wordt weergegeven:

![Services voor netwerkbeleid en -toegang Logboeken](./media/howto-mfa-nps-extension-rdg/image31.png)

Op de server waarop u de NPS-extensie voor Azure AD MFA hebt geïnstalleerd, vindt u Logboeken toepassingslogboeken die specifiek zijn voor de extensie in Logboeken voor toepassingen en _services\Microsoft\AzureMfa._

![Logboeken AuthZ-toepassingslogboeken](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Gids voor probleemoplossing

Als de configuratie niet werkt zoals verwacht, moet u eerst controleren of de gebruiker is geconfigureerd voor het gebruik van Azure AD MFA. De gebruiker verbinding laten maken met [de Azure Portal.](https://portal.azure.com) Als gebruikers om secundaire verificatie wordt gevraagd en kunnen worden geverifieerd, kunt u een onjuiste configuratie van Azure AD MFA elimineren.

Als Azure AD MFA werkt voor de gebruiker(s), moet u de relevante gebeurtenislogboeken bekijken. Dit zijn onder andere de beveiligingsgebeurtenis, de operationele gateway en de Azure AD MFA-logboeken die in de vorige sectie worden besproken.

Hieronder ziet u een voorbeelduitvoer van het beveiligingslogboek met een mislukte aanmeldingsgebeurtenis (gebeurtenis-id 6273).

![Voorbeeld van een mislukte aanmeldingsgebeurtenis](./media/howto-mfa-nps-extension-rdg/image33.png)

Hieronder vindt u een gerelateerde gebeurtenis uit de AzureMFA-logboeken:

![Voorbeeld van Azure AD MFA-aanmeldgegevens Logboeken](./media/howto-mfa-nps-extension-rdg/image34.png)

Als u geavanceerde opties voor probleemoplossing wilt uitvoeren, raadpleegt u de logboekbestanden van de NPS-database-indeling waarin de NPS-service is geïnstalleerd. Deze logboekbestanden worden in _de map %SystemRoot%\System32\Logs_ gemaakt als tekstbestanden met door komma's scheidingstekens.

Zie Interpret [NPS Database Format Log Files (Logboekbestanden in NPS-databaseindeling](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10))interpreteren) voor een beschrijving van deze logboekbestanden. De vermeldingen in deze logboekbestanden kunnen lastig te interpreteren zijn zonder ze te importeren in een spreadsheet of database. U vindt online verschillende IAS-parsers om u te helpen bij het interpreteren van de logboekbestanden.

In de onderstaande afbeelding ziet u de uitvoer van een dergelijke downloadbare [sharewaretoepassing.](https://www.deepsoftware.com/iasviewer)

![Voorbeeld van IAS-parser voor Shareware-app](./media/howto-mfa-nps-extension-rdg/image35.png)

Ten slotte kunt u voor extra opties voor probleemoplossing een protocolanalyse gebruiken, zoals [Microsoft Message Analyzer.](/message-analyzer/microsoft-message-analyzer-operating-guide)

In de onderstaande afbeelding van Microsoft Message Analyzer ziet u netwerkverkeer dat is gefilterd op het RADIUS-protocol dat de gebruikersnaam **CONTOSO\AliceC bevat.**

![Microsoft Message Analyzer met gefilterd verkeer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Volgende stappen

[Azure AD Multi-Factor Authentication krijgen](concept-mfa-licensing.md)

[Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md)

[Uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
