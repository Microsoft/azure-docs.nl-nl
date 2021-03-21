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
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 13bf0f2430e0d58dd9ef28061aad897acf94ac3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493047"
---
# <a name="what-is-custom-speech"></a>Wat is Custom Speech?

[Custom speech](https://aka.ms/customspeech) is een set hulpprogram ma's op basis van een gebruikers interface waarmee u de nauw keurigheid van micro soft speech-naar-tekst kunt evalueren en verbeteren voor uw toepassingen en producten. Alles wat u nodig hebt om aan de slag te gaan is een aantal test audio bestanden. Volg de koppelingen in dit artikel om te beginnen met het maken van een aangepaste spraak-naar-tekst-ervaring.

## <a name="whats-in-custom-speech"></a>Wat is er in Custom Speech?

Voordat u iets met Custom Speech kunt doen, hebt u een Azure-account en een spraak service-abonnement nodig. Nadat u een account hebt, kunt u uw gegevens voorbereiden, uw modellen trainen en testen, de herkennings kwaliteit controleren, de nauw keurigheid evalueren en uiteindelijk het aangepaste spraak-naar-tekst model implementeren en gebruiken.

Dit diagram markeert de onderdelen waaruit het [Custom speech gebied van de speech Studio](https://aka.ms/customspeech)komt. Gebruik de onderstaande koppelingen voor meer informatie over elke stap.

![Diagram dat de onderdelen markeert waaruit het Custom Speech gebied van de speech Studio wordt gemaakt.](./media/custom-speech/custom-speech-overview.png)

1. [Abonneer u en maak een project](#set-up-your-azure-account). Maak een Azure-account en Abonneer u op de spraak service. Dit uniforme abonnement geeft u toegang tot spraak naar tekst, tekst naar spraak, spraak omzetting en de [Speech Studio](https://speech.microsoft.com/customspeech). Gebruik vervolgens uw abonnement voor spraak service om uw eerste Custom Speech-project te maken.

1. [Test gegevens uploaden](./how-to-custom-speech-test-and-train.md). Upload test gegevens (audio bestanden) om de micro soft-functie voor spraak naar tekst te evalueren voor uw toepassingen, hulpprogram ma's en producten.

1. De [herkennings kwaliteit controleren](how-to-custom-speech-inspect-data.md). Gebruik de [Speech Studio](https://speech.microsoft.com/customspeech) om Geüploade audio af te spelen en de kwaliteit van de spraak herkenning van uw test gegevens te controleren. Zie [gegevens controleren](how-to-custom-speech-inspect-data.md)voor kwantitatieve metingen.

1. [Evalueer en verbeter nauw keurigheid](how-to-custom-speech-evaluate-data.md). Evalueer de nauw keurigheid van het spraak-naar-tekst model en verbeter deze. De [Speech Studio](https://speech.microsoft.com/customspeech) biedt een *Word-fout*, die u kunt gebruiken om te bepalen of extra training vereist is. Als u tevreden bent met de nauw keurigheid, kunt u de speech service-Api's rechtstreeks gebruiken. Als u de nauw keurigheid wilt verbeteren met een relatief gemiddelde van 5% tot 20%, gebruikt u het tabblad **training** in de portal voor het uploaden van aanvullende trainings gegevens, zoals transcripten met menselijke labels en gerelateerde tekst.

1. [Train en implementeer een model](how-to-custom-speech-train-model.md). Verbeter de nauw keurigheid van uw spraak-naar-tekst model door geschreven transcripten (10 tot 1.000 uur) en gerelateerde tekst (<200 MB) samen met uw audio test gegevens te voorzien. Deze gegevens helpen u bij het trainen van het spraak-naar-tekst-model. Probeer na de training opnieuw te testen. Als u tevreden bent met het resultaat, kunt u uw model naar een aangepast eind punt implementeren.

## <a name="set-up-your-azure-account"></a>Uw Azure-account instellen

U moet een Azure-account en een spraak service-abonnement hebben voordat u de [Speech Studio](https://speech.microsoft.com/customspeech) kunt gebruiken om een aangepast model te maken. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Zorg ervoor dat u een Standard-abonnement (S0) maakt. Gratis (F0) abonnementen worden niet ondersteund.

Als u van plan bent een aangepast model met **Audio gegevens** te trainen, kiest u een van de volgende regio's met specifieke hardware die beschikbaar is voor training. Dit verkort de tijd die nodig is om een model te trainen en biedt u de mogelijkheid om meer audio voor training te gebruiken. In deze regio's gebruikt de speech-service Maxi maal 20 uur aan audio voor training. in andere regio's zal het Maxi maal acht uur duren.

* Australië - oost
* Canada - midden
* India - centraal
* VS - oost
* VS - oost 2
* VS - noord-centraal
* Europa - noord
* VS - zuid-centraal
* Azië - zuidoost
* Verenigd Koninkrijk Zuid
* VS (overheid) - Arizona
* VS (overheid) - Virginia
* Europa -west
* VS - west 2

Nadat u een Azure-account en een spraak service-abonnement hebt gemaakt, moet u zich aanmelden bij de [Speech Studio](https://speech.microsoft.com/customspeech) en verbinding maken met uw abonnement.

1. Meld u aan bij de [Speech Studio](https://aka.ms/custom-speech).
1. Selecteer het abonnement dat u nodig hebt om te werken en een spraak project te maken.
1. Als u uw abonnement wilt wijzigen, selecteert u de knop tandwiel in het bovenste menu.

## <a name="how-to-create-a-project"></a>Een project maken

Inhoud, zoals gegevens, modellen, testen en eind punten, zijn ingedeeld in *projecten* in de [Speech Studio](https://speech.microsoft.com/customspeech). Elk project is specifiek voor een domein en land/taal. U kunt bijvoorbeeld een project maken voor call centers die Engels gebruiken in de Verenigde Staten.

Als u uw eerste project wilt maken, selecteert u **spraak naar tekst/aangepaste spraak** en selecteert u vervolgens **Nieuw project**. Volg de instructies in de wizard om het project te maken. Nadat u een project hebt gemaakt, ziet u vier tabbladen: **gegevens**, **testen**, **training** en **implementatie**. Gebruik de koppelingen in de [volgende stappen](#next-steps) voor meer informatie over het gebruik van elk tabblad.

> [!IMPORTANT]
> De [Speech Studio](https://aka.ms/custom-speech) , voorheen bekend als ' Custom speech portal ', is onlangs bijgewerkt. Als u eerdere gegevens, modellen, tests en gepubliceerde eind punten in de CRIS.ai-portal of met Api's hebt gemaakt, moet u een nieuw project maken in de nieuwe portal om verbinding met deze oude entiteiten te maken.

## <a name="model-and-endpoint-lifecycle"></a>Levens cyclus van modellen en eind punten

Oudere modellen worden doorgaans minder nuttig in de loop van de tijd, omdat het meest recente model meestal een hogere nauw keurigheid heeft. Daarom zijn basis modellen en aangepaste modellen en eind punten die via de portal zijn gemaakt, onderworpen aan een verval datum van 1 jaar voor aanpassing en 2 jaar voor de code ring. Zie een gedetailleerde beschrijving in het artikel over het [model en de levens cyclus van het eind punt](./how-to-custom-speech-model-and-endpoint-lifecycle.md) .

## <a name="next-steps"></a>Volgende stappen

* [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
* [De nauw keurigheid van modellen evalueren en verbeteren](how-to-custom-speech-evaluate-data.md)
* [Een model trainen en implementeren](how-to-custom-speech-train-model.md)
