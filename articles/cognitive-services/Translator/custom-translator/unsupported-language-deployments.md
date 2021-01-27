---
title: Niet-ondersteunde taal implementaties-aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven hoe u niet-ondersteunde taal paren in azure Cognitive Services Custom Translator implementeert.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898067"
---
# <a name="unsupported-language-deployments"></a>Implementaties in niet-ondersteunde talen

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Met de aanstaande beëindiging van de micro soft Translator-hub zal micro soft de implementatie van alle modellen die momenteel via de hub zijn geïmplementeerd, onbeschikbaar maken. Veel van uw modellen zijn geïmplementeerd in de hub waarvan de taal paren niet worden ondersteund in het aangepaste conversie programma.  We willen niet dat gebruikers in deze situatie geen verdere voor het vertalen van hun inhoud hebben.

We hebben nu een proces waarmee u uw niet-ondersteunde modellen kunt implementeren via het aangepaste conversie programma.  Dit proces stelt u in staat om inhoud te blijven vertalen met behulp van de meest recente v3 API.  Deze modellen worden gehost totdat u ervoor kiest om deze te deimplementeren of het taal paar wordt beschikbaar in het aangepaste conversie programma.  In dit artikel wordt het proces beschreven voor het implementeren van modellen met niet-ondersteunde taal paren.

## <a name="prerequisites"></a>Vereisten

Om ervoor te zorgen dat uw modellen kandidaten zijn voor implementatie, moeten ze voldoen aan de volgende criteria:
* Het project met het model moet zijn gemigreerd van de hub naar de aangepaste vertaler met het migratie programma.  Het proces voor het migreren van projecten en werk ruimten kunt u [hier](how-to-migrate.md)vinden.
* Het model moet zich in de geïmplementeerde status bevinden wanneer de migratie plaatsvindt.  
* Het taal paar van het model moet een niet-ondersteund taal paar zijn in het aangepaste conversie programma.  Taal paren waarin een taal wordt ondersteund naar of van het Engels, maar het paar zelf niet de Engelse versie bevat, zijn kandidaten voor niet-ondersteunde taal implementaties.  Zo wordt bijvoorbeeld een hub-model voor een Frans-naar-Duits taal paar beschouwd als een niet-ondersteund taal paar, ook al is Frans naar Engels en Engels naar Duits ondersteund taal paar.

## <a name="process"></a>Proces
Zodra u modellen van de hub hebt gemigreerd die kandidaten zijn voor implementatie, kunt u deze vinden door naar de pagina **instellingen** voor uw werk ruimte te gaan en naar het einde van de pagina te schuiven, waar u een **niet-ondersteunde sectie training voor Translator hub** ziet.  Deze sectie wordt alleen weer gegeven als u projecten hebt die voldoen aan de hierboven vermelde vereisten.

![Scherm opname van de sectie met niet-ondersteunde training voor Translator hub.](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Op de **niet-ondersteunde pagina training voor Translator hub** bevat het tabblad niet- **aangevraagde opleidingen** modellen die in aanmerking komen voor implementatie.  Selecteer de modellen die u wilt implementeren en dien een aanvraag in.   Vóór de implementatie deadline van 30 april kunt u net zoveel modellen selecteren als u wilt voor de implementatie.
 
![Scherm opname van het tabblad niet-aangevraagde opleidingen.](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Na verzen ding is het model niet meer beschikbaar op het tabblad niet- **aangevraagde opleidingen** en wordt het niet meer weer gegeven op het tabblad **aangevraagde opleidingen** .  U kunt uw aangevraagde trainingen op elk gewenst moment weer geven.

![Migreren vanaf hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Volgende stappen

De modellen die u hebt geselecteerd voor implementatie, worden opgeslagen nadat de hub uit bedrijf is genomen en alle modellen niet meer zijn geïmplementeerd.  U hebt tot 24 mei aanvragen voor de implementatie van niet-ondersteunde modellen te verzenden.  Deze modellen worden op 15 juni geïmplementeerd, op het moment dat ze toegankelijk zijn via de Translator v3 API.  Bovendien zullen ze tot 1 juli via de v2 API beschikbaar zijn.  

Zie [hier](https://www.microsoft.com/translator/business/hub/)voor meer informatie over belang rijke datums in de afschaffing van de hub.
Zodra de implementatie is geïmplementeerd, worden de normale hosting kosten toegepast.  Zie de [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) voor meer informatie.  

Hub modellen zijn, in tegens telling tot standaard aangepaste Translator-modellen, alleen beschikbaar in één regio, dus de kosten voor het hosten van meerdere regio's zijn niet van toepassing.  Na de implementatie kunt u de implementatie van uw hub-model op elk gewenst moment ontoegankelijk maken via het gemigreerde aangepaste Translator-project.

## <a name="next-steps"></a>Volgende stappen

- [Train een model](how-to-train-model.md).
- Begin met het gebruik van uw geïmplementeerde aangepaste Vertaal model via [micro soft Translator text-API v3](../reference/v3-0-translate.md?tabs=curl).