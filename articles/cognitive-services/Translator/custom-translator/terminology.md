---
title: Sleutel termen-aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Lijst met belang rijke termen die worden gebruikt in aangepaste Vertaal artikelen.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/02/2021
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4a0308f2b961ce9b1321fab3e202e038eb199674
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286416"
---
# <a name="custom-translator-key-terms"></a>Aangepaste voor waarden van de vertaler

De volgende tabel bevat een lijst met belang rijke termen die u tijdens het werken met de [aangepaste vertaler](https://portal.customtranslator.azure.ai)kunt vinden.

| Woord of woord groep|Definitie|
|------------------|-----------|
| Bron taal | De bron taal is de start taal die u wilt converteren naar een andere taal (het doel).|
| Doel taal| De doel taal is de taal waarvan u wilt dat de machine vertaling wordt aangeboden nadat de bron taal is ontvangen. |
| Monolingual-bestand | Een Monolingual-bestand heeft één taal die niet is gekoppeld aan een ander bestand in een andere taal. |
| Parallelle bestanden | Een parallel bestand is een combi natie van twee bestanden met bijbehorende tekst. Eén bestand heeft de bron taal. De andere heeft de doel taal.|
| Uitlijning van zin| Parallelle gegevensset moet uitgelijnde zinnen bevatten voor zinnen die dezelfde tekst in beide talen vertegenwoordigen. Bijvoorbeeld: in een bron-parallel bestand moet de eerste zin in theorie worden toegewezen aan de eerste zin in het parallelle doel bestand.|
| Uitgelijnde tekst | Een van de belangrijkste stappen voor bestands validatie is het uitlijnen van de zinnen in de parallelle documenten. Dingen worden in verschillende talen anders weer gegeven. Daarnaast hebben verschillende talen verschillende woord orders. Met deze stap kunt u de zinnen uitlijnen met dezelfde inhoud zodat ze kunnen worden gebruikt voor de training. Een uitlijning met een lage zin geeft aan dat er iets mis is met een of beide bestanden. |
| Woord afbreking/ontbreken | Woord afbreking is de functie voor het markeren van de grenzen tussen woorden. Veel schrijf systemen gebruiken een ruimte om de grens tussen woorden aan te duiden. Woord breuk verwijst naar het verwijderen van een zicht bare markering die tussen woorden in een vorige stap is ingevoegd. |
| Scheidings tekens   | Scheidings tekens zijn de manieren waarop een zin is opgesplitst in segmenten of de marge tussen zinnen te scheiden. Bijvoorbeeld: in de Engelse ruimten worden woorden, dubbele punten en punt komma's als scheidings teken voor componenten en peri Oden als scheidings teken beperkt. |
| Trainings bestanden | Een trainings bestand wordt gebruikt om het machine translation-systeem te leren hoe ze vanuit de ene taal (de bron) moeten worden toegewezen aan een doel taal (het doel). Hoe meer gegevens u levert, hoe beter het systeem wordt uitgevoerd. |
| Bestanden afstemmen | Deze bestanden worden vaak wille keurig afgeleid van de Trainingsset (als u geen afstemmings reeks selecteert). De zinnen zijn automatisch geselecteerd en worden gebruikt om het systeem af te stemmen en ervoor te zorgen dat het goed werkt. Als u een model voor algemene vertaling wilt maken en uw eigen afstemmings bestanden wilt maken, moet u ervoor zorgen dat ze een wille keurige set zinnen in meerdere domeinen zijn |
| Bestanden testen| Deze bestanden zijn vaak afgeleide bestanden die wille keurig uit de Trainingsset zijn geselecteerd (als u geen testset selecteert). Het doel van deze zinnen is om de nauw keurigheid van het Vertaal model te evalueren. Omdat deze zinnen u wilt controleren, moet u ervoor zorgen dat het systeem nauw keurig wordt gevertaald. u kunt een testset maken en deze uploaden naar het conversie programma. Dit zorgt ervoor dat deze zinnen worden gebruikt in de evaluatie van het systeem (de generatie van een BLEU-Score).   |
| Combinatie bestand   | Een type bestand waarin de bron en de vertaalde zinnen zijn opgenomen in hetzelfde bestand. Ondersteunde bestands indelingen (. TMX,. XLIFF,. XLF,. ICI,. XLSX). |
| Archief bestand | Een bestand dat andere bestanden bevat. Ondersteunde bestands indelingen (zip, gz, tgz).  |
| BLEU-score   | [Bleu](what-is-bleu-score.md) is de industrie standaard methode voor het evalueren van de "Precision" of nauw keurigheid van het Vertaal model. Hoewel er andere evaluatie methoden bestaan, is micro soft Translator afhankelijk van de methode BLEU om de nauw keurigheid van project eigenaren te rapporteren.
