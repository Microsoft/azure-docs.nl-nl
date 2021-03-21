---
title: Herkenning van animatie-tekens met Video Indexer
titleSuffix: Azure Media Services
description: In dit onderwerp ziet u hoe u met behulp van animatie teken detectie kunt gebruiken met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854747"
---
# <a name="animated-character-detection-preview"></a>Detectie van geanimeerde tekens (preview)

Azure Media Services Video Indexer ondersteunt de detectie, groepering en herkenning van tekens in inhoud met animatie via integratie met [Cognitive Services aangepaste visie](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Deze functionaliteit is beschikbaar via de portal en via de API.

Nadat u een bewegende video met een specifiek animatie model hebt geüpload, worden in Video Indexer hoofd frames geëxtraheerd, worden animatie-tekens in deze frames gedetecteerd, worden vergelijk bare tekens gegroepeerd en wordt het beste voor beeld gekozen. Vervolgens worden de gegroepeerde tekens verzonden naar Custom Vision waarmee tekens worden geïdentificeerd op basis van de modellen waarop het is getraind. 

Voordat u begint met het trainen van uw model, worden de tekens gedetecteerd namelessly. Wanneer u namen toevoegt en het model traint, worden de tekens door de Video Indexer herkend en worden ze dienovereenkomstig een naam genoemd.

## <a name="flow-diagram"></a>Stroomdiagram

In het volgende diagram ziet u de stroom van het detectie proces voor de animatie.

![Stroomdiagram](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Accounts

Afhankelijk van een type Video Indexer-account, zijn er verschillende functie sets beschikbaar. Zie [een video indexer-account maken dat is verbonden met Azure](connect-to-azure.md)voor meer informatie over het verbinden van uw account met Azure.

* Proef account: Video Indexer gebruikt een intern Custom Vision-account om een model te maken en te verbinden met uw Video Indexer-account. 
* Betaald account: u kunt uw Custom Vision-account koppelen aan uw Video Indexer-account (als u er nog geen hebt, moet u eerst een account maken).

### <a name="trial-vs-paid"></a>Proef versie versus betaald

|Functionaliteit|Proefversie|Betaald|
|---|---|---|
|Custom Vision-account|Wordt beheerd achter de schermen door Video Indexer. |Uw Custom Vision-account is verbonden met Video Indexer.|
|Aantal animatie modellen|Eén|Maxi maal 100 modellen per account (Custom Vision beperking).|
|Het model trainen|Video Indexer treinen het model voor nieuwe tekens meer voor beelden van bestaande tekens.|De eigenaar van het account traint het model wanneer ze klaar zijn om wijzigingen aan te brengen.|
|Geavanceerde opties in Custom Vision|Geen toegang tot de Custom Vision Portal.|U kunt de modellen zelf aanpassen in de Custom Vision Portal.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>De herkenning van de animatie gebruiken met behulp van de portal en API

Zie voor meer informatie [de detectie van bewegende tekens gebruiken met de portal en API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Beperkingen

* Momenteel wordt de functie voor animatie-identificatie niet ondersteund in East-Asia regio.
* Tekens die klein of ten opzichte van de video worden weer gegeven, worden mogelijk niet goed herkend als de kwaliteit van de video slecht is.
* De aanbeveling is een model te gebruiken per set met animatie tekens (bijvoorbeeld per reeks met animatie).

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)