---
title: Azure Stack Edge Pro met FPGA 2101-release opmerkingen | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de Azure Stack Edge 2101-release beschreven.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727546"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro met FPGA 2101-release opmerkingen

De volgende opmerkingen bij de release identificeren de kritieke openstaande problemen en de opgeloste problemen voor de 2101-versie van Azure Stack Edge Pro met een ingebouwd veld Programmeer bare poort matrix (FPGA).

De release opmerkingen worden voortdurend bijgewerkt. Als er kritieke problemen zijn gedetecteerd waarvoor een tijdelijke oplossing nodig is, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw Azure Stack edge-apparaat implementeert.  

Deze release komt overeen met de software versie:

- **Azure stack Edge 2101 (1.6.1475.2528)** -KB 4599267

> [!NOTE]
> Update 2101 kan alleen worden toegepast op apparaten met algemene Beschik baarheid (GA)-versies van de software of hoger.

## <a name="whats-new"></a>Nieuw

Deze release bevat de volgende fout oplossing:

- **Probleem uploaden** : met deze versie wordt een upload probleem opgelost waarbij het uploaden wordt gestart door een storing, de snelheid waarmee de upload is voltooid, kan vertragen. Dit probleem kan zich voordoen wanneer u een gegevensset uploadt die voornamelijk bestaat uit bestanden die relatief zijn ten opzichte van de beschik bare band breedte, met name, maar niet beperkt tot, wanneer bandbreedte regeling actief is. Met deze wijziging zorgt u ervoor dat er voldoende mogelijkheden zijn om de upload te volt ooien voordat de upload voor een bepaald bestand opnieuw wordt gestart.

Deze release bevat ook de volgende updates:

- Alle cumulatieve Windows-updates en .NET Framework-updates die zijn uitgebracht via oktober 2020.
- De versie van de Base Board management controller (BMC) wordt bijgewerkt van 3.32.32.32 naar 3.36.36.36 tijdens de fabrieks installatie om incompatibiliteit te verhelpen met nieuwere Dell Power Supply-eenheden.
- Deze versie ondersteunt IoT Edge 1.0.9.3 op Azure Stack edge-apparaten.

## <a name="known-issues-in-this-release"></a>Bekende problemen in deze release

Er worden geen nieuwe problemen vermeld voor deze release. Alle vermelde release problemen zijn overgenomen uit de vorige releases. Als u een lijst met bekende problemen wilt zien, gaat u naar [bekende problemen in de Ga-release](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden om Azure Stack Edge te implementeren](../databox-online/azure-stack-edge-deploy-prep.md)