---
title: Geavanceerde coderings werk stromen maken met Workflow Designer | Microsoft Docs
description: Meer informatie over het maken van geavanceerde encoding-werk stromen met Workflow Designer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 744d302afd21e6521fb17bf7bef6e68d21fb8372
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639385"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Geavanceerde encoding-werkstromen maken met Workflow Designer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht
De **Workflow Designer** is een Windows-bureau blad dat wordt gebruikt voor het ontwerpen en bouwen van aangepaste werk stromen voor code ring met **Media Encoder Premium workflow**.
Met de kracht van het hulp programma werk stroom ontwerper kunt u complexe werk stromen ontwerpen en maken die worden uitgevoerd in **Media Encoder Premium**.  

Werk stromen kunnen een beslissings logica van de klant en vertakkingen omvatten op basis van de eigenschappen van het invoer bron bestand. U kunt werk stromen maken met Overschrijf bare-eigenschappen en dynamische waarden, zodat u zelfs de meest complexe coderings taken eenvoudig kunt herhalen en aanpassen in de Cloud.

Voor beelden van werk stromen die u kunt maken zijn:

* Op beslissingen gebaseerde werk stromen die de bron inhoud controleren op omzetting en alleen de gewenste uitvoer traceringen coderen.  Dit is handig door de verspilde sporen te elimineren die zouden worden gegenereerd door de bron inhoud per ongeluk te schalen.
* Meerdere invoer bestanden kunnen worden gebruikt ter ondersteuning van bijschriften, overlays en het samen voegen van inhoud. 

Dit hulp programma kan ook worden gebruikt om een van onze [gepubliceerde werk stromen](media-services-workflow-designer.md#existing_workflows)te wijzigen. 

> [!NOTE]
> Neem contact op met om uw exemplaar van het Workflow Designer-hulp programma te verkrijgen mepd@microsoft.com .

Zodra een werk stroom bestand is gemaakt, kan het worden geüpload als een Asset en vervolgens worden gebruikt voor het coderen van media bestanden. Zie [geavanceerde code ring met Media Encoder Premium workflow](media-services-encode-with-premium-workflow.md)voor meer informatie over het coderen met **Media Encoder Premium workflow** met behulp van **.net**.

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Bestaande werk stromen wijzigen
De standaard [gepubliceerde werk stromen](media-services-workflow-designer.md#existing_workflows) kunnen worden gewijzigd met behulp van het ontwerp hulpprogramma. U kunt [hier](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md)de standaard werk stroom bestanden ophalen. De map bevat ook de beschrijving van deze bestanden.

De volgende Video's laten zien hoe u de ontwerp functie kunt gebruiken.

### <a name="day-1--getting-started"></a>Dag 1: aan de slag
Video van dag 1:

* Overzicht van de ontwerp functie
* Basis werk stromen – "Hallo wereld"
* Maken van meerdere MP4-uitvoer bestanden voor gebruik met Azure Media Services streaming

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dag 2
Dag 2 video omvat:

* Scenario's voor verschillende bron bestanden: verwerking van audio
* Werk stromen met geavanceerde logica
* Grafiek fasen

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dag 3
Video over dag 3:

* Scripts in werk stromen/blauw drukken
* Beperkingen met de huidige encoder
* Q&A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Hulp nodig?

U kunt een ondersteunings ticket openen door te navigeren naar de [nieuwe ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Volgende stap
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Trainings Video's voor Azure Premium encoder Workflow Designer](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

