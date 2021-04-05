---
title: 'Web Service input/output: module-verwijzing'
description: Meer informatie over het gebruik van de webservice-modules in Azure Machine Learning Designer voor het beheren van invoer en uitvoer.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421121"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Webservices-invoer en-webservice-uitvoer modules

In dit artikel worden de modules web service input en webservice-uitvoer in Azure Machine Learning Designer beschreven.

De module Web Service input kan alleen verbinding maken met een invoer poort met het type **DataFrameDirectory**. De module webservice-uitvoer kan alleen worden verbonden vanaf een uitvoer poort met het type **DataFrameDirectory**. U kunt de twee modules vinden in de module structuur, onder de categorie **Web Service** . 

De web service-invoer module geeft aan waar de gebruikers gegevens de pijp lijn invoeren. De module voor het uitvoeren van webservices geeft aan waar de gebruikers gegevens worden geretourneerd in een real-time pipeline-pijp lijn.

## <a name="how-to-use-web-service-input-and-output"></a>De invoer en uitvoer van webservices gebruiken

Wanneer u [een real-time pijp lijn](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) van uw trainings pijplijn maakt, worden de webservices-invoer en de webservice-uitvoer modules automatisch toegevoegd om aan te geven waar de gebruikers gegevens de pijp lijn binnenkomen en waar gegevens worden geretourneerd. 

> [!NOTE]
> Het automatisch genereren van een real-time pipeline-pijp lijn is een op regels gebaseerd proces voor de beste werk belasting. Er is geen garantie voor de juistheid. 

U kunt hand matig de webservices-invoer en webservice-uitvoer modules toevoegen of verwijderen om te voldoen aan uw vereisten. Zorg ervoor dat uw realtime-pijp lijn ten minste één invoer module voor de webservice en één webservice-uitvoer module heeft. Als u meerdere webservice-invoer of web service-uitvoer modules hebt, moet u ervoor zorgen dat ze unieke namen hebben. U kunt de naam opgeven in het rechterdeel venster van de module.

U kunt ook hand matig een real-time pijp lijn maken door de modules web service input en web service output toe te voegen aan uw niet-verzonden pijp lijn.

> [!NOTE]
> Het type pijp lijn wordt bepaald wanneer u het voor het eerst verzendt. Zorg ervoor dat u de web service-invoer en webservice-uitvoer modules toevoegt voordat u voor de eerste keer verzendt.

In het volgende voor beeld ziet u hoe u hand matig een real-time-pijp lijn kunt maken op basis van de script module voor het uitvoeren van python. 

![Voorbeeld](media/module/web-service-input-output-example.png)
   
Nadat u de pijp lijn hebt verzonden en de uitvoering is voltooid, kunt u het real-time-eind punt implementeren.
   
> [!NOTE]
>  Voer in het voor gaande voor beeld **hand matig gegevens** in voor de webservice-invoer en is nodig voor het implementeren van het real-time-eind punt. Over het algemeen moet u altijd een module of gegevensset verbinden met de poort waarop de **webservice-invoer** is verbonden om het gegevens schema op te geven.
   
## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het implementeren van het real-time-eind punt](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.