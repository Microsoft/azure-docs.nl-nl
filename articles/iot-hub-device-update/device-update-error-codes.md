---
title: Client fout codes voor het bijwerken van het apparaat voor Azure IoT Hub | Microsoft Docs
description: Dit document bevat een tabel met client fout codes voor diverse onderdelen voor het bijwerken van apparaten.
author: lichris
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: 5251d0cb09e40305d1efd89c31d3af0fa36ad385
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662669"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Update van het apparaat voor IoT Hub fout codes

Dit document bevat een tabel met fout codes voor diverse onderdelen voor het bijwerken van apparaten. Dit is bedoeld om te worden gebruikt als referentie voor gebruikers die willen proberen hun eigen fout codes te parseren om problemen op te sporen en op te lossen.

Er zijn twee primaire onderdelen aan de client zijde die fout codes kunnen genereren: de Update Agent van het apparaat en de agent voor het leveren van de levering.

## <a name="device-update-agent"></a>Update-Agent van apparaat

### <a name="resultcode-and-extendedresultcode"></a>ResultCode en ExtendedResultCode

De update van het apparaat voor IoT Hub core PnP `ResultCode` -Interface rapporten en `ExtendedResultCode` , die kan worden gebruikt voor het vaststellen van fouten. Meer [informatie](device-update-plug-and-play.md) over de apparaat-update kern PnP-interface.

#### <a name="resultcode"></a>ResultCode

`ResultCode` is een algemene status code en volgt de HTTP-status code Conventie.
Meer [informatie](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) over HTTP-status codes.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` is een geheel getal met gecodeerde fout gegevens.

Waarschijnlijk ziet u het `ExtendedResultCode` als een ondertekend geheel getal in de PnP-interface. Als u de wilt decoderen `ExtendedResultCode` , converteert u de ondertekende integer naar niet-ondertekende HEX. Alleen de eerste vier bytes van de `ExtendedResultCode` worden gebruikt en zijn van het formulier `F` `FFFFFFF` waarbij de eerste Nibble de **faciliteit code** is en de rest van de bits de **fout code**.

**Faciliteit codes**

| Faciliteit code     | Beschrijving  |
|-------------------|--------------|
| D                 | Er is een fout opgetreden van de DO-SDK|
| E                 | Fout code is een errno |


Bijvoorbeeld:

`ExtendedResultCode` is `-536870781`

De niet-ondertekende hex-weer gave van `-536870781` is `FFFFFFFF E0000083` .

| Negeren    | Faciliteit code  | Foutcode   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` in hex is `131` een decimale waarde. Dit is de errnowaarde voor `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Delivery Optimization agent
De volgende tabel bevat de fout codes die betrekking hebben op het onderdeel Delivery Optimization (DO) van de client voor het bijwerken van het apparaat. Het onderdeel DO is verantwoordelijk voor het downloaden van update-inhoud naar het IoT-apparaat.

De fout code ' kan worden verkregen door de uitzonde ringen die worden gegenereerd als reactie op een API-aanroep te controleren.

| Foutcode  | Teken reeks fout                       | Type                 | Beschrijving |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | n.v.t.                  | De service kan niet worden geleverd met de leverings optimalisatie |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Download taak         | Het downloaden van een bestand zag geen voortgang binnen de gedefinieerde periode |
| 0x80D02003L | DO_E_JOB_NOT_FOUND                 | Download taak         | Taak is niet gevonden |
| 0x80D02005L | DO_E_NO_DOWNLOADS                  | Download taak         | Er zijn momenteel geen down loads |
| 0x80D0200CL | DO_E_JOB_TOO_OLD                   | Download taak         | De taak is niet voltooid of geannuleerd vóór het bereiken van de drempel waarde voor de maximale leeftijd |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Download taak         | SetProperty () of GetProperty () aangeroepen met een onbekende eigenschaps-ID |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Download taak         | Kan SetProperty () niet aanroepen voor een alleen-lezen eigenschap |
| 0x80D02013L | DO_E_INVALID_STATE                 | Download taak         | De aangevraagde actie is niet toegestaan in de huidige taak status. De taak is mogelijk geannuleerd of is overgedragen. De status is nu alleen-lezen. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Download taak         | Kan het downloaden niet starten omdat er geen Sink voor downloaden (lokaal bestand of stream-Interface) is opgegeven |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload-interface| Het downloaden is gestart zonder een URI op te geven |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Tijdelijke omstandigheden | Downloaden onderbroken wegens verlies van netwerk verbinding |
| 0x80D05001L | DO_E_HTTP_BLOCKSIZE_MISMATCH       | HTTP                 | HTTP-server heeft een antwoord geretourneerd met een gegevens grootte die niet gelijk is aan wat is aangevraagd |
| 0x80D05002L | DO_E_HTTP_CERT_VALIDATION          | HTTP                 | Validatie van het HTTP-server certificaat is mislukt |
| 0x80D05010L | DO_E_INVALID_RANGE                 | HTTP                 | Het opgegeven bereik van de byte is ongeldig |
| 0x80D05011L | DO_E_INSUFFICIENT_RANGE_SUPPORT    | HTTP                 | De server biedt geen ondersteuning voor het benodigde HTTP-protocol. Voor Delivery Optimization (DO) moet de server de protocol header Range ondersteunen |
| 0x80D05012L | DO_E_OVERLAPPING_RANGES            | HTTP                 | De lijst met byte bereiken bevat overlappende bereiken die niet worden ondersteund |
## <a name="device-update-content-service"></a>Inhouds service voor het bijwerken van apparaten
De volgende tabel bevat de fout codes die betrekking hebben op het onderdeel content service van de service voor het bijwerken van apparaten. Het onderdeel content service is verantwoordelijk voor het verwerken van het importeren van update-inhoud.

| Foutcode                    | Teken reeks fout                                                               | Volgende stappen                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Er bestaat al een update met dezelfde identiteit.                              | Zorg ervoor dat u een update importeert die nog niet is geïmporteerd in dit exemplaar van de update van het apparaat voor IoT Hub. |
| "DuplicateContentImport"      | Identieke inhoud tegelijk meerdere keren geïmporteerd.                  | Hetzelfde als voor UpdateAlreadyExists. |
| "CannotProcessImportManifest" | Fout bij het verwerken van het import manifest.                                          | Raadpleeg [concepten importeren](./import-concepts.md) en de documentatie over het importeren van [updates](./import-update.md) voor de juiste indeling van het import manifest. |
| "CannotDownload"              | Het import manifest kan niet worden gedownload.                                           | Controleer of de URL voor het manifest bestand voor importeren nog geldig is. |
| "CannotParse"                 | Het import manifest kan niet worden geparseerd.                                              | Controleer uw import manifest op nauw keurigheid van het schema dat is gedefinieerd in de documentatie over het [importeren van updates](./import-update.md) . |
| "UnsupportedVersion"          | De schema versie voor het importeren van het manifest wordt niet ondersteund.                           | Zorg ervoor dat het import manifest het meest recente schema gebruikt dat is gedefinieerd in de documentatie over het importeren van de [Update](./import-update.md) . |
| "UpdateLimitExceeded"         | Fout bij het importeren van de update omdat de limiet is overschreden.                              | U hebt een limiet bereikt voor het aantal verschillende providers, namen of versies die zijn toegestaan in uw exemplaar van de update van het apparaat voor IoT Hub. Verwijder enkele updates uit uw exemplaar en probeer het opnieuw. |
| "UpdateProvider"              | Er kan geen nieuwe update provider worden geïmporteerd.                                       | U hebt een limiet bereikt voor het aantal verschillende __providers__ dat is toegestaan in uw exemplaar van het bijwerken van het apparaat voor IOT hub. Verwijder enkele updates uit uw exemplaar en probeer het opnieuw. |
| "Updatenaam"                  | Kan geen nieuwe update naam importeren voor de opgegeven provider.                | U hebt een limiet bereikt voor het aantal verschillende __namen__ dat is toegestaan onder een provider in uw exemplaar van de update van het apparaat voor IOT hub. Verwijder enkele updates uit uw exemplaar en probeer het opnieuw. |
| "UpdateVersion"               | Kan geen nieuwe update versie importeren voor de opgegeven provider en naam.    | U hebt een limiet bereikt voor het aantal verschillende __versies__ dat is toegestaan onder één provider en naam in uw exemplaar van de update van het apparaat voor IOT hub. Verwijder een aantal updates met die naam uit uw exemplaar en probeer het opnieuw. |
| "UpdateProviderCompatibility" | Er kan geen aanvullende update provider met de opgegeven compatibiliteit worden geïmporteerd. | Wanneer u de compatibiliteits eigenschappen van de fabrikant en het model van het apparaat in een import manifest definieert, moet u er ook voor zorgen dat update van het apparaat voor IoT Hub een combi natie van een provider en naam ondersteunt voor een bepaalde fabrikant/model. Dit betekent dat als u dezelfde compatibiliteits eigenschappen voor de fabrikant en het model wilt gebruiken met meer dan één combi natie van provider/naam, deze fouten worden weer geven. Om dit probleem op te lossen, moet u ervoor zorgen dat alle updates voor een bepaald apparaat (zoals gedefinieerd door fabrikant/model) dezelfde provider en naam gebruiken. Hoewel dit niet vereist is, kunt u overwegen om de provider hetzelfde te maken als de fabrikant en de naam op dezelfde manier als het model, alleen voor eenvoud. |
| "UpdateNameCompatibility"     | Kan geen aanvullende update naam met de opgegeven compatibiliteit importeren.     | Hetzelfde als voor UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "UpdateVersionCompatibility"  | Er kan geen aanvullende update versie met de opgegeven compatibiliteit worden geïmporteerd.  | Hetzelfde als voor UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "CannotProcessUpdateFile"     | Fout bij het verwerken van het bron bestand.                                              |                                    |
| "ContentFileCannotDownload"   | Kan het bron bestand niet downloaden.                                               | Controleer of de URL voor de update bestanden nog geldig is. |

**[Volgende stap: problemen oplossen met updates van het apparaat](.\troubleshoot-device-update.md)**