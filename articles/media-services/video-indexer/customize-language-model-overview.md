---
title: Een taal model aanpassen in Video Indexer-Azure
titleSuffix: Azure Media Services
description: Dit artikel geeft een overzicht van wat een taal model is in Video Indexer en hoe u het kunt aanpassen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047134"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Een taal model aanpassen met Video Indexer

Video Indexer ondersteunt automatische spraak herkenning via integratie met de micro soft- [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). U kunt het taal model aanpassen door de aanpassings tekst te uploaden, namelijk de tekst van het domein waarvan de woorden lijst de engine moet aanpassen. Zodra u uw model hebt getraind, worden nieuwe woorden die in de aanpassings tekst worden weer gegeven, herkend, aangenomen dat de standaard uitspraak en het taal model nieuwe mogelijke reeksen van woorden bevat. Aangepaste taal modellen worden ondersteund voor Engels, Spaans, Frans, Duits, Italiaans, Chinees (vereenvoudigd), Japans, Russisch, Portugees, Hindi en Koreaans. 

Laten we een woord maken dat zeer specifiek is, zoals ' Kubernetes ' (in de context van de Azure Kubernetes-service), als voor beeld. Omdat het woord nieuw is voor Video Indexer, wordt het herkend als community's. U moet het model trainen om het te herkennen als ' Kubernetes '. In andere gevallen bestaan de woorden, maar het taal model verwacht niet dat ze in een bepaalde context worden weer gegeven. Bijvoorbeeld: ' container service ' is geen reeks van twee woorden die een niet-gespecialiseerd taal model zou herkennen als een specifieke set woorden.

U hebt de optie om woorden zonder context te uploaden in een lijst in een tekst bestand. Dit wordt beschouwd als gedeeltelijke aanpassing. U kunt ook een tekst bestand (en) van documentatie of zinnen uploaden die betrekking hebben op uw inhoud voor een betere aanpassing.

U kunt de Video Indexer-Api's of de website gebruiken om aangepaste taal modellen te maken en te bewerken, zoals beschreven in de sectie [volgende stappen](#next-steps) van dit onderwerp.

## <a name="best-practices-for-custom-language-models"></a>Aanbevolen procedures voor aangepaste taal modellen

Video Indexer is gebaseerd op de waarschijnlijkheid van combi Naties van woorden, zodat u het beste kunt leren:

* Geef voldoende voor beelden van zinnen, zoals ze zouden worden gesp roken.
* Plaats slechts één zin per regel, niet meer. Anders leert het systeem waarschijnlijk meer over zinnen.
* Het is aan te raden om één woord als een zin te plaatsen om het woord te verhogen tegen anderen, maar het systeem leert het beste van volledige zinnen.
* Bij het introduceren van nieuwe woorden of acroniemen moet u, indien mogelijk, zoveel voor beelden van gebruik in een volledige zin geven om zo veel mogelijk context aan het systeem toe te voegen.
* Probeer verschillende aanpassings opties te plaatsen en te zien hoe ze werken.
* Vermijd herhalingen van exact dezelfde zin meermaals. Dit kan bias veroorzaken ten opzichte van de rest van de invoer.
* Vermijd het gebruik van niet-algemene symbolen (~, # @% &), aangezien deze worden genegeerd. De zinnen waarin ze worden weer gegeven, worden ook genegeerd.
* Vermijd het plaatsen van te grote invoer, zoals honderd duizenden zinnen, omdat hierdoor het effect van versterking wordt verkleind.

## <a name="next-steps"></a>Volgende stappen

[Taal model aanpassen met behulp van Api's](customize-language-model-with-api.md)

[Taal model aanpassen met behulp van de website](customize-language-model-with-website.md)
