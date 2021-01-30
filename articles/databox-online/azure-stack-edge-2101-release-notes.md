---
title: Azure Stack Edge Pro met FPGA 2101-release opmerkingen | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de Azure Stack Edge 2101-release beschreven.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 025694dc020bb18ce66574bac476f34034353721
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072598"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro met FPGA 2101-release opmerkingen

In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor de 2101-versie van Azure Stack Edge Pro weer geven met een ingebouwd veld Programmeer bare poort matrix (FPGA).

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
- Het statische IP-adres voor Azure Data Box Gateway wordt bewaard via software-updates.
- Deze versie ondersteunt IoT Edge 1.0.9.3 op Azure Stack edge-apparaten.

## <a name="known-issues-in-this-release"></a>Bekende problemen in deze release

Er worden geen nieuwe problemen vermeld voor deze release. Alle vermelde release problemen zijn overgenomen uit de vorige releases. Als u een lijst met bekende problemen wilt zien, gaat u naar [bekende problemen in de Ga-release](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden om Azure Stack Edge te implementeren](../databox-online/azure-stack-edge-deploy-prep.md)
