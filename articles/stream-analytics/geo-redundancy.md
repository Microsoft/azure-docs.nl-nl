---
title: Geografische redundantie voor Azure Stream Analytics taken
description: In dit artikel wordt beschreven hoe u de geo-redundantie van Azure Stream Analytics taken in plaats van geo-failover kunt gebruiken.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015518"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Geografische redundantie voor Azure Stream Analytics taken

Azure Stream Analytics biedt geen automatische geo-failover, maar u kunt geografische redundantie bezorgen door identieke Stream Analytics taken in meerdere Azure-regio's te implementeren. Elke taak maakt verbinding met een lokale invoer en lokale uitvoer bronnen. Het is de verantwoordelijkheid van uw toepassing voor het verzenden van invoer gegevens naar de twee regionale invoer en het afstemmen tussen de twee regionale uitvoer. De Stream Analytics taken zijn twee afzonderlijke entiteiten.

In het volgende diagram ziet u een voor beeld van een geo-redundante Stream Analytics taak implementatie met Event hub-invoer en Azure data base-uitvoer.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="diagram van geo-redundante stream Analytics-taken":::

## <a name="primarysecondary-strategy"></a>Primaire/secundaire strategie

Uw toepassing moet beheren welke uitvoer database van de regio als primair wordt beschouwd en wordt beschouwd als de secundaire data base. Als er een fout optreedt in de primaire regio, wordt de toepassing overgeschakeld naar de secundaire data base en worden updates van die data base gestart. Het daad werkelijke mechanisme waarmee dubbele Lees bewerkingen kunnen worden geminimaliseerd, is afhankelijk van uw toepassing.U kunt dit proces vereenvoudigen door extra informatie te schrijven naar de uitvoer. U kunt bijvoorbeeld een time stamp of een reeks-ID toevoegen aan elke uitvoer om het overs laan van dubbele rijen een trivial bewerking te maken. Zodra de primaire regio is hersteld, wordt de secundaire data base met vergelijk bare mechanismen onderschept.

Hoewel verschillende invoer-en uitvoer typen verschillende opties voor geo-replicatie toestaan, raden we u aan het patroon te gebruiken dat in dit artikel wordt beschreven om geo-redundantie te garanderen, omdat het flexibiliteit en beheer biedt voor zowel gebeurtenis producenten als consumenten van gebeurtenissen.

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-taken bewaken en beheren met Power shell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Fout opsporing op basis van gegevens in Azure Stream Analytics](stream-analytics-job-diagram-with-metrics.md)