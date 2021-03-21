---
title: Azure Media Services verpakkings-en oorsprongs fouten
description: In dit onderwerp worden fouten beschreven die u kunt ontvangen van de Azure Media Services streaming endpoint-service (Orgin).
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 994e5ae0647f350e0a64f35318bd5803f4ed79b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98894980"
---
# <a name="streaming-endpoint-origin-errors"></a>Fouten met streaming-eindpunten (oorsprong)

In dit onderwerp worden fouten beschreven die mogelijk van de Azure Media Services [streaming endpoint-service](streaming-endpoint-concept.md)worden weer gegeven.

## <a name="400-bad-request"></a>400 Ongeldige aanvraag

De aanvraag bevat ongeldige gegevens en wordt geweigerd met deze fout codes en om een van de volgende redenen:

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Een URL-syntaxis of indelings fout. Voor beelden zijn aanvragen voor een ongeldig type, een ongeldig fragment of een ongeldig nummer. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|De aanvraag heeft geen versleutelings code in de URL. CMAF-aanvragen vereisen een versleutelings code in de URL. Andere protocollen die zijn geconfigureerd met meer dan één versleutelings type, hebben ook de versleutelings code voor ondubbelzinnige configuratie nodig. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|De aanvraag voor opslag om te voldoen aan de aanvraag is mislukt met een onjuiste aanvraag fout. |

## <a name="403-forbidden"></a>403 Verboden

De aanvraag is niet toegestaan vanwege een van de volgende redenen:

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|De aanvraag voor opslag om te voldoen aan de aanvraag is mislukt vanwege een verificatie fout. Dit kan gebeuren als de opslag sleutels zijn gedraaid en de service de opslag sleutels niet kan synchroniseren. <br/><br/>Neem contact op met de ondersteuning van Azure door te gaan naar [Help en ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in de Azure Portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Opslag bewerkings fout, toegang is mislukt vanwege onvoldoende account machtigingen. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |De aanvraag voor opslag om te voldoen aan de aanvraag is mislukt, omdat het opslag account is uitgeschakeld. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Opslag bewerkings fout, toegang is mislukt vanwege algemene fouten. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |De uitvoer indeling wordt geblokkeerd vanwege de configuratie in de StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Versleuteling is vereist voor de inhoud, het leverings beleid is vereist voor de uitvoer indeling. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Versleuteling is niet ingesteld in de instellingen voor het leverings beleid. |

## <a name="404-not-found"></a>404 Niet gevonden

De bewerking probeert uit te voeren op een resource die niet meer bestaat. De resource is bijvoorbeeld mogelijk al verwijderd.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Het aangevraagde spoor is niet gevonden. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |De aangevraagde resource is niet gevonden. |
|MPE_UNAUTHORIZED |0x80890244 |De toegang is niet geautoriseerd. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |De aangevraagde tijds tempel is niet gevonden. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Het aangevraagde dynamische manifest filter is niet gevonden. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |De aangevraagde fragment index ligt buiten het geldige bereik. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Er zijn geen Live Media-vermeldingen gevonden om de Moov-buffer op te halen. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Kan het fragment niet op de aangevraagde tijd voor een bepaald spoor vinden.<br/><br/>Dit kan zijn dat het fragment zich niet in de opslag ruimte bevindt. Probeer een andere laag van de presentatie die een fragment kan hebben. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Kan de media vermelding voor de aangevraagde bitrate niet vinden in het manifest. <br/><br/>Mogelijk is de speler gevraagd om een video spoor van een bepaalde bitrate die niet in het manifest voor komt.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Kan bepaalde meta gegevens niet vinden in het manifest of kan niet worden teruggevonden uit de opslag. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Opslag bewerkings fout, bron niet gevonden. |

## <a name="409-conflict"></a>409 Conflict

De ID die voor een resource voor een `PUT` of `POST` bewerking is gegeven, is door een bestaande resource genomen. Gebruik een andere ID voor de resource om dit probleem op te lossen.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Opslag bewerkings fout, conflict fout.  |

## <a name="410"></a>410

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Voor live streamen, wanneer het filter waarop forceEndTimestamp is ingesteld op True, het begin-of eind tijds tempel zich buiten het huidige DVR-venster bevindt.|

## <a name="412-precondition-failure"></a>412 voorwaardes fout

Met de bewerking is een eTag opgegeven die afwijkt van de versie die beschikbaar is op de server, dat wil zeggen een optimistische gelijktijdigheids fout. Voer de aanvraag opnieuw uit nadat u de meest recente versie van de resource hebt gelezen en de eTag voor de aanvraag hebt bijgewerkt.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Het aangevraagde fragment is niet gereed.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Er is een fout opgetreden bij de opslag bewerking.|

## <a name="415-unsupported-media-type"></a>415 niet-ondersteund media type

De indeling van de nettolading die door de client wordt verzonden, heeft een niet-ondersteunde indeling.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Er mag geen versleuteling worden toegepast op al versleutelde inhoud.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |De versleuteling is ongeldig voor de invoer indeling.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Het type leverings beleid is ongeldig.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |De oorspronkelijke instellingen kunnen worden gedeeld door meerdere uitvoer indelingen.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|De media-indeling of het-type wordt niet ondersteund. Media Services biedt bijvoorbeeld geen ondersteuning voor het aantal kwaliteits niveaus dat groter is dan 64. In FLV video tag biedt Media Services geen ondersteuning voor een video frame met meerdere SPS en meerdere PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| De aangevraagde invoer indeling van Asset wordt niet ondersteund. Media Services ondersteunt indelingen voor vloeiende (Live), MP4 (VoD) en progressieve down loads.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|De aangevraagde uitvoer indeling wordt niet ondersteund. Media Services ondersteunt de indelingen Smooth, DASH (KVP, CMAF), HLS (v3, v4, CMAF) en progressieve down load.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Er is een niet-ondersteund versleutelings type aangetroffen.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Het aangevraagde medium type wordt niet ondersteund door de uitvoer indeling. De ondersteunde typen zijn de subtitel video, audio of SUBT.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|De bron Asset-media zijn gecodeerd met een medium indeling die niet compatibel is met de uitvoer indeling.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|De bron Asset is gecodeerd met een video-indeling die niet compatibel is met de uitvoer indeling. H. 264, AVC, H. 265 (HEVC, hev1 of hvc1) worden ondersteund.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|De bron Asset is gecodeerd met een audio-indeling die niet compatibel is met de uitvoer indeling. Ondersteunde audio-indelingen zijn AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|De bron beveiligde Asset kan niet worden geconverteerd naar de uitvoer indeling.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|De beveiligings indeling wordt niet ondersteund door de uitvoer indeling.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|De beveiligings indeling wordt niet ondersteund door de invoer indeling.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Ongeldige video-NAL-eenheid, bijvoorbeeld alleen de eerste NAL in het voor beeld kan een AUD zijn.|
|MPE_INVALID_NALU_SIZE|0x80890260|Ongeldige grootte van het NAL-eenheid.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Ongeldige waarde voor de NAL-eenheids lengte.|
|MPE_FILTER_INVALID|0x80890236|Ongeldige filters voor dynamische manifesten.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Ongeldige of niet-ondersteunde filter versies.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Ongeldig filter type.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Er is een ongeldig bereik opgegeven door het filter.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Ongeldig spoor kenmerk opgegeven door het filter.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Ongeldige lengte van presentatie venster opgegeven door het filter.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Ongeldige Live-back-ups opgegeven door het filter.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Er wordt slechts één absTimeInHNS-element ondersteund in verouderde filters.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Er zijn helemaal geen streams meer na het Toep assen van de filters.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|De Live-uitschakeling bevindt zich buiten het DVR-venster.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|De Live-back-up is groter dan het presentatie venster.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Het maximum aantal toegestane tien (10) standaard filters is overschreden.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Meerdere eerste video kwaliteits operator is niet toegestaan in gecombineerde aanvraag filters.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Het aantal bitsnelheid voor eerste kwaliteit moet één (1) zijn.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|De HLS-segment duur moet kleiner zijn dan een derde van het DVR-venster en HLS-back-up.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Fragment duur moet kleiner zijn dan of gelijk zijn aan ongeveer 20 seconden, of de invoer kwaliteits niveaus zijn niet tijd uitgelijnd.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-specifieke fout, kan de ReservedBox niet vinden wanneer deze tijdens het parseren van het DTS-vak aanwezig zou moeten zijn in de DTSSpecficBox.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-specifieke fout: er zijn geen kanalen gevonden in de DTSSpecficBox tijdens het parseren van het DTS-vak.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-specifieke fout, type voor beeld komt niet overeen in de DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-specifieke fout, meerdere assets zijn ingesteld, maar de DTSH-voorbeeld typen komen niet overeen.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-specifieke fout. de grootte van de kern stroom is ongeldig.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-specifieke fout, voorbeeld resolutie is ongeldig.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-specifieke fout, extensie-index van substream is ongeldig.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-specifieke fout, blok nummer van substream is ongeldig.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-specifieke fout, sampling frequentie is ongeldig.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-specifieke fout, de referentie klok code in de extensie voor substreams is ongeldig.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifieke fout, het aantal ingestelde luid sprekers is ongeldig.|

Voor versleutelings artikelen en voor beelden raadpleegt u:

- [Concept: beveiliging van inhoud](content-protection-overview.md)
- [Concept: beleid voor inhouds sleutels](content-key-policy-concept.md)
- [Concept: streaming-beleid](streaming-policy-concept.md)
- [Voor beeld: beveiligen met AES-versleuteling](protect-with-aes128.md)
- [Voor beeld: beveiligen met DRM](protect-with-drm.md)

Zie voor richt lijnen voor filters:

- [Concept: dynamische manifesten](filters-dynamic-manifest-overview.md)
- [Concept: filters](filters-concept.md)
- [Voor beeld: filters maken met REST-Api's](filters-dynamic-manifest-rest-howto.md)
- [Voor beeld: filters maken met .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Voor beeld: filters maken met CLI](filters-dynamic-manifest-cli-howto.md)

Voor Live-artikelen en voor beelden raadpleegt u:

- [Concept: overzicht van live streams](live-streaming-overview.md)
- [Concept: live-evenementen en live uitvoer](live-events-outputs-concept.md)
- [Voor beeld: zelf studie voor live streamen](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Het aangevraagde bereik is niet geldig

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Opslag bewerkings fout, geretourneerde http 416-fout, ongeldig bereik.|

## <a name="500-internal-server-error"></a>500 Interne serverfout

Tijdens de verwerking van de aanvraag wordt door Media Services een fout aangetroffen waardoor de verwerking niet kan worden voortgezet.  

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Ontvangen en vertaald van de WinHTTP-fout code ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Ontvangen en vertaald van de WinHTTP-fout code ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Ontvangen en vertaald van de WinHTTP-fout code ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Opslag bewerkings fout, algemene InternalError van een van de HTTP 500-fouten.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Opslag bewerkings fout, algemene OperationTimedOut van een van de HTTP 500-fouten.|
|MPE_STORAGE_FAILURE|0x808900F2|Opslag bewerkings fout, andere HTTP 500-fouten dan InternalError of OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Service niet beschikbaar

De server kan momenteel geen aanvragen ontvangen. Deze fout kan zijn veroorzaakt door een uitzonderlijk hoog aantal aanvragen bij de service. Het resourceverbruik wordt met het beperkingsmechanisme van Media Services beperkt voor toepassingen die een uitzonderlijk hoog aantal aanvragen doen bij de service.

> [!NOTE]
> Controleer het foutbericht en de tekenreeks voor de foutcode voor meer gedetailleerde informatie over de reden waarom u de 503-fout hebt ontvangen. Deze fout duidt niet altijd op beperking.
> 

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Opslag bewerkings fout, ontvangen HTTP-server fout 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="see-also"></a>Zie ook

- [Foutcodes voor codering](/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services concepten](concepts-overview.md)
- [Quota en limieten](limits-quotas-constraints.md)

## <a name="next-steps"></a>Volgende stappen

[Voor beeld: toegang tot error code en bericht van ApiException met .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
