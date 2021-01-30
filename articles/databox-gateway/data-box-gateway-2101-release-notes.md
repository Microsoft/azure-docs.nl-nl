---
title: Opmerkingen bij de release van Azure Data Box Gateway 2101 | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de Azure Data Box Gateway uitgevoerd 2101-versie beschreven.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072640"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Release opmerkingen bij Azure Data Box Gateway 2101

De volgende opmerkingen bij de release identificeren de kritieke openstaande problemen en de opgeloste problemen voor de 2101-versie van Azure Data Box Gateway.

De release opmerkingen worden voortdurend bijgewerkt. Als er kritieke problemen zijn gedetecteerd waarvoor een tijdelijke oplossing nodig is, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw Azure Data Box Gateway implementeert.  

Deze release komt overeen met de software versies:

- **Data Box Gateway 2101 (1.6.1475.2528)** -KB 4603462

> [!NOTE]
> Update 2101 kan alleen worden toegepast op alle apparaten met algemene Beschik baarheid (GA)-versies van de software of hoger.

## <a name="whats-new"></a>Nieuw

Deze release bevat de volgende fout oplossing:

- **Probleem uploaden** : met deze versie wordt een upload probleem opgelost waarbij het uploaden opnieuw wordt gestart, omdat het uploaden kan worden vertraagd door storingen. Dit probleem kan zich voordoen wanneer u een gegevensset uploadt die voornamelijk bestaat uit bestanden die groot zijn ten opzichte van de beschik bare band breedte, met name, maar niet beperkt tot, wanneer bandbreedte regeling actief is. Met deze wijziging zorgt u ervoor dat er voldoende mogelijkheden zijn om de upload te volt ooien voordat de upload voor een bepaald bestand opnieuw wordt gestart.

Deze release bevat ook de volgende updates:

- Alle cumulatieve Windows-updates en .NET Framework-updates die zijn uitgebracht via oktober 2020.
- Het statische IP-adres voor Azure Data Box Gateway wordt bewaard via software-updates.

## <a name="known-issues-in-this-release"></a>Bekende problemen in deze release

Er worden geen nieuwe problemen vermeld voor deze release. Alle vermelde release problemen zijn overgenomen uit de vorige releases. Als u een lijst met bekende problemen wilt zien, gaat u naar [bekende problemen in de Ga-release](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Volgende stappen

- [Voorbereidingen voor de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
