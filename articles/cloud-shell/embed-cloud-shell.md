---
title: Azure Cloud Shell insluiten | Microsoft Docs
description: Meer informatie over het insluiten van Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "60614313"
---
# <a name="embed-azure-cloud-shell"></a>Azure Cloud Shell insluiten

Als u Cloud Shell insluit, kunnen ontwikkel aars en schrijvers van inhoud Cloud Shell rechtstreeks openen vanuit een toegewezen URL, [shell.Azure.com](https://shell.azure.com). Dit biedt direct de volledige kracht van de Cloud Shell verificatie, hulp middelen en recente Azure CLI/Azure PowerShell-hulpprogram ma's voor uw gebruikers.

Knop normaal formaat

[![Regel matig starten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

Knop grote grootte

[![Grote Lance ring](https://shell.azure.com/images/launchcloudshell@2x.png "Azure Cloud Shell starten")](https://shell.azure.com)

## <a name="how-to"></a>Uitleg

De knop Start van Cloud Shell integreren in kortings bestanden door het volgende te kopiëren:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

De HTML-code voor het insluiten van pop-upCloud Shell is lager:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Ervaring aanpassen

Stel een specifieke shell-ervaring in door uw URL uit te breiden.

|Ervaring   |URL   |
|---|---|
|Meest recent gebruikte shell   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Volgende stappen
[Bash in Cloud Shell Quick Start](quickstart.md)<br>
[Power shell in Cloud Shell Quick Start](quickstart-powershell.md)
