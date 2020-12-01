---
title: 'Overzicht van Custom Speech: spraak service'
titleSuffix: Azure Cognitive Services
description: Custom Speech is een set online-hulpprogram ma's waarmee u de nauw keurigheid van spraak naar tekst voor uw toepassingen, hulpprogram ma's en producten kunt evalueren en verbeteren. Alles wat u nodig hebt om aan de slag te gaan zijn een aantal test audio bestanden. Volg de onderstaande koppelingen om aan de slag te gaan met het maken van een aangepaste spraak-naar-tekst-ervaring.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 7f8fb0488c863868b1076ad5b3fa7868d1880a6b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353779"
---
# <a name="what-is-custom-speech"></a>Wat is Custom Speech?

[Custom speech](https://aka.ms/customspeech) is een set hulpprogram ma's op basis van een gebruikers interface waarmee u de nauw keurigheid van micro soft voor uw toepassingen en producten kunt evalueren en verbeteren. Alles wat u nodig hebt om aan de slag te gaan zijn een aantal test audio bestanden. Volg de onderstaande koppelingen om aan de slag te gaan met het maken van een aangepaste spraak-naar-tekst-ervaring.

## <a name="whats-in-custom-speech"></a>Wat is er in Custom Speech?

Voordat u iets met Custom Speech kunt doen, hebt u een Azure-account en een spraak service-abonnement nodig. Zodra u een account hebt, kunt u uw gegevens voorbereiden, uw modellen trainen en testen, de herkennings kwaliteit controleren, de nauw keurigheid evalueren en uiteindelijk het aangepaste spraak-naar-tekst model implementeren en gebruiken.

Dit diagram markeert de onderdelen waaruit de [Custom speech Portal](https://aka.ms/customspeech)is gemaakt. Gebruik de onderstaande koppelingen voor meer informatie over elke stap.

![Hier worden de verschillende onderdelen van de Custom Speech Portal gemarkeerd.](./media/custom-speech/custom-speech-overview.png)

1. [Abonneren en een project maken](#set-up-your-azure-account) : Maak een Azure-account en Abonneer u op de spraak service. Dit uniforme abonnement geeft u toegang tot spraak naar tekst, tekst naar spraak, spraak omzetting en de [Custom speech Portal](https://speech.microsoft.com/customspeech). Maak vervolgens uw eerste Custom Speech project met behulp van uw abonnement op de spraak service.

1. [Test gegevens uploaden](./how-to-custom-speech-test-and-train.md) test gegevens uploaden (audio bestanden) om de spraak-naar-tekst-aanbieding van micro soft voor uw toepassingen, hulpprogram ma's en producten te evalueren.

1. [Herkennings kwaliteit inspecteren](how-to-custom-speech-inspect-data.md) : gebruik de [Custom speech Portal](https://speech.microsoft.com/customspeech) voor het afspelen van geüploade audio en controleer de kwaliteit van de spraak herkenning van uw test gegevens. Zie [gegevens controleren](how-to-custom-speech-inspect-data.md)voor kwantitatieve metingen.

1. [Evalueer de nauw](how-to-custom-speech-evaluate-data.md) keurigheid en verbeter de nauw keurigheid van het spraak-naar-tekst model. De [Custom speech-Portal](https://speech.microsoft.com/customspeech) biedt een *woord fout*, dat kan worden gebruikt om te bepalen of extra training vereist is. Als u tevreden bent met de nauw keurigheid, kunt u de speech service-Api's rechtstreeks gebruiken. Als u de nauw keurigheid wilt verbeteren met een relatief gemiddelde van 5%-20%, gebruikt u het tabblad **training** in de portal voor het uploaden van aanvullende trainings gegevens, zoals transcripten met menselijke labels en gerelateerde tekst.

1. [Een model trainen en implementeren](how-to-custom-speech-train-model.md) : Verbeter de nauw keurigheid van uw spraak-naar-tekst model door geschreven transcripten (10-1000 uur) en gerelateerde tekst (<200 MB) samen met uw audio test gegevens te voorzien. Deze gegevens helpen u bij het trainen van het spraak-naar-tekst-model. Wanneer u klaar bent met training, testen en als u tevreden bent met het resultaat, kunt u uw model implementeren in een aangepast eind punt.

## <a name="set-up-your-azure-account"></a>Uw Azure-account instellen

Een Azure-account en spraak service-abonnement zijn vereist voordat u de [Custom speech Portal](https://speech.microsoft.com/customspeech) kunt gebruiken om een aangepast model te maken. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Zorg ervoor dat u standaard abonnementen (S0), gratis (F0) abonnementen maakt, niet worden ondersteund.

Zodra u een Azure-account en een spraak service-abonnement hebt gemaakt, moet u zich aanmelden bij [Custom speech Portal](https://speech.microsoft.com/customspeech) en uw abonnement verbinden.

1. Meld u aan bij de [Custom speech Portal](https://aka.ms/custom-speech).
1. Selecteer het abonnement waarmee u wilt werken en maak een spraak project.
1. Als u uw abonnement wilt wijzigen, gebruikt u het **tandwiel** -pictogram in de bovenste navigatie balk.

## <a name="how-to-create-a-project"></a>Een project maken

Inhoud, zoals gegevens, modellen, testen en eind punten, zijn ingedeeld in **projecten** in de [Custom speech Portal](https://speech.microsoft.com/customspeech). Elk project is specifiek voor een domein en land/taal. U kunt bijvoorbeeld een project maken voor call centers die Engels gebruiken in de Verenigde Staten.

Als u uw eerste project wilt maken, selecteert u spraak **naar tekst/aangepaste spraak** en klikt u vervolgens op **Nieuw project**. Volg de instructies in de wizard om het project te maken. Nadat u een project hebt gemaakt, ziet u vier tabbladen: **gegevens**, **testen**, **training** en **implementatie**. Gebruik de koppelingen in de [volgende stappen](#next-steps) voor meer informatie over het gebruik van elk tabblad.

> [!IMPORTANT]
> De [Custom speech Portal](https://aka.ms/custom-speech) is onlangs bijgewerkt. Als u eerdere gegevens, modellen, tests en gepubliceerde eind punten in de CRIS.ai-portal of met Api's hebt gemaakt, moet u een nieuw project maken in de nieuwe portal om verbinding met deze oude entiteiten te maken.

## <a name="model-lifecycle"></a>Levens cyclus van model

Aangepaste spraak maakt gebruik van **basis modellen** en **aangepaste modellen**. Elke taal heeft een of meer **basis modellen**. Wanneer een nieuw spraak model wordt vrijgegeven aan de normale spraak service, wordt het ook als een nieuw **basis model** geïmporteerd in de Custom Speech-Service. Deze worden doorgaans elke 3-6 maanden bijgewerkt en oudere modellen zijn minder nuttig in de loop van de tijd, omdat het meest recente model meestal een hogere nauw keurigheid heeft.

**Aangepaste modellen** worden daarentegen gemaakt door het aanpassen van een gekozen basis model aan een bepaald klant scenario. U kunt een bepaald aangepast model gedurende lange tijd blijven gebruiken nadat u het hebt bereikt dat aan uw behoeften voldoet, maar u wordt aangeraden regel matig bij te werken naar het nieuwste basis model en opnieuw te trainen met aanvullende gegevens.

Andere belang rijke termen met betrekking tot de levens cyclus van het model zijn:

* **Aanpassing**: het maken van een basis model en het aanpassen van uw domein/scenario met behulp van tekst gegevens en/of audio gegevens
* **Decoderen**: een model gebruiken en spraak herkenning uitvoeren (audio coderen in tekst)
* **Eind punt**: een gebruikersspecifieke implementatie van een basis model of een aangepast model dat *alleen* toegankelijk is voor een bepaalde gebruiker.

### <a name="expiration-timeline"></a>Verloop tijd lijn

Als nieuwe modellen en nieuwe functionaliteit beschikbaar en ouder worden, worden minder nauw keurige modellen buiten gebruik gesteld. Zie de volgende tijd lijnen voor het model en de verval datum van het eind punt:

**Basis modellen** 

* Aanpassing: beschikbaar voor 1 jaar. Zodra het model is geïmporteerd, is het 1 jaar beschikbaar om aangepaste modellen te maken. Na 1 jaar moeten nieuwe aangepaste modellen worden gemaakt op basis van een nieuwere versie van het basis model.  
* Decodering: 2 jaar na het importeren beschikbaar. Dit betekent dat u een eind punt kunt maken en batch-transcriptie gedurende 2 jaar met dit model moet gebruiken. 
* Eind punten: beschikbaar op dezelfde tijd lijn als decoderen

**Aangepaste modellen**

* Decoderen: beschikbaar gedurende 2 jaar nadat het model is gemaakt. Dit betekent dat u het aangepaste model gedurende 2 jaar (batch/realtime/testen) kunt gebruiken nadat het is gemaakt. Na 2 jaar **moet u uw model opnieuw trainen**, omdat het meest vaak het basis model is afgeschaft voor aanpassing.  
* Eind punten: beschikbaar op dezelfde tijd lijn als decoderen

Wanneer een basis model of aangepast model verloopt, wordt het altijd terugvallen op de **nieuwste versie van het basis model**. Uw implementatie wordt dus nooit onderbroken, maar het kan minder nauw keurig zijn voor *uw specifieke gegevens* als aangepaste modellen de verval datum bereiken. U kunt de verval datum van een model weer geven op de volgende plaatsen in de Custom Speech portal:

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