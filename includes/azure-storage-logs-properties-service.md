---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011129"
---
| Eigenschap | Beschrijving |
|:--- |:---|
|**accountName** | De naam van het opslag account. Bijvoorbeeld: `mystorageaccount`.  |
|**requestUrl** | De aangevraagde URL. |
|**userAgentHeader** | De waarde van de **User-Agent-header** , in aanhalings tekens. Bijvoorbeeld: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | De waarde van de **verwijzende** header. Bijvoorbeeld: `http://contoso.com/about.html`.|
|**clientRequestId** | De waarde voor de **x-MS-Client-Request-id** van de aanvraag. Bijvoorbeeld: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**ETAG** | De ETag-id voor het geretourneerde object, in aanhalings tekens. Bijvoorbeeld: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | De totale tijd in milliseconden voor het uitvoeren van de aangevraagde bewerking. Deze waarde omvat geen netwerk latentie (de tijd voor het lezen van de inkomende aanvraag en het verzenden van het antwoord naar de aanvrager). Bijvoorbeeld: `22`. |
|**Service** | De service die aan deze aanvraag is gekoppeld. Bijvoorbeeld: `blob` ,, `table` `files` of `queue` . |
|**operationCount** | Het aantal geregistreerde bewerkingen dat is betrokken bij de aanvraag. Dit aantal begint met een index van `0` . Voor sommige aanvragen is meer dan één bewerking vereist. De meeste aanvragen voeren slechts één bewerking uit. Bijvoorbeeld: `1`. |
|**requestHeaderSize** | De grootte van de aanvraag header, uitgedrukt in bytes. Bijvoorbeeld: `578`. <br>Als een aanvraag mislukt, kan deze waarde leeg zijn. |
|**requestBodySize** | De grootte van de aanvraag pakketten, uitgedrukt in bytes, die door de opslag service worden gelezen. <br> Bijvoorbeeld: `0`. <br>Als een aanvraag mislukt, kan deze waarde leeg zijn.  |
|**responseHeaderSize** | De grootte van de reactie header, uitgedrukt in bytes. Bijvoorbeeld: `216`. <br>Als een aanvraag mislukt, kan deze waarde leeg zijn.  |
|**responseBodySize** | De grootte van de antwoord pakketten, geschreven door de opslag service, in bytes. Als een aanvraag mislukt, kan deze waarde leeg zijn. Bijvoorbeeld: `216`.  |
|**requestMd5** | De waarde van de kop **Content-MD5** of de header **x-MS-content-MD5** in de aanvraag. De MD5-hash-waarde die is opgegeven in dit veld vertegenwoordigt de inhoud in de aanvraag. Bijvoorbeeld: `788815fd0198be0d275ad329cafd1830`. <br>Dit veld mag leeg zijn.  |
|**serverMd5** | De waarde van de MD5-hash die wordt berekend door de opslag service. Bijvoorbeeld: `3228b3cf1069a5489b298446321f8521`. <br>Dit veld mag leeg zijn.  |
|**lastModifiedTime** | De tijd van de laatste wijziging (LMT) van het geretourneerde object.  Bijvoorbeeld: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Dit veld is leeg voor bewerkingen die meerdere objecten kunnen retour neren. |
|**conditionsUsed** | Een door punt komma's gescheiden lijst met sleutel-waardeparen die een voor waarde vertegenwoordigen. De voor waarden kunnen een van de volgende oorzaken hebben: <li> If-Modified-sinds <li> Als-ongewijzigd-sinds <li> If-Match <li> If-None-Match  <br> Bijvoorbeeld: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | De waarde van de content-length-header voor de aanvraag die naar de opslag service wordt verzonden. Als de aanvraag is voltooid, is deze waarde gelijk aan requestBodySize. Als een aanvraag mislukt, is deze waarde mogelijk niet gelijk aan requestBodySize of kan deze leeg zijn. |
|**tlsVersion** | De TLS-versie die wordt gebruikt in de verbinding van de aanvraag. Bijvoorbeeld: `TLS 1.2`. |
|**smbTreeConnectID** | De SMB- **treeConnectId** (Server Message Block) is ingesteld tijdens de structuur verbindings tijd. Bijvoorbeeld: `0x3` |
|**smbPersistentHandleID** | De permanente ingangs-ID van een SMB2-aanvraag maken die de netwerk verbinding verbreekt.  Ernaar wordt verwezen in [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 als **SMB2_FILEID. Permanent**. Bijvoorbeeld: `0x6003f` |
|**smbVolatileHandleID** | Vluchtige ingangs-ID van een SMB2 maken-aanvraag die wordt gerecycled bij het opnieuw verbinden met het netwerk.  Ernaar wordt verwezen in [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 als **SMB2_FILEID. Vluchtig**. Bijvoorbeeld: `0xFFFFFFFF00000065` |
|**smbMessageID** | De verbinding met de relatieve **MessageId**. Bijvoorbeeld: `0x3b165` |
|**smbCreditsConsumed** | De ingang of uitgave die door de aanvraag wordt verbruikt in eenheden van 64 kB. Bijvoorbeeld: `0x3` |
|**smbCommandDetail** | Meer informatie over deze specifieke aanvraag in plaats van het algemene type aanvraag. Bijvoorbeeld: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | De **Bestands identificatie** die aan het bestand of de map is gekoppeld.  Ongeveer hetzelfde als een NTFS-bestands-id. Bijvoorbeeld: `0x9223442405598953` |
|**smbSessionID** | De SMB2- **sessie-id** is ingesteld tijdens de installatie van de sessie. Bijvoorbeeld: `0x8530280128000049` |
|**smbCommandMajor uint32** | Waarde in de **SMB2_HEADER. opdracht**. Dit is momenteel een getal tussen 0 en 18, inclusief. Bijvoorbeeld: `0x6` |
|**smbCommandMinor** | , Indien van toepassing, de subklasse van **SmbCommandMajor**. Bijvoorbeeld: `DirectoryCloseAndDelete` |