---
title: CEF-sleutel (common Event Format) en CommonSecurityLog veld toewijzing
description: In dit artikel worden CEF-sleutels toegewezen aan de corresponderende veld namen in de CommonSecurityLog in azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 1670d1bb291e30295018146f2a24c5282feac6e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311648"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF en CommonSecurityLog veld toewijzing

In de volgende tabellen worden de CEF-veld namen (common Event Format) toegewezen aan de namen die ze gebruiken in de CommonSecurityLog van Azure Sentinel. Dit kan handig zijn wanneer u werkt met een CEF-gegevens bron in azure Sentinel.

Zie [verbinding maken met uw externe oplossing met de algemene gebeurtenis indeling](connect-common-event-format.md)voor meer informatie.

## <a name="a---c"></a>A-C

|CEF-sleutel naam  |CommonSecurityLog veld naam  |Description  |
|---------|---------|---------|
| regelen    |    <a name="deviceaction"></a> DeviceAction     |  De actie die wordt vermeld in de gebeurtenis.       |
|   app  |    ApplicationProtocol     |  Het protocol dat wordt gebruikt in de toepassing, zoals HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPs, enzovoort.   |
| CNT    |    EventCount     |  Een aantal dat is gekoppeld aan de gebeurtenis, waarin wordt weer gegeven hoe vaak dezelfde gebeurtenis is waargenomen.       |
| | | |

## <a name="d"></a>D

|CEF-sleutel naam  |CommonSecurityLog-naam  |Description  |
|---------|---------|---------|
|Leverancier van apparaat     |  DeviceVendor       | Een teken reeks die samen met de product-en versie definities van het apparaat een unieke identificatie vormt van het type verzendende apparaat.       |
|Product apparaat     |   DeviceProduct      |   Een teken reeks die samen met de leveranciers-en versie definities van het apparaat een unieke identificatie vormt van het type verzendende apparaat.        |
|Apparaatstatus     |   DeviceVersion      |      Een teken reeks die samen met de product-en leveranciers definities een unieke identificatie vormt van het type verzendende apparaat.     |
| destinationDnsDomain    | DestinationDnsDomain        |   Het DNS-gedeelte van de Fully Qualified Domain Name (FQDN).      |
| destinationServiceName | DestinationServiceName | De service die wordt beoogd door de gebeurtenis. Bijvoorbeeld `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identificeert de vertaalde bestemming waarnaar wordt verwezen door de gebeurtenis in een IP-netwerk als een IPv4-IP-adres. |
| destinationTranslatedPort | DestinationTranslatedPort | Poort, na de vertaling, zoals een firewall. <br>Geldige poort nummers: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | Alle informatie over de richting waarin de waargenomen communicatie heeft plaatsgevonden. Geldige waarden: <br>- `0` = Binnenkomend <br>- `1` = Uitgaande |
| deviceDnsDomain | DeviceDnsDomain | Het DNS-domein gedeelte van de FQDN-naam (Fully Qualified Domain Name) |
|DeviceEventClassID     |   DeviceEventClassID     |   Een teken reeks of een geheel getal dat fungeert als een unieke id per gebeurtenis type.      |
| deviceExternalID | DeviceExternalID | Een unieke naam voor het apparaat waarmee de gebeurtenis wordt gegenereerd. |
| deviceFacility | DeviceFacility | De faciliteit waarmee de gebeurtenis wordt gegenereerd.|
| deviceInboundInterface | DeviceInboundInterface |De interface waarop het pakket of de gegevens van het apparaat zijn binnengekomen.  |
| deviceNtDomain | DeviceNtDomain | Het Windows-domein van het adres van het apparaat |
| deviceOutboundInterface | DeviceOutboundInterface |De interface waarop het apparaat of de gegevens zijn achtergelaten. |
| devicePayloadId |DevicePayloadId |De unieke id voor de nettolading die aan de gebeurtenis is gekoppeld. |
| deviceProcessName | ProcessName | De proces naam die aan de gebeurtenis is gekoppeld. <br><br>In UNIX is dit bijvoorbeeld het proces dat het syslog-item genereert. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Hiermee wordt het vertaalde adres van het apparaat geïdentificeerd waarnaar de gebeurtenis verwijst, in een IP-netwerk. <br><br>De indeling is een IPv4-adres. |
| dhost |DestinationHostName | De bestemming waarnaar de gebeurtenis verwijst in een IP-netwerk.  <br>De indeling moet een FQDN zijn die is gekoppeld aan het doel knooppunt, wanneer er een knoop punt beschikbaar is. Bijvoorbeeld `host.domain.com` of `host`. |
| dmac | DestinationMacAddress | Het MAC-adres van de bestemming (FQDN) |
| dntdom | DestinationNTDomain | De Windows-domein naam van het doel adres.|
| dpid | DestinationProcessId |De ID van het doel proces dat is gekoppeld aan de gebeurtenis.|
| dpriv | DestinationUserPrivileges | Hiermee worden de bevoegdheden van het doel gebruik gedefinieerd. <br>Geldige waarden: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | De naam van het doel proces van de gebeurtenis, zoals `telnetd` of `sshd.` |
| dpt | DestinationPort | Doel poort. <br>Geldige waarden: `*0` - `65535` |
| zomer tijd | DestinationIP | Het IpV4-doel adres waarnaar de gebeurtenis verwijst in een IP-netwerk. |
| dtz | DeviceTimeZon | Tijd zone van het apparaat dat de gebeurtenis genereert |
| Duid |DestinationUserId | Identificeert de doel gebruiker op ID. |
| duser | DestinationUserName |Hiermee wordt de doel gebruiker op naam aangeduid.|
| dvc | DeviceAddress | Het IPv4-adres van het apparaat dat de gebeurtenis genereert. |
| dvchost | DeviceName | De FQDN die is gekoppeld aan het knoop punt van het apparaat, wanneer er een knoop punt beschikbaar is. Bijvoorbeeld `host.domain.com` of `host`.| 
| dvcmac | DeviceMacAddress | Het MAC-adres van het apparaat dat de gebeurtenis genereert. |
| dvcpid | Proces-id | Hiermee definieert u de ID van het proces op het apparaat waarmee de gebeurtenis wordt gegenereerd. |

## <a name="e---i"></a>E-I

|CEF-sleutel naam  |CommonSecurityLog-naam  |Description  |
|---------|---------|---------|
|beëindigen     |  EndTime       | Het tijdstip waarop de activiteit met betrekking tot de gebeurtenis is beëindigd.        |
|externalId    |   ExternalID      | Een ID die wordt gebruikt door het oorspronkelijke apparaat. Deze waarden bevatten doorgaans waarden die elk aan een gebeurtenis zijn gekoppeld.        |
|fileCreateTime     |  FileCreateTime      | Tijdstip waarop het bestand is gemaakt.        |
|fileHash     |   FileHash      |   Hash van een bestand.      |
|Id     |   Id      |Een ID die is gekoppeld aan een bestand, zoals de inode.         |
| fileModificationTime | FileModificationTime |Tijdstip waarop het bestand voor het laatst is gewijzigd. |
| Bestandspad | Bestandspad | Het volledige pad naar het bestand, inclusief de bestands naam. Bijvoorbeeld: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` of `/usr/bin/zip` .|
| filePermission |FilePermission |De machtigingen voor het bestand. |
| Type | Type | Bestands type, zoals pipe, socket, enzovoort.|
|fname | Bestands| De naam van het bestand, zonder het pad. |
| fsize | FileSize | De grootte van het bestand. |
|Host    |  Computer       | Host, van syslog        |
|in     |  ReceivedBytes      |Het aantal binnenkomende bytes dat is overgedragen.         |
| | | |

## <a name="m---p"></a>M-P

|CEF-sleutel naam  |CommonSecurityLog-naam  |Description  |
|---------|---------|---------|
|msg   |  Bericht       | Een bericht met meer informatie over de gebeurtenis.        |
|Name     |  Activiteit       |   Een teken reeks die een door de mens lees bare en begrijpelijke beschrijving van de gebeurtenis vertegenwoordigt.     |
|oldFileCreateTime     |  OldFileCreateTime       | Tijdstip waarop het oude bestand is gemaakt.        |
|oldFileHash     |   OldFileHash      |   Hash van het oude bestand.      |
|oldFileId     |   OldFileId     |   En de ID die aan het oude bestand is gekoppeld, zoals de inode.      |
| oldFileModificationTime | OldFileModificationTime |Tijdstip waarop het oude bestand het laatst is gewijzigd. |
| oldFileName |  OldFileName |De naam van het oude bestand. |
| oldFilePath | OldFilePath | Volledig pad naar het oude bestand, inclusief de bestands naam. <br>Bijvoorbeeld `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` of `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Machtigingen van het oude bestand. |
|oldFileSize | OldFileSize | Grootte van het oude bestand.|
| oldFileType | OldFileType | Het bestands type van het oude bestand, zoals een pipe, socket, enzovoort.|
| uit | SentBytes | Het aantal uitgaande bytes dat is overgedragen. |
| Resultaat | Resultaat | Het resultaat van de gebeurtenis, zoals `success` of `failure` .|
|proto    |  Protocol       | Transport protocol dat het Layer-4-protocol identificeert dat wordt gebruikt. <br><br>Mogelijke waarden zijn protocol namen, zoals `TCP` of `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|CEF-sleutel naam  |CommonSecurityLog-naam  |Description  |
|---------|---------|---------|
|Reden     |  Reden      |De reden waarom een controle gebeurtenis is gegenereerd. <br><br>Bijvoorbeeld `Bad password` of `Unknown user`.         |
|Aanvraag     |   RequestURL      | De URL die wordt gebruikt voor een HTTP-aanvraag, met inbegrip van het protocol. Bijvoorbeeld: `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   De gebruikers agent die is gekoppeld aan de aanvraag.      |
| requestContext | RequestContext | Beschrijft de inhoud waarvan de aanvraag afkomstig is, zoals de HTTP-verwijzings bron. |
| requestCookies | RequestCookies |Cookies die zijn gekoppeld aan de aanvraag. |
| requestMethod | RequestMethod | De methode die wordt gebruikt voor toegang tot een URL. <br><br>Geldige waarden zijn methoden zoals `POST` , `GET` , enzovoort. |
| RT | ReceiptTime | Het tijdstip waarop de gebeurtenis met betrekking tot de activiteit is ontvangen. |
|Severity     |  <a name="logseverity"></a> LogSeverity       |  Een teken reeks of geheel getal dat het belang van de gebeurtenis beschrijft.<br><br> Geldige teken reeks waarden: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>Geldige waarden voor een geheel getal zijn:<br> - `0`-`3` = Laag <br>- `4`-`6` = Gemiddeld<br>- `7`-`8` = Hoog<br>- `9`-`10` = Very-High |
| shost    | SourceHostName        |Identificeert de bron waarnaar de gebeurtenis verwijst in een IP-netwerk. De indeling moet een Fully Qualified Domain Name (DQDN) zijn die is gekoppeld aan het bron knooppunt, wanneer er een knoop punt beschikbaar is. Bijvoorbeeld `host` of `host.domain.com`. |
| smac | SourceMacAddress | Bron-MAC-adres. |
| sntdom | SourceNTDomain | De Windows-domein naam voor het bron adres. |
| sourceDnsDomain | SourceDnsDomain | Het DNS-domein gedeelte van de volledige FQDN. |
| sourceServiceName | SourceServiceName | De service die verantwoordelijk is voor het genereren van de gebeurtenis. |
| sourceTranslatedAddress | SourceTranslatedAddress | Hiermee wordt de vertaalde bron geïdentificeerd waarnaar de gebeurtenis in een IP-netwerk verwijst. |
| sourceTranslatedPort | SourceTranslatedPort | Bron poort na omzetting, zoals een firewall. <br>Geldige poort nummers zijn `0`  -  `65535` . |
| spid's | SourceProcessId | De ID van het bron proces dat is gekoppeld aan de gebeurtenis.|
| spriv | SourceUserPrivileges | De bevoegdheden van de bron gebruiker. <br><br>Geldige waarden zijn: `Administrator` , `User` , `Guest` |
| sproc | SourceProcessName | De naam van het bron proces van de gebeurtenis.|
| spt | SourcePort | Het poort nummer van de bron. <br>Geldige poort nummers zijn `0`  -  `65535` . |
| src | SourceIP |De bron waarnaar een gebeurtenis verwijst in een IP-netwerk als een IPv4-adres. |
| starten | StartTime | Het tijdstip waarop de activiteit waarnaar de gebeurtenis verwijst, wordt gestart. |
| suid | SourceUserID | Hiermee wordt de bron gebruiker op ID geïdentificeerd. |
| suser | SourceUserName | Identificeert de bron gebruiker op naam. |
| type | EventType | Gebeurtenis type. Waarde waarden zijn onder andere: <br>- `0`: basis gebeurtenis <br>- `1`: samengevoegd <br>- `2`: correlatie gebeurtenis <br>- `3`: actie gebeurtenis <br><br>**Opmerking**: deze gebeurtenis kan worden wegge laten voor basis gebeurtenissen. |
| | | |

## <a name="custom-fields"></a>Aangepaste velden

In de volgende tabellen worden de namen van CEF-en CommonSecurityLog-velden die beschikbaar zijn voor klanten, gebruikt voor gegevens die niet van toepassing zijn op een van de ingebouwde velden.

### <a name="custom-ipv6-address-fields"></a>Aangepaste IPv6-adres velden

In de volgende tabel worden CEF sleutel-en CommonSecurityLog namen voor de *IPv6-* adres velden weer gegeven die beschikbaar zijn voor aangepaste gegevens.

|CEF-sleutel naam  |CommonSecurityLog-naam  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Aangepaste numerieke velden

In de volgende tabel worden CEF-en CommonSecurityLog-namen toegewezen voor de *numerieke* velden die beschikbaar zijn voor aangepaste gegevens.

|CEF-sleutel naam  |CommonSecurityLog-naam  |
|---------|---------|
|     cn1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     cn2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     cn3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Aangepaste teken reeks velden

In de volgende tabel worden CEF sleutel-en CommonSecurityLog namen toegewezen voor de *teken reeks* velden die beschikbaar zijn voor aangepaste gegevens.

|CEF-sleutel naam  |CommonSecurityLog-naam  |
|---------|---------|
|     CS1    |     DeviceCustomString1 <sup> [1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup> [1](#use-sparingly)</sup>    |
|     CS2    |     DeviceCustomString2 <sup> [1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label <sup> [1](#use-sparingly)</sup> |
|     CS3    |     DeviceCustomString3 <sup> [1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label <sup> [1](#use-sparingly)</sup> |
|     gebruiken    |     DeviceCustomString4 <sup> [1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label <sup> [1](#use-sparingly)</sup>  |
|     cs5    |     DeviceCustomString5 <sup> [1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup> [1](#use-sparingly)</sup>    |
|     cs6    |     DeviceCustomString6 <sup> [1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label <sup> [1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> we raden u aan om de velden **DeviceCustomString** spaarzaam te gebruiken en waar mogelijk specifieke, ingebouwde velden te gebruiken.
> 

### <a name="custom-timestamp-fields"></a>Aangepaste tijds tempel velden

In de volgende tabel worden CEF sleutel-en CommonSecurityLog namen voor de *tijds tempel* velden die beschikbaar zijn voor aangepaste gegevens, toegewezen.

|CEF-sleutel naam  |CommonSecurityLog-naam  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Aangepaste gegevens velden met gehele getallen

In de volgende tabel worden de namen van de CEF-en CommonSecurityLog weer gegeven voor de velden met *gehele getallen* die beschikbaar zijn voor aangepaste gegevens.

|CEF-sleutel naam  |CommonSecurityLog-naam  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Verrijkings velden

De volgende **CommonSecurityLog** -velden worden toegevoegd door Azure Sentinel om de oorspronkelijke gebeurtenissen te verrijken die van de bron apparaten worden ontvangen, en ze hebben geen toewijzingen in CEF-sleutels:

### <a name="threat-intelligence-fields"></a>Threat Intelligence-velden

|CommonSecurityLog veld naam  |Description  |
|---------|---------|
|   **IndicatorThreatType**  |  Het [MaliciousIP](#MaliciousIP) -bedreigings type, op basis van de bedreigings informatie feed.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | Een lijst met alle IP-adressen in het bericht die overeenkomen met de huidige bedreigings informatie-feed. |
|  **MaliciousIPCountry**   | Het [MaliciousIP](#MaliciousIP) land, op basis van de geografische gegevens op het moment van de opname opname.        |
| **MaliciousIPLatitude**    |   De [MaliciousIP](#MaliciousIP) -lengte, op basis van de geografische gegevens op het moment van de opname opname.      |
| **MaliciousIPLongitude**    |  De [MaliciousIP](#MaliciousIP) -lengte, op basis van de geografische gegevens op het moment van de opname opname.       |
| **ReportReferenceLink**    |    Koppeling naar het rapport van de bedreigings informatie.     |
|  **ThreatConfidence**   |   Het betrouw baarheid van [MaliciousIP](#MaliciousIP) -bedreigingen, volgens de feed voor bedreigings informatie.      |
| **ThreatDescription**    |   De beschrijving van de [MaliciousIP](#MaliciousIP) -bedreiging, volgens de feed voor bedreigings informatie.      |
| **ThreatSeverity** | De bedreigings ernst voor de [MaliciousIP](#MaliciousIP), volgens de bedreigings informatie toevoer op het moment van de opname opname. |
|     |         |

### <a name="additional-enrichment-fields"></a>Aanvullende verrijkings velden

|CommonSecurityLog veld naam  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  Altijd leeg, ondersteund voor integratie met CiscoASA. <br>Zie het veld [LogSeverity](#logseverity) voor meer informatie over de ernst waarden van het logboek.       |
|**RemoteIP**     |     Het externe IP-adres. <br>Deze waarde is, indien mogelijk, gebaseerd op het veld [CommunicationDirection](#communicationdirection) .     |
|**RemotePort**     |   De externe poort. <br>Deze waarde is, indien mogelijk, gebaseerd op het veld [CommunicationDirection](#communicationdirection) .      |
|**SimplifiedDeviceAction**     |   Vereenvoudigt de [DeviceAction](#deviceaction) -waarde naar een statische set waarden, terwijl de oorspronkelijke waarde wordt behouden in het veld [DeviceAction](#deviceaction) . <br>Bijvoorbeeld: `Denied`  >  `Deny` .      |
|**SourceSystem**     | Altijd gedefinieerd als **OpsManager**.        |
|     |         |

## <a name="next-steps"></a>Volgende stappen

Zie [verbinding maken met uw externe oplossing met de algemene gebeurtenis indeling](connect-common-event-format.md)voor meer informatie.
