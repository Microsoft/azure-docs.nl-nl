---
title: Microsoft Threat Modeling Tool release 03/22/2020-Azure
description: Documenteer de release opmerkingen voor de 7.3.00316.1 van het hulp programma voor het maken van bedreigings modellen.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516897"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool Update release 7.3.00316.1-03/22/2020

Versie 7.3.00316.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 22 2020 maart en bevat de volgende wijzigingen:

- Verbeterde toegankelijkheid
- Opgeloste fouten
- Nieuwe DiagramReader-functie

## <a name="notable-bug-fixes"></a>Belangrijkste oplossingen voor oplossingen

### <a name="exporting-the-threat-list-to-csv"></a>De lijst met bedreigingen exporteren naar CSV

De functie exporteren naar CSV was inconsistent en selecteert welke velden uit de lijst met bedreigingen moeten worden geëxporteerd. Nu worden alle velden uit de lijst met bedreigingen geëxporteerd naar het CSV-bestand. 

### <a name="ux-bugs"></a>UX-fouten

- De Help-menu's in de primaire werk stroom (maken/openen/analyseren) en de sjabloon editor-ervaring hebben nu consistente menu opties.
- De zoek balk in het deel venster stencils heeft nu een standaard cursor en de juiste labels zijn toegevoegd.

## <a name="new-features"></a>Nieuwe functies

### <a name="diagramreader-feature-has-been-added"></a>De functie DiagramReader is toegevoegd

Er is een nieuwe DiagramReader-functie toegevoegd in het hoofd menu terwijl een model is geopend. Met dit onderdeel wordt de grafische weer gave van het model geconverteerd naar een op tekst gebaseerde afspiegeling. 

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen:
  - [Micro soft Windows 10 jubileum update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist:
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten:
  - Een Internet verbinding voor het ontvangen van updates voor het hulp programma en voor sjablonen

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.Microsoft.com](./threat-modeling-tool.md)en bevat informatie [over het gebruik van het hulp programma](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Volgende stappen

Down load de nieuwste versie van de [Microsoft Threat Modeling tool](https://aka.ms/threatmodelingtool).