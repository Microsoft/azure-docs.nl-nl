---
title: Servicelimieten
titleSuffix: Azure Digital Twins
description: Grafiek met de limieten van de Azure Digital Twins service.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 15c76bc042cb66dafbdeebac2951f5cb68310aa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482788"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins servicelimieten

Dit zijn de servicelimieten van Azure Digital Twins.

> [!NOTE]
> Sommige gebieden van deze service hebben aanpasbare limieten. Dit wordt weergegeven in de onderstaande tabellen met de *kolom Aanpasbaar?* . Wanneer de limiet kan worden aangepast, is de waarde *Aanpasbaar?* *Ja.*
>
> Als uw bedrijf een aanpasbare limiet of quotum boven de standaardlimiet moet verhogen, kunt u aanvullende resources aanvragen door [een ondersteuningsticket te openen.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="limits-by-type"></a>Limieten per type

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Werken met limieten

Wanneer een limiet is bereikt, beperkt de service extra aanvragen. Dit resulteert in een 429-foutreactie van deze aanvragen.

Hier volgen enkele aanbevelingen voor het werken met limieten om dit te beheren.
* **Logica voor opnieuw proberen gebruiken.** De [Azure Digital Twins SDK's](how-to-use-apis-sdks.md) implementeren logica voor opnieuw proberen voor mislukte aanvragen, dus als u met een opgegeven SDK werkt, is dit al ingebouwd. U kunt ook logica voor opnieuw proberen implementeren in uw eigen toepassing. De service stuurt een header terug in de foutreactie, die u kunt gebruiken om te bepalen hoe lang moet worden gewacht `Retry-After` voordat het opnieuw wordt gedaan.
* **Gebruik drempelwaarden en meldingen om te waarschuwen over bijna-limieten.** Sommige van de servicelimieten voor Azure Digital Twins hebben bijbehorende metrische gegevens [die](troubleshoot-metrics.md) kunnen worden gebruikt om het gebruik in deze gebieden bij te houden. Zie de instructies in Problemen oplossen: Waarschuwingen instellen als u drempelwaarden wilt configureren en een waarschuwing wilt instellen voor metrische gegevens wanneer een drempelwaarde [*wordt bereikt.*](troubleshoot-alerts.md) Als u meldingen wilt instellen voor andere limieten waar geen metrische gegevens worden opgegeven, kunt u deze logica implementeren in uw eigen toepassingscode.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de huidige versie van Azure Digital Twins in het serviceoverzicht:
* [*Overzicht: Wat is Azure Digital Twins?*](overview.md)
