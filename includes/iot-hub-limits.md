---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82204511"
---
De volgende tabel bevat de limieten die aan de verschillende servicelagen (S1, S2, S3 en F1) zijn gekoppeld. Voor informatie over de kosten van elke *eenheid* in elke laag raadpleegt u [Prijzen van Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resource | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Berichten/dag |400,000 |6,000,000 |300,000,000 |8,000 |
| Maximum aantal eenheden |200 |200 |10 |1 |

> [!NOTE]
> Neem contact op met Microsoft Ondersteuning als u verwacht dat u meer dan 200 eenheden met een hub uit laag S1 of S2 of meer dan 10 eenheden met een hub uit laag S3 gaat gebruiken.
> 
> 

De volgende tabel bevat de limieten die van toepassing zijn op IoT Hub-resources.

| Resource | Limiet |
| --- | --- |
| Maximum aantal betaalde IoT-hubs per Azure-abonnement |50 |
| Maximum aantal gratis IoT-hubs per Azure-abonnement |1 |
| Maximumaantal tekens in een apparaat-id | 128 |
| Maximumaantal apparaat-id's<br/> dat in één aanroep wordt geretourneerd |1000 |
| Maximale bewaartermijn van IoT Hub-berichten voor apparaat-naar-cloud berichten |7 dagen |
| Maximale grootte van apparaat-naar-cloud berichten |256 kB |
| Maximale grootte van apparaat-naar-cloud batch |AMQP en HTTP: 256 KB voor de hele batch <br/>MQTT: 256 KB voor elk bericht |
| Maximum aantal berichten in apparaat-naar-cloud batch |500 |
| Maximale grootte van cloud-naar-apparaat bericht |64 kB |
| Maximale TTL voor cloud-naar-apparaat berichten |2 dagen |
| Maximumaantal leveringen voor cloud-naar-apparaat <br/> berichten |100 |
| Maximale cloud-naar-apparaat wachtrijlengte per apparaat |50 |
| Maximumaantal leveringen voor feedbackberichten <br/> als antwoord op een cloud-naar-apparaat bericht |100 |
| Maximale TTL voor feedbackberichten <br/> als antwoord op een cloud-naar-apparaat bericht |2 dagen |
| [Maximale grootte van apparaatdubbel](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB voor de sectie Tags, en 32 KB elk voor de secties Gewenste eigenschappen en Gerapporteerde eigenschappen |
| Maximale lengte van tekenreekssleutel van apparaatdubbel | 1 kB |
| Maximale lengte van tekenreekswaarde van apparaatdubbel | 4 kB |
| [Maximale diepte van object in apparaatdubbel](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maximale grootte van de nettolading van directe methode | 128 kB |
| Maximale bewaartermijn van taakgeschiedenis | 30 dagen |
| Maximum aantal gelijktijdige taken | 10 (voor S3), 5 voor (S2), 1 (voor S1) |
| Maximaal aantal extra eindpunten | 10 (voor S1, S2 en S3) |
| Maximum aantal regels voor berichtroutering | 100 (voor S1, S2 en S3) |
| Maximumaantal gelijktijdig verbonden apparaatstreams | 50 (alleen voor S1, S2, S3 en F1) |
| Maximale gegevensoverdracht voor apparaatstreams | 300 MB per dag (alleen voor S1, S2, S3 en F1) |

> [!NOTE]
> Neem contact op met Microsoft Ondersteuning als u meer dan 50 betaalde IoT-hubs in een Azure-abonnement nodig hebt.

> [!NOTE]
> Het totaalaantal apparaten plus modules dat bij één IoT-hub kan worden geregistreerd, is momenteel 1.000.000. Als u deze limiet wilt verhogen, kunt u contact opnemen met [Microsoft Ondersteuning](https://azure.microsoft.com/support/options/).

IoT Hub vertraagt aanvragen wanneer de volgende quota worden overschreden.

| Vertragen | Waarde per hub |
| --- | --- |
| Registerbewerkingen voor identiteit <br/> (maken, ophalen, weergeven, bijwerken en verwijderen), <br/> afzonderlijk of bulksgewijs importeren/exporteren |83,33 per seconde per eenheid (5.000 per minuut per eenheid) (voor S3). <br/> 1,67 per seconde per eenheid (100 per minuut per eenheid) (voor S1 en S2). |
| Apparaatverbindingen |6\.000 per seconde per eenheid (voor S3), 120 per seconde per eenheid (voor S2), 12 per seconde per eenheid (voor S1). <br/>Minimaal 100 per seconde. |
| Apparaat-naar-cloud verzendt |6\.000 per seconde per eenheid (voor S3), 120 per seconde per eenheid (voor S2), 12 per seconde per eenheid (voor S1). <br/>Minimaal 100 per seconde. |
| Cloud-naar-apparaat verzendt | 83,33 per seconde per eenheid (5.000 per minuut per eenheid) (voor S3), 1,67 per seconde per eenheid (100 per minuut per eenheid) (voor S1 en S2). |
| Cloud-naar-apparaat ontvangt |833,33 per seconde per eenheid (50.000 per minuut per eenheid) (voor S3), 16,67 per seconde per eenheid (1.000 per minuut per eenheid) (voor S1 en S2). |
| Bestandsuploadbewerkingen |83,33 bestandsuploadinitiaties per seconde per eenheid (5.000 per minuut per eenheid) (voor S3), 1,67 bestandsuploadinitiaties per seconde per eenheid (100 per minuut per eenheid) (voor S1 en S2). <br/> Voor een Azure Storage-account kunnen 10.000 SAS URI's tegelijk zijn uitgeschakeld.<br/> Er kunnen 10 SAS URI's per apparaat tegelijk zijn uitgeschakeld. |
| Directe methoden | 24 MB per seconde per eenheid (voor S3), 480 KB per seconde per eenheid (voor S2), 160 KB per seconde per eenheid (voor S1).<br/> Gebaseerd op een beperkingsmetergrootte van 8 KB. |
| Apparaatdubbel leest | 500 per seconde per eenheid (voor S3), maximaal 100 per seconde of 10 per seconde per eenheid (voor S2), 100 per seconde (voor S1) |
| Apparaatdubbel werkt bij | 250 per seconde per eenheid (voor S3), maximaal 50 per seconde of 5 per seconde per eenheid (voor S2), 50 per seconde (voor S1) |
| Taakbewerkingen <br/> (maken, bijwerken, weergeven en verwijderen) | 83,33 per seconde per eenheid (5.000 per minuut per eenheid) (voor S3), 1,67 per seconde per eenheid (100 per minuut per eenheid) (voor S2), 1,67 per seconde per eenheid (100 per minuut per eenheid) (voor S1). |
| Doorvoer van taken per apparaat bewerkingen | 50 per seconde per eenheid (voor S3), maximaal 10 per seconde of 1 per seconde per eenheid (voor S2), 10 per seconde (voor S1). |
| Initiatiesnelheid voor apparaatstreams | 5 nieuwe streams per seconde (alleen voor S1, S2, S3 en F1). |
