---
title: Hoge Beschik baarheid Azure Analysis Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe Azure Analysis Services hoge Beschik baarheid biedt tijdens de onderbreking van de service.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: abfcc38601887cd14509ac436e0344b681e3577e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506749"
---
# <a name="analysis-services-high-availability"></a>Hoge beschikbaarheid van Analysis Services

In dit artikel wordt beschreven hoe u maximale Beschik baarheid voor Azure Analysis Services-servers waarborgt. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Hoge Beschik baarheid garanderen tijdens een onderbreking van de service

In zeldzame gevallen kan een Azure-Data Center een storing hebben. Als er zich een storing voordoet, wordt er een probleem ondertreden waardoor het een paar minuten duurt of dat de uren langer duren. Hoge Beschik baarheid wordt meestal bereikt met server redundantie. Met Azure Analysis Services kunt u redundantie krijgen door extra, secundaire servers in een of meer regio's te maken. Bij het maken van redundante servers om ervoor te zorgen dat de gegevens en meta gegevens op die servers synchroon zijn met de server in een regio die offline is, kunt u het volgende doen:

* Implementeer modellen op redundante servers in andere regio's. Deze methode vereist het verwerken van gegevens op zowel uw primaire server als redundante servers parallel, waardoor alle servers worden gesynchroniseerd.

* [Back-ups maken](analysis-services-backup.md) van data bases van uw primaire server en herstellen op redundante servers. U kunt bijvoorbeeld avond back-ups naar Azure Storage automatiseren en herstellen naar andere redundante servers in andere regio's. 

Als op de primaire server een storing optreedt, moet u de verbindings reeksen in Reporting-clients wijzigen om verbinding te maken met de server in een ander regionaal Data Center. Deze wijziging moet worden beschouwd als een laatste redmiddel en alleen als er sprake is van een onherstelbare regionale Data Center storing. Het is waarschijnlijker dat een Data Center-storing die uw primaire server host, weer online komt voordat u verbindingen op alle clients zou kunnen bijwerken. 

Als u wilt voor komen dat verbindings reeksen op Reporting-clients worden gewijzigd, kunt u een server [alias](analysis-services-server-alias.md) maken voor de primaire server. Als de primaire server uitvalt, kunt u de alias wijzigen zodat deze verwijst naar een redundante server in een andere regio. U kunt de alias voor de server naam automatiseren door een eindpunt status controle op de primaire server uit te schrijven. Als de status controle mislukt, kan hetzelfde eind punt worden doorgestuurd naar een redundante server in een andere regio. 

## <a name="related-information"></a>Gerelateerde informatie

[Back-ups maken en herstellen](analysis-services-backup.md)   
[Azure Analysis Services beheren](analysis-services-manage.md)   
[Alias server namen](analysis-services-server-alias.md) 

