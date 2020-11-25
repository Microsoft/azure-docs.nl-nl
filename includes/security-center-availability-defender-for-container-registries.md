---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014569"
---
## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|Voor **Azure Defender voor containerregisters** gelden de prijzen op de [pagina Prijzen](../articles/security-center/security-center-pricing.md)|
|Ondersteunde registers en installatiekopieën:|Linux-installatiekopieën in ACR-registers die toegankelijk zijn via het internet en shell-toegang hebben|
|Niet-ondersteunde registers en installatiekopieën:|Windows-installatiekopieën<br>Privéregisters<br>Registers waarvan de toegang is afgeschermd met een firewall, service-eindpunt of privé-eindpunten als Azure Private Link<br>Superminimalistische installatiekopieën als [Docker scratch](https://hub.docker.com/_/scratch/) of 'Distroless-installatiekopieën' die alleen een toepassing bevatten en de runtime-afhankelijkheden ervan, zonder pakketbeheerder, shell of besturingssysteem|
|Vereiste rollen en machtigingen:|**Beveiligingslezer** en [Azure Container Registry-rollen en -machtigingen](../articles/container-registry/container-registry-roles.md)|
|Clouds:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Commerciële clouds<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov - Alleen de functie Scannen bij push is momenteel ondersteund. Meer leren in [Wanneer worden installatiekopieën gescand?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: China Gov, andere overheden|
|||