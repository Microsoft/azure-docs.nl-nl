---
title: Selecteer een domein voor een Custom Vision project-Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u in de Custom Vision Service een domein selecteert voor uw project.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 0dbd6ea13069b72e6bca5c065af92568a5c7cdb8
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97844937"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Een domein voor een Custom Vision project selecteren

Op het tabblad instellingen van het Custom Vision project kunt u een domein voor uw project selecteren. Kies het domein dat zich het dichtst bij uw scenario bevindt. Als u toegang hebt tot Custom Vision via een client bibliotheek of REST API, moet u een domein-ID opgeven bij het maken van het project. U kunt een lijst met domein-Id's ophalen met behulp van [domeinen ophalen](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)of de onderstaande tabel gebruiken.

## <a name="image-classification"></a>Classificatie van afbeeldingen

|Domain|Doel|
|---|---|
|__Algemeen__| Geoptimaliseerd voor een breed scala aan afbeeldingsclassificatietaken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u wilt kiezen, selecteert u het domein algemeen. ID `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Voedsel__|Geoptimaliseerd voor foto's van gerechten zoals op de menukaart van een restaurant. Gebruik het domein Voedsel als u foto's van afzonderlijke soorten fruit of groenten wilt classificeren. ID `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Oriëntatiepunten__|Geoptimaliseerd voor herkenbare oriëntatiepunten, zowel natuurlijke als kunstmatige. Dit domein werkt het beste wanneer het oriëntatiepunt duidelijk te zien is in de foto. Dit domein werkt ook als het oriëntatiepunt slechts gedeeltelijk zichtbaar is omdat er mensen voor staan. ID `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Retail__|Geoptimaliseerd voor afbeeldingen zoals die te vinden zijn in de catalogus of op de website van een winkel. Gebruik dit domein voor een nauwkeurige classificatie van kleding, zoals jurken, broeken en shirts. ID `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time classificatie op edge-apparaten.|

## <a name="object-detection"></a>Objectdetectie

|Domain|Doel|
|---|---|
|__Algemeen__| Geoptimaliseerd voor een breed scala aan objectdetectietaken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u wilt kiezen, selecteert u het domein algemeen. ID `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Logo__|Geoptimaliseerd voor het vinden van merklogo's in afbeeldingen. ID `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Producten op schappen__|Geoptimaliseerd voor het detecteren en classificeren van producten op schappen. ID `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van real-time object detectie op edge-apparaten.|

## <a name="compact-domains"></a>Compacte domeinen

De modellen die door compacte domeinen worden gegenereerd, kunnen worden geëxporteerd om lokaal te worden uitgevoerd. In de open bare preview-API van Custom Vision 3,4 kunt u een lijst met de Exporteer bare platforms voor compacte domeinen verkrijgen door de GetDomains-API aan te roepen.

Model prestaties verschillen per geselecteerd domein. In de onderstaande tabel rapporteren we de model grootte en de tijd voor het afnemen van de Intel-Desktop-CPU en NVidia GPU \[ 1 \] . Deze getallen bevatten geen voorverwerkende en postprocessing tijd.

|Taak|Domain|Id|Modelgrootte|Time-outtijd van CPU|Time-outtijd GPU|
|---|---|---|---|---|---|
|Classificatie|Algemeen (compact)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|5 MB|13 MS|5 MS|
|Objectdetectie|Algemeen (compact)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 MS|
|Objectdetectie|Algemeen (compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 MS|

>[!NOTE]
>__Algemeen (compact)__ domein voor object detectie vereist speciale postprocessing-logica. Raadpleeg voor meer informatie een voorbeeld script in het geëxporteerde zip-pakket. Als u een model nodig hebt zonder de postprocessing Logic, gebruikt u __Algemeen (compact) [S1]__.

>[!IMPORTANT]
>Er is geen garantie dat de geëxporteerde modellen precies hetzelfde resultaat hebben als de Voorspellings-API in de Cloud. Een enigszins verschil in het actieve platform of de voor verwerking van de implementatie kan een groter verschil veroorzaken in de model uitvoer. Zie [dit document](quickstarts/image-classification.md)voor meer informatie over de logica voor voor verwerking.

\[1 \] Intel Xeon E5-2690 CPU en Nvidia Tesla M60
