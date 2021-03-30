---
title: Abnormale toename in het uitzonderings volume-Azure-toepassing Insights
description: Bewaak toepassings uitzonderingen met Slimme detectie in Azure-toepassing inzichten voor ongebruikelijke patronen in het uitzonderings volume.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86499423"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Uitzonderings volume met een abnormale toename (preview-versie)

Application Insights analyseert automatisch de uitzonde ringen die in uw toepassing worden gegenereerd en kunnen u waarschuwen over ongebruikelijke patronen in de telemetrie van de uitzonde ring.

Voor deze functie is geen speciale configuratie vereist, anders dan het [configureren van uitzonderings rapportage](./asp-net-exceptions.md#set-up-exception-reporting) voor uw app. Het is actief wanneer uw app voldoende uitzonderings-telemetrie genereert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer kan ik dit type Smart Detection-melding krijgen?
Dit type melding kan worden weer geven als uw app een abnormale toename van het aantal uitzonde ringen van een specifiek type gedurende een dag vertoont, vergeleken met een basis lijn die in de afgelopen zeven dagen is berekend.
Machine learning-algoritmen worden gebruikt voor het detecteren van de toename van het aantal uitzonde ringen, waarbij rekening wordt gehouden met een natuurlijke groei in het gebruik van uw toepassing.

## <a name="does-my-app-definitely-have-a-problem"></a>Heeft mijn app een probleem?
Nee, een melding betekent niet dat uw app een probleem heeft. Hoewel een uitzonderlijk aantal uitzonde ringen meestal duidt op een probleem met een toepassing, kunnen deze uitzonde ringen goed aardig zijn en correct worden verwerkt door uw toepassing.

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?
De meldingen bevatten diagnostische gegevens voor ondersteuning bij het diagnose proces:
1. **Sorteren.** In de melding ziet u hoeveel gebruikers of hoeveel aanvragen er worden beïnvloed. Dit kan handig zijn bij het toewijzen van een prioriteit aan het probleem.
2. **Ligt.** Is het probleem van invloed op al het verkeer of alleen een bepaalde bewerking? Deze informatie kan worden opgehaald uit de melding.
3. **Vaststellen.** De detectie bevat informatie over de methode van waaruit de uitzonde ring is opgetreden, evenals het type uitzonde ring. U kunt ook de koppeling Verwante items en rapporten met ondersteunende informatie gebruiken om u te helpen het probleem verder te onderzoeken.
