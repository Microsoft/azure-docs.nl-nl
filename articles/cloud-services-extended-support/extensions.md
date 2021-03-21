---
title: Uitbrei dingen voor Cloud Services (uitgebreide ondersteuning)
description: Uitbrei dingen voor Cloud Services (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f9029a36dc3b778e139b4553524e8e2ca6b4bbad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757166"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Uitbrei dingen voor Cloud Services (uitgebreide ondersteuning)

Uitbrei dingen zijn kleine toepassingen die configuratie van na de implementatie en automatiserings taken op rollen bieden. U kunt bijvoorbeeld een Extern bureaublad verbinding in uw rol inschakelen tijdens de implementatie van de Cloud service (uitgebreide ondersteuning) met behulp van Extern bureaublad-extensie.  

## <a name="remote-desktop-extension"></a>Extern bureaublad extensie

Met Extern bureaublad kunt u toegang krijgen tot het bureau blad van een rol die wordt uitgevoerd in Azure. U kunt een verbinding met een extern bureau blad gebruiken om problemen met uw toepassing op te lossen en te diagnosticeren tijdens de uitvoering.

U kunt een verbinding met een extern bureau blad in uw rol tijdens de ontwikkeling inschakelen door de extern bureau blad-modules in uw service definitie of via de extern bureau blad-extensie op te nemen. 

Zie [extern bureau blad configureren vanaf de Azure Portal](enable-rdp.md) voor meer informatie

## <a name="windows-azure-diagnostics-extension"></a>Extensie voor Windows-Azure Diagnostics

U kunt belang rijke prestatie gegevens voor elke Cloud service bewaken. Elke Cloud service functie verzamelt minimale gegevens: CPU-gebruik, netwerk gebruik en schijf gebruik. Als de Cloud service de micro soft. Azure. Diagnostics-extensie op een rol heeft toegepast, kan die rol aanvullende gegevens punten verzamelen. 

Met de basis controle worden gegevens van de prestatie meter items uit rolinstanties gesampled en verzameld met een interval van 3 minuten. Deze basis bewakings gegevens worden niet opgeslagen in uw opslag account en er zijn geen extra kosten aan verbonden. 

Met geavanceerde controle worden extra metrische gegevens bemonsterd en verzameld met intervallen van vijf minuten, 1 uur en 12 uur. De geaggregeerde gegevens worden opgeslagen in een opslag account, in tabellen, en worden na 10 dagen verwijderd. Het gebruikte opslag account wordt geconfigureerd door de rol; u kunt verschillende opslag accounts voor verschillende rollen gebruiken. 

Zie [de Windows Azure Diagnostics-extensie Toep assen in Cloud Services (uitgebreide ondersteuning)](enable-wad.md) voor meer informatie


## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
