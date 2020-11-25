---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021261"
---
## <a name="get-the-training-and-prediction-keys"></a>De training en voorspellingssleutels ophalen

Voor het project is een geldige set abonnementssleutels nodig om met de service te kunnen werken. U kunt de items vinden op de  [Custom Vision-website](https://customvision.ai). Meld u aan met het account dat is gekoppeld aan het Azure-account dat u hebt gebruikt om uw Custom Vision-resources te maken. Selecteer op de startpagina (de pagina met de optie om een nieuw project toe te voegen) het __tandwielpictogram__ in de rechterbovenhoek. Zoek uw trainings- en voorspellingsresources in de lijst en vouw deze uit. Hier vindt u de waarden voor de trainingssleutel, de voorspellingssleutel en de voorspelling van de resource-id. Sla deze waarden op een tijdelijke locatie op.

> [!NOTE]
> Als u een alles-in-een-sleutel van Cognitive Services gebruikt om toegang te krijgen tot Custom Vision, ziet u slechts één sleutel op het instellingenscherm. In dit geval gebruikt u dezelfde sleutel voor trainings- en voorspellingsbewerkingen.

![Afbeelding van de gebruikersinterface van de sleutels](../media/csharp-tutorial/training-prediction-keys.png)

Of u kunt deze sleutels en id verkrijgen via de [Azure Portal](https://www.portal.azure.com) door de resources voor Custom Vision-trainingen en voorspellingen te bekijken en te navigeren naar het tabblad __Sleutels__. Hier vindt u uw trainingssleutel en de voorspellingssleutel. Ga naar het tabblad __Eigenschappen__ van de voorspellingsresource om de resource-id voor de voorspelling te krijgen.

