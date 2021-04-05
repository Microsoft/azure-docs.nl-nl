---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516988"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Wat kan ik met een cluster krijgen?

Hoeveel u voor een Event Hubs cluster kunt opnemen en streamen, is afhankelijk van verschillende factoren, zoals uw producenten, consumenten, de snelheid waarmee u opnamet en verwerkt, en nog veel meer. 

In de volgende tabel ziet u de benchmark resultaten die we hebben behaald tijdens onze tests:

| De shape Payload | Ontvangers | Ingangs bandbreedte| Berichten binnenkomend | Uitgangs band breedte | Uitstaande berichten | Totaal aantal TUs | TUs per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches van 100x1KB | 2 | 400 MB/sec. | 400k berichten per seconde | 800 MB/sec. | 800k berichten per seconde | 400 TUs | 100 TUs | 
| Batches van 10x10KB | 2 | 666 MB/sec. | 66.6 k-berichten/sec | 1,33 GB/sec. | 133k berichten per seconde | 666 TUs | 166 TUs |
| Batches van 6x32KB | 1 | 1,05 GB/sec. | 34k berichten per seconde | 1,05 GB/sec. | 34k berichten per seconde | 1000 TUs | 250 TUs |

Bij het testen zijn de volgende criteria gebruikt:

- Er is een toegewezen Event Hubs cluster met vier capaciteits eenheden (CUs) gebruikt. 
- De Event Hub die wordt gebruikt voor opname, had 200 partities. 
- De gegevens die zijn opgenomen, zijn ontvangen door twee receiver-toepassingen die van alle partities ontvangen.

### <a name="can-i-scale-updown-my-cluster"></a>Kan ik mijn cluster omhoog/omlaag schalen?

Na het maken van clusters worden er mini maal vier uur verbruik in rekening gebracht. In de preview-versie van de zelf-onderhouds ervaring kunt u een [ondersteunings aanvraag](https://ms.portal.azure.com/#create/Microsoft.Support) indienen bij het event hubs team onder het **technische**  >  **quotum**  >  **verzoek om het toegewezen cluster** omhoog of omlaag te schalen om uw cluster omhoog of omlaag te schalen. Het kan tot 7 dagen duren voordat de aanvraag is voltooid om uw cluster omlaag te schalen. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Hoe werkt geo-DR met mijn cluster?

U kunt geografisch koppelen aan een naam ruimte onder een cluster met een specifieke laag met een andere naam ruimte onder een cluster met een specifieke laag. Het koppelen van een naam ruimte met een toegewezen laag aan een naam ruimte in onze standaard aanbieding wordt niet aanbevolen, omdat de doorvoer limiet incompatibel is en er fouten optreden. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kan ik mijn standaard naam ruimten migreren naar een cluster met een toegewezen laag?
Er wordt momenteel geen geautomatiseerd migratie proces ondersteund voor het migreren van uw event hubs-gegevens uit een standaard naam ruimte naar een speciaal bestand. 
