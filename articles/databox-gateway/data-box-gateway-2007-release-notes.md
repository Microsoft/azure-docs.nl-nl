---
title: Azure Stack Edge & Azure Data Box Gateway 2007-release opmerkingen | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de Azure Stack Edge en Data Box Gateway uitgevoerd 2007-versie beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582197"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Release opmerkingen bij Azure Stack Edge en Azure Data Box Gateway 2007

De volgende opmerkingen bij de release identificeren de kritieke openstaande problemen en de opgeloste problemen voor de 2007-release voor Azure Stack Edge en Data Box Gateway.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw Azure Stack EDGE/Data Box Gateway implementeert.

Deze release komt overeen met de software versies:

- **Azure stack Edge 2007 (1.6.1280.1667)** -KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** -KB 4566550

> [!NOTE]
> Update 2007 kan alleen worden toegepast op alle apparaten met algemene Beschik baarheid (GA)-versies van de software of hoger.

## <a name="whats-new"></a>Nieuw

Deze release bevat de volgende fout oplossing:

- **Upload probleem** : in deze release wordt een upload probleem opgelost waarbij het uploaden opnieuw wordt gestart als gevolg van fouten, waardoor de upload bewerking kan worden vertraagd. Dit probleem kan zich voordoen wanneer u een gegevensset uploadt die voornamelijk bestaat uit bestanden die groot zijn ten opzichte van de beschik bare band breedte, met name, maar niet beperkt tot, wanneer bandbreedte regeling actief is. Deze wijziging zorgt ervoor dat er voldoende mogelijkheden zijn om de upload te volt ooien voordat de upload voor een bepaald bestand opnieuw wordt gestart.

Deze release bevat ook de volgende updates:

- De basis installatie kopie voor de Windows-VHD is bijgewerkt.
- Alle cumulatieve Windows-updates en .NET Framework-updates zijn opgenomen die zijn uitgebracht via mei 2020.
- Deze versie ondersteunt IoT Edge 1.0.9.3 op Azure Stack edge-apparaten.

## <a name="known-issues-in-this-release"></a>Bekende problemen in deze release

Er worden geen nieuwe problemen vermeld voor deze release. Alle vermelde release problemen zijn overgenomen uit de vorige releases. Als u een lijst met bekende problemen wilt zien, gaat u naar [bekende problemen in de Ga-release](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden om Azure Stack Edge te implementeren](../databox-online/azure-stack-edge-deploy-prep.md)
- [Voorbereidingen voor de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
