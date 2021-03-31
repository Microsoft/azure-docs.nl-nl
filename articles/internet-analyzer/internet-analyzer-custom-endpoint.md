---
title: Een aangepast eindpunt maken | Microsoft Docs
description: In dit artikel leert u hoe u een aangepast eindpunt kunt configureren om te meten met uw Internet Analyzer-resource.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6ca71bce726f16eeacc96e10eb654bb3e21c5924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744082"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Aangepaste eindpunten meten om te evalueren in uw Internet Analyzer-tests 

In dit artikel wordt getoond hoe u een aangepast eindpunt instelt om te meten als onderdeel van uw Internet Analyzer-tests. Aangepaste eindpunten helpen bij het evalueren van on-premises workloads, workloads die worden uitgevoerd bij andere cloudproviders, en aangepaste Azure-configuraties.  Het is mogelijk om twee aangepaste eindpunten te vergelijken in één test, indien één van de eindpunten een Azure-resource is. Bekijk het [overzicht](internet-analyzer-overview.md) voor meer informatie over Internet Analyzer. 

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Internet Analyzer-resource instelt en de optie Aangepast eindpunt. Internet Analyzer gaat ervanuit dat uw aangepaste eindpunt toegankelijk is via internet. Zie [Een Internet Analyzer-resource maken](internet-analyzer-create-test-portal.md) voor meer informatie.


## <a name="create-custom-endpoint"></a>Aangepast eindpunt maken

1. Download [hier](https://fpc.msedge.net/apc/trans.gif) een transparante testafbeelding van één pixel. Deze afbeelding van één pixel is het activum dat met de JavaScript-client wordt opgehaald om de prestaties te meten.
2. Implementeer de testafbeelding, in de aangepaste webtoepassing, in een openbaar toegankelijk pad. Het pad moet werken via HTTPS. 
3. Kopieer de volledige URL voor het aangepaste eindpunt (bijvoorbeeld `https://contoso.com/test/trans.gif`) in het veld Aangepast eindpunt tijdens het maken van de test.

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md)

