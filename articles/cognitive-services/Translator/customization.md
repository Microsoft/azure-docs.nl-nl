---
title: Aanpassing van vertalingen-Translator
titleSuffix: Azure Cognitive Services
description: Gebruik de micro soft Translator-hub om uw eigen machine-Vertaal systeem te bouwen met behulp van de gewenste terminologie en stijl.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898033"
---
# <a name="customize-your-text-translations"></a>Uw tekst vertalingen aanpassen

Het aangepaste conversie programma is een functie van de Translator-service, waarmee gebruikers de geavanceerde Neural machine vertalingen van micro soft Translator kunnen aanpassen bij het vertalen van tekst met behulp van Translator (alleen versie 3).

De functie kan ook worden gebruikt om spraak omzetting aan te passen wanneer u deze gebruikt met [Cognitive Services speech](../speech-service/index.yml).

## <a name="custom-translator"></a>Custom Translator

Met Custom Translator kunt u Neural-Vertaal systemen bouwen die inzicht hebben in de terminologie die wordt gebruikt in uw eigen bedrijf en branche. Het aangepaste Vertaal systeem wordt vervolgens geïntegreerd in bestaande toepassingen, werk stromen en websites.

### <a name="how-does-it-work"></a>Hoe werkt het?

Gebruik uw eerder vertaalde documenten (folders, webpagina's, documentatie enz.) om een Vertaal systeem te maken dat overeenkomt met uw specifieke terminologie en stijl van uw domein, beter dan een standaard Vertaal systeem. Gebruikers kunnen TMX-, XLIFF-, TXT-, DOCX-en XLSX-documenten uploaden.  

Het systeem accepteert ook gegevens die parallel zijn op document niveau, maar die nog niet zijn afgestemd op het niveau van de zin. Als gebruikers toegang hebben tot versies van dezelfde inhoud in meerdere talen, maar in afzonderlijke documenten aangepaste vertalers kunnen automatisch worden afgestemd op de verschillende documenten.  Het systeem kan ook Monolingual-gegevens in een of beide talen gebruiken om de parallelle trainings gegevens aan te vullen om de vertalingen te verbeteren.

Het aangepaste systeem is vervolgens beschikbaar via een regel matige aanroep naar Translator met de para meter Category.

Gezien het passende type en de juiste hoeveelheid trainings gegevens, is het niet ongebruikelijk om te verwachten dat er geen winst meer is tussen 5 en 10, of zelfs meer BLEU punten op Vertaal kwaliteit door gebruik te maken van aangepaste vertalers.

Meer informatie over de verschillende aanpassings niveaus op basis van beschik bare gegevens vindt u in de [Gebruikers handleiding voor aangepaste vertalers](./custom-translator/overview.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast taal systeem instellen met behulp van aangepaste Translator](./custom-translator/overview.md)
