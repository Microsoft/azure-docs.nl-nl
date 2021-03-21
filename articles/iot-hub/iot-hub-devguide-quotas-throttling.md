---
title: Meer informatie over Azure IoT Hub quota's en beperking | Microsoft Docs
description: "Ontwikkelaars handleiding: beschrijving van de quota's die van toepassing zijn op IoT Hub en het verwachte beperkings gedrag."
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 4b65d42522f40eb7d0e65356223313a924de3039
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656988"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referentie-IoT Hub quota's en beperking

In dit artikel worden de quota's voor een IoT Hub beschreven en vindt u informatie over het beperken van de werking van de functie.

## <a name="quotas-and-throttling"></a>Quota en beperkingen

Elk Azure-abonnement kan Maxi maal 50 IoT hubs hebben en Maxi maal één gratis hub.

Elke IoT-hub is ingericht met een bepaald aantal eenheden in een bepaalde laag. De laag en het aantal eenheden bepalen het maximale dagelijkse quotum van berichten dat u kunt verzenden. De grootte van het bericht dat wordt gebruikt voor het berekenen van het dagelijkse quotum is 0,5 KB voor een gratis hub en 4KB voor alle andere lagen. Zie [prijzen van Azure IOT hub](https://azure.microsoft.com/pricing/details/iot-hub/)voor meer informatie.

De laag bepaalt ook de beperkings limieten die IoT Hub afgedwongen voor alle bewerkingen.

## <a name="iot-plug-and-play"></a>IoT Plug and Play

IoT Plug en Play-apparaten verzenden ten minste één telemetrie-bericht voor elke interface, met inbegrip van de hoofdmap, waardoor het aantal berichten dat kan worden meegeteld voor uw bericht quotum kan toenemen.

## <a name="operation-throttles"></a>Bewerking beperken

Bewerkings vertraging zijn frequentie beperkingen die worden toegepast in minutes en zijn bedoeld om misbruik te voor komen. Ze zijn ook onderhevig aan [verkeers vorming](#traffic-shaping).

De volgende tabel geeft de afgedwongen vertragingen weer. Waarden verwijzen naar een afzonderlijke hub.

| Vertragen | Gratis, B1 en S1 | B2 en S2 | B3 en S3 | 
| -------- | ------- | ------- | ------- |
| [Identiteits register bewerkingen](#identity-registry-operations-throttle) (maken, ophalen, weer geven, bijwerken, verwijderen) | 1.67 per seconde per eenheid (100 per minuut/eenheid) | 1.67 per seconde per eenheid (100 per minuut/eenheid) | 83.33 per seconde per eenheid (5000/min per eenheid) |
| [Nieuwe verbindingen voor apparaten](#device-connections-throttle) (deze limiet geldt voor de frequentie van _nieuwe verbindingen_, niet het totale aantal verbindingen) | Hoger van 100 per seconde of 12 per seconde per eenheid <br/> Zo zijn twee S1-eenheden 2 \* 12 = 24 nieuwe verbindingen per seconde, maar u hebt ten minste 100 nieuwe verbindingen per seconde over uw eenheden. Met negen S1-eenheden hebt u 108 nieuwe verbindingen per seconde (9 \* 12) in uw eenheden. | 120 nieuwe verbindingen per seconde per eenheid | 6.000 nieuwe verbindingen per seconde per eenheid |
| Apparaat-naar-cloud verzendt | Meer dan 100 verzend bewerkingen per seconde of 12 verzend bewerkingen per seconde/eenheid <br/> Bijvoorbeeld: twee S1-eenheden zijn 2 \* 12 = 24 per seconde, maar u hebt ten minste 100 verzend bewerkingen per seconde over uw eenheden. Met negen S1-eenheden hebt u 108 verzend bewerkingen per seconde (9 \* 12) in uw eenheden. | 120 verzend bewerkingen per seconde per eenheid | 6.000 verzend bewerkingen per seconde per eenheid |
| Verzenden van Cloud naar apparaat<sup>1</sup> | 1,67 verzend bewerkingen per seconde/eenheid (100 berichten/minuut/eenheid) | 1,67 verzend bewerkingen per seconde/eenheid (100 verzend bewerkingen per minuut/eenheid) | 83,33 verzend bewerkingen per seconde/eenheid (5.000 verzend bewerkingen per minuut/eenheid) |
| Cloud-naar-apparaat ontvangt<sup>1</sup> <br/> (alleen wanneer het apparaat HTTPS gebruikt)| 16,67 receive-bewerkingen per seconde/eenheid (1.000-ontvangst bewerkingen/min/eenheid) | 16,67 receive-bewerkingen per seconde/eenheid (1.000-ontvangst bewerkingen/min/eenheid) | 833,33 receive-bewerkingen per seconde/eenheid (50.000-ontvangst bewerkingen/min/eenheid) |
| Bestand uploaden | 1,67 initiëringen van het uploaden van bestanden per seconde per eenheid (100/min/eenheid) | 1,67 initiëringen van het uploaden van bestanden per seconde per eenheid (100/min/eenheid) | 83,33 initiëringen van het uploaden van bestanden per seconde per eenheid (5000/min per eenheid) |
| Directe methoden<sup>1</sup> | 160KB per seconde/eenheid<sup>2</sup> | 480KB per seconde/eenheid<sup>2</sup> | 24MB per seconde/eenheid<sup>2</sup> | 
| Query's | 20 per minuut/eenheid | 20 per minuut/eenheid | 1000/min per eenheid |
| Dubbele Lees bewerkingen (apparaat en module)<sup>1</sup> | 100 per seconde | Hoger van 100 per seconde of 10 per seconde per eenheid | 500 per seconde per eenheid |
| Dubbele updates (apparaat en module)<sup>1</sup> | 50 per seconde | Meer dan 50 per seconde of 5 per seconde per eenheid | 250 per seconde per eenheid |
| Taak bewerkingen<sup>1</sup> <br/> (maken, bijwerken, weergeven, verwijderen) | 1.67 per seconde per eenheid (100 per minuut/eenheid) | 1.67 per seconde per eenheid (100 per minuut/eenheid) | 83.33 per seconde per eenheid (5000/min per eenheid) |
| Bewerkingen voor taken apparaat<sup>1</sup> <br/> (update dubbele, direct-methode aanroepen) | 10 per seconde | Hoger van 10 per seconde of 1 per seconde per eenheid | 50 per seconde per eenheid |
| Configuraties en Edge-implementaties<sup>1</sup> <br/> (maken, bijwerken, weergeven, verwijderen) | 0.33 per seconde per eenheid (20 per minuut/eenheid) | 0.33 per seconde per eenheid (20 per minuut/eenheid) | 0.33 per seconde per eenheid (20 per minuut/eenheid) |
| Start frequentie van Device stream<sup>1</sup> | 5 nieuwe streams per seconde | 5 nieuwe streams per seconde | 5 nieuwe streams per seconde |
| Maxi maal aantal gelijktijdig verbonden apparaat stromen<sup>1</sup> | 50 | 50 | 50 |
| Maximale stroom voor gegevens overdracht van apparaatgegevens<sup>1</sup> (samengevoegd volume per dag) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Deze functie is niet beschikbaar in de laag basis van IoT Hub. Zie [de juiste IOT hub kiezen](iot-hub-scaling.md)voor meer informatie. <br/><sup>2</sup> De grootte van de beperkings meter is 4 KB. Beperking is alleen gebaseerd op de grootte van de aanvraag-nettolading.

### <a name="throttling-details"></a>Beperkings Details

* De meter grootte bepaalt op welke stappen uw beperkings limiet wordt verbruikt. Als de nettolading van uw directe oproep tussen 0 en 4 KB ligt, wordt deze geteld als 4 KB. U kunt Maxi maal 40 aanroepen per seconde per eenheid maken voordat u de limiet van 160 KB per seconde per eenheid aankomt.

   Op dezelfde manier geldt dat als uw nettolading tussen 4 KB en 8 KB ligt, elke oproep account voor 8 KB en u Maxi maal 20 aanroepen per seconde per eenheid mag maken voordat de maximum limiet wordt bereikt.

   Ten slotte, als de grootte van de nettolading tussen 156KB en 160 KB ligt, kunt u slechts één oproep per seconde per eenheid in uw hub maken voordat u de limiet van 160 KB per seconde/eenheid aanroept.

*  Voor *bewerkingen voor taken apparaten (update dubbele, directe methode aanroepen)* voor tier S3 geldt dat 50 per seconde per eenheid alleen van toepassing is wanneer u methoden aanroept met behulp van taken. Als u rechtstreeks directe methoden aanroept, geldt de oorspronkelijke beperkings limiet van 24 MB per seconde per eenheid (voor S3).

*  **Quota** is het totale aantal berichten dat *per dag* in uw hub kan worden verzonden. U kunt de quotum limiet van uw hub vinden onder het kolom **Totaal aantal berichten/dag** op de [pagina met IOT hub prijzen](https://azure.microsoft.com/pricing/details/iot-hub/).

*  De beperkings limieten voor Cloud-naar-apparaat-en apparaat-naar-Cloud bepalen *het maximum aantal* berichten dat kan worden verzonden, ongeacht de 4 KB-segmenten. Elk bericht kan Maxi maal 256 KB groot zijn. Dit is de [maximale bericht grootte](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Het is een goed idee om uw oproepen te beperken, zodat u de beperkings limieten niet bereikt of overschrijdt. Als u de limiet bereikt, reageert IoT Hub met fout code 429. de client moet er een back-up van maken en het opnieuw proberen. Deze limieten zijn per hub (of in sommige gevallen per hub/eenheid). Raadpleeg [connectiviteit en betrouw bare patronen voor berichten/nieuwe pogingen beheren](iot-hub-reliability-features-in-sdks.md#retry-patterns)voor meer informatie.

### <a name="traffic-shaping"></a>Verkeers vorming

Om burst-verkeer te kunnen verwerken, worden de aanvragen voor een beperkte tijd boven de gashendel geaccepteerd IoT Hub. De eerste enkele van deze aanvragen worden direct verwerkt. Als het aantal aanvragen echter de vertraging overloopt, IoT Hub begint met het plaatsen van de aanvragen in een wachtrij en verwerkt tegen de limiet snelheid. Dit effect wordt *Traffic Shaping* genoemd. Bovendien is de grootte van deze wachtrij beperkt. Als de fout met de vertraging wordt vervolgd, wordt de wachtrij uiteindelijk gevuld en IoT Hub wordt het weigeren van aanvragen gestart `429 ThrottlingException` .

U gebruikt bijvoorbeeld een gesimuleerd apparaat voor het verzenden van 200 apparaat-naar-Cloud-berichten per seconde naar uw S1-IoT Hub (dat een limiet van 100/sec. D2C-verzen ding heeft). Voor de eerste minuut of twee worden de berichten onmiddellijk verwerkt. Omdat het apparaat echter nog steeds meer berichten verzendt dan de beperkings limiet, worden met IoT Hub slechts 100 berichten per seconde verwerkt en wordt de rest in een wachtrij geplaatst. U begint merkt meer latentie. Uiteindelijk kunt u beginnen `429 ThrottlingException` met het ophalen van de wachtrij, waarna het [aantal beperkende fouten IOT hub meet](monitor-iot-hub-reference.md#device-telemetry-metrics) tijd wordt verg root. Zie [IOT hub controleren](monitor-iot-hub.md)voor meer informatie over het maken van waarschuwingen en grafieken op basis van metrische gegevens.

### <a name="identity-registry-operations-throttle"></a>Beperking van de identiteits register bewerkingen

Register bewerkingen voor de apparaat-id zijn bedoeld voor gebruik in runtime in scenario's voor het beheren en inrichten van apparaten. Het lezen of bijwerken van een groot aantal apparaat-id's wordt ondersteund via [import-en export taken](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

Bij het initiëren van identiteits bewerkingen via [bulk bewerkingen voor het REGI ster](https://docs.microsoft.com/rest/api/iothub/service/bulkregistry/updateregistry) (*geen* Bulk Import en export taken), gelden dezelfde beperkings limieten. Als u bijvoorbeeld een bulk bewerking wilt indienen om 50-apparaten te maken en u een S1-IoT Hub hebt met 1 eenheid, worden slechts twee van deze bulk aanvragen per minuut geaccepteerd. Dit omdat de identiteits bewerking voor een S1-IoT Hub met 1 eenheid 100/min per eenheid is. In dit geval zou een derde aanvraag (en later) in dezelfde minuut worden afgewezen, omdat de limiet al was bereikt. 

### <a name="device-connections-throttle"></a>Beperking van Apparaatinstellingen

De beperking voor het aantal *verbindingen* bepaalt de snelheid waarmee nieuwe verbindingen met een IOT-hub tot stand kunnen worden gebracht. De beperking van het aantal *apparaten* houdt geen rekening met het maximum van de apparaten die tegelijkertijd zijn verbonden. De snelheids beperking van de *apparaat* is afhankelijk van het aantal eenheden dat is ingericht voor de IOT-hub.

Als u bijvoorbeeld één S1-eenheid koopt, krijgt u een beperking van 100 verbindingen per seconde. Om 100.000-apparaten te verbinden, duurt het daarom ten minste 1.000 seconden (ongeveer 16 minuten). U kunt echter net zoveel gelijktijdig verbonden apparaten hebben als apparaten die zijn geregistreerd in uw identiteits register.

## <a name="other-limits"></a>Andere limieten

IoT Hub dwingt andere operationele limieten af:

| Bewerking | Limiet |
| --------- | ----- |
| Apparaten | Het totale aantal apparaten plus modules dat kan worden geregistreerd voor één IoT-hub, is beperkt tot 1.000.000. De enige manier om deze limiet te verhogen, is om contact op te nemen met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/).|
| Uploads van bestanden | 10 gelijktijdige uploads van bestanden per apparaat. |
| Taken<sup>1</sup> | Maximum aantal gelijktijdige taken is 1 (gratis en S1), 5 (voor S2) en 10 (voor S3). Het maximum aantal gelijktijdige [import/export-taken voor apparaten](iot-hub-bulk-identity-mgmt.md) is echter 1 voor alle lagen. <br/>De taak geschiedenis wordt Maxi maal 30 dagen bewaard. |
| Extra eind punten | Betaalde SKU hubs kunnen 10 extra eind punten hebben. Gratis SKU-hubs kunnen één extra eind punt hebben. |
| Bericht routering query's | Betaalde SKU-hubs hebben mogelijk 100 routerings query's. Gratis SKU-hubs kunnen vijf routerings query's hebben. |
| Berichtverrijkingen | Betaalde SKU hubs kunnen Maxi maal 10 bericht verrijkingen hebben. Gratis SKU-hubs kunnen Maxi maal 2 bericht verrijkingen hebben.|
| Apparaat-naar-Cloud-berichten | Maximale bericht grootte 256 KB |
| Cloud-naar-apparaat-berichten<sup>1</sup> | Maximale bericht grootte van 64 KB. Maximum aantal in behandeling zijnde berichten voor levering is 50 per apparaat. |
| Directe methode<sup>1</sup> | De maximale grootte van de directe methode lading is 128 KB. |
| Automatische apparaat-en module configuraties<sup>1</sup> | 100 configuraties per betaalde SKU-hub. 20 configuraties per gratis SKU-hub. |
| Automatische implementaties IoT Edge<sup>1</sup> | 50 modules per implementatie. 100 implementaties (inclusief gelaagde implementaties) per betaalde SKU-hub. 10 implementaties per gratis SKU-hub. |
| Apparaatdubbels<sup>1</sup> | De sectie maximale grootte van gewenste eigenschappen en gerapporteerde eigenschappen is 32 KB. De sectie maximum grootte van tags is 8 KB. |
| Gedeeld toegangsbeleid | Het maximum aantal Shared Access-beleids regels is 16. |
| x509 CA-certificaten | Het maximum aantal x509 CA-certificaten dat kan worden geregistreerd op IoT Hub is 25. |

<sup>1</sup> Deze functie is niet beschikbaar in de laag basis van IoT Hub. Zie [de juiste IOT hub kiezen](iot-hub-scaling.md)voor meer informatie.

## <a name="increasing-the-quota-or-throttle-limit"></a>De limiet voor quotum of vertraging verhogen

U kunt op elk gewenst moment quota's of bandbreedte beperkingen verhogen door [het aantal ingerichte eenheden in een IOT-hub te verhogen](iot-hub-upgrade.md).

## <a name="latency"></a>Latentie

IoT Hub streeft ernaar lage latentie te bieden voor alle bewerkingen. Vanwege netwerk omstandigheden en andere onvoorspelbare factoren kan er echter geen enkele latentie worden gegarandeerd. Bij het ontwerpen van uw oplossing moet u het volgende doen:

* Vermijd het nemen van veronderstellingen over de maximale latentie van een IoT Hub bewerking.
* Richt uw IoT-hub in de Azure-regio die het dichtst bij uw apparaten ligt.
* Overweeg het gebruik van Azure IoT Edge om latentie gevoelige bewerkingen uit te voeren op het apparaat of op een gateway dicht bij het apparaat.

Meerdere IoT Hub eenheden beïnvloeden de beperking zoals eerder beschreven, maar bieden geen extra latentie voordelen of-garanties.

Als er onverwachte toename van de bewerkings latentie wordt weer geven, neemt u contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Volgende stappen

Zie het blog bericht [IOT hub beperking en u](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)voor een diep gaande bespreking van IOT hub beperkings gedrag.

Andere naslag onderwerpen in deze IoT Hub ontwikkelaars handleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)
* [IoT Hub bewaken](monitor-iot-hub.md)
