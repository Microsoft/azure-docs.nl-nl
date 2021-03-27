---
title: 'Quick Start: telefoon nummers beheren met Azure Communication Services'
description: Meer informatie over het beheren van telefoon nummers met Azure Communication Services
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: e28b8581342824032921ad75e0297b3604fc0bb4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629342"
---
# <a name="quickstart-manage-phone-numbers"></a>Snelstartgids: telefoon nummers beheren

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Ga aan de slag met Azure Communication Services met behulp van de Azure Portal of de client bibliotheek communicatie Services telefoon nummers om telefoon nummers te beheren.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Problemen oplossen

Veelvoorkomende vragen en problemen:

- Het kopen van telefoon wordt alleen ondersteund in de VS. Als u telefoon nummers wilt kopen, moet u het volgende doen:
  - Het factuur adres van het gekoppelde Azure-abonnement bevindt zich in de Verenigde Staten. U kunt op dit moment geen resource verplaatsen naar een ander abonnement.
  - Uw communicatie Services-bron is ingericht op de Verenigde Staten locatie van gegevens. U kunt op dit moment geen resource naar een andere gegevens locatie verplaatsen.

- Wanneer een telefoonnummer wordt vrijgegeven, wordt het telefoonnummer pas vrijgegeven en kan het pas worden teruggekocht aan het einde van de factureringsperiode.

- Wanneer een Communication Services-resource wordt verwijderd, worden de telefoonnummers die aan die resource zijn gekoppeld, automatisch op hetzelfde moment vrijgegeven.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een telefoonnummer kopen
> * Uw telefoonnummer beheren
> * Een telefoonnummer vrijgeven

> [!div class="nextstepaction"]
> [Een sms verzenden](../telephony-sms/send.md)
> [Aan de slag met gesprekken](../voice-video-calling/getting-started-with-calling.md)
