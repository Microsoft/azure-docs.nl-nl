---
title: Naslag informatie over Azure Sentinel entitys | Microsoft Docs
description: In dit artikel worden de Azure Sentinel-entiteits typen en de vereiste id's weer gegeven.
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456250"
---
# <a name="azure-sentinel-entity-types-reference"></a>Naslag informatie over de Azure Sentinel-entiteit

## <a name="entity-types-and-identifiers"></a>Entiteits typen en-id's

In de volgende tabel ziet u de **entiteits typen** die momenteel beschikbaar zijn voor toewijzing in azure Sentinel en de **kenmerken** die beschikbaar zijn als **id's** voor elk entiteits type. deze worden weer gegeven in de vervolg keuzelijst **id's** in de sectie [entiteits toewijzing](map-data-fields-to-entities.md) van de [wizard Analytics-regel](tutorial-detect-threats-custom.md).

Elk van de id's in de **vereiste id** -kolom is mini maal nodig om de entiteit te identificeren. Een vereiste id kan echter niet zelf voldoende zijn om *unieke* identificatie te bieden. Hoe meer id's worden gebruikt, des te groter is de kans op unieke identificatie. U kunt Maxi maal drie id's voor één entiteits toewijzing gebruiken.

Voor de beste resultaten: voor gegarandeerde unieke identificatie moet u, indien mogelijk, id's uit de kolom **sterkst mogelijke id's** gebruiken. Het gebruik van meerdere sterke id's maakt correlatie mogelijk tussen sterke id's van verschillende gegevens bronnen en schema's. Op die manier kan Azure Sentinel uitgebreidere inzichten bieden voor een bepaalde entiteit.

| Entiteitstype | Id's | Vereiste id's | Sterkst mogelijke id's |
| - | - | - | - |
| [**Gebruikersaccount**](#user-account)<br>*Account* | Name<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Geval<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>GUID | FullName<br>Geval<br>Name<br>AadUserId<br>PUID<br>GUID | Naam en NTDomain<br>Naam en UPNSuffix<br>AADUserId<br>Geval |
| [**Host**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | Hostnaam + NTDomain<br>Hostnaam + DnsDomain<br>NetBIOS-NTDomain<br>NetBIOS-DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP-adres**](#ip-address)<br>*ONDERZOEK* | Adres | Adres | |
| [**Malware**](#malware) | Name<br>Categorie | Name | |
| [**File**](#file) | Directory<br>Name | Name | |
| [**Proces**](#process) | Process<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>Process | |
| [**Cloud toepassing**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**Domeinnaam**](#domain-name)<br>*DNS* | DomainName | DomainName | |
| [**Azure-resource**](#azure-resource) | ResourceId | ResourceId | |
| [**Bestandshash**](#file-hash)<br>*(FileHash)* | Algoritme<br>Waarde | Algoritme + waarde | |
| [**Register sleutel**](#registry-key) | Hive<br>Sleutel | Hive<br>Sleutel | Hive + sleutel |
| [**Register waarde**](#registry-value) | Name<br>Waarde<br>ValueType | Name | |
| [**Beveiligings groep**](#security-group) | DistinguishedName<br>SID<br>GUID | DistinguishedName<br>SID<br>GUID | |
| [**URL**](#url) | URL | URL | |
| [**IoT-apparaat**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Bron<br>SourceRef<br>Fabrikant<br>Modelleren<br>OperatingSystem<br>IPAdres<br>MacAddress<br>Protocollen<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**Persoonlijke**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>UPN<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**E-mail cluster**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Bedreigingen<br>Query<br>QueryTime<br>Aantal mailitems<br>IsVolumeAnomaly<br>Bron<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Query<br>Bron | Query + bron |
| [**E-mail bericht**](#mail-message) | Ontvanger<br>Adres<br>Bedreigingen<br>Afzender<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>Ontvangst<br>NetworkMessageId<br>InternetMessageId<br>Onderwerp<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Taal<br>ThreatDetectionMethods | NetworkMessageId<br>Ontvanger | NetworkMessageId + ontvanger |
| [**E-mail verzenden**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Indiener<br>NetworkMessageId<br>Tijdstempel<br>Ontvanger<br>Afzender<br>SenderIp<br>Onderwerp<br>Report type | SubmissionId<br>NetworkMessageId<br>Ontvanger<br>Indiener |  |
|

## <a name="entity-type-schemas"></a>Entiteits type schema's

Hieronder vindt u meer gedetailleerde informatie over de volledige schema's van elk type entiteit. U ziet dat veel van deze schema's koppelingen naar andere entiteits typen bevatten, bijvoorbeeld het schema van het gebruikers account bevat een koppeling naar het type host-entiteit, omdat één kenmerk van een gebruikers account de host is die is gedefinieerd in. Deze extern gekoppelde entiteiten kunnen niet worden gebruikt als id's voor entiteits toewijzing, maar ze zijn erg handig voor het geven van een volledige afbeelding van entiteiten op de entiteits pagina's en het onderzoek diagram.

> [!NOTE]
> Een vraag teken na de waarde in de kolom **type** geeft aan dat het veld Null kan zijn.

## <a name="user-account"></a>Gebruikersaccount

*Entiteits naam: account*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | account |
| Name | Tekenreeks | De naam van het account. Dit veld mag alleen de naam bevatten zonder dat er een domein aan is toegevoegd. |
| *Juiste* | *N.v.t.* | *Geen onderdeel van schema, opgenomen voor achterwaartse compatibiliteit met oude versie van entiteits toewijzing.*
| NTDomain | Tekenreeks | De NETBIOS-domein naam zoals deze wordt weer gegeven in de indeling van de waarschuwing – domein\gebruikersnaam Voor beelden: Financiën, NT AUTHORITY |
| DnsDomain | Tekenreeks | De volledig gekwalificeerde DNS-naam van het domein. Voor beelden: finance.contoso.com |
| UPNSuffix | Tekenreeks | Het user principal name achtervoegsel voor het account. In sommige gevallen is dit ook de domein naam. Voor beelden: contoso.com |
| Host | Entiteit | De host die het account bevat, als dit een lokaal account is. |
| Geval | Tekenreeks | De beveiligings-id van het account, zoals S-1-5-18. |
| AadTenantId | GPT? | De Azure AD-Tenant-ID, indien bekend. |
| AadUserId | GPT? | De object-ID van het Azure AD-account, indien bekend. |
| PUID | GPT? | De gebruikers-ID van Azure AD Pass Port, indien bekend. |
| IsDomainJoined | BOOL? | Hiermee wordt bepaald of dit een domein account is. |
| DisplayName | Tekenreeks | De weergave naam van het account. |
| GUID | GPT? | Het kenmerk objectGUID is een kenmerk met één waarde dat de unieke id voor het object is, toegewezen door Active Directory. |
|

Sterke id's van een account entiteit:

- Naam en UPNSuffix
- AadUserId
- Sid + host (vereist voor Sid's van ingebouwde accounts)
- Sid (behalve voor Sid's van ingebouwde accounts)
- Name + NTDomain (tenzij NTDomain een ingebouwd domein is, bijvoorbeeld "werk groep")
- Naam + host (als NTDomain een ingebouwd domein is, bijvoorbeeld ' werk groep ')
- Naam en DnsDomain
- PUID
- GUID

Zwakke id's van een account entiteit:

- Name

## <a name="host"></a>Host

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | hostsite |
| DnsDomain | Tekenreeks | Het DNS-domein waartoe deze host behoort. Moet het volledige DNS-achtervoegsel voor het domein bevatten, indien bekend. |
| NTDomain | Tekenreeks | Het NT-domein waartoe deze host behoort. |
| HostName | Tekenreeks | De hostnaam zonder het domein achtervoegsel. |
| *Juiste* | *N.v.t.* | *Geen onderdeel van schema, opgenomen voor achterwaartse compatibiliteit met oude versie van entiteits toewijzing.*
| NetBiosName | Tekenreeks | De naam van de host (vóór Windows 2000). |
| IoTDevice | Entiteit | De IoT-apparaat-entiteit (als deze host een IoT-apparaat vertegenwoordigt). |
| AzureID | Tekenreeks | De Azure-Resource-ID van de virtuele machine, indien bekend. |
| OMSAgentID | Tekenreeks | De OMS-agent-ID, als OMS-agent is geïnstalleerd op de host. |
| OSFamily | Vaste? | Een van de volgende waarden: <li>Linux<li>Windows<li>Android<li>iOS |
| OSVersion | Tekenreeks | Een vrije tekst weergave van het besturings systeem.<br>Dit veld is bedoeld om specifieke versies op te slaan. de waarden zijn nauw keuriger dan OSFamily, of toekomst worden niet ondersteund door OSFamily-inventarisatie. |
| IsDomainJoined | Booleaanse waarde | Hiermee wordt bepaald of deze host tot een domein behoort. |
|

Sterke id's van een host-entiteit:
- Hostnaam + NTDomain
- Hostnaam + DnsDomain
- NetBIOS-NTDomain
- NetBIOS-DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (niet ondersteund voor entiteits toewijzing)

Zwakke id's van een host-entiteit:
- HostName
- NetBiosName

## <a name="ip-address"></a>IP-adres

*Naam van entiteit: IP*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | onderzoek |
| Adres | Tekenreeks | Het IP-adres als teken reeks, bijvoorbeeld 127.0.0.1 (in IPv4 of IPv6). |
| Locatie | Geolocatie | De geo-locatie context die is gekoppeld aan de IP-entiteit. |
|

Sterke id's van een IP-entiteit:
- Adres

## <a name="malware"></a>Malware

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | genoemd |
| Name | Tekenreeks | De naam van de malware door de leverancier, zoals `Win32/Toga!rfn` . |
| Categorie | Tekenreeks | De categorie malware door de leverancier, bijvoorbeeld Trojan. |
| Bestanden | Orderverzamellijst\<Entity> | Lijst met gekoppelde bestands entiteiten waarop de schadelijke software is gevonden. Kan de bestands entiteiten inline of als verwijzing bevatten.<br>Raadpleeg de bestands entiteit voor meer informatie over de structuur. |
| Processen | Orderverzamellijst\<Entity> | Lijst met gekoppelde proces entiteiten waarop de schadelijke software is gevonden. Dit wordt vaak gebruikt wanneer de waarschuwing wordt geactiveerd voor bestanden die zijn gefiled.<br>Zie de entiteit [proces](#process) voor meer informatie over de structuur. |
|

Sterke id's van een schadelijke entiteit:

- Naam en categorie

## <a name="file"></a>File

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | Profiler |
| Directory | Tekenreeks | Het volledige pad naar het bestand. |
| Name | Tekenreeks | De bestands naam zonder het pad (sommige waarschuwingen bevatten mogelijk geen pad). |
| Host | Entiteit | De host waarop het bestand is opgeslagen. |
| FileHashes | Entiteit weer geven &lt;&gt; | De bestands-hashes die aan dit bestand zijn gekoppeld. |
|

Sterke id's van een bestands entiteit:
- Naam en map
- Naam en FileHash
- Naam en adres lijst + FileHash

## <a name="process"></a>Proces

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | host |
| Process | Tekenreeks | De proces-ID. |
| CommandLine | Tekenreeks | De opdracht regel die wordt gebruikt om het proces te maken. |
| ElevationToken | Vaste? | Het verhogings token dat aan het proces is gekoppeld.<br>Mogelijke waarden:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | Het tijdstip waarop het proces is gestart. |
| Installatie | Entiteit (bestand) | Kan de bestands entiteit inline of als verwijzing bevatten.<br>Raadpleeg de bestands entiteit voor meer informatie over de structuur. |
| Account | Entiteit | Het account dat de processen uitvoert.<br>Kan de [account](#user-account) entiteit inline of als verwijzing bevatten.<br>Zie de [account](#user-account) entiteit voor meer informatie over de structuur. |
| ParentProcess | Entiteit (proces) | De bovenliggende proces entiteit. <br>Kan gedeeltelijke gegevens bevatten, dat wil zeggen alleen de PID. |
| Host | Entiteit | De host waarop het proces werd uitgevoerd. |
| LogonSession | Entiteit (HostLogonSession) | De sessie waarin het proces werd uitgevoerd. |
|

Sterke id's van een proces-entiteit:

- Host + ProcessId + CreationTimeUtc
- Host + ParentProcessId + CreationTimeUtc + CommandLine
- Host + ProcessId + CreationTimeUtc + installatie kopie
- Host + ProcessId + CreationTimeUtc + installatie kopie. FileHash

Zwakke id's van een proces-entiteit:

- ProcessId + CreationTimeUtc + CommandLine (en geen host)
- Proces-process + CreationTimeUtc + installatie kopie (en geen host)

## <a name="cloud-application"></a>Cloud toepassing

*Entiteits naam: CloudApplication*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | Cloud-toepassing |
| AppId | Int | De technische id van de toepassing. Dit moet een van de waarden zijn die zijn gedefinieerd in de lijst met [Cloud toepassings-id's](#cloud-application-identifiers). De waarde voor het veld AppId is optioneel. |
| Name | Tekenreeks | De naam van de gerelateerde Cloud toepassing. De waarde van de toepassings naam is optioneel. |
| InstanceName | Tekenreeks | De door de gebruiker gedefinieerde exemplaar naam van de Cloud toepassing. Dit wordt vaak gebruikt om onderscheid te maken tussen verschillende toepassingen van hetzelfde type als een klant. |
|

Sterke id's van een Cloud toepassings entiteit:
 - AppId (zonder INSTANCENAME)
 - Naam (zonder INSTANCENAME)
 - AppId + INSTANCENAME
 - Naam en INSTANCENAME

## <a name="domain-name"></a>Domeinnaam

*Entiteits naam: DNS*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | DNS |
| DomainName | Tekenreeks | De naam van de DNS-record die aan de waarschuwing is gekoppeld. |
| IPAdres | Lijst &lt; entiteit (IP)&gt; | Entiteiten die overeenkomen met de opgeloste IP-adressen. |
| DnsServerIp | Entiteit (IP) | Een entiteit die de DNS-server voor het oplossen van de aanvraag vertegenwoordigt. |
| HostIpAddress | Entiteit (IP) | Een entiteit die de DNS-aanvraag client vertegenwoordigt. |
|

Sterke id's van een DNS-entiteit:
- DomainName en DnsServerIp + HostIpAddress

Zwakke id's van een DNS-entiteit:
- DomainName-HostIpAddress

## <a name="azure-resource"></a>Azure-resource

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | ' Azure-resource ' |
| ResourceId | Tekenreeks | De Azure-Resource-ID van de resource. |
| SubscriptionId | Tekenreeks | De abonnements-ID van de resource. |
| TryGetResourceGroup | Booleaanse waarde | De waarde van de resource groep als deze bestaat. |
| TryGetProvider | Booleaanse waarde | De waarde van de provider als deze bestaat. |
| TryGetName | Booleaanse waarde | De naam waarde als deze bestaat. |
|

Sterke id's van een Azure-resource-entiteit:
- ResourceId

## <a name="file-hash"></a>Bestandshash

*Entiteits naam: FileHash*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | 'filehash' |
| Algoritme | Enum | Het type hash-algoritme. Mogelijke waarden:<li>Onbekend<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Waarde | Tekenreeks | De hash-waarde. |
|

Sterke id's van een bestand hash-entiteit:
- Algoritme + waarde

## <a name="registry-key"></a>Registersleutel

*Entiteits naam: RegistryKey*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | register sleutel |
| Hive | Vaste? | Een van de volgende waarden:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Sleutel | Tekenreeks | Het pad van de register sleutel. |
|

Sterke id's van een register sleutel entiteit:
- Hive + sleutel

## <a name="registry-value"></a>Registerwaarde

*Entiteits naam: RegistryValue*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | register waarde |
| Sleutel | Entiteit (RegistryKey) | De entiteit van de register sleutel. |
| Name | Tekenreeks | De naam van de register waarde. |
| Waarde | Tekenreeks | Weer gave van de waarde gegevens in teken reeksen. |
| ValueType | Vaste? | Een van de volgende waarden:<li>Tekenreeks<li>Binair<li>32<li>Qword<li>Fixedlength<li>ExpandString<li>Geen<li>Onbekend<br>De waarden moeten overeenkomen met micro soft. Win32. RegistryValueKind-inventarisatie. |
|

Sterke id's van een register waarde-entiteit:
- Sleutel en naam

Zwakke id's van een register waarde-entiteit:
- Naam (zonder sleutel)

## <a name="security-group"></a>Beveiligings groep

*Entiteits naam: beveiligings groep*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | beveiligings groep |
| DistinguishedName | Tekenreeks | De DN-naam van de groep. |
| SID | Tekenreeks | Het SID-kenmerk is een kenmerk met één waarde waarmee de beveiligings-id (SID) van de groep wordt opgegeven. |
| GUID | GPT? | Het kenmerk objectGUID is een kenmerk met één waarde dat de unieke id voor het object is, toegewezen door Active Directory. |
|

Sterke id's van een entiteit van een beveiligings groep:
 - DistinguishedName
 - SID
 - GUID

## <a name="url"></a>URL

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | URL |
| URL | URI | Een volledige URL waarnaar de entiteit verwijst. |
|

Sterke id's van een URL-entiteit:
- URL (als er een absolute URL)

Zwakke id's van een URL-entiteit:
- URL (als een relatieve URL)

## <a name="iot-device"></a>IoT-apparaat

*Entiteits naam: IoTDevice*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | 'iotdevice' |
| IoTHub | Entiteit (bronnen) | De bronnen-entiteit die de IoT Hub waarvan het apparaat deel uitmaakt. |
| DeviceId | Tekenreeks | De ID van het apparaat in de context van de IoT Hub. |
| DeviceName | Tekenreeks | De beschrijvende naam van het apparaat. |
| IoTSecurityAgentId | GPT? | De ID van de *Defender voor IOT* -agent die wordt uitgevoerd op het apparaat. |
| DeviceType | Tekenreeks | Het type van het apparaat (' temperatuur sensor ', ' Vries ', ' wind turbine ' enz.). |
| Bron | Tekenreeks | De bron (micro soft/leverancier) van de entiteit apparaat. |
| SourceRef | Entiteit (URL) | Een URL-verwijzing naar het bron item waar het apparaat wordt beheerd. |
| Fabrikant | Tekenreeks | De fabrikant van het apparaat. |
| Model | Tekenreeks | Het model van het apparaat. |
| OperatingSystem | Tekenreeks | Het besturings systeem waarop het apparaat wordt uitgevoerd. |
| IPAdres | Entiteit (IP) | Het huidige IP-adres van het apparaat. |
| MacAddress | Tekenreeks | Het MAC-adres van het apparaat. |
| Protocollen | &lt;Teken reeks weer geven&gt; | Een lijst met protocollen die door het apparaat worden ondersteund. |
| SerialNumber | Tekenreeks | Het serienummer van het apparaat. |
|

Sterke id's van een IoT-apparaat-entiteit:
- IoTHub + DeviceId

Zwakke id's van een IoT-apparaat-entiteit:
- DeviceId (zonder IoTHub)

## <a name="mailbox"></a>Postvak

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | persoonlijke |
| MailboxPrimaryAddress | Tekenreeks | Het primaire adres van het postvak. |
| DisplayName | Tekenreeks | De weergave naam van het postvak. |
| UPN | Tekenreeks | De UPN van het postvak. |
| RiskLevel | Vaste? | Het risico niveau van dit postvak. Mogelijke waarden:<li>Geen<li>Beperkt<li>Normaal<li>Hoog |
| ExternalDirectoryObjectId | GPT? | De AzureAD-id van het postvak. Vergelijkbaar met AadUserId in de account entiteit, maar deze eigenschap is specifiek voor het object postvak aan aan de kant van het kantoor. |
|

Sterke id's van een postvak-entiteit:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>E-mail cluster

*Naam van entiteit: mailcluster*

> [!NOTE]
> **MDO**  =  **Micro soft Defender voor office 365**, voorheen bekend als Office 365 Advanced Threat Protection (O365 ATP).

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | ' mail-cluster ' |
| NetworkMessageIds | IList- &lt; teken reeks&gt; | De e-mail bericht-Id's die deel uitmaken van het e-mail cluster. |
| CountByDeliveryStatus | IDictionary &lt; -teken reeks, int&gt; | Aantal e-mail berichten op DeliveryStatus teken reeks representatie. |
| CountByThreatType | IDictionary &lt; -teken reeks, int&gt; | Aantal e-mail berichten op ThreatType teken reeks representatie. |
| CountByProtectionStatus | IDictionary &lt; -teken reeks, lang&gt; | Aantal e-mail berichten op de status van de bedreigings beveiliging. |
| Bedreigingen | IList- &lt; teken reeks&gt; | De bedreigingen van e-mail berichten die deel uitmaken van het e-mail cluster. |
| Query | Tekenreeks | De query die is gebruikt om de berichten van het e-mail cluster te identificeren. |
| QueryTime | DateTime? | De query tijd. |
| Aantal mailitems | Integer? | Het aantal e-mail berichten dat deel uitmaakt van het e-mail cluster. |
| IsVolumeAnomaly | BOOL? | Hiermee wordt bepaald of dit een mail cluster met een afwijkings volume is. |
| Bron | Tekenreeks | De bron van het e-mail cluster (de standaard waarde is ' O365 ATP '). |
| ClusterSourceIdentifier | Tekenreeks | De netwerk bericht-ID van de e-mail die de bron is van dit e-mail cluster. |
| ClusterSourceType | Tekenreeks | Het bron type van het e-mail cluster. Hiermee wordt toegewezen aan de MailClusterSourceType-instelling van MDO (zie opmerking hierboven). |
| ClusterQueryStartTime | DateTime? | Start tijd van cluster: wordt gebruikt als begin tijd voor de query cluster aantallen. Meestal datums tot de eind tijd minus de DaysToLookBack-instelling van MDO (zie opmerking hierboven). |
| ClusterQueryEndTime | DateTime? | Eind tijd van cluster: wordt gebruikt als eind tijd voor de query cluster aantallen. Doorgaans de ontvangen tijd van de e-mail gegevens. |
| ClusterGroup | Tekenreeks | Komt overeen met de Kusto-query sleutel die wordt gebruikt in MDO (zie opmerking hierboven). |
|

Sterke id's van een e-mail cluster entiteit:
- Query + bron

## <a name="mail-message"></a>E-mail bericht

*Entiteits naam: mail bericht*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | ' e-mail bericht ' |
| Bestanden | IList- &lt; bestand&gt; | De bestands entiteiten van de bijlagen van dit e-mail bericht. |
| Ontvanger | Tekenreeks | De ontvanger van dit e-mail bericht. In het geval van meerdere ontvangers wordt het e-mail bericht gekopieerd en bevat elke kopie één ontvanger. |
| Adres | IList- &lt; teken reeks&gt; | De Url's die zijn opgenomen in dit e-mail bericht. |
| Bedreigingen | IList- &lt; teken reeks&gt; | De bedreigingen in dit e-mail bericht. |
| Afzender | Tekenreeks | Het e-mail adres van de afzender. |
| P1Sender | Tekenreeks | E-mail-ID van (gedelegeerd) gebruiker die de e-mail heeft verzonden "namens P2 (primaire) gebruiker". Als het e-mail bericht niet door de gemachtigde is verzonden, is deze waarde gelijk aan P2Sender. |
| P1SenderDisplayName | Tekenreeks | Weergave naam van de (gedelegeerde) gebruiker die deze e-mail heeft verzonden "namens P2 (Primary) User". Wordt weer gegeven in de e-mailkop tekst door de eigenschap OnbehalfofSenderDisplayName. |
| P1SenderDomain | Tekenreeks | E-mail domein van de (gedelegeerde) gebruiker die deze e-mail heeft verzonden "namens P2 (Primary) User". Als het e-mail bericht niet door de gemachtigde is verzonden, is deze waarde gelijk aan P2SenderDomain. |
| P2Sender | Tekenreeks | E-mail van de (primaire) gebruiker namens wie dit e-mail bericht is verzonden. |
| P2SenderDisplayName | Tekenreeks | Weergave naam van de (primaire) gebruiker namens wie dit e-mail bericht is verzonden. Als het e-mail bericht niet door de gemachtigde is verzonden, vertegenwoordigt dit de weergave naam van de afzender. |
| P2SenderDomain | Tekenreeks | Het e-mail domein van de (primaire) gebruiker namens wie dit e-mail bericht is verzonden. Als het e-mail bericht niet door de gemachtigde is verzonden, vertegenwoordigt dit het domein van de afzender. |
| SenderIP | Tekenreeks | Het IP-adres van de afzender. |
| Ontvangst | DateTime | De ontvangst datum van dit bericht. |
| NetworkMessageId | GPT? | De netwerk bericht-ID van dit e-mail bericht. |
| InternetMessageId | Tekenreeks | De Internet bericht-ID van dit e-mail bericht. |
| Onderwerp | Tekenreeks | Het onderwerp van dit e-mail bericht. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Wordt gebruikt door micro soft Defender voor Office 365 om overeenkomende of vergelijk bare e-mail berichten te vinden. |
| AntispamDirection | Vaste? | De directionality van dit e-mail bericht. Mogelijke waarden:<li>Onbekend<li>Inkomend<li>Uitgaand<li>Intraorg (intern) |
| DeliveryAction | Vaste? | De bezorgings actie van dit e-mail bericht. Mogelijke waarden:<li>Onbekend<li>DeliveredAsSpam<li>Afgeleverd<li>Geblokkeerd<li>Geplaatst |
| DeliveryLocation | Vaste? | De bezorgings locatie van dit e-mail bericht. Mogelijke waarden:<li>Onbekend<li>Postvak IN<li>JunkFolder<li>DeletedFolder<li>Quarantaine<li>Extern<li>Mislukt<li>Minder<li>Aanvrag |
| Taal | Tekenreeks | De taal waarin de inhoud van het e-mail bericht wordt geschreven. |
| ThreatDetectionMethods | IList- &lt; teken reeks&gt; | De lijst met methoden voor het detecteren van bedreigingen die zijn toegepast op deze e-mail. |
|

Sterke id's van een e-mail bericht entiteit:
- NetworkMessageId + ontvanger

## <a name="submission-mail"></a>E-mail verzenden

*Entiteits naam: SubmissionMail*

| Veld | Type | Beschrijving |
| ----- | ---- | ----------- |
| Type | Tekenreeks | 'SubmissionMail' |
| SubmissionId | GPT? | De inzendings-ID. |
| SubmissionDate | DateTime? | De gerapporteerde datum tijd voor deze inzending. |
| Indiener | Tekenreeks | Het e-mail adres van de indiener. |
| NetworkMessageId | GPT? | De netwerk bericht-ID van de e-mail waarvan de verzen ding deel uitmaakt. |
| Tijdstempel | DateTime? | Het tijds tempel waarop het bericht is ontvangen (mail). |
| Ontvanger | Tekenreeks | De ontvanger van de e-mail. |
| Afzender | Tekenreeks | De afzender van het e-mail bericht. |
| SenderIp | Tekenreeks | Het IP-adres van de afzender. |
| Onderwerp | Tekenreeks | Het onderwerp van e-mail voor verzen ding. |
| Report type | Tekenreeks | Het type inzending voor het opgegeven exemplaar. Dit is gekoppeld aan ongewenste E-mail, phishing, malware of NotJunk. |
|

Sterke id's van een SubmissionMail entiteit:
- SubmissionId, indiener, NetworkMessageId, ontvanger

## <a name="cloud-application-identifiers"></a>Cloud toepassings-id's

De volgende lijst definieert id's voor bekende Cloud toepassingen. De ID-waarde van de app wordt gebruikt als entiteits-id van de [Cloud toepassing](#cloud-application) .

|App-id|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive-software|
|11114|SalesForce|
|11161|Office 365|
|11162|Micro soft OneNote online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive voor Bedrijven|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Auto Desk Fusion-levens cyclus|
|23043|Slack|
|23233|Online Microsoft Office|
|25275|Microsoft Skype voor Bedrijven|
|25988|Google Docs|
|26055|Microsoft Office 365-beheer centrum|
|26060|OPSWATe tand|
|26061|Micro soft Word online|
|26062|Micro soft power point online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Micro soft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace van Facebook|
|28373|CAS-Proxy-emulator|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Micro soft Dynamics talen|
|

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd over de entiteits structuur, id's en schema's in azure Sentinel.

Meer informatie over [entiteiten](entities-in-azure-sentinel.md) en [entiteits toewijzingen](map-data-fields-to-entities.md). 
