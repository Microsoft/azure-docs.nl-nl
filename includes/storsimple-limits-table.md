---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176433"
---
| Limiet-id | Limiet | Opmerkingen |
| --- | --- | --- |
| Maximumaantal opslagaccountreferenties |64 | |
| Maximumaantal volumecontainers |64 | |
| Maximumaantal volumes |255 | |
| Maximumaantal planningen per bandbreedtesjabloon |168 |Een planning voor elk uur, elke dag van de week. |
| Maximumgrootte van een gelaagd volume op fysieke apparaten |64 TB voor StorSimple 8100 en StorSimple 8600 |StorSimple 8100 en StorSimple 8600 zijn fysieke apparaten. |
| Maximumgrootte van een gelaagd volume op virtuele apparaten in Azure |30 TB voor StorSimple 8010 <br></br> 64 TB voor StorSimple 8020 |StorSimple 8010 en StorSimple 8020 zijn virtuele apparaten in Azure die respectievelijk gebruikmaken van Standard-opslag en Premium-opslag. |
| Maximumgrootte van een lokaal vastgemaakt volume op fysieke apparaten |9 TB voor StorSimple 8100 <br></br> 24 TB voor StorSimple 8600 |StorSimple 8100 en StorSimple 8600 zijn fysieke apparaten. |
| Maximumaantal iSCI-verbindingen |512 | |
| Maximumaantal iSCSI-verbindingen van initiators |512 | |
| Maximumaantal Access Control Records per apparaat |64 | |
| Maximumaantal volumes per back-upbeleid |24 | |
| Maximumaantal bewaarde back-ups per back-upbeleid |64 | |
| Maximumaantal planningen per back-upbeleid |10 | |
| Maximumaantal momentopnamen van elk type dat per volume kan worden bewaard |256 |Dit bedrag omvat lokale momentopnamen en Cloud-momentopnamen. |
| Het maximumaantal momentopnamen dat aanwezig kan zijn op een apparaat |10.000 | |
| Maximumaantal volumes dat parallel kan worden verwerkt voor back-ups, herstelbewerkingen of klonen |16 |<ul><li>Als er meer dan 16 volumes zijn, worden ze opeenvolgend verwerkt wanneer er verwerkingsplekken beschikbaar zijn.</li><li>Nieuwe back-ups van een gekloond of hersteld gelaagd volume kunnen niet plaatsvinden tot de bewerking is voltooid. Voor een lokaal volume zijn back-ups toegestaan nadat het volume online.</li></ul> |
| Hersteltijd en kloonhersteltijd voor gelaagde volumes |<2 minuten |<ul><li>Het volume is binnen twee minuten na een herstel- of kloonbewerking beschikbaar, ongeacht de grootte van het volume.</li><li>De volumeprestaties zijn mogelijk langzamer aan het begin dan normaal, omdat de meeste gegevens en metagegevens zich nog in de cloud bevinden. De prestaties nemen toe naarmate gegevens van de cloud naar het StorSimple-apparaat worden overgezet.</li><li>De totale tijd om metagegevens te downloaden hangt af van de totaal toegewezen volumegrootte. Metagegevens worden automatisch op de achtergrond op het apparaat gezet met een snelheid van 5 minuten per TB aan toegewezen volumegegevens. Dit aantal is onderhevig aan internetbandbreedte voor de cloud.</li><li>De herstel- of kloonbewerking is voltooid wanneer alle metagegevens op het apparaat staan.</li><li>Back-upbewerkingen kunnen niet worden uitgevoerd totdat de herstel- of kloonbewerking volledig is voltooid. |
| Hersteltijd voor lokaal vastgemaakt volumes |<2 minuten |<ul><li>Het volume is binnen twee minuten na de herstelbewerking beschikbaar, ongeacht de grootte van het volume.</li><li>De volumeprestaties zijn mogelijk langzamer aan het begin dan normaal, omdat de meeste gegevens en metagegevens zich nog in de cloud bevinden. De prestaties nemen toe naarmate gegevens van de cloud naar het StorSimple-apparaat worden overgezet.</li><li>De totale tijd om metagegevens te downloaden hangt af van de totaal toegewezen volumegrootte. Metagegevens worden automatisch op de achtergrond op het apparaat gezet met een snelheid van 5 minuten per TB aan toegewezen volumegegevens. Dit aantal is onderhevig aan internetbandbreedte voor de cloud.</li><li>In tegenstelling tot gelaagde volumes worden volumegegevens ook lokaal op het apparaat gedownload als er lokaal vastgemaakt volumes zijn. De herstelbewerking is voltooid wanneer alle volumegegevens op het apparaat staan.</li><li>De herstelbewerkingen kunnen lang zijn en de totale tijd om de herstelbewerking te voltooien zijn afhankelijk van de grootte van het ingerichte lokale volume, uw internetbandbreedte en de bestaande gegevens op het apparaat. Back-upbewerkingen op het lokaal vastgemaakt volume zijn toegestaan terwijl de herstelbewerking worden uitgevoerd. |
| Beschikbaarheid van dub herstellen |Laatste failover | |
| Maximale lees/schrijfdoorvoer voor client, wanneer bediend vanuit de SSD-laag* |920/720 MB/sec met een enkele enthernetnetwerkinterface van 10 gigabit |Maximaal twee keer met MPIO en twee netwerkinterfaces. |
| Maximale lees/schrijfdoorvoer voor client, wanneer bediend vanuit de HDD-laag* |120/250 MB/sec | |
| Maximale lees/schrijfdoorvoer voor client, wanneer bediend vanuit de cloudlaag* |11/41 MB/sec |De leesdoorvoer is afhankelijk van clients die voldoende I/O-wachtrijdiepte genereren en behouden. |

&#42;Maximale doorvoer per I/O-type is gemeten met 100% lees- en 100% schrijfscenario's. De werkelijke doorvoer kan lager zijn en is afhankelijk van I/O-meng- en netwerkomstandigheden.

