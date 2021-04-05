---
title: Een merk model aanpassen in Video Indexer-Azure
titleSuffix: Azure Media Services
description: Dit artikel geeft een overzicht van wat een merk model is in Video Indexer en hoe u het kunt aanpassen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586071"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Een merk model aanpassen in Video Indexer

Video Indexer ondersteunt merk detectie van spraak-en visuele tekst tijdens het indexeren en opnieuw indexeren van video-en audio-inhoud. De merk detectie functie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merken database van Bing. Als micro soft bijvoorbeeld wordt vermeld in een video-of audio-inhoud of als het in visuele tekst in een video wordt weer gegeven, detecteert Video Indexer deze als een merk in de inhoud. Brands worden disambiguated uit andere termen die gebruikmaken van context.

Merk detectie is handig voor een groot aantal bedrijfs scenario's, zoals het archiveren van inhoud en detectie, contextuele advertenties, analyse van sociale media, analyse van de detail handel en nog veel meer. Met Video Indexer merk detectie kunt u merk vermeldingen indexeren in spraak-en visuele tekst, met behulp van de merken-data base van Bing en de aanpassing door een aangepast merk model te bouwen voor elk Video Indexer-account. Met de functie voor het aangepaste merk model kunt u selecteren of Video Indexer Brands detecteert van de Bing Brands-data base, dat bepaalde merken niet worden gedetecteerd (in wezen een lijst van niet-goedgekeurde merken te maken) en dat er Brands worden opgenomen die deel uitmaakt van uw model en dat zich mogelijk niet in de merken database van Bing bevinden (in feite een lijst met goedgekeurde merken maken) Het aangepaste merk model dat u maakt, is alleen beschikbaar in het account waarin u het model hebt gemaakt.

## <a name="out-of-the-box-detection-example"></a>Voor beeld van de detectie van het vak

In de presentatie ' micro soft build 2017 Day 2 ' wordt het merk ' micro soft Windows ' meerdere keren weer gegeven. Soms in het transcript, soms als visuele tekst en nooit als Verbatim. Video Indexer detecteert met zeer nauw keurigheid dat een term op basis van de context inderdaad is gebaseerd op het 90k Brands-merk en voortdurend wordt bijgewerkt. Bij 02:25 detecteert Video Indexer het merk van spraak en vervolgens opnieuw op 02:40 van visuele tekst, die deel uitmaakt van het Windows-logo.

![Overzicht van Brands](./media/content-model-customization/brands-overview.png)

Het praten over Windows in de context van de constructie detecteert het woord ' Windows ' niet als een merk en is hetzelfde voor box, Apple, Fox, etc., op basis van geavanceerde Machine Learning-algoritmen die weten hoe u dubbel zinnigheid van de context. Merk detectie werkt voor alle ondersteunde talen.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende onderwerpen om uw eigen merken te maken:

[Het merk model aanpassen met behulp van Api's](customize-brands-model-with-api.md)

[Brands model aanpassen met behulp van de website](customize-brands-model-with-website.md)
