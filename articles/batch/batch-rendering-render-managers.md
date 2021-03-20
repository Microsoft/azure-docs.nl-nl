---
title: Ondersteuning voor rendering Manager
description: De integratie van Azure Batch render Manager gebruiken. Meer informatie over ingebouwde ondersteuning of invoeg toepassingen voor populaire render-managers.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83726448"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Azure Batch gebruiken met Farm beheerders weer geven

Als u een bestaande on-premises render-farm gebruikt, is het zeer waarschijnlijk dat een weergave beheerder de capaciteit van de farm genereren en de taken renderen beheert.

Azure biedt ingebouwde ondersteuning of invoeg toepassingen voor populaire weergave beheerders. U kunt vervolgens virtuele machines van Azure toevoegen en verwijderen, inclusief Vm's met de licentie voor het gebruik van toepassingen voor betalen en gebruiken en virtuele machines met lage prioriteit.

De volgende render-managers worden ondersteund:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Koninklijk Rendering](https://www.royalrender.de/)
* [Deadline Thinkbox](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure render-hub

Azure render hub vereenvoudigt het maken en beheren van Azure-render-farms.  Render hub heeft systeem eigen ondersteuning voor PipelineFx Qube en deadline 10.  Zie [de GitHub-opslag plaats](https://github.com/Azure/azure-render-hub)voor meer informatie en gedetailleerde instructies.

## <a name="using-azure-with-pipelinefx-qube"></a>Azure gebruiken met PipelineFX Qube

De Azure render hub biedt ondersteuning voor populaire weergave Managers, inclusief deadline.  Zie [de GitHub-opslag plaats](https://github.com/Azure/azure-render-hub)voor instructies over het implementeren en gebruiken van render hub.

Scripts en instructies voor het inschakelen van Azure Batch groeps-Vm's die moeten worden gebruikt als Qube-werk nemers, zijn ook beschikbaar in [de GitHub-opslag plaats](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Azure gebruiken met Royal Rendering

Royal rendering heeft ingebouwde integratie van Azure en Azure Batch, waarmee u een render-farm kunt uitbreiden met op Azure gebaseerde Vm's. Zie [de Help-bestanden](https://www.royalrender.de/help8/index.html?Cloudrendering.html)voor een overzicht.

Voor een voor beeld van een Royal render-klant die gebruikmaakt van Azure-integratie, raadpleegt u het [verhaal Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Azure gebruiken met Thinkbox-deadline

De Azure render hub biedt ondersteuning voor populaire weergave Managers, inclusief deadline.  Zie [de GitHub-opslag plaats](https://github.com/Azure/azure-render-hub)voor instructies over het implementeren en gebruiken van render hub.

## <a name="next-steps"></a>Volgende stappen

Probeer de Azure Batch-integratie voor uw render Manager uit met behulp van de juiste invoeg toepassing en instructies op GitHub, indien van toepassing.
