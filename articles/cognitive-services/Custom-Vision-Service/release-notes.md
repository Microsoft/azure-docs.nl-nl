---
title: Release opmerkingen-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ontvang de meest recente informatie over nieuwe releases van het Custom Vision-team.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: 3724a7d515197c1f969bb051fc201b82bee64c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97813493"
---
# <a name="custom-vision-service-release-notes"></a>Opmerkingen bij de release Custom Vision Service

## <a name="may-2-2019-and-may-10-2019"></a>2 mei 2019 en 10 mei 2019

- Verbeteringen in bugfixes en back-end

## <a name="may-23-2019"></a>23 mei 2019

- Verbeterde portal UX-ervaring met Azure-abonnementen, waardoor het eenvoudiger wordt om uw Azure-mappen te selecteren.

## <a name="april-18-2019"></a>18 april 2019 

- Export van object detectie is toegevoegd voor de Vision AI dev kit.
- UI-aanpassingen, waaronder project zoeken.

## <a name="april-3-2019"></a>3 april 2019

- De limiet voor het aantal selectie vakjes per afbeelding is verhoogd tot 200. 
- Bugfixes, met inbegrip van belang rijke prestatie-updates voor modellen die zijn geëxporteerd naar tensor flow. 

## <a name="march-26-2019"></a>26 maart 2019

- Custom Vision Service is algemene Beschik baarheid in azure ingevoerd.
- Er is een geavanceerde trainings functie toegevoegd met een nieuwe machine learning back-end voor betere prestaties, met name op uitdagende gegevens sets en een verfijnde classificatie. Met geavanceerde training kunt u een reken tijd-budget voor training en Custom Vision een experimenteel identificeren van de beste trainingen en uitbrei ding van de instellingen. Voor snelle herhalingen kunt u de bestaande snelle training blijven gebruiken.
- 3,0 Api's geïntroduceerd. Er wordt op 1 oktober 2019 een afschaffing van vóór 3,0 Api's aangekondigd. Raadpleeg de documentatie [Quick](./quickstarts/image-classification.md) starts voor voor beelden over hoe u aan de slag kunt gaan.
- "Standaard herhalingen" vervangen door publiceren/ongedaan maken in de 3,0-Api's.
- Er zijn nieuwe model export doelen toegevoegd. Dockerfile export is bijgewerkt om ARM voor Raspberry Pi 3 te ondersteunen. Ondersteuning voor exporteren is toegevoegd aan de [Vision AI dev kit.](https://visionaidevkit.com/)
- De limiet voor labels per project is verhoogd tot 500 voor de S0-laag. De limiet voor afbeeldingen per project is verhoogd tot 100.000 voor de S0-laag.
- Het domein voor volwassenen is verwijderd. In plaats daarvan wordt het algemene domein aanbevolen.
- Aangekondigde [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor algemene Beschik baarheid.  

## <a name="february-25-2019"></a>25 februari 2019

- Het einde van projecten met een beperkte proef versie (projecten die niet zijn gekoppeld aan een Azure-resource) is aangekondigd, omdat Custom Vision bijna de voltooiing van de overstap naar Azure open bare preview is voltooid. Vanaf 25 maart 2019 biedt de site CustomVision.ai alleen ondersteuning voor het weer geven van projecten die zijn gekoppeld aan een Azure-resource, zoals de gratis Custom Vision resource. Tot en met 1 oktober 2019 hebt u nog steeds toegang tot uw bestaande projecten met een beperkte proef versie via de Custom Vision-Api's. Dit geeft u de tijd om de API-sleutels bij te werken voor alle apps die u hebt geschreven met Custom Vision. Na 1 oktober 2019 worden alle beperkte proef projecten die u niet naar Azure hebt verplaatst, verwijderd.

## <a name="january-22-2019"></a>22 januari 2019

- Ondersteuning toegevoegd voor nieuwe Azure-regio's: VS-West 2, VS-Oost, VS-Oost 2, Europa-west, Europa-noord, Zuidoost-Azië, Australië-oost, Centraal-India, UK-zuid, Japan-Oost en Noord-Centraal vs. Ondersteuning wordt voortgezet voor Zuid-Centraal vs.

## <a name="december-12-2018"></a>12 december 2018

- Ondersteuning voor het exporteren van object detectie modellen (geïntroduceerd compact domain voor object detectie).
- Er zijn een aantal toegankelijkheids problemen opgelost voor verbeterde ondersteuning van scherm lezers en toetsenbord navigatie.
- UX-updates voor Image Viewer en verbeterde coderings ervaring voor object detectie voor snellere tagging.  
- Het basis model voor object detectie domein is bijgewerkt voor betere kwaliteit van object detectie.
- Oplossingen voor fouten.

## <a name="november-6-2018"></a>6 november 2018

- Er is ondersteuning toegevoegd voor het logo domein in object detectie.

## <a name="october-9-2018"></a>9 oktober 2018

- Er wordt een betaalde preview geactiveerd voor object detectie. U kunt nu object detectie projecten maken met een Azure-resource.
- De functie ' verplaatsen naar Azure ' is toegevoegd aan de website, zodat u een beperkte proef project kunt bijwerken om een koppeling naar een Azure te maken. aan resource gekoppeld project (F0 of s0.) U kunt dit vinden op de pagina instellingen voor uw product.  
- De export naar ONNX 1,2 is toegevoegd ter ondersteuning van Windows 2018 oktober update versie van Windows ML.
Oplossingen voor fouten, waaronder ONNX-export met speciale tekens.

## <a name="august-14-2018"></a>14 augustus 2018

- De widget ' aan de slag ' is toegevoegd aan de customvision.ai-site om gebruikers te begeleiden via Project training.
- Verdere verbeteringen in de machine learning pijp lijn voor het voor deel van projecten met meerdere etiketten (nieuwe verlies laag).

## <a name="june-28-2018"></a>28 juni 2018

- Problemen & back-end-verbeteringen.
- Ingeschakelde classificatie voor meerdere klassen, voor projecten waarbij afbeeldingen precies één label hebben. In de voor spellingen voor de multi klasse-modus wordt waarschijnlijk opgeteld bij één (alle installatie kopieën worden geclassificeerd op de opgegeven Tags).

## <a name="june-13-2018"></a>13 juni 2018

- UX-vernieuwing, gericht op gebruiks gemak en toegankelijkheid.
- Verbeteringen in de machine learning-pijp lijn voor het delen van projecten met een groot aantal tags.
- Er is een fout opgelost in tensor flow export. De versie beheer van het geëxporteerde model is ingeschakeld, waardoor herhalingen meermaals kunnen worden geëxporteerd.

## <a name="may-7-2018"></a>7 mei 2018

- Preview-functie voor objectdetectie geïntroduceerd voor beperkte proefprojecten.
- Upgrade naar 2.0 API’s
- S0-laag is uitgebreid naar 250 labels en 50.000 afbeeldingen.
- Belangrijke back-endverbeteringen aan de machine learning-pijplijn voor classificatieprojecten voor afbeeldingen. Projecten die zijn getraind na 27 april 2018, profiteren van deze updates.
- Modelexport naar ONNX toegevoegd voor gebruik met Windows ML.
- Modelexport naar DockerFile toegevoegd. Dit stelt u in staat om de artefacten te downloaden om uw eigen Windows- en Linux-containers te bouwen, inclusief een DockerFile, TensorFlow-model en servicecode.
- Voor nieuwe getrainde modellen die zijn geëxporteerd naar tensor flow in het algemeen (compact) en het oriëntatie punt (compact)-domeinen, [zijn de gemiddelde waarden nu (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), voor consistentie tussen alle projecten.

## <a name="march-1-2018"></a>1 maart 2018

- De betaalde preview en de onboarding van de Azure Portal. Projecten kunnen nu worden gekoppeld aan Azure-resources met een F0-laag (gratis) of een S0-laag (Standard). Introductie van S0-laagprojecten die ruimte bieden voor maximaal 100 labels en 25.000 afbeeldingen.
- Back-endwijzigingen in de machine learning-pijplijn/-normalisatieparameter. Hierdoor hebben klanten meer controle bij afwegingen over de precisie van de resultaten bij het aanpassen van de waarschijnlijkheidsdrempelwaarde. Als onderdeel van deze wijzigingen is de standaardwaarde van de waarschijnlijkheidsdrempel in de CustomVision.ai-portal ingesteld op 50%.

## <a name="december-19-2017"></a>19 december 2017

- Exporteren naar Android (tensor flow), naast eerder uitgebrachte export naar iOS (CoreML.) Zo kunt u een getraind compact model exporteren dat offline moet worden uitgevoerd in een toepassing.
- Domeinen Detailhandel en Oriëntatiepunt (compact) zijn toegevoegd om modelexports voor deze domeinen in te schakelen.
- Uitgebrachte versie [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) en [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Bijgewerkte API’s bieden ondersteuning voor modelexports, nieuwe voorspellingsbewerking waarmee afbeeldingen niet worden opgeslagen in Voorspellingen, en introductie van batchbewerkingen in de Training API.
- UX-aanpassingen, waaronder de mogelijkheid om te zien welk domein is gebruikt om een iteratie te trainen.
- Bijgewerkte [C# SDK en voorbeeld](https://github.com/Microsoft/Cognitive-CustomVision-Windows).