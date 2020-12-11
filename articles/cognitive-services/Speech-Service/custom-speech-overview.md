---
title: 'Overzicht van Custom Speech: spraak service'
titleSuffix: Azure Cognitive Services
description: Custom Speech is een set online-hulpprogram ma's waarmee u de nauw keurigheid van micro soft speech naar Text kunt evalueren en verbeteren voor uw toepassingen, hulpprogram ma's en producten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.custom: contperf-fy21q2
ms.openlocfilehash: be01309fee3454fbd4be78130f9826b493e7bf7a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033762"
---
# <a name="what-is-custom-speech"></a>Wat is Custom Speech?

[Custom speech](https://aka.ms/customspeech) is een set hulpprogram ma's op basis van een gebruikers interface waarmee u de nauw keurigheid van micro soft speech-naar-tekst kunt evalueren en verbeteren voor uw toepassingen en producten. Alles wat u nodig hebt om aan de slag te gaan is een aantal test audio bestanden. Volg de koppelingen in dit artikel om te beginnen met het maken van een aangepaste spraak-naar-tekst-ervaring.

## <a name="whats-in-custom-speech"></a>Wat is er in Custom Speech?

Voordat u iets met Custom Speech kunt doen, hebt u een Azure-account en een spraak service-abonnement nodig. Nadat u een account hebt, kunt u uw gegevens voorbereiden, uw modellen trainen en testen, de herkennings kwaliteit controleren, de nauw keurigheid evalueren en uiteindelijk het aangepaste spraak-naar-tekst model implementeren en gebruiken.

Dit diagram markeert de onderdelen waaruit de [Custom speech Portal](https://aka.ms/customspeech)is gemaakt. Gebruik de onderstaande koppelingen voor meer informatie over elke stap.

![Diagram dat de onderdelen van de Custom Speech Portal markeert.](./media/custom-speech/custom-speech-overview.png)

1. [Abonneer u en maak een project](#set-up-your-azure-account). Maak een Azure-account en Abonneer u op de spraak service. Dit uniforme abonnement geeft u toegang tot spraak naar tekst, tekst naar spraak, spraak omzetting en de [Custom speech Portal](https://speech.microsoft.com/customspeech). Gebruik vervolgens uw abonnement voor spraak service om uw eerste Custom Speech-project te maken.

1. [Test gegevens uploaden](./how-to-custom-speech-test-and-train.md). Upload test gegevens (audio bestanden) om de micro soft-functie voor spraak naar tekst te evalueren voor uw toepassingen, hulpprogram ma's en producten.

1. De [herkennings kwaliteit controleren](how-to-custom-speech-inspect-data.md). Gebruik de [Custom speech Portal](https://speech.microsoft.com/customspeech) om Geüploade audio af te spelen en de kwaliteit van de spraak herkenning van uw test gegevens te controleren. Zie [gegevens controleren](how-to-custom-speech-inspect-data.md)voor kwantitatieve metingen.

1. [Evalueer en verbeter nauw keurigheid](how-to-custom-speech-evaluate-data.md). Evalueer de nauw keurigheid van het spraak-naar-tekst model en verbeter deze. De [Custom speech-Portal](https://speech.microsoft.com/customspeech) biedt een *Word-fout*, die u kunt gebruiken om te bepalen of extra training vereist is. Als u tevreden bent met de nauw keurigheid, kunt u de speech service-Api's rechtstreeks gebruiken. Als u de nauw keurigheid wilt verbeteren met een relatief gemiddelde van 5% tot 20%, gebruikt u het tabblad **training** in de portal voor het uploaden van aanvullende trainings gegevens, zoals transcripten met menselijke labels en gerelateerde tekst.

1. [Train en implementeer een model](how-to-custom-speech-train-model.md). Verbeter de nauw keurigheid van uw spraak-naar-tekst model door geschreven transcripten (10 tot 1.000 uur) en gerelateerde tekst (<200 MB) samen met uw audio test gegevens te voorzien. Deze gegevens helpen u bij het trainen van het spraak-naar-tekst-model. Probeer na de training opnieuw te testen. Als u tevreden bent met het resultaat, kunt u uw model naar een aangepast eind punt implementeren.

## <a name="set-up-your-azure-account"></a>Uw Azure-account instellen

U moet een Azure-account en een spraak service-abonnement hebben voordat u de [Custom speech Portal](https://speech.microsoft.com/customspeech) kunt gebruiken om een aangepast model te maken. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Zorg ervoor dat u een Standard-abonnement (S0) maakt. Gratis (F0) abonnementen worden niet ondersteund.

Nadat u een Azure-account en een spraak service-abonnement hebt gemaakt, moet u zich aanmelden bij de [Custom speech Portal](https://speech.microsoft.com/customspeech) en uw abonnement verbinden.

1. Meld u aan bij de [Custom speech Portal](https://aka.ms/custom-speech).
1. Selecteer het abonnement dat u nodig hebt om te werken en een spraak project te maken.
1. Als u uw abonnement wilt wijzigen, selecteert u de knop tandwiel in het bovenste menu.

## <a name="how-to-create-a-project"></a>Een project maken

Inhoud, zoals gegevens, modellen, testen en eind punten, zijn ingedeeld in *projecten* in de [Custom speech Portal](https://speech.microsoft.com/customspeech). Elk project is specifiek voor een domein en land/taal. U kunt bijvoorbeeld een project maken voor call centers die Engels gebruiken in de Verenigde Staten.

Als u uw eerste project wilt maken, selecteert u **spraak naar tekst/aangepaste spraak** en selecteert u vervolgens **Nieuw project**. Volg de instructies in de wizard om het project te maken. Nadat u een project hebt gemaakt, ziet u vier tabbladen: **gegevens**, **testen**, **training** en **implementatie**. Gebruik de koppelingen in de [volgende stappen](#next-steps) voor meer informatie over het gebruik van elk tabblad.

> [!IMPORTANT]
> De [Custom speech Portal](https://aka.ms/custom-speech) is onlangs bijgewerkt. Als u eerdere gegevens, modellen, tests en gepubliceerde eind punten in de CRIS.ai-portal of met Api's hebt gemaakt, moet u een nieuw project maken in de nieuwe portal om verbinding met deze oude entiteiten te maken.

## <a name="model-lifecycle"></a>Levens cyclus van model

Custom Speech maakt gebruik van *basis modellen* en *aangepaste modellen*. Elke taal heeft een of meer basis modellen. Wanneer een nieuw spraak model wordt vrijgegeven aan de normale spraak service, wordt dit doorgaans ook als een nieuw basis model geïmporteerd in de Custom Speech-Service. Ze worden elke 3 tot 6 maanden bijgewerkt. Oudere modellen worden doorgaans minder nuttig in de loop van de tijd, omdat het meest recente model meestal een hogere nauw keurigheid heeft.

Aangepaste modellen worden daarentegen gemaakt door het aanpassen van een gekozen basis model aan een bepaald klant scenario. U kunt een bepaald aangepast model gedurende lange tijd blijven gebruiken nadat u er een hebt dat aan uw behoeften voldoet. We raden u echter aan regel matig bij te werken naar het nieuwste basis model en het opnieuw te trainen in een periode met aanvullende gegevens. 

Andere belang rijke termen met betrekking tot de levens cyclus van het model zijn:

* **Aanpassing**: het maken van een basis model en het aanpassen van uw domein/scenario met behulp van tekst gegevens en/of audio gegevens.
* **Decoderen**: het gebruik van een model en het uitvoeren van spraak herkenning (het coderen van audio in tekst).
* **Eind punt**: een gebruikersspecifieke implementatie van een basis model of een aangepast model dat *alleen* toegankelijk is voor een bepaalde gebruiker.

### <a name="expiration-timeline"></a>Verloop tijd lijn

Als nieuwe modellen en nieuwe functionaliteit beschikbaar en ouder worden, worden minder nauw keurige modellen buiten gebruik gesteld. Zie de volgende tijd lijnen voor het model en de verval datum van het eind punt:

**Basis modellen** 

* Aanpassing: beschikbaar voor één jaar. Nadat het model is geïmporteerd, is het één jaar beschikbaar om aangepaste modellen te maken. Na één jaar moeten nieuwe aangepaste modellen worden gemaakt op basis van een nieuwere versie van het basis model.  
* Decodering: twee jaar na het importeren beschikbaar. U kunt dus een eind punt maken en batch transcriptie voor twee jaar gebruiken met dit model. 
* Eind punten: beschikbaar op dezelfde tijd lijn als decoderen.

**Aangepaste modellen**

* Decodering: twee jaar nadat het model is gemaakt. U kunt het aangepaste model twee jaar gebruiken (batch/realtime/testen) nadat het is gemaakt. Na twee jaar *moet u uw model opnieuw trainen* omdat het basis model meestal is afgeschaft voor aanpassing.  
* Eind punten: beschikbaar op dezelfde tijd lijn als decoderen.

Wanneer een basis model of aangepast model verloopt, wordt het altijd terugvallen op de *nieuwste versie van het basis model*. Uw implementatie wordt dus nooit onderbroken, maar het kan minder nauw keurig zijn voor *uw specifieke gegevens* als aangepaste modellen de verval datum bereiken. U kunt de verval datum van een model weer geven op de volgende plaatsen in de Custom Speech portal:

* Overzicht van model trainingen
* Details van model training
* Implementatieoverzicht
* Implementatie Details

U kunt de verval datums ook controleren via de [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) en [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) aangepaste spraak-api's onder de `deprecationDates` eigenschap in het JSON-antwoord.

Houd er rekening mee dat u het model op een aangepast spraak eindpunt zonder downtime kunt bijwerken door het model te wijzigen dat door het eind punt wordt gebruikt in de implementatie sectie van de aangepaste spraak portal of via de aangepaste spraak-API.

## <a name="next-steps"></a>Volgende stappen

* [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
* [De nauw keurigheid van modellen evalueren en verbeteren](how-to-custom-speech-evaluate-data.md)
* [Een model trainen en implementeren](how-to-custom-speech-train-model.md)