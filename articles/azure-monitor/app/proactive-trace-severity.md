---
title: Degradatie van de ernst verhouding van de tracering-Azure-toepassing inzichten
description: Bewaak toepassings traceringen met Azure-toepassing inzichten voor ongebruikelijke patronen in de telemetrie Trace met Slimme detectie.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 2b27860adfc1652b58fe9c51d4d0b0a6c271fc0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86539870"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradatie van de verhouding van de ernst van de tracering (preview-versie)

Traceringen worden veel gebruikt in toepassingen, omdat ze het verhaal van wat er achter de schermen gebeurt. Wanneer er iets mis gaat, bieden traceringen cruciaal inzicht in de volg orde van gebeurtenissen waardoor de status niet kan worden gewenst. Terwijl traceringen over het algemeen niet worden gestructureerd, is er een ding die u op concrete wijze kunt onderscheiden: hun ernst niveau. In de stationaire status van een toepassing verwachten we dat de verhouding tussen ' goede ' traceringen (*info* en *uitgebreid*) en ' slechte ' traceringen (*waarschuwing*, *fout* en *kritiek*) stabiel blijft. De veronderstelling is dat "slechte" traceringen regel matig op een bepaald gebied worden uitgevoerd als gevolg van een aantal redenen (tijdelijke netwerk problemen voor instantie). Maar wanneer een echt probleem groeit, wordt het doorgaans als een verhoging van het relatieve aandeel van ' slechte ' traceringen versus ' goede ' traceringen genoemd. Application Insights Slimme detectie analyseert automatisch de traceringen die door uw toepassing worden vastgelegd en kunnen u een waarschuwing krijgen over ongebruikelijke patronen in de ernst van de telemetrie van de tracering.

Voor deze functie is geen speciale configuratie vereist, behalve het configureren van traceer logboek registratie voor uw app (zie een listener voor traceer logboeken configureren voor [.net](./asp-net-trace-logs.md) of [Java](./java-trace-logs.md)). Het is actief wanneer uw app voldoende uitzonderings-telemetrie genereert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer kan ik dit type Smart Detection-melding krijgen?
Dit type melding kan worden weer gegeven als de verhouding tussen ' goede ' traceringen (traceringen die zijn geregistreerd met een niveau van *info* of *uitgebreid*) en ' slechte ' traceringen (traceringen die zijn vastgelegd met een *waarschuwings* niveau, *fout* of *onherstelbare*) in een bepaalde dag worden verminderd, vergeleken met een basis lijn die in de afgelopen zeven dagen is berekend.

## <a name="does-my-app-definitely-have-a-problem"></a>Heeft mijn app een probleem?
Nee, een melding betekent niet dat uw app een probleem heeft. Hoewel een vermindering van de verhouding tussen ' goede ' en ' slechte ' traceringen kan duiden op een toepassings probleem, kan deze wijziging in verhouding mogelijk onschadelijk zijn. De toename kan bijvoorbeeld worden veroorzaakt door een nieuwe stroom in de toepassing die meer ' slechte ' traceringen dan bestaande stromen verzendt.

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?
De meldingen bevatten diagnostische gegevens voor ondersteuning bij het diagnose proces:
1. **Sorteren.** In de melding ziet u hoeveel bewerkingen worden beïnvloed. Dit kan handig zijn bij het toewijzen van een prioriteit aan het probleem.
2. **Ligt.** Is het probleem van invloed op al het verkeer of alleen een bepaalde bewerking? Deze informatie kan worden opgehaald uit de melding.
3. **Vaststellen.** U kunt de gerelateerde items en rapporten koppelen aan ondersteunende informatie, om u te helpen bij het oplossen van het probleem.
