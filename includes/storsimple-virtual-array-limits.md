---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176066"
---
| **Limiet-id** | **Limiet** | **Opmerkingen** |
| --- | --- | --- |
| Totale capaciteit (inclusief Cloud) |Tot 64 TB per virtueel apparaat |U kunt een failover uitvoeren voor een volledige StorSimple-virtuele matrix naar een andere lege matrix. Als u probeert te herstellen op hetzelfde apparaat, moet u ervoor zorgen dat u voldoende ruimte hebt op het apparaat om deze bewerking te volt ooien. Nadat u 32 TB hebt overschreden, kunt u niet meer herstellen op hetzelfde apparaat. |
| Maximum aantal referenties voor het opslag account per apparaat |1 | |
| Maximum aantal volumes/shares |16 | |
| Minimale grootte van een gelaagde share |500 GB | |
| Minimale grootte van een gelaagd volume |500 GB | |
| Maximale grootte van een gelaagde share |20 TB | |
| Maximale grootte van een gelaagd volume |5 TB | |
| Minimale grootte van een lokaal vastgemaakte share |50 GB | |
| Minimum grootte van lokaal vastgemaakt volume |50 GB | |
| Maximale grootte van een lokaal vastgemaakte share |2 TB | |
| Maximale grootte van lokaal vastgemaakt volume |200 GB | |
| Maximumaantal iSCSI-verbindingen van initiators |512 | |
| Maximumaantal Access Control Records per apparaat |64 | |
| Maximum aantal back-ups dat door het virtuele apparaat in wordt bewaard in de map *. backups* voor bestands server |5 |Dit omvat het meest recente geplande (gegenereerd door het standaard back-upbeleid) en hand matige back-ups. |
| Maximum aantal geplande back-ups dat door het apparaat wordt bewaard |55 |30 dagelijkse back-ups<br>12 maandelijkse back-ups<br>13 jaar back-ups |
| Maximum aantal hand matige back-ups dat door het apparaat wordt bewaard |45 | |
| Maximum aantal bestanden per share voor een bestands server |1 miljoen |Bij het herstellen van een apparaat zijn de herstel tijden evenredig met het aantal bestanden in alle shares op het apparaat. |
| Maximum aantal bestanden per volume voor een iSCSI-server |1 miljoen | |
| Maximum aantal bestanden per virtuele matrix |4.000.000 | |
| Herstel tijd herstellen |Snel herstellen |De herstel bewerking is gebaseerd op de heatmap en is afhankelijk van de grootte van het volume.<br>Er kunnen back-upbewerkingen optreden terwijl er een herstel actie wordt uitgevoerd. |

