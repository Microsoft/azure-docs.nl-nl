---
title: Azure Sentinel data normalisatie-schema verwijzing | Microsoft Docs
description: In dit artikel wordt het Azure Sentinel data normalisatie-schema weer gegeven.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806932"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Naslag informatie over het Azure Sentinel data normalisatie-schema

## <a name="terminology"></a>Terminologie

De volgende terminologie wordt gebruikt in azure Sentinel-schema's:

| Termijn | Definitie |
| ---- | ---------- |
| Rapportage apparaat | Het systeem dat de records naar Azure Sentinel verzendt. Dit mag niet het onderliggende systeem van de record zijn. |
| Record | Een eenheid van gegevens die vanaf het rapport apparaat worden verzonden. Dit wordt vaak aangeduid als ' Log ', ' event ' of ' alert ', maar hoeft niet noodzakelijkerwijs een van beide te zijn. |
|

## <a name="data-types-and-formats"></a>Gegevens typen en-indelingen

De waarden moeten worden genormaliseerd op basis van de onderstaande richt lijnen. Dit is verplicht voor genormaliseerde velden en wordt aanbevolen voor andere velden. 

| Gegevenstype | Fysiek type | Indeling en waarde |
| --------- | ------------- | ---------------- |
| **Datum en tijd** | Afhankelijk van de functie van de opname methode in aflopende prioriteit:<ul><li>Log Analytics ingebouwde type datum/tijd</li><li>Een geheel getal-veld met Log Analytics datetime-numerieke representatie</li><li>Een teken reeks veld met Log Analytics datetime-numerieke representatie</li></ul> | Weer gave van Log Analytics DateTime. <br></br>Log Analytics datum & tijd weergave lijkt op dezelfde aard, maar is anders dan bij de Unix-tijd weergave. Raadpleeg deze conversie richtlijnen. <br></br>De datum waarop & tijd zone moet worden aangepast. |
| **MAC-adres** | Tekenreeks | Colon-Hexadecimal notatie |
| **IP-adres** | IP-adres | Het schema heeft geen afzonderlijke IPv4-en IPv6-adressen. Elk IP-adres veld kan een IPv4-adres of een IPv6-adres zijn:<ul><li>IPv4 in een punt decimale notatie</li><li>IPv6 in 8 hextets-notatie, waardoor de korte formulieren hier worden beschreven.</li></ul> |
| **Gebruiker** | Tekenreeks | De volgende drie gebruikers velden zijn beschikbaar:<ul><li>Gebruikersnaam</li><li>UPN van gebruiker</li><li>Gebruikers domein</li></ul> |
| **Gebruikers-id** | Tekenreeks | De volgende twee gebruikers-Id's worden momenteel ondersteund:<ul><li>Gebruikers-SID</li><li>Azure Active Directory-ID</li></ul> |
| **Apparaat** | Tekenreeks | De volgende drie apparaat-en host-kolommen worden ondersteund:<ul><li>Id</li><li>Name</li><li>Fully Qualified Domain Name (FQDN)</li></ul> |
| **Land** | Tekenreeks | Een teken reeks die gebruikmaakt van ISO 3166-1 volgens deze prioriteit:<ul><li>Alfanumerieke 2-codes (dus voor de Verenigde Staten)</li><li>Alpha-3-codes (Verenigde Staten voor de Verenigde Staten)</li><li>Korte naam</li></ul> |
| **Regio** | Tekenreeks | De subafdelings naam van het land met ISO 3166-2 |
| **Plaats** | Tekenreeks | |
| **Lengte graad** | Dubbel | ISO 6709-coördinaten weer gave (ondertekende decimaal) |
| **Breedte graad** | Dubbel | ISO 6709-coördinaten weer gave (ondertekende decimaal) |
| **Hash-algoritme** | Tekenreeks | De volgende vier hash-kolommen worden ondersteund:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Bestands type** | Tekenreeks | Het type van het bestands type:<ul><li>Toestelnummer</li><li>Klas</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Tabel schema voor netwerk sessies

Hieronder ziet u het schema van de tabel netwerk sessies, versiond 1.0.0

| Veldnaam | Waardetype | Voorbeeld | Beschrijving | Gekoppelde OSSEM-entiteiten |
|-|-|-|-|-|
| EventType | Tekenreeks | Verkeer | Het type gebeurtenis dat wordt verzameld | Gebeurtenis |
| EventSubType | Tekenreeks | Verificatie | Aanvullende beschrijving van het type, indien van toepassing | Gebeurtenis |
| EventCount | Geheel getal  | 10 | Het aantal gebeurtenissen dat is geaggregeerd, indien van toepassing. | Gebeurtenis |
| EventEndTime | Datum/tijd | Zie "gegevens typen" | Het tijdstip waarop de gebeurtenis is beëindigd | Gebeurtenis |
| EventMessage | tekenreeks |  toegang geweigerd | Een algemeen bericht of beschrijving, hetzij opgenomen in, ofwel gegenereerd op basis van de record | Gebeurtenis |
| DvcIpAddr | IP-adres |  23.21.23.34 | Het IP-adres van het apparaat dat de record genereert | Apparaatconfiguratie<br>IP |
| DvcMacAddr | Tekenreeks | 06:10:9F: EB: 8F: 14 | Het MAC-adres van de netwerk interface van het rapport apparaat waarvan de gebeurtenis is verzonden. | Apparaatconfiguratie<br>Mac |
| DvcHostname | Apparaatnaam (teken reeks) | syslogserver1.contoso.com | De apparaatnaam van het apparaat dat het bericht genereert. | Apparaat |
| EventProduct | Tekenreeks | OfficeSharepoint | Het product waarmee de gebeurtenis wordt gegenereerd. | Gebeurtenis |
| EventProductVersion | tekenreeks | 9.0 |  De versie van het product waarmee de gebeurtenis wordt gegenereerd. | Gebeurtenis |
| EventResourceId | Apparaat-ID (teken reeks) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | De resource-ID van het apparaat dat het bericht genereert. | Gebeurtenis |
| EventReportUrl | Tekenreeks | https://192.168.1.1/repoerts/ae3-56.htm | Een koppeling naar het volledige rapport dat is gemaakt door het rapport apparaat | Gebeurtenis |
| EventVendor | Tekenreeks | Microsoft | De leverancier van het product dat de gebeurtenis genereert. | Gebeurtenis |
| EventResult | Meerdere waarden: geslaagd, gedeeltelijk, mislukt, [leeg] (teken reeks) | Geslaagd | Het resultaat dat is gerapporteerd voor de activiteit. Lege waarde wanneer deze niet van toepassing is. | Gebeurtenis |
| EventResultDetails | Tekenreeks | Onjuist wacht woord | Reden of Details voor het resultaat dat is gerapporteerd in EventResult | Gebeurtenis |
| EventSchemaVersion | Realistische | 0,1 | Versie van het Sentinel-schema van Azure. Momenteel 0,1. | Gebeurtenis |
| EventSeverity | Tekenreeks | Beperkt | Als de geregistreerde activiteit een beveiligings effect heeft, duidt dit op de ernst van de impact. | Gebeurtenis |
| EventOriginalUid | Tekenreeks | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | De record-ID van het rapport apparaat. | Gebeurtenis |
| EventStartTime | Datum/tijd | Zie "gegevens typen" | Het tijdstip waarop de gebeurtenis is opgegeven | Gebeurtenis |
| TimeGenerated | Datum/tijd | Zie "gegevens typen" | Het tijdstip waarop de gebeurtenis heeft plaatsgevonden, zoals gerapporteerd door de rapport bron. | Aangepast veld |
| EventTimeIngested | Datum/tijd | Zie "gegevens typen" | Het tijdstip waarop de gebeurtenis is opgenomen in azure Sentinel. Wordt toegevoegd door Azure Sentinel. | Gebeurtenis |
| EventUid | GUID (teken reeks) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | De unieke id die door Azure Sentinel wordt gebruikt om een rij te markeren. | Gebeurtenis |
| NetworkApplicationProtocol | Tekenreeks | HTTPS | Het protocol voor de toepassings laag dat wordt gebruikt door de verbinding of de sessie. | Netwerk |
| DstBytes | int | 32455 | Het aantal bytes dat vanaf het doel naar de bron voor de verbinding of sessie is verzonden. | Doel |
| SrcBytes | int | 46536 | Het aantal bytes dat vanaf de bron naar het doel voor de verbinding of sessie is verzonden. | Bron |
| NetworkBytes | int | 78991 | Het aantal bytes dat in beide richtingen is verzonden. Als zowel BytesReceived als bytes sent bestaan, moet BytesTotal gelijk zijn aan het totaal. | Netwerk |
| NetworkDirection | Meerdere waarden: Inkomend, uitgaand (teken reeks) | Inkomend | De richting van de verbinding of sessie naar of van de organisatie. | Netwerk |
| DstGeoCity | Tekenreeks | Burlington | De plaats die is gekoppeld aan het doel-IP-adres | Beoogde<br>Geo |
| DstGeoCountry | Land (teken reeks) | VS | Het land dat is gekoppeld aan het bron-IP-adres | Beoogde<br>Geo |
| DstDvcHostname | Apparaatnaam (teken reeks) |  victim_pc | De apparaatnaam van het doel apparaat | Doel<br>Apparaat |
| DstDvcFqdn | Tekenreeks | victim_pc. contoso. local | De Fully Qualified Domain Name van de host waarop het logboek is gemaakt | Beoogde<br>Apparaat |
| DstDomainHostname | tekenreeks | CONTOSO | Het domein van de bestemming, het domein van de doelhost (website, domein naam, enzovoort), bijvoorbeeld voor DNS-Zoek opdrachten of NS-zoek acties | Doel |
| DstInterfaceName | tekenreeks | Microsoft Hyper-V netwerk adapter | De netwerk interface die wordt gebruikt voor de verbinding of de sessie door het doel apparaat. | Doel |
| DstInterfaceGuid | tekenreeks | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | GUID van de netwerk interface die voor de verificatie aanvraag is gebruikt  | Doel |
| DstIpAddr | IP-adres | 2001: db8:: FF00:42:8329 | Het IP-adres van de verbinding of sessie doel, meestal aangeduid als de doel-IP in het netwerk pakket | Beoogde<br>IP |
| DstDvcIpAddr | IP-adres | 75.22.12.2 | Het doel-IP-adres van een apparaat dat niet rechtstreeks is gekoppeld aan het netwerk pakket | Beoogde<br>Apparaatconfiguratie<br>IP
| DstGeoLatitude | Breedte graad (dubbel) | 44,475833 | De breedte graad van de geografische coördinaat die is gekoppeld aan het doel-IP-adres | Beoogde<br>Geo |
| DstMacAddr | Tekenreeks | 06:10:9F: EB: 8F: 14 | Het MAC-adres van de netwerk interface waarbij de verbinding of sessie is beëindigd, meestal aangeduid als de doel-MAC in het netwerk pakket | Beoogde<br>MAC |
| DstDvcMacAddr | Tekenreeks | 06:10:9F: EB: 8F: 14 | Het MAC-adres van het doel van een apparaat dat niet direct aan het netwerk pakket is gekoppeld. | Beoogde<br>Apparaatconfiguratie<br>MAC |
| DstDvcDomain | Tekenreeks | CONTOSO | Het domein van het doel apparaat. | Beoogde<br>Apparaat |
| DstPortNumber | Geheel getal | 443 | De doel-IP-poort. | Beoogde<br>Poort |
| DstGeoRegion | Regio (teken reeks) | Vermont | De regio binnen een land dat is gekoppeld aan het doel-IP-adres | Beoogde<br>Geo |
| DstResourceId | Apparaat-ID (teken reeks) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | De resource-id van het doel apparaat. | Doel |
| DstNatIpAddr | IP-adres | 2::1 | Als dit wordt gerapporteerd door een tussenliggend NAT-apparaat, zoals een firewall, wordt het IP-adres dat door het NAT-apparaat wordt gebruikt voor communicatie met de bron. | Doel-NAT,<br>IP |
| DstNatPortNumber | int | 443 | Als dit wordt gerapporteerd door een tussenliggend NAT-apparaat, zoals een firewall, wordt de poort die door het NAT-apparaat wordt gebruikt voor communicatie met de bron. | Doel-NAT,<br>Poort |
| DstUserSid | Gebruikers-SID |  S-12-1445 | De gebruikers-ID van de identiteit die is gekoppeld aan de doel bestemming van de sessie. Normaal gesp roken is dit de identiteit die wordt gebruikt om een server te verifiëren. Zie "gegevens typen" voor meer informatie. | Beoogde<br>Gebruiker |
| DstUserAadId | Teken reeks (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | De object-ID van het Azure AD-account van de gebruiker aan het doel einde van de sessie | Beoogde<br>Gebruiker |
| DstUserName | Gebruikers naam (teken reeks) | Johnd | De gebruikers naam van de identiteit die is gekoppeld aan de doel bestemming van de sessie.  | Beoogde<br>Gebruiker |
| DstUserUpn | tekenreeks | johnd@anon.com | De UPN van de identiteit die is gekoppeld aan de doel bestemming van de sessie. | Beoogde<br>Gebruiker |
| DstUserDomain | tekenreeks | GROEP | De domein-of computer naam van het account op het doel van de sessie | Beoogde<br>Gebruiker |
| DstZone | Tekenreeks | DMZ | De netwerk zone van de bestemming, zoals gedefinieerd door het rapport apparaat. | Doel |
| DstGeoLongitude | Lengte graad (double) | -73,211944 | De lengte graad van de geografische coördinaat die is gekoppeld aan het doel-IP-adres | Beoogde<br>Geo |
| DvcAction | Meerdere waarden: toestaan, weigeren, neerzetten (teken reeks) | Toestaan | Indien gerapporteerd door een tussenliggend apparaat, zoals een firewall, de actie die door het apparaat wordt uitgevoerd. | Apparaat |
| DvcInboundInterface | Tekenreeks | ETH0 | Als deze wordt gerapporteerd door een tussenliggend apparaat, zoals een firewall, wordt de netwerk interface gebruikt die door de computer is gemaakt voor de verbinding met het bron apparaat. | Apparaat |
| DvcOutboundInterface | Tekenreeks  | Ethernet-adapter Ethernet 4 | Als deze wordt gerapporteerd door een tussenliggend apparaat, zoals een firewall, wordt de netwerk interface gebruikt die door de computer is gemaakt voor de verbinding met het doel apparaat. | Apparaat |
| NetworkDuration | Geheel getal | 1500 | De hoeveelheid tijd, in milliseconden, voor het volt ooien van de netwerk sessie of verbinding | Netwerk |
| NetworkIcmpCode | Geheel getal | 34 | Voor een ICMP-bericht, ICMP-bericht type numerieke waarde (RFC 2780 of RFC 4443). | Netwerk |
| NetworkIcmpType | Tekenreeks | Doel onbereikbaar | Voor een ICMP-bericht, tekst weergave van het ICMP-bericht type (RFC 2780 of RFC 4443). | Netwerk |
| DstPackets | int  | 446 | Het aantal pakketten dat vanaf het doel naar de bron voor de verbinding of sessie is verzonden. De betekenis van een pakket wordt gedefinieerd door het rapport apparaat. | Doel |
| SrcPackets | int  | 6478 | Het aantal pakketten dat vanaf de bron naar het doel voor de verbinding of sessie is verzonden. De betekenis van een pakket wordt gedefinieerd door het rapport apparaat. | Bron |
| NetworkPackets | int  | 0 | Aantal verzonden pakketten in beide richtingen. Als zowel PacketsReceived als PacketsSent bestaan, moet BytesTotal gelijk zijn aan het totaal. | Netwerk |
| HttpRequestTime | Geheel getal | 700 | De hoeveelheid tijd die nodig is om de aanvraag naar de server te verzenden, indien van toepassing. | HTTP |
| HttpResponseTime | Geheel getal | 800 | De hoeveelheid tijd die nodig is om een antwoord te ontvangen op de server, indien van toepassing. | HTTP |
| NetworkRuleName | Tekenreeks | AnyAnyDrop | De naam of ID van de regel waarmee DeviceAction is vastgesteld | Netwerk |
| NetworkRuleNumber | int |  23 | Nummer van overeenkomende regel  | Netwerk |
| NetworkSessionId | tekenreeks | 172_12_53_32_4322__123_64_207_1_80 | De sessie-id zoals gerapporteerd door het rapport apparaat. Bijvoorbeeld: de id van de N7-sessie voor specifieke toepassingen na verificatie | Netwerk |
| SrcGeoCity | Tekenreeks | Burlington | De plaats die is gekoppeld aan het bron-IP-adres | Bron<br>Geo |
| SrcGeoCountry | Land (teken reeks) | VS | Het land dat is gekoppeld aan het bron-IP-adres | Bron<br>Geo |
| SrcDvcHostname | Apparaatnaam (teken reeks) |  villain | De apparaatnaam van het bron apparaat | Bron<br>Apparaat |
| SrcDvcFqdn | tekenreeks | Villain.malicious.com | De Fully Qualified Domain Name van de host waarop het logboek is gemaakt | Bron<br>Apparaat |
| SrcDvcDomain | tekenreeks | EVILORG | Domein van het apparaat waarvandaan de sessie is gestart | Bron<br>Apparaat |
| SrcDvcOs | Tekenreeks | iOS | Het besturings systeem van het bron apparaat | Bron<br>Apparaat |
| SrcDvcModelName | Tekenreeks | Samsung Galaxy Opmerking | De model naam van het bron apparaat | Bron<br>Apparaat |
| SrcDvcModelNumber | Tekenreeks | 10.0 | Het model nummer van het bron apparaat | Bron<br>Apparaat |
| SrcDvcType | Tekenreeks | Mobiel | Het type van het bron apparaat | Bron<br> Apparaat |
| SrcIntefaceName | Tekenreeks | eth01 | De netwerk interface die wordt gebruikt voor de verbinding of de sessie van het bron apparaat. | Bron |
| SrcInterfaceGuid | Tekenreeks | 46ad544b-eaf0-47ef-827c-266030f545a6 | GUID van de gebruikte netwerk interface | Bron |
| SrcIpAddr | IP-adres | 77.138.103.108 | Het IP-adres waarmee de verbinding of sessie afkomstig is. | Bron<br>IP |
| SrcDvcIpAddr | IP-adres | 77.138.103.108 | Het bron-IP-adres van een apparaat dat niet rechtstreeks is gekoppeld aan het netwerk pakket (verzameld door een provider of expliciet is berekend). | Bron<br>Apparaatconfiguratie<br>IP |
| SrcGeoLatitude | Breedte graad (dubbel) | 44,475833 | De breedte graad van het geografische coördinaat dat is gekoppeld aan het bron-IP-adres | Bron<br>Geo |
| SrcGeoLongitude | Lengte graad (double) | -73,211944 | De lengte graad van de geografische coördinaat die is gekoppeld aan het bron-IP-adres | Bron<br>Geo |
| SrcMacAddr | Tekenreeks | 06:10:9F: EB: 8F: 14 | Het MAC-adres van de netwerk interface waarvan de verbinding met de sessie verod is. | Bron<br>Mac |
| SrcDvcMacAddr | Tekenreeks | 06:10:9F: EB: 8F: 14 | Het bron-MAC-adres van een apparaat dat niet direct aan het netwerk pakket is gekoppeld. | Bron<br>Apparaatconfiguratie<br>Mac |
| SrcPortNumber | Geheel getal | 2335 | De IP-poort van waaruit de verbinding afkomstig is. Is mogelijk niet relevant voor een sessie die bestaat uit meerdere verbindingen. | Bron<br>Poort |
| SrcGeoRegion | Regio (teken reeks) | Vermont | De regio binnen een land dat is gekoppeld aan het bron-IP-adres | Bron<br>Geo |
| SrcResourceId | Tekenreeks | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | De resource-ID van het apparaat dat het bericht genereert. | Bron |
| SrcNatIpAddr | IP-adres | 4.3.2.1 | Als dit wordt gerapporteerd door een tussenliggend NAT-apparaat, zoals een firewall, wordt het IP-adres dat door het NAT-apparaat wordt gebruikt voor communicatie met het doel. | Bron-NAT,<br>IP |
| SrcNatPortNumber | Geheel getal | 345 | Als dit wordt gerapporteerd door een tussenliggend NAT-apparaat, zoals een firewall, wordt de poort die door het NAT-apparaat wordt gebruikt voor communicatie met het doel. | Bron-NAT,<br>Poort |
| SrcUserSid | Gebruikers-ID (teken reeks) | S-15-1445 | De gebruikers-ID van de identiteit die is gekoppeld aan de sessie bron. Normaal gesp roken voert gebruiker een actie uit op de client. Zie "gegevens typen" voor meer informatie. | Bron<br>Gebruiker |
| SrcUserAadId | Teken reeks (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | De object-ID van het Azure AD-account van de gebruiker aan het bron einde van de sessie | Bron<br>Gebruiker |
| SrcUserName | Gebruikers naam (teken reeks) | Bob | De gebruikers naam van de identiteit die is gekoppeld aan de sessie bron. Normaal gesp roken voert gebruiker een actie uit op de client. Zie "gegevens typen" voor meer informatie. | Bron<br>Gebruiker |
| SrcUserUpn | tekenreeks | bob@alice.com | De UPN van het account waarmee de sessie wordt gestart | Bron<br>Gebruiker |
| SrcUserDomain | tekenreeks | PRINTER | Het domein voor het account dat de sessie initieert | Bron<br>Gebruiker |
| SrcZone | Tekenreeks | Op | De netwerk zone van de bron, zoals gedefinieerd door het rapport apparaat. | Bron |
| NetworkProtocol | Tekenreeks | TCP | Het IP-protocol dat wordt gebruikt door de verbinding of de sessie. Normaal gesp roken TCP, UDP of ICMP | Netwerk |
| CloudAppName | Tekenreeks | Facebook | De naam van de doel toepassing voor een HTTP-toepassing, zoals geïdentificeerd door een proxy. | Cloud |
| CloudAppId | Tekenreeks | 124 | De ID van de doel toepassing voor een HTTP-toepassing, zoals geïdentificeerd door een proxy. Deze waarde is doorgaans specifiek voor de gebruikte proxy. | Cloud |
| CloudAppOperation | Tekenreeks | Delete | De bewerking die de gebruiker heeft uitgevoerd in de context van de doel toepassing voor een HTTP-toepassing, zoals geïdentificeerd door een proxy. Deze waarde is doorgaans specifiek voor de gebruikte proxy. | Cloud |
| CloudAppRiskLevel | Tekenreeks | 3 | Het risico niveau dat is gekoppeld aan een HTTP-toepassing, zoals geïdentificeerd door een proxy. Deze waarde is doorgaans specifiek voor de gebruikte proxy. | Cloud |
| Bestands | Tekenreeks | ImNotMalicious.exe | De bestands naam die via de netwerk verbindingen wordt verzonden voor protocollen, zoals FTP en HTTP, die de bestandsnaam informatie bevat. | File |
| Bestandspad | Tekenreeks | C:\Malicious\ImNotMalicious.exe | Het volledige pad, inclusief de bestands naam, van het bestand | File |
| FileHashMd5 | Tekenreeks | 51BC68715FC7C109DCEA406B42D9D78F | De MD5-hash-waarde van het bestand dat via de netwerk verbindingen voor protocollen wordt verzonden. | File |
| FileHashSha1 | Tekenreeks | 491AE3... C299821476F4 | De SHA1-hash-waarde van het bestand dat via de netwerk verbindingen voor protocollen wordt verzonden. | File |
| FileHashSha256 | Tekenreeks | 9B8F8EDB... C129976F03 | De SHA256 hash-waarde van het bestand dat via de netwerk verbindingen voor protocollen wordt verzonden. | File |
| FileHashSha512 | Tekenreeks | 5E127D... F69F73F01F361 | De SHA512 gebruikt hash-waarde van het bestand dat via de netwerk verbindingen voor protocollen wordt verzonden. | File |
| File Extension |  Tekenreeks | exe | Het type van het bestand dat wordt verzonden via de netwerk verbindingen voor protocollen zoals FTP en HTTP. | File
| FileMimeType | Tekenreeks | toepassings-MSWord | Het MIME-type van het bestand dat wordt verzonden via de netwerk verbindingen voor protocollen zoals FTP en HTTP | File |
| FileSize | Geheel getal | 23500 | De bestands grootte, in bytes, van het bestand dat via de netwerk verbindingen voor protocollen wordt verzonden. | File |
| HttpVersion | Tekenreeks | 2,0 | De versie van de HTTP-aanvraag voor HTTP/HTTPS-netwerk verbindingen. | HTTP |
| HttpRequestMethod | Tekenreeks | GET | De HTTP-methode voor HTTP/HTTPS-netwerk sessies. | HTTP |
| Http status code | Tekenreeks | 404 | De HTTP-status code voor HTTP/HTTPS-netwerk sessies. | HTTP |
| HttpContentType | Tekenreeks | meerdelige/formulier-data; grens = iets | De header van het inhouds type van het HTTP-antwoord voor HTTP/HTTPS-netwerk sessies. | HTTP |
| HttpReferrerOriginal | Tekenreeks | https://developer.mozilla.org/en-US/docs/Web/JavaScript | De HTTP-verwijzende header voor HTTP/HTTPS-netwerk sessies. | HTTP |
| HttpUserAgentOriginal | Tekenreeks | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, zoals gecko) Chrome/83.0.4103.97 Safari/537.36 | De HTTP-header van de gebruikers agent voor HTTP/HTTPS-netwerk sessies. | HTTP |
| HttpRequestXff | Tekenreeks | 120.12.41.1 | De HTTP X-Forwarded-For-header voor HTTP/HTTPS-netwerk sessies. | HTTP |
| UrlCategory | Tekenreeks | Zoek machines | De gedefinieerde groepering van een URL (of kan net zijn gebaseerd op het domein in de URL) die betrekking heeft op wat het is (bijvoorbeeld: volwassene, nieuws, reclame, geparkeerde domeinen enz.) | url |
| UrlOriginal | Tekenreeks | https://contoso.com/fo/?k=v&q = u # f | De URL van de HTTP-aanvraag voor HTTP/HTTPS-netwerk sessies. | URL |
| UrlHostname | Tekenreeks | contoso.com | Het domein onderdeel van een HTTP-aanvraag-URL voor HTTP/HTTPS-netwerk sessies. | URL |
| ThreatCategory | Tekenreeks | Trojan | De categorie van een bedreiging die wordt geïdentificeerd door een beveiligings systeem, zoals Web Security Gateway van een IP'S en is gekoppeld aan deze netwerk sessie. | Bedreiging |
| ThreatId | Tekenreeks | Tr. 124 | De ID van een bedreiging die wordt geïdentificeerd door een beveiligings systeem, zoals Web Security Gateway van een IP'S en is gekoppeld aan deze netwerk sessie. | Bedreiging |
| Bedreigings | Tekenreeks | EICAR-test bestand | De naam van de geïdentificeerde bedreiging of malware | Bedreiging |
| AdditionalFields | Dynamisch (JSON Bag) | {<br>Property1: "val1",<br>Property2: "val2"<br>} | Als er geen overeenkomende kolom in het schema wordt gevonden, kunnen extra velden worden opgeslagen in een JSON-Bag.<br>Voor het parseren van query's wordt het aanbevolen deze methode niet te gebruiken als het verpakken van gegevens in een JSON de query prestaties verslechteren. In plaats daarvan wordt het aanbevolen om extra kolommen te promo veren.<br>Voor toekomstige scenario's voor het parseren van opname tijd worden er extra gegevens in deze JSON-Bag-kolom verzameld. | Aangepast veld |
| 
