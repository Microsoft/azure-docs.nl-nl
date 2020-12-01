---
title: Veelgestelde vragen over Text to Speech
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op veelgestelde vragen over de Text to Speech-Service.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 67284225299641a71050342056ad7ca91bf39740
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353711"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Veelgestelde vragen over Text to Speech

Als u in deze veelgestelde vragen geen antwoorden op uw vragen kunt vinden, kunt u [Andere ondersteunings opties](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)bekijken.

## <a name="general"></a>Algemeen

**V: wat is het verschil tussen een standaard spraak model en een aangepast spraak model?**

**A**: het standaard spraak model (ook wel een _gesp roken letter type_ genoemd) is getraind met behulp van gegevens van micro soft en is al geïmplementeerd in de Cloud. U kunt een aangepast spraak model gebruiken om een gemiddelde model aan te passen en de timbre en de expressie van de stem stijl van de spreker over te dragen of een volledig nieuw model te trainen op basis van de trainings gegevens die door de gebruiker zijn voor bereid. Vandaag, meer en meer klanten willen een van de een een-op-een-een-op-een-spraak-stem voor hun bots. Het aangepaste platform voor spraak opbouw is de juiste keuze voor die optie.

**V: waar kan ik beginnen als ik een standaard spraak model wil gebruiken?**

**A**: meer dan 80 standaard spraak modellen in meer dan 45 talen zijn beschikbaar via HTTP-aanvragen. Haal eerst een [abonnements sleutel](./overview.md#try-the-speech-service-for-free)op. Zie de [rest API](./overview.md#reference-docs)voor het maken van rest-aanroepen naar de voorgeïmplementeerde spraak modellen.

**V: als ik een aangepast spraak model wil gebruiken, is de API hetzelfde als voor de standaard stemmen?**

**A**: wanneer u een aangepast spraak model maakt en implementeert, krijgt u een uniek eind punt voor uw model. Als u de stem wilt gebruiken om in uw apps te spreken, moet u het eind punt in uw HTTP-aanvragen opgeven. De functionaliteit die beschikbaar is in de REST API voor de Text to Speech-Service is beschikbaar voor uw aangepaste eind punt. Meer informatie over [het maken en gebruiken van uw aangepaste eind punt](./how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint).

**V: moet ik de trainings gegevens voorbereiden voor het maken van aangepaste spraak modellen?**

**A**: Ja, u moet de trainings gegevens zelf voorbereiden voor een aangepast spraak model.

Er is een verzameling spraak gegevens vereist om een aangepast spraak model te maken. Deze verzameling bestaat uit een set audio bestanden met spraak opnames en een tekst bestand met de transcriptie van elk audio bestand. Het resultaat van uw digitale stem is sterk afhankelijk van de kwaliteit van uw trainings gegevens. Als u een goede tekst-naar-spraak-stem wilt maken, is het belang rijk dat de opnamen worden gemaakt in een stille ruimte met een permanente microfoon van hoge kwaliteit. Een consistent volume, een spreek snelheid en een uitmuntende Toon hoogte, en zelfs consistentie in expres mannerisms van spraak zijn essentieel voor het bouwen van een fantastische digitale stem. Het is raadzaam om de stemmen in een opname studio op te nemen.

Momenteel bieden we geen ondersteuning voor online-opnames of geen opname studio-aanbevelingen. Zie [opnamen en Transcripten voorbereiden](./how-to-custom-voice-create-voice.md)voor de indelings vereiste.

**V: welke scripts moet ik gebruiken voor het vastleggen van de spraak gegevens voor een aangepaste spraak training?**

**A**: de scripts voor spraak opname worden niet beperkt. U kunt uw eigen scripts gebruiken om de spraak op te nemen. Zorg ervoor dat u voldoende fonetische dekking in uw spraak gegevens hebt. Als u een aangepaste stem wilt trainen, kunt u beginnen met een klein aantal spraak gegevens dat 50 verschillende zinnen kan zijn (ongeveer 3-5 minuten van spraak). Hoe meer gegevens u verstrekt, hoe natuurlijk uw stem meer is. U kunt beginnen met het trainen van een volledige spraak letter type wanneer u opnamen van meer dan 2.000 zinnen levert (ongeveer 3-4 uur spraak). Als u een hoge kwaliteit wilt krijgen, moet u opnamen van meer dan 6.000 zinnen voorbereiden (ongeveer 8-10 uur spraak).

We bieden extra services die u helpen bij het voorbereiden van scripts voor de opname. Neem contact op met de [aangepaste Voice-klanten service](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) voor query's.

**V: wat moet ik doen als ik een hogere gelijktijdigheid nodig dan de standaard waarde of wat er wordt aangeboden in de portal?**

**A**: u kunt uw model omhoog schalen in stappen van 20 gelijktijdige aanvragen. Neem contact op met de [aangepaste ondersteuning voor spraak klanten](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) voor meer informatie over hogere schaling.

**V: kan ik mijn model downloaden en lokaal uitvoeren?**

**A**: modellen kunnen niet lokaal worden gedownload en uitgevoerd.

**V: mijn aanvragen worden beperkt?**

**A**: Zie de [quota en limieten voor spraak Services](speech-services-quotas-and-limits.md).

## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen](troubleshooting.md)
- [Releaseopmerkingen](releasenotes.md)