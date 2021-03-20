---
title: Aangepaste tref woorden-spraak service
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor aangepaste tref woorden met behulp van de speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91356601"
---
# <a name="what-is-a-keyword"></a>Wat is een tref woord?

Een tref woord is een woord of korte woord groep waarmee uw product spraak kan worden geactiveerd. "Hey Cortana" is bijvoorbeeld het tref woord voor de Cortana-assistent. Met de functie voor spraak activering kunnen uw gebruikers hun product volledig zelf door nemen door het tref woord te spreken. Als uw product voortdurend luistert naar het tref woord, wordt alle audio lokaal op het apparaat van de gebruiker verwerkt totdat er een detectie plaatsvindt om ervoor te zorgen dat de gegevens zo privé blijven.

## <a name="core-features-of-custom-keyword"></a>Belangrijkste functies van aangepast sleutel woord

Met de aanpassings-, prestatie-en integratie functies van het aangepaste sleutel woord kunt u spraak activering aanpassen aan het beste bij de visie van uw product en gebruikers behoeften.

| Functie | Beschrijving |
|----------|----------|
| Trefwoord aanpassing | Een sleutel woord is een uitbrei ding van uw merk en versterkt het eigen vermogen dat u met uw klanten hebt gemaakt. Met de portal voor aangepaste tref woorden in speech Studio kunt u een woord of korte woord groep opgeven die het beste uw merk vormt. U kunt uw tref woord verder aanpassen door de juiste uitspraak te kiezen. dit wordt gerespecteerd door het gegenereerde trefwoord model.
| Trefwoord verificatie | Als er een hoge mate van vertrouwen in het sleutel woord lokaal wordt gedetecteerd, wordt audio naar de Cloud verzonden voor verdere verificatie dat een gebruiker het tref woord heeft genoemd. Verificatie van tref woorden biedt een extra beveiligingslaag door de impact van een onjuiste lokale detectie te verminderen en de privacy van gebruikers te beschermen.
| Spraak assistent & Speech SDK-integratie | Tref woorden die zijn gegenereerd op basis van het aangepaste tref woord in speech Studio, kunnen eenvoudig worden geïntegreerd binnen uw apparaat of toepassing via de spraak-SDK. U hoeft alleen maar de SDK te laten verwijzen naar het trefwoord model dat wordt meegeleverd met Speech Studio en uw product is geactiveerd met spraak, dat wordt ondersteund door de verificatie van het sleutel woord. U kunt de spraak ervaring van uw product volt ooien door uw eigen [Voice Assistant](voice-assistants.md)te bouwen.

## <a name="get-started-with-custom-keywords"></a>Aan de slag met aangepaste tref woorden

* Zie [basis beginselen van tref woorden](custom-keyword-basics.md) voor basis gebruik en ontwerp patronen.
* Hoe u [uw product kunt activeren met de Speech SDK met behulp van C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>Een effectief tref woord kiezen

Het maken van een effectief sleutel woord is essentieel om ervoor te zorgen dat uw apparaat consistent en nauw keurig reageert. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken. Houd rekening met de volgende richt lijnen wanneer u een tref woord kiest:

> [!div class="checklist"]
> * Het sleutel woord moet een Engels woord of woord groep zijn.
> * Het duurt niet langer dan twee seconden om te zeggen.
> * Woorden van 4 tot 7 letter grepen werken het beste. "Hoi computer" is bijvoorbeeld een goed tref woord. Alleen een ' Hoi ' is een slechte versie.
> * Tref woorden moeten volgen op algemene regels voor de uitspraak van het Engels.
> * Een uniek of zelfs een opgesteld woord dat voldoet aan de algemene Engelse regels voor uitspraak, kan fout-positieven verminderen. Zo kan ' computerama ' een goed tref woord zijn.
> * Kies geen algemeen woord. "Eten" en "Go" zijn bijvoorbeeld woorden die mensen vaak in een gewone conversatie hebben gedicteerd. Ze kunnen worden ingesteld op ONWAAR voor uw apparaat.
> * Vermijd het gebruik van een tref woord dat mogelijk alternatieve uitspraak heeft. Gebruikers moeten de uitspraak van het ' recht ' weten om hun apparaat te laten reageren. "509" kan bijvoorbeeld "5 0 9," "5 0 9," of "509" worden uitgesp roken. "R.E.I." kan worden uitgesp roken "r-e-i" of "Ray". ' Live ' kan worden uitgesp roken '/līv/' of '/Liv/'.
> * Gebruik geen speciale tekens, symbolen of cijfers. Bijvoorbeeld ' go # ' en ' 20 + katten ' kunnen problematische tref woorden zijn. ' Go Sharp ' of ' twintig plus katten ' kan echter wel werken. U kunt nog steeds de symbolen in uw huis stijl gebruiken en marketing en documentatie gebruiken om de juiste uitspraak te versterken.

> [!NOTE]
> Als u een woord in handels merk kiest als uw tref woord, zorg er dan voor dat u eigenaar bent van het handels merk of dat u toestemming hebt van de eigenaar van het handels merk om het woord te gebruiken. Micro soft is niet aansprakelijk voor juridische problemen die kunnen voortvloeien uit uw keuze aan tref woorden.

## <a name="see-samples-on-github"></a>Bekijk de voor beelden op GitHub

* [Tref woorden herkennen met de spraak-SDK, op Universeel Windows-platform met C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Tref woorden herkennen met de Speech SDK, op Android met Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Volgende stappen

* [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)
* [De Speech SDK ophalen](speech-sdk.md)
* [Meer informatie over spraak assistenten](voice-assistants.md)
