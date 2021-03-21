---
title: De beveiliging van extern beheer verbeteren in Azure | Microsoft Docs
description: In dit artikel worden de stappen beschreven voor het verbeteren van de beveiliging van extern beheer bij het beheren van Microsoft Azure omgevingen, inclusief Cloud Services, virtuele machines en aangepaste toepassingen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2020
ms.author: terrylan
ms.openlocfilehash: e9eabc73c244526f0ea15b9c72b5377545f662b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94844860"
---
# <a name="security-management-in-azure"></a>Beveiligingsbeheer in Azure
Azure-abonnees kunnen hun cloudomgevingen beheren vanaf meerdere apparaten, waaronder beheerwerkstations, de pc's van ontwikkelaars en zelfs apparaten van bevoegde eindgebruikers met taakspecifieke rechten. In sommige gevallen worden beheerfuncties uitgevoerd via op het web gebaseerde consoles, zoals de [Azure Portal](https://azure.microsoft.com/features/azure-portal/). In andere gevallen zijn er mogelijk rechtstreekse verbindingen naar Azure vanaf on-premises systemen via virtuele particuliere netwerken (VPN), Terminal Services, protocollen van clienttoepassingen of de Azure Service Management API (SMAPI) (via een programma). Clienteindpunten kunnen bovendien zowel in een domein zijn samengevoegd als op zichzelf staand en niet-beheerd zijn, zoals tablets en smartphones.

Hoewel meerdere mogelijkheden voor toegang en beheer uitgebreide opties bieden, kan deze verscheidenheid voor een cloudimplementatie aanzienlijke risico's met zich meebrengen. Het beheren, volgen en controleren van beheeracties kan hierdoor worden bemoeilijkt. Deze verscheidenheid kan ook leiden tot beveiligingsrisico's, omdat er ongereglementeerde toegang plaatsvindt tot clienteindpunten die worden gebruikt voor het beheer van cloudservices. Het gebruik van algemene of persoonlijke werkstations voor het ontwikkelen en beheren van infrastructuur brengt onvoorspelbare bedreigingen met zich mee voor bijvoorbeeld surfen op internet (denk aan waterhole-aanvallen) of e-mail (zoals social engineering en phishing).

![Een diagram met de verschillende manieren waarop een bedreiging kan worden gekoppeld.](./media/management/typical-management-network-topology.png)

Het risico op aanvallen neemt in dit type omgeving toe, omdat het moeilijk is beveiligingsbeleid en -methoden te maken die op de juiste manier toegang tot Azure-interfaces (zoals SMAPI) beheren vanuit uiteenlopende eindpunten.

### <a name="remote-management-threats"></a>Bedreigingen bij extern beheer
Aanvallers proberen vaak bevoegde toegang te krijgen door accountreferenties te misbruiken (bijvoorbeeld door het wachtwoord te kraken via brute force, phishing of credential harvesting) of door gebruikers met een truc aan te zetten tot het uitvoeren van schadelijke code (bijvoorbeeld op schadelijke websites met drive-by downloads of in schadelijke e-mailbijlagen). In een extern beheerde cloudomgeving kunnen accountschendingen leiden tot een verhoogd risico omdat gebruikers altijd en overal toegang hebben.

Zelfs als de primaire beheerdersaccounts extra goed worden beschermd, kunnen alsnog de onderliggende gebruikersaccounts worden ingezet om zwakke punten in de beveiligingsstrategie te misbruiken. Een gebrek aan correcte beveiligingstraining kan leiden tot schendingen door het onbedoeld vrijgeven van accountgegevens.

Wanneer een gebruikerswerkstation ook voor beheertaken wordt gebruikt, kan er op veel verschillende punten inbreuk plaatsvinden. Dit kan bijvoorbeeld wanneer gebruikers op internet surfen, wanneer ze open-sourcebestanden of bestanden van derden gebruiken, of als er een schadelijk document met een Trojaans paard wordt geopend.

In het algemeen kunnen de meeste gerichte aanvallen die tot inbreuk op gegevens leiden, worden herleid tot browseraanvallen, invoegtoepassingen (zoals Flash, PDF, Java) of spear phishing (e-mail) op desktopapparaten. Als deze apparaten worden gebruikt voor ontwikkeling of voor het beheren van andere assets, hebben ze mogelijk machtigingen op beheerniveau of serviceniveau voor toegang met bewerkingsrechten tot liveservers of netwerkapparaten.

### <a name="operational-security-fundamentals"></a>Grondbeginselen van operationele beveiliging
Voor veiliger beheer en veiligere bewerkingen kunt u de kwetsbaarheid van de client minimaliseren door het aantal mogelijke toegangspunten te beperken. Dit doet u aan de hand van de beveiligingsprincipes 'scheiding van functies' en 'scheiding van omgevingen'.

Scheid gevoelige functies van elkaar om de kans te verkleinen dat een fout op één niveau leidt tot een inbreuk op een ander niveau. Voorbeelden:

* Beheertaken moeten niet worden gecombineerd met activiteiten die mogelijk leiden tot een inbreuk (bijvoorbeeld malware in een e-mailbericht van een beheerder die vervolgens een infrastructuurserver infecteert).
* Een werkstation dat wordt gebruikt voor zeer gevoelige bewerkingen, mag niet ook worden gebruikt voor bewerkingen met een hoog risico, zoals surfen op internet.

Verminder de kwetsbaarheid van het systeem door onnodige software te verwijderen. Voorbeeld:

* Als het voornaamste doel van het apparaat het beheren van cloudservices is, vereist een standaardwerkstation voor beheer, ondersteuning of ontwikkeling geen installatie van een e-mailclient of van andere productiviteitstoepassingen.

Clientsystemen die beheerderstoegang hebben tot infrastructuuronderdelen, moeten worden onderworpen aan het strengst mogelijke beleid, zodat beveiligingsrisico's worden beperkt. Voorbeelden:

* Beveiligingsbeleid kan bestaan uit instellingen voor groepsbeleid die geen open internettoegang toestaan voor het apparaat en een beperkende firewallconfiguratie gebruiken.
* Gebruik VPN-verbindingen met internetprotocolbeveiliging (IPsec) als u directe toegang nodig hebt.
* Configureer afzonderlijke Active Directory-domeinen voor beheer en voor ontwikkeling.
* Isoleer en filter het netwerkverkeer van werkstations.
* Gebruik antimalwaresoftware.
* Implementeer Multi-Factor Authentication om het risico van gestolen referenties te verminderen.

Door toegangsbronnen te consolideren en niet-beheerde eindpunten te verwijderen, vereenvoudigt u ook uw beheertaken.

### <a name="providing-security-for-azure-remote-management"></a>Beveiliging voor extern beheer van Azure
Azure biedt beveiligingsmethoden om beheerders te helpen met het beheer van cloudservices en virtuele machines in Azure. Tot deze methoden behoren onder meer:

* Verificatie en [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Bewaking, logboekregistratie en controle.
* Certificaten en gecodeerde communicatie.
* Een webbeheerportal.
* Filteren van netwerkpakketten.

Met de beveiligingsconfiguratie aan clientzijde en de datacenterimplementatie van een beheergateway is het mogelijk beheerderstoegang tot cloudtoepassingen en -gegevens te beperken en te bewaken.

> [!NOTE]
> Bepaalde aanbevelingen in dit artikel kunnen leiden tot een toegenomen gebruik van gegevens, het netwerk en computerresources, en verhogen mogelijk de kosten van uw licentie of abonnement.
>
>

## <a name="hardened-workstation-for-management"></a>Beperkt werkstation voor beheer
Beperking van een werkstation heeft als doel om alle functies te elimineren, behalve de functies die echt onmisbaar zijn voor de werking van het apparaat. Zo wordt het risico op aanvallen zo laag mogelijk gemaakt. Als u een systeem beperkt, minimaliseert u het aantal geïnstalleerde services en toepassingen, beperkt u het uitvoeren van toepassingen, staat u netwerktoegang alleen toe als dit noodzakelijk is en houdt u het systeem altijd up-to-date. Daarnaast kunt u door het gebruik van een beperkt werkstation voor beheer de beheertools en -activiteiten scheiden van taken voor eindgebruikers.

Binnen een on-premises bedrijfsomgeving kunt u de kwetsbaarheid van de fysieke infrastructuur beperken via speciale beheernetwerken, serverruimten met kaarttoegang, en werkstations die worden uitgevoerd in beveiligde gebieden van het netwerk. In een cloud- of hybride IT-model is het moeilijker om beheerservices te beveiligen vanwege het gebrek aan fysieke toegang tot de IT-bronnen. Implementatie van veiligheidsoplossingen vereist zorgvuldige softwareconfiguratie, op beveiliging gerichte processen en uitgebreid beleid.

Als u de omgeving voor extern beheer en ontwikkeling standaardiseert door voor cloudbeheer (en toepassingsontwikkeling) vergrendelde werkstations te gebruiken met zo weinig mogelijk software en minimale bevoegdheden, kan het risico op beveiligingsincidenten worden verminderd. Een beperkte werkstationsconfiguratie kan helpen voorkomen dat er inbreuk plaatsvindt op accounts die worden gebruikt voor het beheer van kritieke cloudbronnen. Dit wordt bereikt door veelgebruikte routes voor schadelijke software en aanvallen af te sluiten. U kunt [Windows AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759117(v=ws.11)) en Hyper-V-technologie gebruiken om het gedrag van clientsystemen te beheren en te isoleren, en bedreigingen te verhelpen, waaronder e-mail en surfen op internet.

Op een beperkt werkstation voert de beheerder een standaardgebruikersaccount uit (dat uitvoering op beheerdersniveau blokkeert). De benodigde toepassingen worden beheerd via een lijst Toestaan. De basiselementen van een beperkt werkstation zijn:

* Actief scannen en patchen. Implementeer antimalwaresoftware, voer reguliere scans op zwakke plekken uit en werk tijdig alle werkstations bij naar de meest recente beveiligingsupdate.
* Beperkte functionaliteit. Verwijder alle toepassingen die niet nodig zijn en schakel onnodige (opstart)services uit.
* Beperkte netwerktoegang. Gebruik Windows Firewall-regels om alleen geldige IP-adressen, poorten en URL's met betrekking tot het beheer van Azure toe te staan. Zorg er ook voor dat inkomende externe verbindingen met het werkstation worden geblokkeerd.
* Beperkte uitvoering van software. Sta alleen een aantal vooraf gedefinieerde uitvoerbare bestanden toe die nodig zijn voor uitvoering van het beheer (de standaardinstelling is 'Niet toestaan'). Standaard moeten gebruikers geen toestemming hebben voor het uitvoeren van programma's, tenzij expliciet gedefinieerd in de acceptatielijst.
* Minimale bevoegdheden. Gebruikers van beheerwerkstations mogen geen beheerdersbevoegdheden hebben op de lokale computer. Op deze manier kunnen zij niet per ongeluk of met opzetwijzigen aanbrengen in de systeemconfiguratie of de systeembestanden.

U kunt dit afdwingen via [Groepsbeleidsobjecten](../../active-directory-domain-services/manage-group-policy.md) (GPO's) in Active Directory Domain Services (AD DS) en door uw (lokale) beheerdomein toe te passen op alle beheeraccounts.

### <a name="managing-services-applications-and-data"></a>Services, toepassingen en gegevens beheren
Azure-cloudservices worden geconfigureerd via de Azure Portal of SMAPI, hetzij met behulp van de Windows PowerShell-opdrachtregelinterface, hetzij met een op maat gemaakte toepassing die gebruikmaakt van deze RESTful-interfaces. Services die gebruikmaken van deze methoden, zijn onder meer Azure Active Directory (Azure AD), Azure Storage, Azure Websites en Azure Virtual Network.

Toepassingen die zijn geïmplementeerd op een virtuele machine, bieden hun eigen clienthulpprogramma's en -interfaces, zoals de Microsoft Management Console (MMC), een enterprisebeheerconsole (zoals Microsoft System Center of Windows Intune) of een andere beheertoepassing, bijvoorbeeld Microsoft SQL Server Management Studio. Deze hulpprogramma's bevinden zich meestal in een bedrijfsomgeving of clientnetwerk. Ze zijn mogelijk afhankelijk van specifieke netwerkprotocollen, zoals Remote Desktop Protocol (RDP), die directe, stateful verbindingen vereisen. Sommige hulpprogramma's hebben wellicht een webinterface die niet openbaar mag worden gepubliceerd of toegankelijk mag zijn via internet.

U kunt de toegang tot de infrastructuur en het beheer van platformservices in Azure beperken met behulp van [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md), [x.509-beheercertificaten](/archive/blogs/azuresecurity/certificate-management-in-azure-dos-and-donts) en firewallregels. De Azure Portal en SMAPI vereisen Transport Layer Security (TLS). Services en toepassingen die u in Azure implementeert, vereisen echter dat u passende beveiligingsmaatregelen neemt op basis van uw toepassing. Deze methoden kunnen vaak eenvoudiger worden ingeschakeld met behulp van een gestandaardiseerde configuratie voor beperkte werkstations.

### <a name="management-gateway"></a>Beheergateway
Als u alle beheerderstoegang wilt centraliseren en controle en logboekregistratie wilt vereenvoudigen, kunt u in uw on-premises netwerk een speciale [Extern bureaublad-gateway](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560672(v=ws.10))-server implementeren, die is verbonden met uw Azure-omgeving.

Een Extern bureaublad-gateway is een op beleid gebaseerde RDP-proxyservice die beveiligingsvereisten afdwingt. Door een Extern bureaublad-gateway samen met netwerktoegangsbeveiliging (NAP) voor Windows-servers te implementeren, zorgt u ervoor dat alleen clients die voldoen aan de specifieke criteria voor beveiligingsstatus, tot stand gebracht met groepsbeleidsobjecten (GPO's) van Active Directory Domain Services (AD DS), verbinding kunnen maken. Daarnaast:

* Richt een [Azure-beheercertificaat](/previous-versions/azure/gg551722(v=azure.100)) in op de Extern bureaublad-gateway, zodat deze de enige toegestane host is voor toegang tot Azure Portal.
* Maak de Extern bureaublad-gateway lid van hetzelfde [beheerdomein](/previous-versions/windows/it-pro/windows-2000-server/bb727085(v=technet.10)) als de werkstations van de beheerders. Dit is nodig als u een IPsec-VPN tussen sites of een ExpressRoute gebruikt binnen een domein met een eenzijdige vertrouwensrelatie naar Azure AD, of als u referenties tussen uw on-premises exemplaar van AD DS en Azure AD federeert.
* Configureer [verificatiebeleid voor clientverbindingen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753324(v=ws.11)), zodat de Extern bureaublad-gateway controleert of de naam van het clientapparaat geldig is (lid is van een domein) en toegang heeft tot Azure Portal.
* Gebruik IPsec voor [Azure-VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) om beheerverkeer extra te beschermen tegen afluisteren en diefstal van tokens, of breng een geïsoleerde internetkoppeling tot stand via [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Schakel multi-factor Authentication (via [Azure AD multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)) of smart card-verificatie in voor beheerders die zich via RD-gateway aanmelden.
* Configureer in Azure [IP-adresbeperkingen](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) voor bronnen of [Netwerkbeveiligingsgroepen](../../virtual-network/network-security-groups-overview.md) om het aantal toegestane eindpunten voor beheer te minimaliseren.

## <a name="security-guidelines"></a>Richtlijnen voor beveiliging
In het algemeen sluit het beveiligen van werkstations van beheerders voor gebruik met de cloud aan bij de uitgangspunten die gelden voor elk ander on-premises werkstation, zoals een minimale build en beperkte machtigingen. Sommige unieke aspecten van cloudbeheer lijken meer op extern beheer of out-of-band-enterprise-beheer. Hierbij horen het gebruiken en controleren van referenties, verbeterde beveiliging van externe toegang, en bedreigingsdetectie en -respons.

### <a name="authentication"></a>Verificatie
U kunt Azure-aanmeldingsbeperkingen gebruiken om de bron-IP-adressen te beperken die toegang hebben tot beheerhulpprogramma's, en om toegangsaanvragen te controleren. Om Azure Management-clients (werk stations en/of toepassingen) te identificeren, kunt u zowel SMAPI (via door de klant ontwikkelde hulpprogram ma's zoals Windows Power shell-cmdlets) als de Azure Portal voor het installeren van beheer certificaten aan client zijde, naast TLS/SSL-certificaten, configureren. We raden u ook aan Multi-Factor Authentication verplicht te stellen voor beheerderstoegang.

Sommige toepassingen of services die u in Azure implementeert, hebben wellicht hun eigen verificatiemethoden voor toegang voor zowel eindgebruikers als beheerders, terwijl andere volledig gebruikmaken van de mogelijkheden die Azure AD biedt. Afhankelijk van het gegeven of u referenties federeert via Active Directory Federation Services (AD FS), adreslijstsynchronisatie gebruikt of gebruikersaccounts uitsluitend in de cloud beheert, helpt het gebruik van [Microsoft Identity Manager](/microsoft-identity-manager/) (onderdeel van Azure AD Premium) u bij het beheer van levenscycli van identiteiten tussen de bronnen.

### <a name="connectivity"></a>Connectiviteit
Er zijn diverse methoden die u kunt gebruiken om clientverbindingen met uw virtuele netwerken in Azure te beveiligen. Twee van deze methoden, [site-naar-site VPN](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) en [punt-naar-site VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) (P2S), maken het gebruik van IPsec volgens de industrienorm (S2S) of het [Secure Socket Tunneling Protocol](/previous-versions/technet-magazine/cc162322(v=msdn.10)) (SSTP) (P2S) mogelijk voor codering en tunneling. Wanneer Azure verbinding maakt met openbaar Azure-servicesbeheer, zoals Azure Portal, vereist Azure Hypertext Transfer Protocol Secure (HTTPS).

Een zelfstandig, beperkt werkstation dat geen verbinding met Azure maakt via een Extern bureaublad-gateway, moet de punt-naar-site VPN op basis van SSTP gebruiken voor het maken van de eerste verbinding met het virtuele netwerk in Azure en vervolgens via de VPN-tunnel een RDP-verbinding tot stand brengen met afzonderlijke virtuele machines.

### <a name="management-auditing-vs-policy-enforcement"></a>Beheercontrole versus beleid afdwingen
Er zijn in principe twee methoden voor het beveiligen van beheerprocessen: controle en het afdwingen van beleid. Als u beide methoden toepast, beschikt u over uitgebreide beheermogelijkheden, maar dit is wellicht niet in alle gevallen mogelijk. Elke benadering brengt haar eigen risico's, kosten en tijdsinvestering met zich mee voor het beheren van beveiliging, met name met betrekking tot het vertrouwen dat wordt geplaatst in gebruikers en de systeemarchitectuur.

Bewaking, logboekregistratie en controle bieden een basis voor het bijhouden en het begrijpen van beheeractiviteiten, maar vanwege de grote hoeveelheid gegevens die wordt gegenereerd, is het niet altijd haalbaar om alle acties nauwgezet te controleren. Het controleren van de effectiviteit van de beleidsregels voor beheer is echter een aanbevolen procedure.

Als u beleid afdwingt dat strikt toegangsbeheer omvat, implementeert u programmatische mechanismen waarmee wordt bepaald welke acties beheerders mogen uitvoeren. Bovendien zorgt dergelijk beleid er mede voor dat alle mogelijke beschermingsmaatregelen worden gebruikt. Het afdwingen wordt vastgelegd in een logboek. Hier wordt ook opgenomen wie wat heeft gedaan, op welk tijdstip en vanaf welk apparaat. Aan de hand van het logboek kunt u ook nagaan en vergelijken hoe beheerders omgaan met het beleid. Daarnaast biedt het logboek bewijs van de uitgevoerde activiteiten

## <a name="client-configuration"></a>Clientconfiguratie
Voor beperkte werkstations raden we drie primaire configuraties aan. De verschillen tussen deze configuraties hebben voornamelijk betrekking op de kosten, de gebruiksmogelijkheden en de toegankelijkheid. Het beveiligingsprofiel is echter voor alle opties vergelijkbaar. De volgende tabel geeft een korte analyse van de voordelen en de risico's van elk van de opties. (De term 'zakelijke pc' verwijst naar een standaardconfiguratie voor desktop-pc's die voor alle domeingebruikers wordt geïmplementeerd, ongeacht de rol.)

| Configuratie | Voordelen | Nadelen |
| --- | --- | --- |
| Zelfstandig, beperkt werkstation |Strak beheerd werkstation |Hogere kosten voor speciale desktop-pc’s |
| - | Minder risico op misbruik van zwakke plekken in toepassingen |Meer tijd/werk nodig voor beheer |
| - | Duidelijke scheiding van functies | - |
| Zakelijke pc als virtuele machine |Lagere kosten voor hardware | - |
| - | Scheiding van rollen en toepassingen | - |

Het is belangrijk dat het beperkte werkstation de host is en niet de gast. Er mag zich niets bevinden tussen het hostbesturingssysteem en de hardware. Op basis van het principe van de 'schone bron' (ook wel 'veilige oorsprong' genoemd) moet de host de meeste beperkingen hebben. Anders wordt het beperkte werkstation (gast) blootgesteld aan aanvallen op het systeem waarop het wordt gehost.

U kunt de beheerfuncties verder scheiden door voor alle beperkte werkstations gebruik te maken van speciale systeemkopieën met uitsluitend de hulpprogramma's en machtigingen die nodig zijn voor het beheer van geselecteerde Azure- en cloudtoepassingen. Voor de benodigde taken implementeert u dan specifieke lokale AD DS-groepsbeleidsobjecten.

Voor IT-omgevingen die geen on-premises infrastructuur hebben (groepsbeleidsobjecten hebben bijvoorbeeld geen toegang tot een lokaal AD DS-exemplaar omdat alle servers zich in de cloud bevinden), kan een service zoals [Microsoft Intune](/mem/intune/) de implementatie en het onderhoud van werkstationconfiguraties vereenvoudigen.

### <a name="stand-alone-hardened-workstation-for-management"></a>Zelfstandig, beperkt werkstation voor beheer
Met een zelfstandig, beperkt werkstation hebben beheerders een pc of laptop die ze voor beheertaken gebruiken en een andere, afzonderlijke pc of laptop voor andere taken. Op een werkstation dat is ingericht voor het beheren van Azure-services, hoeven geen andere toepassingen te worden geïnstalleerd. Daarnaast helpt het gebruik van werkstations die ondersteuning bieden voor een [Trusted Platform Module](/previous-versions/windows/it-pro/windows-vista/cc766159(v=ws.10)) (TPM) of soortgelijke cryptografische technologie op hardwareniveau, bij de apparaatverificatie en het voorkomen van bepaalde aanvallen. Via [BitLocker-stationsversleuteling](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) kan TPM bovendien ondersteuning bieden voor de beveiliging van het volledige volume van het systeemstation.

In het scenario voor zelfstandige beperkte werkstations (zie hieronder) is het lokale exemplaar van Windows Firewall (of een niet-Microsoft-clientfirewall) zo geconfigureerd dat binnenkomende verbindingen, zoals RDP, worden geblokkeerd. De beheerder kan zich aanmelden bij het beperkte werkstation en een RDP-sessie starten die verbinding maakt met Azure na het tot stand brengen van een VPN-verbinding met een virtueel netwerk in Azure. De beheerder kan zich echter niet aanmelden bij een zakelijke pc en een RDP-sessie gebruiken om verbinding te maken met het beperkte werkstation zelf.

![Een diagram met het zelfstandige, beperkte werk station.](./media/management/stand-alone-hardened-workstation-topology.png)

### <a name="corporate-pc-as-virtual-machine"></a>Zakelijke pc als virtuele machine
In gevallen waarin een afzonderlijk zelfstandig, beperkt werkstation te duur is of onhandig, kan het beperkte werkstation een virtuele machine hosten voor het uitvoeren van niet-beheertaken.

![Een diagram van het beveiligde werk station dat als host fungeert voor een virtuele machine om niet-beheerders taken uit te voeren.](./media/management/hardened-workstation-enabled-with-hyper-v.png)

Ter voorkoming van diverse beveiligingsrisico's die voortvloeien uit het gebruik van hetzelfde werkstation voor systeembeheer en voor andere dagelijkse taken, kunt u op het beperkte werkstation een virtuele machine met Windows Hyper-V implementeren. Deze virtuele machine kan vervolgens worden gebruikt als zakelijke pc. De omgeving van de zakelijke pc kan geïsoleerd blijven van de host, waardoor u de kwetsbaarheid voor aanvallen vermindert en de dagelijkse activiteiten van de gebruiker (zoals het verzenden van e-mail) niet meer op hetzelfde systeem worden uitgevoerd als gevoelige beheertaken.

De virtuele machine die als zakelijke pc wordt gebruikt, wordt uitgevoerd in een beveiligde ruimte en biedt de benodigde gebruikerstoepassingen. De host blijft een 'schone bron' en past strikt netwerkbeleid toe op het basisbesturingssysteem (bijvoorbeeld het blokkeren van RDP-toegang tot de virtuele machine).

## <a name="best-practices"></a>Aanbevolen procedures
Houd rekening met de volgende aanvullende richtlijnen bij het beheren van toepassingen en gegevens in Azure.

### <a name="dos-and-donts"></a>Wat u wel en wat u niet moet doen
Ga er niet van uit dat er na het 'dichttimmeren' van een werkstation niet meer aan andere algemene beveiligingsvereisten hoeft te worden voldaan. Vanwege de verhoogde toegangsniveaus waarover beheerdersaccounts over het algemeen beschikken, is het mogelijke risico juist hoger. In onderstaande tabel ziet u voorbeelden van risico's en alternatieve veilige handelswijzen.

| Niet doen | Wel doen |
| --- | --- |
| Geen e-mail referenties voor beheerders toegang of andere geheimen (bijvoorbeeld TLS/SSL-of beheer certificaten) |Waarborg de vertrouwelijkheid door gebruikersnamen en wachtwoorden voor accounts mondeling door te geven (maar spreek ze niet op iemands voicemail in), voer een externe installatie van client/server-certificaten uit (via een gecodeerde sessie), voer downloads uit vanaf een beveiligde netwerkshare of distribueer gegevens handmatig via verwisselbare media. |
| - | Beheer de levenscycli van uw beheercertificaat proactief. |
| Sla wachtwoorden niet op in toepassingsopslag (zoals een spreadsheet, een SharePoint-site of een bestandsshare) als ze niet zijn versleuteld of er geen hash-bewerking op is uitgevoerd. |Stel richtlijnen voor beveiligingsbeheer en beleid voor het beperken van het systeem op en pas deze toe op uw ontwikkelomgeving. |
| - | Gebruik [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751)-regels voor het opslaan van certificaten om correcte toegang tot Azure SSL-/TLS-sites te waarborgen. |
| Deel geen accounts en wachtwoorden tussen beheerders, en gebruik wachtwoorden niet voor meerdere gebruikersaccounts of services, vooral als deze betrekking hebben op sociale media of andere niet-beheeractiviteiten. |Maak voor het beheer van uw Azure-abonnement een exclusief Microsoft-account en gebruik dit account niet voor persoonlijke e-mail. |
| Verzend configuratiebestanden niet via e-mail. |Configuratiebestanden en -profielen moeten worden geïnstalleerd vanuit een vertrouwde bron (bijvoorbeeld een gecodeerd USB-flashstation), niet via een methode waarmee gemakkelijk inbreuk kan worden gepleegd, zoals e-mail. |
| Gebruik geen zwakke of eenvoudige wachtwoorden. |Dwing beleid voor sterke wachtwoorden, verloopcycli (changeon-first-use), consoletime-outs en automatische accountvergrendeling af. Gebruik een beheersysteem met clientwachtwoorden met Multi-Factor Authentication voor wachtwoordtoegang tot de kluis. |
| Stel beheerpoorten niet bloot aan internet. |Vergrendel Azure-poorten en -IP-adressen om de beheertoegang te beperken. Voor meer informatie raadpleegt u het technisch document [Azure Network Security](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) (Azure-netwerkbeveiliging). |
| - | Gebruik voor alle beheerverbindingen firewalls, VPN-verbindingen en NAP. |

## <a name="azure-operations"></a>Azure-bewerkingen
Bij Microsoft gebruiken de technici en ondersteuningsmedewerkers die toegang hebben tot de productiesystemen van Azure, [beperkte werkstation-pc's met virtuele machines](#stand-alone-hardened-workstation-for-management) voor toegang tot het interne bedrijfsnetwerk en toepassingen (zoals e-mail, intranet, enz.). Alle beheerwerkstations hebben TPM's, het hostopstartstation is versleuteld met BitLocker en de computers zijn lid van een speciale organisatie-eenheid (OE) in het primaire bedrijfsdomein van Microsoft.

Systeembeveiliging wordt afgedwongen via groepsbeleid en software wordt centraal bijgewerkt. Voor controle en analyse worden vanaf de beheerwerkstations gebeurtenislogboekgegevens (bijvoorbeeld voor beveiliging en AppLocker) verzameld en opgeslagen op een centrale locatie.

Bovendien worden er in het netwerk van Microsoft speciale jumpboxes gebruikt waarvoor tweeledige verificatie is vereist, om verbinding te maken met het Azure-productienetwerk.

## <a name="azure-security-checklist"></a>Controlelijst voor Azure-beveiliging
Door het aantal taken te minimaliseren dat beheerders op een beperkt werkstation kunnen uitvoeren, vermindert u de kwetsbaarheid van uw ontwikkel- en beheeromgeving. Pas de volgende technologieën toe om uw beperkte werkstation te beveiligen:

* Verbeterde beveiliging van Internet Explorer. Vanwege de uitgebreide interactie met externe servers vormt de browser Internet Explorer (net als elke andere browser) een belangrijk toegangspunt voor schadelijke code. Neem uw clientbeleid onder de loep en dwing het volgende af: gebruik van de beveiligde modus, uitschakelen van invoegtoepassingen, uitschakelen van bestandsdownloads en toepassing van [Microsoft SmartScreen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj618329(v=ws.11))-filteren. Zorg ervoor dat er beveiligingswaarschuwingen worden weergegeven. Profiteer van internetzones en maak een lijst met vertrouwde sites waarvoor u afdoende beveiliging hebt geconfigureerd. Blokkeer alle andere sites en code in de browser, zoals ActiveX en Java.
* Standaardgebruiker. Aanmelden als standaardgebruiker heeft diverse voordelen. Het belangrijkste voordeel is dat het stelen van beheerdersreferenties via schadelijke software moeilijker wordt. Bovendien heeft een standaardgebruikersaccount geen verhoogde bevoegdheden voor het basisbesturingssysteem, en zijn veel configuratie-opties en API's standaard vergrendeld.
* AppLocker. Gebruik [AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619725(v=ws.10)) om de programma's en scripts te beperken die gebruikers kunnen uitvoeren. U kunt AppLocker uitvoeren in de controlemodus of in de afdwingingsmodus. Standaard bevat AppLocker een regel voor toestaan die gebruikers met een beheertoken in staat stelt om alle code op de client uit te voeren. Deze regel geldt alleen voor tokens met verhoogde bevoegdheid en heeft als doel te voorkomen dat beheerders zichzelf buitensluiten. Zie ook Code-integriteit als onderdeel van de [kernbeveiliging](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd348705(v=ws.10)) van Windows Server.
* Programmacode ondertekenen. Ondertekening van de programmacode van alle hulpprogramma's en scripts die door beheerders worden gebruikt, biedt een goed te beheren methode voor het implementeren van beleid voor het vergrendelen van toepassingen. Hashes passen zich niet aan aan snelle wijzigingen in de code en bestandspaden bieden geen hoog beveiligingsniveau. AppLocker-regels moeten worden gecombineerd met een PowerShell-[uitvoeringsbeleid](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)) dat alleen [uitvoering](/powershell/module/microsoft.powershell.security/set-executionpolicy) van bepaalde ondertekende codes en scripts toestaat.
* Groepsbeleid. Maak een overkoepelend beheerbeleid dat wordt toegepast op alle domeinwerkstations die voor beheer worden gebruikt (en blokkeer de toegang vanaf alle overige werkstations), evenals op gebruikersaccounts die op die werkstations worden geverifieerd.
* Inrichting met verbeterde beveiliging. Bescherm de installatiekopie van uw beperkte basiswerkstation om het risico op inbreuk te reduceren. Gebruik beveiligingsmaatregelen zoals versleuteling en isolatie voor het opslaan van installatiekopieën, virtuele machines en scripts, en beperk de toegang (gebruik bijvoorbeeld een controleerbaar in-/uitcheckproces).
* Patchen. Waarborg een consistente build (of bewaar afzonderlijke installatiekopieën voor ontwikkeling, bewerkingen en andere beheertaken), scan regelmatig op wijzigingen en malware, houd de build up-to-date en activeer apparaten alleen als dat nodig is.
* Versleuteling. Zorg ervoor dat beheerwerkstations een TMP hebben voor een veiligere inzet van [Encrypting File System](/previous-versions/tn-archive/cc700811(v=technet.10)) (EFS) en BitLocker.
* Beheer. Gebruik AD DS-groepsbeleidsobjecten voor het beheren van de Windows-interfaces van alle beheerders, zoals het delen van bestanden. Voer voor beheerwerkstations controle-, bewakings- en logboekregistratieprocessen uit. Houd toegang en gebruik van alle beheerders en ontwikkelaars bij.

## <a name="summary"></a>Samenvatting
Met behulp van een beperkte werkstationconfiguratie voor het beheer van uw Azure-cloudservices, virtuele machines en toepassingen kunt u diverse risico's en bedreigingen voorkomen die het op afstand beheren van kritieke IT-infrastructuur met zich meebrengt. Zowel Azure als Windows biedt methoden die u kunt gebruiken voor beveiliging en beheer van communicatie, verificatie en clientgedrag.

## <a name="next-steps"></a>Volgende stappen
Naast de specifieke items waarnaar in dit artikel wordt verwezen, zijn ook de volgende resources beschikbaar voor meer algemene informatie over Azure en gerelateerde Microsoft-services:

* [Bevoegde toegang beveiligen](/windows-server/identity/securing-privileged-access/securing-privileged-access): technische informatie over het ontwerpen en bouwen van een beveiligd beheerwerkstation voor Azure-beheer
* [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure): informatie over de functionaliteit van het Azure-platform voor het beschermen van de Azure-infrastructuur en de in Azure uitgevoerde workloads
* [Microsoft Security Response Center](https://www.microsoft.com/msrc): hier kunt u beveiligingsproblemen met Microsoft-services (waaronder Azure) rapporteren. Dit kan ook via e-mail: [secure@microsoft.com](mailto:secure@microsoft.com)