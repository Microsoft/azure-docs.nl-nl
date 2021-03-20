---
title: Veelgestelde vragen-Computer Vision
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op veelgestelde vragen over de Computer Vision-API in azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68564606"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Veelgestelde vragen over Computer Vision-API

> [!TIP]
> Als u in deze veelgestelde vragen geen antwoorden op uw vragen kunt vinden, kunt u de Computer Vision-API Community vragen op [stack overflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) of contact opnemen met de [Help en ondersteuning op UserVoice](https://cognitive.uservoice.com/)

---

**Vraag**: *kan ik computer vision-API trainen om aangepaste labels te gebruiken?  Ik wil bijvoorbeeld dat ik in afbeeldingen van kat rassen de AI ' Train ', en vervolgens de RAS-waarde op een AI-aanvraag kan ontvangen.*

**Antwoord**: deze functie is momenteel niet beschikbaar. Onze technici werken echter aan om deze functionaliteit te Computer Vision.

---

**Vraag**: *kan computer vision lokaal worden gebruikt zonder Internet verbinding?*

**Antwoord**: we bieden momenteel geen on-premises of lokale oplossing.

---

**Vraag**: *kan computer vision worden gebruikt voor het lezen van de licentie platen?*

**Antwoord**: de Vision-API biedt een goede detectie van tekst met OCR, maar is momenteel niet geoptimaliseerd voor licentie platen. We proberen onze services voortdurend te verbeteren en hebben OCR toegevoegd voor de erkenning van automatische licentie plaat aan onze lijst met functie aanvragen.

---

**Vraag**: *welke typen schrijf oppervlakken worden ondersteund voor handschrift herkenning?*

**Antwoord**: de technologie werkt met verschillende soorten Opper vlakken, waaronder white boards, wit papier en gele plak notities.

---

**Vraag**: *hoe lang duurt de bewerking voor handschrift herkenning?*

**Antwoord**: de hoeveelheid tijd die nodig is, is afhankelijk van de lengte van de tekst. Voor langere teksten kan het enkele seconden duren. Nadat de bewerking handgeschreven tekst herkennen is voltooid, moet u daarom mogelijk wachten voordat u de resultaten kunt ophalen met behulp van de bewerking handgeschreven tekst bewerking ophalen.

---

**Vraag**: *Hoe gaat de technologie voor handschrift herkenning tekst die is ingevoegd met een caret in het midden van een regel?*

**Antwoord**: deze tekst wordt geretourneerd als afzonderlijke regel door de bewerking voor handschrift herkenning.

---

**Vraag**: *Hoe wordt de technologie voor handschrift herkenning gebruikt voor het afhandelen van woorden of lijnen?*

**Antwoord**: als de woorden worden gepasseerd met meerdere regels om ze onherkenbaar weer te geven, worden ze niet door de bewerking voor handschrift herkenning gekozen. Als de woorden echter worden gepasseerd met behulp van één regel, wordt die kruising beschouwd als ruis en de woorden worden nog steeds opgehaald door de bewerking voor handschrift herkenning.

---

**Vraag**: *welke tekst standen worden ondersteund voor de technologie voor handschrift herkenning?*

**Antwoord**: tekst gericht op hoeken van Maxi maal 30 graden tot 40 graden kan worden opgehaald door de bewerking voor handschrift herkenning.

---
