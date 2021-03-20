---
title: Het opname beleid beheren-Azure
description: In dit onderwerp wordt uitgelegd hoe u het registratie beleid beheert.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99224986"
---
# <a name="manage-recording-policy"></a>Opnamebeleid beheren

U kunt live video Analytics op IoT Edge gebruiken voor [continue video-opname](continuous-video-recording-concept.md), waarbij u gedurende weken of maanden video in de cloud kunt opnemen. U kunt de lengte (in dagen) van het Cloud archief beheren met behulp van de [levenscyclus beheer hulpprogramma's](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) die zijn ingebouwd in azure Storage.  

Uw media service-account is gekoppeld aan een Azure Storage-account en wanneer u video opneemt in de Cloud, wordt de inhoud naar een media service- [Asset](../latest/assets-concept.md)geschreven. Elk activum wordt toegewezen aan een container in het opslag account. Met levenscyclus beheer kunt u een [beleid](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) voor een opslag account definiëren, waarbij u een [regel](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) zoals de volgende kunt opgeven.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

De bovenstaande regel:

* Is van toepassing op alle blok-blobs in het opslag account.
* Hiermee geeft u op dat wanneer de blobs ouder zijn dan 30 dagen, ze worden verplaatst van de [laag Hot Access naar cool](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).
* En wanneer blobs ouder zijn dan 90 dagen, moeten ze worden verwijderd.

Wanneer u live video Analytics gebruikt om te registreren bij een Asset, geeft u een `segmentLength` eigenschap op die aangeeft dat de module een minimale duur van de video (in seconden) moet aggregeren voordat deze naar de Cloud wordt geschreven. Uw activum bevat een reeks segmenten, elk met een tijds tempel voor maken die `segmentLength` nieuwer is dan de vorige. Wanneer het levenscyclus beheer beleid in gaat, worden de segmenten die ouder zijn dan de opgegeven drempel waarde verwijderd. U kunt echter nog steeds toegang krijgen tot de resterende segmenten via de media service-Api's. Zie [back-opnames afspelen](playback-recordings-how-to.md)voor meer informatie. 

## <a name="limitations"></a>Beperkingen

Hieronder vindt u enkele bekende beperkingen met betrekking tot levenscyclus beheer:

* U kunt Maxi maal 100 regels in het beleid hebben en elke regel kan Maxi maal 10 containers bevatten. Als u dus een ander registratie beleid nodig hebt (bijvoorbeeld een archief met drie dagen voor de camera op de parkeer partij, 30 dagen voor de camera in het laad perron en 180 dagen voor de camera achter de teller voor uitchecken), kunt u met één media service-account de regels voor de meeste 1000 camera's aanpassen.
* Updates voor het levenscyclus beheer beleid zijn niet direct. Zie [deze sectie met veelgestelde vragen](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) voor meer informatie.
* Als u ervoor kiest om een beleid toe te passen waarbij blobs worden verplaatst naar de cool-laag, kan dit gevolgen hebben voor het afspelen van dat gedeelte van het archief. Mogelijk worden extra latenties of sporadische fouten weer geven. Media Services biedt geen ondersteuning voor het afspelen van inhoud in de laag van het archief.

## <a name="next-steps"></a>Volgende stappen

[Opnamen afspelen](playback-recordings-how-to.md)
