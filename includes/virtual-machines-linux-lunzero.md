---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77590828"
---
Wanneer u gegevens schijven toevoegt aan een virtuele Linux-machine, kunnen er fouten optreden als er geen schijf aanwezig is op LUN 0. Als u een schijf hand matig toevoegt met behulp van de `az vm disk attach -new` opdracht en u een LUN opgeeft ( `--lun` ) in plaats van het Azure-platform te toestaan de juiste LUN te bepalen, moet u er rekening mee houden dat er al een schijf bestaat/bestaat op LUN 0. 

Bekijk het volgende voor beeld met een fragment van de uitvoer van `lsscsi` :

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

De twee gegevens schijven bestaan op LUN 0 en LUN 1 (de eerste kolom in de `lsscsi` uitvoer gegevens `[host:channel:target:lun]` ). Beide schijven moeten vanuit de VM toegankelijk zijn. Als u hand matig de eerste schijf hebt opgegeven die moet worden toegevoegd bij LUN 1 en de tweede schijf op LUN 2, ziet u mogelijk de schijven niet correct vanuit uw VM.

> [!NOTE]
> De Azure `host` -waarde is 5 in deze voor beelden, maar dit kan variëren afhankelijk van het type opslag dat u selecteert.
> 
> 

Dit schijf gedrag is geen Azure-probleem, maar de manier waarop de Linux-kernel de SCSI-specificaties volgt. Wanneer de Linux-kernel de SCSI-bus voor aangesloten apparaten scant, moet een apparaat worden gevonden op LUN 0 zodat het systeem kan door gaan met scannen op extra apparaten. Als zodanig:

* Bekijk de uitvoer van `lsscsi` na het toevoegen van een gegevens schijf om te controleren of u een schijf op LUN 0 hebt.
* Als uw schijf niet correct wordt weer gegeven in uw virtuele machine, controleert u of er een schijf is op LUN 0.

