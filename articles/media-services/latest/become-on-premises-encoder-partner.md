---
title: Een on-premises coderings partner worden
description: In dit artikel wordt beschreven hoe u on-premises live streaming encoders kunt controleren.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: e07831ce4a016d49e0343da66efc0345effafd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954358"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Uw on-premises live streaming encoder controleren

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Als Azure Media Services on-premises encoder-partner Media Services propageert u uw product door uw Codeer aan te bevelen voor zakelijke klanten. Als u een on-premises encoder partner wilt worden, moet u de compatibiliteit van uw on-premises encoder controleren met Media Services. Voer hiervoor de volgende verificaties uit.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Geslaagde live gebeurtenis verificatie

1. Controleer of het **streaming-eind punt** wordt uitgevoerd In uw Media Services-account. 
2. Maak en start de gebeurtenis **Pass-Through** Live. <br/> Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.
3. De opname-Url's ophalen en uw on-premises Encoder configureren om de URL te gebruiken voor het verzenden van een multi-bitrate live stream naar Media Services.
4. Haal de Preview-URL op en gebruik deze om te controleren of de invoer van het coderings programma daad werkelijk wordt ontvangen.
5. Maak een nieuw **Asset** -object.
6. Een **Live uitvoer** maken en de Asset-naam gebruiken die u hebt gemaakt.
7. Maak een **streaming-Locator** met de ingebouwde **streaming-beleids** typen.
8. Geef een lijst van de paden op de **streaming-Locator** op om terug te gaan naar de url's die u wilt gebruiken.
9. Haal de hostnaam op voor het **streaming-eind punt** van waaruit u gegevens wilt streamen.
10. Combi neer de URL uit stap 8 met de hostnaam in stap 9 om de volledige URL op te halen.
11. Voer uw Live coderings programma uit voor ongeveer 10 minuten.
12. Stop de livegebeurtenis. 
13. Gebruik een speler als [Azure Media Player](https://aka.ms/azuremediaplayer) om de gearchiveerde Asset te bekijken om ervoor te zorgen dat het afspelen geen zicht bare storingen op alle kwaliteits niveaus heeft. U kunt ook controleren en valideren via de Preview-URL tijdens de Live sessie.
14. Registreer de Asset-ID, de gepubliceerde streaming-URL voor het Live Archief en de instellingen en versie die worden gebruikt in uw Live coderings programma.
15. Stel de status van de live-gebeurtenis na het maken van elk voor beeld opnieuw in.
16. Herhaal stap 5 tot en met 15 voor alle configuraties die worden ondersteund door uw coderings programma (met en zonder AD-Signa lering, bijschriften of verschillende coderings snelheden).

## <a name="live-encoding-live-event-verification"></a>Live-gebeurtenis verificatie voor Live-code ring

1. Controleer of het **streaming-eind punt** wordt uitgevoerd In uw Media Services-account. 
2. De live-gebeurtenis voor het **coderen** van Live maken en starten. <br/> Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.
3. Haal de opname-Url's op en configureer uw encoder om een live stream met één bitsnelheid te pushen naar Media Services.
4. Haal de Preview-URL op en gebruik deze om te controleren of de invoer van het coderings programma daad werkelijk wordt ontvangen.
5. Maak een nieuw **Asset** -object.
6. Een **Live uitvoer** maken en de Asset-naam gebruiken die u hebt gemaakt.
7. Maak een **streaming-Locator** met de ingebouwde **streaming-beleids** typen.
8. Geef een lijst van de paden op de **streaming-Locator** op om terug te gaan naar de url's die u wilt gebruiken.
9. Haal de hostnaam op voor het **streaming-eind punt** van waaruit u gegevens wilt streamen.
10. Combi neer de URL uit stap 8 met de hostnaam in stap 9 om de volledige URL op te halen.
11. Voer uw Live coderings programma uit voor ongeveer 10 minuten.
12. Stop de livegebeurtenis.
13. Gebruik een speler als [Azure Media Player](https://aka.ms/azuremediaplayer) om de gearchiveerde Asset te bekijken om ervoor te zorgen dat het afspelen geen zicht bare storingen voor alle kwaliteits niveaus heeft. U kunt ook controleren en valideren via de Preview-URL tijdens de Live sessie.
14. Registreer de Asset-ID, de gepubliceerde streaming-URL voor het Live Archief en de instellingen en versie die worden gebruikt in uw Live coderings programma.
15. Stel de status van de live-gebeurtenis na het maken van elk voor beeld opnieuw in.
16. Herhaal stap 5 tot en met 15 voor alle configuraties die worden ondersteund door uw coderings programma (met en zonder AD-Signa lering, bijschriften of verschillende coderings snelheden).

## <a name="longevity-verification"></a>Duurzaamheids verificatie

Volg dezelfde stappen als in [Pass-through live-gebeurtenis verificatie](#pass-through-live-event-verification) , met uitzonde ring van stap 11. <br/>Voer in plaats van tien minuten uw Live coderings programma uit gedurende een week of langer. Gebruik een speler zoals [Azure Media Player](https://aka.ms/azuremediaplayer) om de live-streaming van tijd tot tijd (of een gearchiveerde Asset) te bekijken om ervoor te zorgen dat het afspelen geen zicht bare storingen heeft.

## <a name="email-your-recorded-settings"></a>Uw vastgelegde instellingen per e-mail verzenden

Als laatste moet u de vastgelegde instellingen en de para meters van uw Live-Archief per e-mail verzenden naar Azure Media Services amshelp@microsoft.com als een melding dat alle verificatie controles zelf zijn geslaagd. Neem ook uw contact gegevens op voor elke follow-up. U kunt contact opnemen met het Azure Media Services team met vragen over dit proces.

## <a name="see-also"></a>Zie ook

[Geteste on-premises encoders](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Volgende stappen

[Live streamen met Media Services v3](live-streaming-overview.md)
