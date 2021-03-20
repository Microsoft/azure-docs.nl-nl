---
title: Microsoft Threat Modeling Tool release 07/29/2020-Azure
description: Documenteer de release opmerkingen voor de 7.3.00729.1 van het hulp programma voor het maken van bedreigings modellen.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516370"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool Update release 7.3.00729.1-07/29/2020

Versie 7.3.00729.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 29 2020 juli en bevat de volgende wijzigingen:

- Opgeloste fouten
 
## <a name="known-issues"></a>Bekende problemen

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Fouten met betrekking tot het deserialiseren van TMT7. Application file

#### <a name="issue"></a>Probleem

Sommige klanten hebben het volgende fout bericht gerapporteerd tijdens het downloaden van de Threat Modeling Tool:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Deze fout treedt op omdat sommige browsers geen systeem eigen ondersteuning bieden voor de installatie van ClickOnce. In deze gevallen wordt het bestand van de ClickOnce-toepassing gedownload naar de harde schijf van de gebruiker.

#### <a name="workaround"></a>Tijdelijke oplossing

Deze fout wordt weer gegeven als de Threat Modeling Tool wordt gestart door te dubbel klikken op het bestand TMT7. Application. Het hulp programma werkt echter normaal na het overs laan van de fout. In plaats van de Threat Modeling Tool te starten door te dubbel klikken op het bestand TMT7. Application, moeten gebruikers tijdens de installatie snelkoppelingen gebruiken die zijn gemaakt in het Windows-menu om de Threat Modeling Tool te starten.

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - [Micro soft Windows 10 jubileum update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten
  - Er is een Internet verbinding vereist voor het ontvangen van updates voor het hulp programma en voor sjablonen.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.Microsoft.com](./threat-modeling-tool.md)en bevat informatie [over het gebruik van het hulp programma](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Volgende stappen

Down load de nieuwste versie van de [Microsoft Threat Modeling tool](https://aka.ms/threatmodelingtool).